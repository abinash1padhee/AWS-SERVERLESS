# DynamoDB Notes

## Parameters to choose a database for your architecture

| Category              | Details |
|-----------------------|---------|
| Workload Type         | Read Heavy / Write Heavy, Patterns with traffic |
| Storage Requirements  | GBs / TBs / PBs |
| Data Durability       | Reliability considerations |
| Performance Needs     | Low Latency, Concurrent Users, Throughput |
| Data Structure        | Relational, Key Value (DynamoDB / DAX / Keyspaces), Document (Mongo), In-Memory, Graph, TimeSeries |
| Schema Flexibility    | Yes / No |
| Cost Considerations   | Pricing impact |

---

## AWS Databases

| Category   | Services |
|------------|----------|
| RDBMS      | RDS, Aurora, RedShift |
| Key Value  | DynamoDB, DAX, Keyspaces |
| Document   | DocumentDB |
| In Memory  | Elasticache, Elasticache for Redis, Memcached |

---

## DynamoDB Overview

- Fast, Flexible, Cost-effective, Fault Tolerant, Secure
- Multi-region, multi-master database (Global Tables)
- Single-digit millisecond performance at any scale
- In-memory caching with DAX (DynamoDB Accelerator, microsecond latency)
- Supports CRUD (Create/Read/Update/Delete) operations through APIs
- Supports transactions across multiple tables (**ACID support**)
- Access patterns must be known ahead of time for efficient design and performance

---

## Tables Info

- Independent Entities
- You control performance at the table level
- Table items stored as JSON (DynamoDB-specific JSON)
- Primary keys are mandatory, rest of the schema is flexible
  - Can be simple or composite
- **Primary Keys**
  - Simple Key → Single attribute (= partition/hash key)
  - Composite Key → Two attributes (= partition/hash key + sort/range key)
- Non-key attributes (including secondary key attributes) are optional

---

## DynamoDB Terms

| Relational Term | DynamoDB Equivalent |
|-----------------|----------------------|
| Rows            | Items |
| Columns         | Attributes |
| Indexes         | Local Secondary Indexes |
| Views           | Global Secondary Indexes |

---

## DynamoDB Consistency

- **Read**:
  - Strong
  - Eventual
- **Write**:
  - Strong
  - Eventual
  - Transactional

---

## Provisioned Capacity

- You pay for the capacity you provision (= number of reads and writes per second)
- You can use auto-scaling to adjust the provisioned capacity
- Uses Capacity Units: **Read Capacity Units (RCUs)** and **Write Capacity Units (WCUs)**
- Consumption beyond provisioned capacity may result in throttling
- Use Reserved Capacity for discounts over 1 or 3-year term contracts

---

## On Demand Capacity

- You pay per request (= number of read and write requests your application makes)
- No need to provision capacity units
- DynamoDB instantly accommodates your workloads as they ramp up or down
- Uses Request Units → Read Request Units and Write Request Units
- Cannot use reserved capacity with On-Demand mode

---

## Calculating Capacity Units

- Calculate capacity units to read and write a 15KB item
- **RCUs with strong consistency**: 15KB/4KB = 3.75 → rounded up → 4 RCUs 
- **RCUs with eventual consistency**: (1/2) × 4 RCUs = 2 RCUs 
- **RCUs for transactional read**: 2 × 4 RCUs = 8 RCUs 
- **WCUs**: 15KB/1KB = 15 WCUs 
- **WCUs for transactional write**: 2 × 15 WCUs = 30 WCUs

---

## Local Secondary Indexes (LSIs)

- Can define up to 5 LSIs
- Has same partition/hash key attribute as the primary index of the table
- Has different sort/range key than the primary index of the table
- Must have a sort/range key (= composite key)
- Indexed items must be ≤ 10 GB
- Can only be created at the time of creating the table and cannot be deleted later
- Can only query single partition (specified by hash key)
- Support eventual / strong / transactional consistency
- Consumes provisioned throughput of the base table

---

## Global Secondary Indexes (GSIs)

- Can define up to 20 GSIs (soft limit)
- Can have same or different partition/hash key than the table’s primary index 
- Can have same or different sort/range key than the table’s primary index
- Can omit sort/range key (= simple and composite)
- No size restrictions for indexed items
- Can be created or deleted any time (only one GSI can be deleted at a time)
- Can query across partitions (over entire table)
- Support only eventual consistency
- Has its own provisioned throughput
- Can only query projected attributes (attributes included in the index)

---

## When to choose which index?

- **LSIs**:
  - When application needs same partition key as the table
  - When you need to avoid additional costs
  - When application needs strongly consistent index reads

- **GSIs**:
  - When application needs different or same partition key as the table
  - When application needs finer throughput control
  - When application only needs eventually consistent index reads
