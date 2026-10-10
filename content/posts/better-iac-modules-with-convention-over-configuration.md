+++
title = "Convention Over Configuration: Writing Better IaC Modules"
date = "2026-10-10"
tags = ["idlc", "iac", "terraform", "pulumi"]
+++

## What's It All About?

When conducting technical interviews, I often hear that good Infrastructure as Code should use versioned, documented modules. That's a good answer, but very few people go further and explain how they design those modules for the people who will use them. In my opinion, that is the difference between simply writing IaC and treating it as a craft.

One approach I rarely hear mentioned is convention over configuration.

Many module APIs require callers to repeat decisions the company has already made. Consider this VM module:

```hcl
module "payments_vm" {
  source = "./modules/vm"

  name               = "payments"
  zone               = "europe-north1-a"
  machine_type       = "e2-medium"
  image              = "debian-cloud/debian-12"
  enable_secure_boot = true
  subnetwork         = module.network.subnetwork_id
}
```

Let's say we normally run our VMs in `europe-north1-a`, use Debian, and enable Secure Boot. Why should I repeat those values every time I create another VM? The better way is to put them in the module as documented defaults and only provide different values when I actually need to.

The GCP ecosystem already has reusable modules, such as those in [`terraform-google-modules`](https://github.com/terraform-google-modules) and Google's [Cloud Foundation Fabric](https://github.com/GoogleCloudPlatform/cloud-foundation-fabric). There is no need to rebuild all of that functionality ourselves. But these modules serve many organizations, so their defaults and interfaces cannot fully reflect our company's conventions.

Without a wrapper, each team may end up repeating the same configuration. I'd put those shared choices in a module of our own and call the general-purpose module from there. We still reuse its implementation, but our teams only need to configure what's specific to their workload.

So what would I give defaults to? I'd start with:

* Naming conventions — specific formats, prefixes, and suffixes.
* Locations — the regions and zones normally used by the company.
* Security defaults — for example, enabling Secure Boot for VMs using compatible images.

Let's go back to the VM example and see what its caller actually needs to provide once those defaults are in place.

## Practical example

I'll use a small standalone module to show the idea; the same defaults can go into a wrapper.

I'll start with the zone. Making it optional only requires adding a default:

```hcl
variable "zone" {
  type    = string
  default = "europe-north1-a"               # <--- DEFAULT
}
```

Here, omitting `zone` always means `europe-north1-a` — no automatic location discovery is needed.

I'd do the same for the image, machine type, and Secure Boot setting. For the internal IP, I'd let GCP allocate one unless I provide a specific address. Here's what the module looks like:

`variables.tf`: 
```hcl
variable "name" {
  type = string
}

variable "subnetwork" {
  type = string
}

variable "internal_ip" {
  description = "Optional internal IPv4 address. When omitted, GCP allocates one from the subnet."
  type        = string
  default     = null                       # <--- DEFAULT
}

variable "zone" {
  type    = string
  default = "europe-north1-a"              # <--- DEFAULT
}

variable "machine_type" {
  type    = string
  default = "e2-medium"                    # <--- DEFAULT
}

variable "image" {
  type    = string
  default = "debian-cloud/debian-12"       # <--- DEFAULT
}

variable "enable_secure_boot" {
  type    = bool
  default = true                           # <--- DEFAULT
}
```

`main.tf`
```hcl
resource "google_compute_instance" "this" {
  name         = var.name
  zone         = var.zone
  machine_type = var.machine_type

  boot_disk {
    initialize_params {
      image = var.image
    }
  }

  network_interface {
    subnetwork = var.subnetwork
    network_ip = var.internal_ip
  }

  shielded_instance_config {
    enable_secure_boot = var.enable_secure_boot
  }
}
```

In this example, the subnet comes from the environment's network module. The caller also configures the Google provider with the project and credentials.

The module implementation still contains the configuration, but its callers no longer need to repeat it:

```hcl
module "payments_vm" {
  source = "./modules/vm"

  name       = "payments"
  subnetwork = module.network.subnetwork_id
}
```

If this particular service needs a larger machine, I can simply add `machine_type = "e2-standard-4"`. The other conventions still apply.

The same goes for the image. Debian is our default, but if I need a custom image for a particular service, I can override it. One thing worth documenting: the default points to an image family, so newly created VMs may get a newer image over time.

For the internal IP, I usually don't need to choose an address myself. Leaving it unset lets GCP allocate one from the subnet. If a service needs to keep the same address when its VM is replaced, I can reserve one and pass it to the module:

```hcl
module "payments_vm" {
  source = "./modules/vm"

  name        = "payments"
  subnetwork  = module.network.subnetwork_id
  internal_ip = google_compute_address.payments.address
}
```

Here, `google_compute_address.payments` refers to an internal address reserved separately in the same subnet. The VM also has no public IP, because the module doesn't include an `access_config` block.

This is the benefit of convention over configuration: **I don't have to repeat the same configuration every time, but I can still change it when I need to.** The defaults should be documented, and changing them deserves care because callers that omit those values will inherit the changes. Of course, if a setting is mandatory, like Secure Boot, a default alone won't enforce it.

That's what I want from an internal module: it should already know how we normally do things, so I only need to configure what's different.

-- Mikolaj
