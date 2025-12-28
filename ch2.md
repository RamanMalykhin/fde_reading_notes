All DE activities follow a lifecycle: 
Generation -> Storage (Ingestion -> Transformation -> Serving) -> Downstream (Analytics/ML/Reverse ETL).
And all of these are affected by a number of undercurrents: Security, Data mgmt, Data Ops, Data Architecture, Orchestration, SWE. They are a kind of bedrock the lifecycle is build upon.

The full data life  cycle is broader, but the above is where a DE is involved as part of their work. 

Generation refers to upstream source systems. Depending on the architecture, they may be extremely varied - several application servers with different schemas all sending to the same DWH. This pattern may be common with IOT systems. Important questions to consider:
- Fundamental nature of the source system
- How is data persisted in the source systems?
- At what rate is data generated?
- How consistent (schema? error rate? duplicates? late-coming records?) is the data?
- Is the schema available and known? How are schema changes handled operationally?
- How frequently should the data be pulled? If the system is stateful, will it be providing CDC or snapshots?
- Does reading from the source impact its performance?

Storage is what backs the data-engineered system in question. It may be a complex DWH or just object storage, or something in between (even S3 supports queries). Choosing the storage is extremely important to the entire system and the following should be considered:
- Is the solution compatible with required write/read speeds? Are there SLAs you must adhere to? Will it bottleneck downstream systems? (As things stand now.)
  - Specific point: data access patterns downstream. What parts of your data are "hot", what are "cold"? Can your system handle this?
- Will the system be able to handle the scale (to the extent that you can predict how up and downstream will scale in the future.)
- Are you using the storage system optimally (read/write patterns)? Do you have the ability to understand the oeprating principles of the system, to the extent that you can tell what is optimal and what is not?
- Is your system a pure storage solution or does it support complex query patterns, etc?  
- Are you capturing metadata? Is your data completely schemaless, flexible schema, enforced schema?
- Do you have legal data sovereignty/regulatory compliance requirements? Does your system adhere to them?

Considerations for ingestion phase (more in ch7):
- What are the usecases for the ingested data and are you really sure it cannot be reused from upstream?
- What is the volume or frequency of arriving data?
- What format is the data in? How far removed is it from the shape (schema, normalization, etc) in which it is usable downstream?
- Will we be doing batch or streaming ingestion? Will it be true milisecond-real-time streaming or microbatch a-la Spark?
  - Is there a specific need for streaming? Is the streaming redundant? Can downstream systems handle the rate of streaming data flow?
- Is the ingestion push-based (source writes to downstream), or pull based (storage queries source), or something in between (message broker, queue, ...)? (These are not cleanly delineated categories.)

Considerations for transformations (more in ch8):
- What is the ROI of doing this transformation for the business? 
- Is it self-isolated or affects other data? Does it do it in a way that makes the data less usable for other usecases?
- What business requirements does this support?
- Are we going to do the transforming in-flight, or after landing in storage? 

Considerations for serving:
- Are the analytics customer-facing or internal? How do we manage access controls?
- Are we serving ML? 
  - Do we have to maintain a feature store solution specifically for their needs?
  - Is the data high-quality enough to reliably feature engineer?
  - Are there organizational boundaries between DE team and ML team (architecture implications)?
  - Is the dataset reliable to represent ground truth? Are any exclusion filters going to bias it?
- Are we doing reverse ETL? How is data delivered back upstream?

Undercurrents:
- Security
  - Access management, application of relevant security policies and roles, password policies, encryption standards.
- Data mgmt
  - Data governance (incl discoverability by stakeholders and accountability), metadata and lineage, data quality, and GDPR/CCPA/etc compliant handling.  
- Data Ops
  - Refers to all oeprations a data team performs as part of KTLO activity. Process automation, change management and processes, observability and monitoring , and incident response.  
- Data architecture
  - See ch 3
- Orchestration
  - Tracking what actions come after what and are triggered by what throughout the entire pipeline, scheduling, etc. Usually done through dedicated tooling (Airflow, Prefect, Dagster, etc.)
- SWE
  - Version-controlling and applying best practices to pipeline logic, orchestration, infrastructure (IaC, GitOps).