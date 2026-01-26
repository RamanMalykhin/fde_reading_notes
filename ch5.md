Many system patterns for generating data upstream exist. A data engineer must be intimately familiar with the way their data gets generated upstream. This is especially important if the upstream data is RDBMS: different dbs have different patterns related to commits, ACID guarantees, etc. 

Some possible upstream data types include:
- Files
  - structured: excel, CSV, specialized data storage formats (Avro, Parquet, Delta, Iceberg)
  - semi-structuredL JSON, XML, CSV
  - unstructured: TXT, images, also sometimes CSV
- APIs
  - REST
  - GraphQL
  - Webhooks
  - RPC
- OLTP databases
  - Typically relational
    - ACID guarantees: atomicity, consistency, isolation, durability
  - Usually upstream from CRUD apps: create, read, update, delete
  - Sometimes insert-only instead of CRUD. More secure but downsides: huge data volume, and heavier lookups
- OLAP databases
  - Columnar storage
  - Optimized for read-heavy workloads
  - Sometimes also inputs, but usually destinations for analytical workloads
- CDC systems
  - Change Data Capture systems capture changes in a database and stream them to downstream systems
  - Examples: Debezium, AWS DMS, ...
- Log streams
  - Who, what, when
  - Encoding options: binary-encoded, semistructured (JSON), plain text
  - Special case: database WALs (write-ahead logs). Usually binary and in a specific format for the database
- Message queues
  - Examples: Kafka, RabbitMQ, AWS SQS, Google Pub/Sub
  - Often used for event-driven architectures
  - Can be used for decoupling systems and buffering data
  - Append-only pattern
  - Important to understand differnt time stamps:
    - Event time: event occurs
    - Ingestion time: event ingested into the system
    - Processing time: event processed by downstream system

Some important practical considerations:
- Databases: 
  - How are lookups handled? Are your reading operations going to impact the performance of the upstream service? 
  - Is there a query-optimizer?
  - How are the CRUD operations handled?
  - Is the data assumed to be consistent at read time? Is there eventual consistency?
  - RDBMS: rows are "relations" between tables, and have diff columns/aka fields. Some one column will be a primary key uniquely identifying each row. Foreign keys link rows between tables. Usually ACID compliant. 
  - NoSQL: usually weaker guarantees than ACID but higher performance. 
    - KV stores: basically a hashmap upscaled to a whole database. Sometimes in-memory: typically used for caching by web services, e.g. Redis, Memcached. Sometimes also persistent, and then used as an input for data pipelines.
      - Fun fact: AWS S3 is a distributed KV store!
    - Document stores: store semi-structured data, usually JSON-like documents. Examples: MongoDB. 
    - Do not support joins. 
    - Typically do not enforce any sort of schema. Easy to use, but hard to parse data from. Uncontrolled schema evolution requires a lot of efforts from downstream systems to ingest and parse the data correctly. 
    - Do not guarantee immediage consistently. Either full scan for ingestion, or use CDC. 
    - Wide-column stores: store data in tables, rows, and dynamic columns. Examples: Cassandra, HBase. Good for timeseries data.
    - Graph databases: store data as nodes and edges. Examples: Neo4j, Amazon Neptune. Good for highly connected data.
      - Usually have their own unconventional query languages, like SPARQL.
    - Search databases: optimized for search operations. Sometimes used by ecommerce to power their website search. Examples: Elasticsearch, Solr. 
      - Often used for log data, full-text search, analytics.
- APIs
  - May be a third-party API, first-party would usually give a DE access to the DB to run CDC on. But sometimes huge complicated systems are used upstream and the easiest way to ingest from them would be via API. 
    - Example: CRMs. Common workflow: get data from a CRM, blend the CRM data through the customer scoring model, and then use reverse ETL to send that data back into CRM for salespeople to contact better-qualified leads. 
  - REST - currently dominant paradigm. Built around HTTP verbs from Roy Fielding's 2000 dissertation. Dissertation outlined many verbs, but only a fraction are pratically used. POST, GET are most dominant. 
    - Most important: stateless interaction. No sessions. REST requests may affect the system, but globally. Interactions are stateless. 
    - In reality session tokens do exist and implementations differ widely. Read the documentation!
    - There may exist a custom library in your language of choice wrapping over the API. May save dev time. 
  - GraphQL - Alternative to REST APIs developed by Facebook. GraphQL - allows clients to specify exactly what data they need. More flexible than REST, but more complex to implement and maintain. 
  - Webhooks - simple event-based data transmission pattern. When source system experiences a specific event, it sends a call to the HTTP endpoint hosted by the consumer (sometimes called a reverse API). 
    - Rare for a DE to interact directly with webhooks, usually a middleware service is used to receive the webhook, like a message queue.
  - RPC - generic term for making a call to run a procedure on distributed system. 
    - Relevant because of gRPC - Google's alternative to REST. Much more efficient than REST because binary. Builds the schema/business logic right in the protocol via so called protobuf files. Usually allows the user to compile/generate a client library. 
- Message queues.
  - Async send data between discrete systems. Built around PubSub (publish/subscribe) semantics. Allow decoupling and thus highly popular in microservice architectures. 
    - Ordering and delivery: do not necessarily expect timeliness. Assume possibility of out-of-order messages incoming (but highly dependent on what your upstream guarantees re: timeliness).
  - Delivery frequency: at least once, or exactly once? If exactly once is not guaranteed, plan for duplicates. 
  - Some terminology:
    - A topic is a named channel to which messages are sent by producers. Topics are usually thematically consistent: for a web store, one topic may serve all purchases. Some of its customers may be fulfilment services, marketing services, etc. 
    - Stream partitioning: subdivision of a stream into multiple streams. Kind of like a multilane freeway. Based on a partition key contained in the message. Important to choose such as to partition evenly, if not - failure to distribute load is called "hotspotting".

A non-technical consideration: important to understand the business architecture, who owns what and how to diplomatically communicate with them. 
- Data stakeholders: own and control access to the data you want.
- System stakeholders: own and control the systems that generate the data. 
Sometimes these are the same team, sometimes not. Important to build good relationships with them.
A DE is often at the mercy of the stakeholders' ability to follow good engineering practices, provide reasonable guarantees and uphold them.
Data contracts have been proposed as a semi-formal way to document commitments between data teams and services. Estabilish SLAs, guarantees, etc. Gaining some adoption, but not nearly ubiquitous yet.

Undercurrent consideration: 
- Security
  - How are you accessing upstream: VPN? Open internet?
  - Are you securing the keys to access the data properly?
  - Do you trust the upstream system to not maliciously corrupt data? 
- Data mgmt
  - Who is the owner of the data?
  - Does the owner provide guarantees wrt data quality?
  - Schema will probably evolve over time. Will you be notified? How? Will you have enough time to handle the change?
  - Are there privacy/legal implications to you having access to the data? 
- DataOps
  - How do you synchronise changes between upstream and downstream systems?
  - How do you find out if the upstream system is having a problem?
  - What is your incident response plan?
- Data architecture
  - You will have little impact on this, so relatively few considerations.
  - How are the upstream system expected to fail. Do you have response plans to these cases?
  - Is data loss possible? Is it acceptable for you? Are you responsible for this?
- Orchestration
  - Is the data available on a fixed schedule? Event-driven? Hybrid?
- SWE
  - How are you accessing the data? REST? RPC? If API: will you have to deal with pagination? IS your database driver (if relevant) compatible with upstream source? Are you able to handle retries, timeouts, and rate limits?
  - Is your code well integrated with an orchestration system?
  - How are you managing parallel access/scaling? Will you have to care about this?
  - How are you handling the deployment of changes? 