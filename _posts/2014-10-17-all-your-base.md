---
layout: post
title:  "All Your Base 2014: A database conference"
date:   2014-10-17 17:30:00
categories: talks ideas
---

I attended the "All Your Base" database conference in Oxford on September 17th, 2014. This
was a single-track one-day conference. Below are my notes, with links to videos of the talks:

## First talk: [Jeni Tennison], Technical Director at the [Open Data Institute][] ([video](http://vimeo.com/album/3108317/video/109863941))

[Jeni Tennison]: https://twitter.com/JeniT
[Open Data Institute]: http://theodi.org/

  - Distribution of information
  - How can everyone benefit from the data reformation?
    - What data can you get?
      - Open data; there's quite a lot of it but should be more
        - Government data; leading the way because it's quite easy to collect data for public good using public funds
        - Philanthropic organisations
        - Private sector data
          - Often closed because it's commercially sensitive; what's useful to the public could be used by the competition
        - Crowd-sourced data, e.g. [OpenStreetMap]
        - Data you own; your personal data that law gives you access to
    - What can you do with it?
      - Tooling to combine, aggregate and analyse data
      - Start simple and accessible - Excel is almost a de-facto option
      - Visualisations, e.g. [CartoDB] for mapping
      - Yahoo! Pipes
  - Publish open data
    - statistical summaries
    - reference data
    - accessible, usable, shareable
    - so that everyone can benefit from the data reformation
  - Make tools that people need
    - visualisation
    - combination/aggregation
    - not just for developers
  - Up to us [technologists?] to make data available to everyone

[CartoDB]: https://cartodb.com/
[OpenStreetMap]: http://www.openstreetmap.org/

### Audience questions

  - How to handle technical illiteracy or target those without access to technology?
    - Target those without technical access indirectly via an intermediary

### Further reading/watching

  - Watch: [Wealth Inequality in America](http://www.youtube.com/watch?v=QPKKQnijnsM)
  - Read: [Positive Linking](http://www.amazon.co.uk/dp/0571279201)
  - Read: [Everything is Obvious: Why Common Sense is Nonsense](http://www.amazon.co.uk/dp/184887216X)

## Second talk: [Yodit Stanton], Founder at [OpenSensors.io][] ([video](http://vimeo.com/album/3108317/video/109904567))

[Yodit Stanton]: https://twitter.com/yoditstanton
[OpenSensors.io]: https://opensensors.io/

  - "Scaling the internet of things"
    - [OpenSensors.io] = clojure shop
    -  Connect an object to a web service, e.g. [React] or [D3]
      - Monitor your cat
    - Connect an object to one or many other objects
    - Which protocols to use? HTTP can be a little bloated
      - [CoAP]
        - Like HTTP but lighter
        - Uses UDP
        - GET, POST, PUT, DELETE
      - [MQTT]
        - PUB/SUB protocol
        - Tiny packets
        - Suitable for low bandwith devices
        - TCP
        - Uses topics (like RabbitMQ)
        - Security: Username/password auth; optional SSL
      - MQTT many-to-many; CoAP one-to-one
    - [Azondi] engine; open-source
      - Project Reactor-based broker for MQTT
      - Uses the [LMAX] Disruptor Ring buffer
  - Don't build an 'Intranet' of things, i.e. proprietary interconnectivity each with its own protocol
  - ChatOps - using chatops to turn off your kitchen light or shutdown your Roomba

[Azondi]: https://github.com/OpenSensorsIO/azondi
[CoAP]: http://coap.technology/
[D3]: http://d3js.org/
[LMAX]: http://martinfowler.com/articles/lmax.html
[MQTT]: http://mqtt.org/
[React]: https://facebook.github.io/react/

### Audience questions

  - Importance of security?
    - e.g. knowing when you're on holiday by monitoring your home's temperature
    - must be taken seriously from day one
      - Yodit: Yes it's important, needs to be considered

## Third talk: [Ian Plosker], Co-founder and CTO of [Orchestrate.io][] ([video](http://vimeo.com/album/3108317/video/109909156))

[Ian Plosker]: https://twitter.com/dstroyallmodels
[Orchestrate.io]: http://orchestrate.io/

  - "Databases, The Cloud And Its Discontents"
  - Software has evolved based on the constraints of hardware
  - "Databases in the Cloud kinda suck"; not easy, doesn't always work the way you'd hope
  - Orchestrate - essentially NoSQL via HTTP
  - Sigmund Freud: Minds forged before civilization and thus sometimes mal-adapted
  - Ian Plosker: Databases were forged before the Cloud and thus mal-adapted to the Cloud
    - Path dependence: choices made due to past constraints which are no longer relevant today
  - Assumptions that storage engines make
    - Disks are local
    - Disk is spinning media
    - Memory pages are contiguous
    - Kernel is omnipotent
    - Records have a repeating form and a consistent size
  - Assumptions distributed databases make:
    - The network is reliable
    - Nodes in a cluster share a switch
    - Nodes ins a cluster are in the same datacentre
    - Switch ingress/egress buffers never fill up
    - Networks are not congested
  - To understand present choices, we must understand the past
    - Writing, paper and libraries: (600 BCE, 105 CE, 2600 BCE)
    - Punch card databases (~1800)
    - Drums (invented 1930, general use 1950s); 'attached storage'; predecessors to hard drives
    - Disks (invented 1954, general use 1960s)
    - Solid state (invented 1950s, general use 2000s)
  - Review of storage and query models
    - File systems (proposed 1958, general use 1970s); e.g. ERMA Mark 1
      - Not efficent for storing data
        - Blocks are an inefficient abstraction
    - Two dominant models in 60s-70s
      - Navigational model; can span storage media, can be really large
      - Hierarchial model
    - Enter the Database Management Systems (DBMS) (1960s, general use 1970s)
      - Relational model
      - Needed btrees to be invented
        - Improved time to search a file
        - Reduced insertion/deletion complexity
        - O(log N) for insert/update/delete/search
    - Summary:
      - 50s, 60s: constraint is size of storage media
      - 70s: Btrees arrive
      - 80s: Relational database systems; PC era
      - End of 80s: fight for relational database market
      - 90s: End of hierarchial/navigational database systems
      - 'Web 1.0': Static sites; more interactive in late 90s
      - 2005: Very few choices for databases; all SQL, all btrees, very homogenous, all employ same tradeoffs
      - 2014: 25+ databases in Production that didn't exist 8 years ago
  - Enter: The Cloud
    - Network is __not__ reliable; congestion
    - Databases not really fit for the Cloud
  - Running out of time; "Orchestrate is the solution"

## Fourth talk: [Laine Campbell], Chief Unicorn and former CEO & Co-Founder of Blackbird.io  ([video](http://vimeo.com/album/3108317/video/110123419u))

[Laine Campbell]: https://twitter.com/LaineVCampbell

  - Laine == Database architect
  - "Scaling MySQL in AWS"
  - RDS and EC2/MySQL: A love story
    - Amazon RDS: Operations managed, easy of deployment, scaling via replication
    - Multi-AZ failover
      - Auto-failover
      - Block-level replication
  - Cost:
    - Instance tax: 35-37%
    - Provisioned IOPS: 15%
    - Lack of transparency can increase downtime
      - Can't do dtrace or tcpdump
  - Cost over non-RDS: $15k USD
    - DBA costs $144K/year = $108 hour (time off, productivity, retention, churn)
    - Automating EC2 is a one time job, RDS tax is ALWAYS
    - Extra cost of RDS = 51 weeks of a DBA working
  - Lock-in:
    - In 5.6, you can replicate out, making this moot
    - Lack of visibility:
      - dtrace, tcpdump, top, vmstat etc.
      - i.e. is NTP in sync? can't see without asking Amazon
    - Lack of control:
      - data security, shared environments, backups?
      - restarts due to exploits, etc.
  - Running on EC2 without RDS
    - OpenStack with Trove, open source
    - Tesora Trove; DBaaS; community edition is free
    - RDS versus EC2
      - RDS for legacy and less demanding SLAs
    - which version of MySQL? MariaDB? XtraDB?
    - storage option charts: ephemeral SSD gives max throughput of 390MBps
      - free with instance cost
      - provisioned IOPS, etc.
  - Definition of scaling:
    - capacity is elastic, automated
    - consistent performance
    - etc.
  - Workload scaling:
    - does it need to be relational?
    - break out workloads into their own clusters
      - facilitate sharding large data-sets
      - segregate specific workload characteristics
    - evalute workload's read/write needs
      - total data dataset size and growth
      - data change
    - Constraints:
      - __write I/O__
      - replication limits single-threaded pre 5.6;
      - parallel per schema in 5.6
      - operational difficulties (lack of experience)
      - Summary
        - main constraint is write IOPS
        - sharding eliminates constraint
        - ways to reduce reads:
          - caching
        - reduce writes:
          - throttling concurrency with queueing and loose coupling to keep write IO down
          - compression: native or application-based (prefer application-based)
    - failover scenarios
      - Amaozon region failure - 1 to 2 times/year
      - failover to another region and bring up application services from snapshots in the event of a region failure
        - bring up slave replicas on failover event
        - 30m downtime but event is rare
  - Cluster management
    - Roll your own...
      - configuration management
      - automation scripting
      - HAProxy
    - Spend some dough
      - RDS
      - Continuent Tungsten
      - Scalearc
    - Bleeding edge "but clotting"
      - Galera, via MariaDB or XtraDB Cluster
      - Works really well; close to Production-ready, running healthcare.gov
  - Resiliency
    - EBS snapshots
      - Rapid redeployment of failed nodes; kill and rebuild
  - Operational visibility
    - Monitoring and alerting: Sensu (Nagios not good for ephemeral environments)
    - Time-series trending: Graphite or OpenTSDB (HBase)
    - Graphing of data: Grafana
    - Logging: Logstash or Splunk
    - Application analytics: Relic or AppDynamics

## Fifth talk: [Patricia Gorla], [Cassandra] Consultant at [The Last Pickle][] ([video](http://vimeo.com/album/3108317/video/110057728))

[Cassandra]: https://cassandra.apache.org/
[Patricia Gorla]: https://twitter.com/patriciagorla
[The Last Pickle]: http://thelastpickle.com/

  - "NoSQL Data Modelling in Apache Cassandra With CQL"
    - Case study of how to build out a data model
  - What's Cassandra?
    - Created by Facebook, open sourced in 2008
    - Distributed, by hash; nodes are rack- and datacentre-aware
    - Eventually consistent (_not_ 'hopefully consistent')
    - Consistency level for reads:
      - quorum
      - one
      - all
      - any
    - Fast; append-only
      - 2.1 - 190,000 wps
      - 2.0 - 105,000 wps
    - CQL = Cassandra Query Language
    - cqlsh CLI tool
  - The hard part: data modelling
    - no concept of joins in Cassandra
    - all data denormalised
    - data will be duplicated
      - how to manage updating data in multiple places
    - identify usage
      - schema can develop but can save headaches by planning in advance
  - Case study: City bike share application
    - List properties of a bike:
      - is it damaged? where is it? is it checked out (in use)?
    - Can the bike be checked out?
      - traditional method: query inside a transaction
      - Cassanda might use a 2-phase commit Paxos 'lightweight transition'; don't use for performance-sensitive tables
    - Get customer details
      - Address would be serialised (sub-columns but can be directly accessed)
    - How many bikes available at a particular station?
      - separate table `bikes_at_stations_count`
    - List trips a customer has taken
        - correlate bike ID to trip ID?
        - flaw: all trips for a bike would be stored in the same partition
          - add abritrary 'bucket' column
    - User data: use `WITH CLUSTERING ORDER BY` to find most recent
  - Conclusion:
    - There is hope
    - Understand access patterns
    - Be mindful of the storage engine

### Questions

  - How do you guarantee uniqueness?
    - You can't; Cassandra is append-only
    - Uniqueness must be enforced by application; e.g. UUID

* * *

Lunch

* * *

## Sixth talk: [Ben Foxall], Developer at [White October][] ([video](http://vimeo.com/album/3108317/video/110060328))

[Ben Foxall]: https://twitter.com/benjaminbenben
[White October]: http://www.whiteoctober.co.uk/

  - "Data - A Post-prandial Demo..."
  - Not a traditional talk [notes may be sketchy]
  - Collecting transient things from multiple devices
    - Go to http://bit.ly/aybstuff to vote on questions; click button to vote
    - Using D3 to visualise the data
      - Dots light up on visualisation
    - Convert to bar graphs, heat map showing correlation between responses
    - Enter your Twitter handle
      - Graph followers, followees; use Twitter avatar in visualisation
    - [Firebase](https://www.firebase.com/) to store data
    - Tweet each other; show connections as a graph


## Seventh talk: [David Mytton], Founder & CEO of [Server Density][] ([video](http://vimeo.com/album/3108317/video/109937457))

[David Mytton]: https://twitter.com/davidmytton
[Server Density]: https://www.serverdensity.com/

  - "Stories from Production - Scaling, failure and (avoiding) data loss"
  - Server Density does web site monitoring
  - Production war stories from Server Density
    - Architecture
    - Performance
    - Where to host?
    - Downtime
    - Preparation
  - ~100 servers running Ubuntu 12.04 Precise
  - 50:50 virtual/dedicated
  - 200Tb/m processed data
  - Nginx, Python, MongoDB
  - Softlayer 1TB RAM >5GB SSDs
  - Advantages of virtualisation:
    - Easy to manage
    - Fast boot
    - Easy to resize/migrate
    - Templating/snapshots
    - Containment
  - Disadvantages of virtualisation:
    - Another layer
      - Hypervisor overhead
      - Host contention
      - Poorer I/O performance
  - Bare metal advantages
    - Dedicated resources
    - Direct access to harware
    - Customisable specification; not constrained to a pigeon-hole
  - Disadvantages of bare metal
    - Build/deploy time
    - Lead time can be weeks
    - More difficult to resize (need to replace physical hardware)
    - Difficult to migrate/snapshot
    - High capital expenditure; lifetime (less efficient as it gets older)
  - Performance problems?
    - Move to bare metal
  - Key performance factors
    - Network; round-trip time (replication lag); consider speed of light (fibre optic connections)
    - Memory; much faster than disk but expensive; SSDs are cheaper
  - Why use the Cloud?
    - Flexible
    - Unlimited resources
    - Cheap to get started (no/little CapEx)
  - Why use colocation?
    - Vastly cheaper
  - Cloud outages
    - Amazon US East region 'service event'; excellent post-mortem reports
    - No matter how much you spend, there are always outages
  - Preparation
    - Rotations (on call)
    - Reachability (no phone signal? 'do not disturb'? phone upgrade?)
    - Work the next day after responding to an alert? Take time off next day.
    - Documentation:
        - Searchable
        - Easy to edit
        - Independent of your infrastructure
    - Unexpected failures
      - Communication systems (what if HipChat/Slack is down?)
      - Network connectivity
      - Access to vendor support
    - When you get an alert:
      - Incident response checklist
      - Log incident in JIRA
      - Log into Ops War Room
      - Public status post
      - Initial investigation, followed by post-mortem
    - Key response principles
      - Log everything
      - Frequent public status updates
      - Gather the team
      - Escalate issues with vendor support

## Eight talk: [Laura Thomson], Senior Engineering Manager at [Mozilla][] ([video](http://vimeo.com/album/3108317/video/110088288))

[Laura Thomson]: https://twitter.com/lxt
[Mozilla]: https://www.mozilla.org/en-US/foundation/

  - "Many Moving Parts: Monitoring Complex Systems"
  - Laura: "I'm not a sysadmin, I'm a developer"
  - Everyone has experienced: http://cdn.meme.am/instances/500x/22605665.jpg
  - System: Socorro
    - Telemetry system for Firefox
    - Receives data when Firefox crashes
    - Add comments to the crash reports, they get prioritised; "Hi Laura!"
    - Crash stats: https://crash-stats.mozilla.com/home/products/Firefox
    - Use multiple databases to take advantages of different aspects of CAP
      - HBase, PostgreSQL, Elasticsearch
    - 3000 crashes per minute, 3 million per day
    - Crash size 150k - 20MB
    - ~800GB in PostgreSQL
    - Like a firehose
  - Shipping Firefox
    - Complex architecture
    - 4k build and 10k test jobs per day
    - topped 100k/day in August
    - record: 690 commits in a single day in August
    - ~6000 machines
    - 80% of build and 50% of test in cloud
    - all builds using Amazon spot instances, 75% saving in costs
  - How to monitor complex systems
    - Pull data for normal operating systems
    - Push soft alerts for warnings
    - Pages for critical issues
    - Analytics tell you: "Is this load normal?"
      - visualise healthy system performance
      - makes it easier to find anomalies
    - monitoring coverage is logically equivalent to test coverage
      - (and suffers the same issues)
      - easy things to monitor/test versus things we really care about
      - 'more' is not better; noise is enemy number one
    - Thomson's Hierarchy of Monitoring
      - Diagnostic
        - Host DOWN; 500 HTTP errors; replication lag
        - You know where too look; you know what you need to fix; not always simple but well-defined
        - Log monitoring
            - Something is wrong; maybe upstream?
            - Why is it useful?
              - The thing you don't know how to monitor yet
        - Monitoring connection errors
          - 1% errors are normal; 5% mean something bad is happening
        - Aggregate; show error rates
      - Trend monitoring
        - Disk is 85% full; how fast did it get into that state?
          - Rates of change very important to monitor
      - Performance monitoring
        - e.g. NewRelic, sentry
        - tooling is improving
        - how long does front page take to load?
        - need threshold/trend alerting
        - watch changes following deployments or new versions
        - Transactions/hour, conversion rates; is something broken if there's an anomaly?
    - Automated monitoring is better than (angry) human monitoring
    - Monitors == tests
      - Run tests on Prod, but be careful (the "Knight disaster")
      - If you have an outage that wasn't monitored, add a check (like you'd protect against a bug regression)
    - Gracefully degrade
      - Decouple unreliable components
      - Load/feature shed; temporarily ditch unimportant functionality
      - Automate recovery
    - What changes in larger systems?
      - Thresholds more interesting than single alerts
      - Many alerts should become warnings
      - Avoid alert fatigue

## Ninth talk: [Ola Gasidlo], Frontend & Javascript Developer at [Hood.ie][] ([video](http://vimeo.com/album/3108317/video/110094274))

[Hood.ie]: http://hood.ie/
[Ola Gasidlo]: https://twitter.com/misprintedtype

  - "Say Hello to Offline First"
  - Huge dependency on online applications
    - Need to find ways to stay connected even when offline
  - Global problem; connectivity not omnipresent
  - Need to change the way we build applications
  - Some good offline examples; Google Maps (more recently); Gmail, minutes.io
  - What are the problems we're facing?
    - We know almost nothing about offline users
  - Offline != error handling; it's a recurring state
  - Inform user they're offline?
  - Build trust
    - Trust that the app won't lose data
      - So the user need not worry about losing connectivity
    - Redesign the save icon
  - Organise data
  - Graceful errors
  - Decide what metrics we base architectural decisions on
  - Before, the server was always the source of trust; client completely dependent
    - Need to decouple this relationship
  - How to handle disconnection
    - Block requests? Ignore request? Do nothing?
    - Use sensible timeouts (no one will actually wait for one minute)
    - Try buffering - flush early to show enough data for end-user to read while the rest loads
    - [Hood.ie](http://hood.ie) = open source javascript library
      - Three tiers:
        - Frontend (client-side)
          - Only speaks to hoodie.store
            - Hoodie uses Pouch for storage
          - Do you even need to store data server-side, aside from user credentials?
        - Hoodie Sync
          - Client-side interface between frontend and backend
        - Backend (server-side)
          - REST interface to CouchDB, used by Hoodie Sync when device is online
          - Plugins (node.js)
      - Eventual consistency
      - Application cache manifest
        - Fast
        - Offline-first
        - But application cache is a douchebag
          - Serves cached content even when online
          - Only updaes if manifest changed
          - App cache is an additional cache
          - Never change the manifest URL
            - Ever. User still sees old app manifest
          - Non-cached resources, e.g. images will not load
          - Use appcache-nanny
            - additional cache
            - deals with all requests
            - offline first, push notifications and more
    - First law of offline-first: Don't lose any data
    - Pouchdb is inspired by CouchDB
  - Offline should be a feature

## Tenth talk: [Dr. Alistair Hann], CTO at [Skyscanner][] ([video](http://vimeo.com/album/3108317/video/110102451))

[Dr. Alistair Hann]: https://twitter.com/alistairhann
[Skyscanner]: http://www.skyscanner.net/

  - "SomeSQL - Scaling in a Changing World of Data Stores and Hardware"
    - AKA "Using all the databases"
  - Skyscanner: 150 million live searches per month
  - Skyscanner searches and aggregates data then makes it available via APIs
  - When searcing:
    - Which websites should we show? Which will have the most relevant data and good pricing?
    - What pricing do we already have cahced?
    - Live update what we still need
    - Return prices to user
    - Clean up and save new data
  - Were using Microsoft; SQL server
    - Introduced read layer to scale horizontally
    - Started partitioning tables
    - Queueing tables
    - But there was latency: cached result not necessarily available on second request
    - Then added memcached to cache prices
    - Was OK but not sustainable
      - Move from queueing tables to use RabbitMQ
      - Couchbase to cache prices (rather than memcached)
      - Swapped out network storage for SSDs
      - Then removed SQL server; rely entirely on RabbitMQ and Couchbase
  - New car hire service
    - More complicated queries so used map-reduce for those in Couchbase
    - Which car hire companies cater to certain itineraries - lots of aggregation required
  - Hardware landscape changing
    - Flexible flight search == demanding load
    - Limited by IOPS
    - Tried Fusion I/O cards for 0.25M mixed IOPS
    - Too many IOPS, started re-architecting applications to make use of commodity hardware, i.e. Amazon EC2
    - Now using Thrift; Hadooop/Amazon Elastic MapReduce
  - Distributed commit log - see [blog](http://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying) from LinkedIn engineer
    - Now using [Kafka](http://kafka.apache.org/), which receives raw events from fluentd
    - Searchable using Elasticsearch
  - Timetable data still using relational database; makes sense given data structure
  - Using PostGIS (PostgreSQL) for geo searches
  - Still facing the same challenges
    - RAM and disk I/O concerns
    - Administration (skills)
    - Security (NoSQL, not NoSecurity)
    - Data insertion and retrieval
    - Monitoring and alerting
    - Performance optimisation

## Eleventh talk: [Monty Widenius], Creator of [MySQL] and [MariaDB][] ([video](http://vimeo.com/album/3108317/video/110123354))

[MariaDB]: https://mariadb.org/
[Monty Widenius]: https://twitter.com/montywi
[MySQL]: https://www.mysql.com/

  - "The Current State and Future of MariaDB"
  - Why Maria?
    - Oracle bought MySQL
    - Wasn't clear what would happen
    - Keep the MySQL talent together
    - To ensure that a free version of MySQL always exists
    - MariaDB Corporation employees +70 people, +20 of which are engineers working on MariaDB
    - MariaDB Foundation to prevent yet another fork ("another child")
  - Drop-in replacement for MySQL server
  - Enable feedback plugin to help MariaDB know what to focus on
  - 10.1 will include Galera, multi-master replication
  - MariaDB 5.2:
    - SphinxSE text search
    - Virtual columns
    - Extended user statistics
    - Segmented MyISAM key cache
    - Pluggable authentication
  - MariaDB 5.3:
    - Faster subqueries (subquery optimisation)
    - Faster joins; batch key access (BKA)
    - NoSQL support
      - 50% faster HANDLER commands
      - each row can have a different set of columns
  - Support for storage backends in MariaDB 10.0
    - LevelDB
    - Cassandra
    - Connect
    - RocksDB
    - TokuDB
  - Support for thread pools
  - MariaDB 10.0 rather than 5.6
    - MariaDB 5.5 was already a superset of MySQL features
    - Code from MySQL 5.6 not portable to MariaDB (some code too complex to port over or could be done better)
  - Much better GIS support in MariaDB 10.0
  - Global transaction ID in MariaDB 10
    - Can be enabled/disabled independently and online for both masters and slaves
    - Supports multi-source replication
