# Data engineers and data engineering
Data engineering is development, implementation and maintenance of processes and systems that  take in raw data and produce high-quality and consistent information that supports downstream (business) usecases. 

Data engineering generally operates within a "lifecycle": 
- Generation 
- Storage
- Ingestion
- Transformation
- Serving

And has a number of "undercurrent" topics that are relevant in all the steps of the lifecycle:
- Security
- Data management
- DataOPS
- Data architecture
- Orchestration
- Software engineering

## History 
- 1980s-2000, Inmon formulates DWH as a concept, IBM & Oracle develop the relational DB and SQL. Kimbal and Co come up with data modelling as a school. BI is the buzzword of the day. Contemprorary approach to scaling analytics are MPP dbs, usually proprietary, expensive and heavily licensed.
- Early 2000s, dot-com boom leaves few survivors. Monolithic RDBMS DWHs fall out of favour. Hardware becomes a lot cheaper. Big data (aka 3Vs: Velocity, Variety, Volume) is a buzzword of the day. 2004, Google pioneers MapReduce, 2006, Yahoo creates Hadoop and entrusts to Apache. All still relevant today. Amazon starts a significant internal infrastructure program (EC2, S3, DynamoDB) and starts offering it commercially as AWS, pioneers pay-as-you-go billing. Others (Google GCP, MS Azure, DO) follow, public "hyperscaler" cloud is a breakthrough in how software is developed and operated. 
- 2000s and 2010s, Hadoop-based tool ecosystem rapidly matures. Pig, Hive, Dremel, HBase, Spark etc all hail from this era. DE becomes code-first, reliance on GUI-based tooling is out of fashion. Engineering focus shifts away from core tech to delivery. The hype is immense and eventually ceases: the term "big data" becomes overused and companies do not see value in maintaining expensive big data engineering teams.
- 2020s, evolution is rapid. Focus is on high-level and abstracted tooling and away from low level details. Data governance becomes important, and as business models of enterprise data software vendors have become friendlier, smaller companies adopt them.
# Data maturity levels
Data maturity is the progression towards higher utilization, capabilities and integration of data within a company or organization. It shows how much and in what way data is leveraged as a competitive advantage. 
- Stage 1 is "Starting with data": 
  - The company does not have a lot of buy-in into data as advantage. Data team is single-digit headcount. Data engineers are generalists and roles overlap with SWE, DS, etc. Most requests are ad-hoc and ML efforts are inconsistent. 
  - The DE is focused on getting buy-in, defining the right data architecture, and builds a solid foundation of DA and ML.
  - Pitfalls are waning organizational buy-in, silos between DE and downstream teams, and overuse of custom solutions where off-the-shelf would suffice and be cheap.
- Stage 2 is "Scaling with data":
  - The company has already recognized value and has some formal practices around data. It is not yet very data driven. There is specialization between DEs, focusing on different lifecycle stages.
  - The DE is focused on estabilishing formal practices, creating scalable architectures, adopting DevOps and DataOps practices and building support systems for ML.
  - Pitfalls are too eagerly adopting the newest and most fashionable technologies, scaling the DE human resources and builing maintainable systems, and remaining pragmatic in terms of showing value.
- Stage 3 is "Leading with data":
  - The company is data driven. The buy-in is ultimate. Automated pipelines have made ML and DA mostly self-serve. 
  - DE is focused on automation, custom tools where the advantage is genuinely delivered, "enterprisey" aspects such as governance and quality, and lineage and catalog systems.
  - Pitfalls are complacency in the face of competition and growing volume, and pursuing expensive and useless hobby projects.
  
# Responsibilities and types of DEs
DE is a holistic practice. The required skills are among business and technical.
- Business:
  - Communication with technical and non-technical stakeholders
  - Scoping and gathering requirements
  - Understanding philosophical foundations of Agile, DevOps, DataOps
  - Cost control
  - Continuous learning
- Technical:
  - SQL
  - Python
  - JVM languages
  - bash

DEs can be organized along two important dichotomies - Type A vs Type B, and external vs internal-facing.
- A/B:
  - Type A stands for Abstract. Uses mostly off-the-shelf tooling. Manages the whole lifecycle. Works across all types of maturity.
  - Type B stands for Build. Builds custom systems. More typical in Stage 2 and 3 companies.
- External/internal:
  - External-facing engineers build systems that interface with end-user exposed applications out of the company's direct control. A lot of focus is on transactional processing, handling very heavy concurrency load, and security (esp. in mulitenant scenarios).
  - Internal-facing engineers focus on internal stakeholders. Focus on concurrency and security is less: more important is serving downstream analytics/ML.  

# Who data engineers interface with 
- Upstream
  - Data architects
    - Provide guidance, interface with non-technical stakeholders on a high level. Role not very prominent in cloud-first companies.
  - SWEs
    - Provide context on the data that is being generated by upstream applications. 
  - DevOps/SRE
    - Generate data through operational telemetry, sometimes consume it through dashboards. May interface w/ DE through coordinating deployment of data systems.
- Downstream
  - DS
    - Heavy users of DE products. Spend a lot of time on cleaning/data prep. Collaboration with DE should reduce this work as much as possible and let them focus on research, model tuning, etc.
  - DA
    - Heavy users of SQL warehouses for EDA and dashboard platforms, frequently subject-matter experts on data. Provide requirements on reporting pipelines.
  - MLE/AI researchers
    - Overlap with DE and DS to some extent. Need support from DE to assist with productionalization and with delivery of data. 
- Misc
  - Leadership 
    - many positions: CEO, CIO, CTO, CD(ata)O, CA(nalytics)O, CA(lgorightms)O-2
  - Project managers 
    - Need to interface to prioritize and sprint-plan
  - Product managers
    - Similar interface as SWEs, balancing activity of teams against customer, biz needs.