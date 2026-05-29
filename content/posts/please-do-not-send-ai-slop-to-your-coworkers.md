+++
title = "Please Do Not Send AI Slop To Your Coworkers"
date = 2026-05-29
tags = ["ai", "communication"]
+++

On our helpdesk, we recently received a message like this. I obfuscated the details from the original message, but the structure is unchanged.

<details>

<summary>Message from the developer</summary>
<br>

------ START OF THE MESSAGE -----

**Summary**

The @acme/ui CDN buckets serve manifest.json without an Access-Control-Allow-Origin header, so the @acme/ui dev harness cannot read it cross-origin and shows every environment as "unreachable". We need CORS enabled on the buckets cdnstaging (staging) and cdnprod (production). Preferred fix: grant the deploy service account the storage.buckets.update permission so the deploy pipeline applies and maintains CORS automatically.

**Other relevant information**

**Context.** The @acme/ui dev harness ("playground", deployed on GitLab Pages frontend.example.tech and also run on localhost during development) shows which @acme/ui version is live in each environment by fetching that environment's manifest.json cross-origin. remoteEntry.js is unaffected because it loads as a `<script>` (CORS-exempt); only the JSON metadata read via fetch() needs CORS.
**Evidence.** The manifest serves correctly (HTTP 200) but with no CORS header:
```
curl -sI -H "Origin: https://frontend.example.tech" \
  ttps://staging.acme.com/cdnstaging/acme-ui/manifest.json
# -> HTTP/2 200, no access-control-allow-origin header
```
**Root cause.** The deploy pipeline (core-ui/shell repo, gitlab-ci/20-deploy-ui.yml, .deploy-ui-template) already runs an idempotent, non-clobbering gsutil cors set on every deploy, but it is best-effort and currently a no-op: the deploy service account (CI variables GCLOUD_STAGING_KEY and GCLOUD_PROD_CDN_KEY) has object-level rights (storage.objects.*) but not the bucket-level storage.buckets.update that CORS configuration requires.
**Requested change - Option A (preferred, future-proof).** Grant the service accounts behind GCLOUD_STAGING_KEY and GCLOUD_PROD_CDN_KEY the storage.buckets.update permission (a custom role with storage.buckets.get + storage.buckets.update, or roles/storage.admin) on:
- gs://cdnstaging (staging)
- gs://cdnprod (production)
The pipeline step then applies and keeps the CORS policy correct on every deploy, with no manual upkeep, and it automatically covers any future bucket that uses the same deploy template.
**Option B (fallback, one-time).** A DevOps operator runs once per bucket:
```
echo '[{"origin":["*"],"method":["GET","HEAD"],"responseHeader":["Content-Type"],"maxAgeSeconds":3600}]' > /tmp/acme-ui-cors.json
gsutil cors set /tmp/acme-ui-cors.json gs://cdnstaging
gsutil cors set /tmp/acme-ui-cors.json gs://cdnprod
```
This works immediately but is not self-maintaining (lost if a bucket is recreated; does not cover new buckets).

**CORS policy.**

```json
[{ "origin": ["*"], "method": ["GET", "HEAD"], "responseHeader": ["Content-Type"], "maxAgeSeconds": 3600 }]
```
origin: ["*"] is appropriate here: these are public, non-credentialed CDN assets (build metadata plus JS bundles that are already served publicly), the access is read-only (GET/HEAD), and no cookies or credentials are involved, so the wildcard carries no data-exposure risk.
**Done when.** For both buckets, a request carrying an Origin header returns the CORS header:
```
curl -sI -H "Origin: https://frontend.example.tech" <base-url>/acme-ui/manifest.json | grep -i access-control-allow-origin
```
The pipeline post-deploy verify step (which already checks for this header) then passes instead of warning.

**Importance and priority**

Internal - developer tooling (the @acme/ui dev harness environment comparison). Not customer-facing, no production impact. Perceived priority: Low.

----- END OF THE MESSAGE -----

</details>

<br>

This is the kind of message AI makes dangerously easy to produce: long, polished, and expensive for someone else to process!

And then, at the very end, we see: "Perceived priority: low".

If you use AI for internal communication, use it to compress, not to expand. This could have been three sentences.

-- Mikolaj
