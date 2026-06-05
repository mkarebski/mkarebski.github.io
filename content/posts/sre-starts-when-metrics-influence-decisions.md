+++
title = "SRE Starts When Metrics Influence Decisions"
date = "2026-06-05"
tags = ["sre", "reliability"]
+++

I'm currently rereading Google's Site Reliability Engineering book, and it made me think about how companies adopt
SRE in practice.

Many companies naturally adopt parts of SRE as their systems grow. They add monitoring, alerting, dashboards,
incident reviews, and postmortems because at some point operating without them becomes too painful.

But there is an important step that is easier to miss: connecting those signals back to business decisions.

Reliability metrics should not live only in engineering dashboards. They should become one of the inputs to roadmap
discussions, next to customer needs, revenue goals, and strategic initiatives.

Error budgets are one way to make this connection visible. They are not the whole of SRE, but they show the kind of
feedback loop SRE tries to create.

When the budget is healthy, the organization can consciously choose to invest more in new features. When it is being
consumed too quickly, it should trigger a discussion about stability, bugs, performance, or technical debt.

The point is not that reliability metrics should automatically overrule every other priority. The point is that they
should be visible when priorities are discussed. A roadmap does not have to be driven only by reliability, but it
should be sensitive to it.

If reliability signals never influence planning, they become just another dashboard: useful for engineers, but
disconnected from the decisions that shape the product.

-- Mikolaj
