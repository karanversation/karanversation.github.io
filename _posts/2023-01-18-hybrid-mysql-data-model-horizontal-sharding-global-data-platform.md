---
layout: post
title: "A hybrid MySQL data model with Horizontal Sharding and a Global data platform"
categories: [programming]
date: 2023-01-18
---

![MySQL Hosting Platform](https://cdn-images-1.medium.com/max/800/0*L7YtvCgGaabAJnZL.png)

*Image src: [https://geekflare.com/mysql-hosting-platform/](https://geekflare.com/mysql-hosting-platform/)*

A very popular interview question revolves around choosing MySQL vs NoSQL data stores. This decision is much more relevant for early startups as their choice determines potential data scalability and performance bottlenecks in the future. A smart decision at this point can save painstaking effort to redesign and mass-migrate data later.

For us, our final data model ended up helping us build our own sharding scheme that scaled our database infra by 100x, and also build a cross-region replication framework with a team of just two engineering in a span of ~6 months.

Let's look at the high-level database requirements of a typical startup -

- The overall system needs to be very stable and easy to operate and maintain
- The schema should be easy to update and flexible to avoid large structural changes in the future.
- Ok to have not petabyte-scale upfront, but easy to scale both vertically and horizontally in the near future as more customers get onboarded.
- Has strong documentation and community support to help with quick decision-making.

Now MySQL ticks a lot of checkboxes above and provides many inbuilt DB features (auto increment, indexes, partitions) that make it easy to create efficient datastores upfront with relative ease.
But the matter of fact is — MySQL was originally designed as a single-node system and not with the modern distributed data center setup in mind. Also, unless very carefully planned, audited and updated, MySQL schema can become too rigid to help accommodate needs of a dynamic data model.

At my current organisation, our data architects realized this early on and thought of how to implement a datastore that gives us the best of both worlds -

- Excels at traditional DB properties (ACID) providing us DB features like auto increment, indexes, paritions, etc.
- Has a balanced read/write performance
- Doesn't have us worried about the future limitations of scale
- And best of all — lets us use SQL, which can be called the 'English language of the database world'

To achieve this, we implemented our data model in MySQL with a few key ground rules -

- No SQL foreign key relationships
- No JOINS in SQL for production queries
- By default, prefer columns instead of an opaque JSON blob
- JSON is OK for certain data that may be unstructured
- Denormalize data in tables based on use case, caching and write/update paths

It might be evident from above that each of our table has one set of individual columns and another set of JSON blobs. This helps us achieve a normalization and denormalization hybrid.

Each top-level entity — like candidate profile or job position — has its table with standard individual columns that we may frequently use in indexes, eg: timestamps, users, category, etc.
The json blob field, on the other hand, may store any unstructured data that is directly associated with table entity. Eg: Details of certifications of a profile as a certificates_json column in profile table.

This follows a set of second-level entities like profile tags, notes, etc. that need a reference to the original top-level entity — profile in this case.

While we may store the id of the top-level profile in a column in profile_tag for instance, we do not establish a SQL foreign key relationship. This helps us still keep relationships loose.

A top-level entity may have a one-to-many relationship with other entities just by aggregating entity ids as part of the data_json field.

Our tables are designed in a way that most application lookups are

## Sharding MySQL

The true merit of our data model showed up once we started implementing our own custom application-level sharding on top of MySQL.

Almost 95% of our data is logically partitioned on basis of customer ids. We needed a way to physically partition these customer ids into multiple database clusters without losing out on O(1) lookup performance.

Introducing a layer of indirection in between, we mapped customers to logical shards and shards to physical clusters. This made sure we could distribute a customer in one or many clusters, or have multiple customers present on the same physical cluster. Our data infrastructure could operate in both single and multi tenant modes.

Implementation — We use entity id to encode details of which shard a customer maps to. Each table row also has the customer_id.

```xml
entity_id = <shard_id> << A | <sequence_id>
customer_id
```

<sequence_id> is generated from a sequence table that has an auto_increment field, while there is no auto_increment field in our main data table.

Now once our metadata stores mapping between customer_id to shards and shards to physical clusters, so while making db connections we can pick the correct cluster or set of clusters to execute the query in.

## Shard migration

Keeping our sharding scheme backwards compatible, we had used shard_id 0 for all these customers. But with more data we needed to 'extract' these customers out from shard_id 0 to newer shards existing on separate clusters.

To achieve this, we created a shard migration framework that was capable of performing in-place id translation from one shard_id to another. We were able to achieve this in a simple way due to the no-foreign-key constraint.

## Global data platform

While operating in data stores across multiple AWS regions — some of these did not support AWS Global clusters. We hence wanted to build a equivalent global replication framework, that can make sure clusters across regions are eventually consistent.

We again made use of our sharding strategy to include a global bit -

```
entity_id = <global_bit> << B | <shard_id> << A | <sequence_id>
```

We also implemented SQS based propagation of db save operations that are handled in the other region by a batch processing framework.

Originally delivered as a Tech Talk at Rootconf 2021 — Choosing Datastores

[https://hasgeek.com/rootconf/choosing-datastores/sub/a-hybrid-mysql-data-model-with-horizontal-sharding-76HxjKhwwWXVbmUvbGphXT](https://hasgeek.com/rootconf/choosing-datastores/sub/a-hybrid-mysql-data-model-with-horizontal-sharding-76HxjKhwwWXVbmUvbGphXT)
