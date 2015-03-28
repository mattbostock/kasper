---
layout: post
title:  "Scale Summit 2015: Performance and scalability unconference"
date:   2015-03-28 21:46:21
categories: talks ideas
---

This year's [ScaleSummit](http://www.scalesummit.org/) was excellent, as it was
[last time](http://tech.mattbostock.com/2014/03/23/scale-summit/). I would
strongly encourage anyone involved in the scaling of large systems to attend.
Thanks to [@bruntonspall](https://twitter.com/bruntonspall),
[@jtopper](https://twitter.com/jtopper),
[@scalefactory](https://twitter.com/scalefactory) and
[@jonty](https://twitter.com/jonty) for organising.

Below are my rough notes taken in the sessions I went to. Notes on other
sessions can be found in:

* [Baz Bremner](https://twitter.com/bazbremner)'s [GitHub
  notes](https://github.com/bazbremner/scalesummit-2015-notes)

### First session: Multi-cloud PaaS

- VPC per app
- Engineers in each division; product teams inc. engineers; own app in
  Production
- OH: "I don't believe in punctuation, makes it looks like Perl"
- Tightly coupled to AWS due to services used
- Deployments managed by platform team (based on templates), product teams
  deploy
- Blue/green rolling deployments
- AWS rate limits; some are hard limits (network NAT, etc); AWS account per
  development team
- Separate AWS accounts for Production and testing environments
- Core team managed SSH bastion hosts and DNS
- Core team only called out if AWS is down or rate limits hit
- Hybrid - AWS and bare metal
- London PaaS user group
- Developers tend to want to use AWS-specific products
- Application developers woken up at 2am on cloud platform - alarm trips, call
  developers
- Or make application developers the first-line support; help from core team if
  required (e.g. second opinion)
- Core team have little domain knowledge of applications; worse people to call
  out
- "Full stack engineers" - no dev/ops split - everyone should know about
  everything?
- Docker and Mesos on Hybrid cloud, Marathon, generate a new repository as a
  template; git push to environment
- GoCD as alternative to Jenkins
- PCI-compliant servers outside of Mesos
- Secrets mounted via volume mounts, no access to hosts
- SmartStack for service discovery, on each localhost
- Logging in SmartStack
- HACheck; check constant healthchecks received
- Container introspection for outdated libraries
- Product teams must ensure mechanism for checking outdated libraries on
  arbitrary OS and Docker images rebuilt
- Deadline of 48 hours for security update otherwise service switched off
- Docker container security isolation? Docker proxy daemon (per-user socket);
  intercepts volume mounts; turns off raw sockets, suid, guid, restricts what
  can be mounted
- CloudFoundry have at least one pair of developers in each team that review
  external contributions
- Docker and CloudFoundry possible (CloudFoundry version 3), using Garden

### Second session: Queues; RabbitMQ and others

- Used to use RabbitMQ; then Redis; then RabbitMQ
- RabbitMQ overly complicated, difficult to maintain
- Celery client, then PyRes, wrapper around Pika (bad experiences of Celery;
  don't use)
- ActiveMQ worse than RabbitMQ
- Samza
- Beanstalkd for job queue
- Pub/sub versus work queue
- Redis not well suited to queues
- Beanstalkd fell over, ate its own queue once, some good experiences
- SQS; be aware of semantics
- PostgreSQL for queueing? Notification system; in memory row-level locks
- Publisher confirms if you really care about not losing messages; e.g.
  financial data
- Federated clusters
- Cluster mode risks losing data
- More exchanges, gets worse; especially if flushing to disk with poor IOPS
- Delayed retrying queues; dead-lettering queues
- OH: "Queuing is fine so as long as there is no queue"
- Several thousands bindings fine
- Disque? Very new
- ZeroMQ is risky if your code is not robust
- Beware of Redis master persisting to disk and invoking the OOM killer and
  slave deleting all data
- Need to apply back pressure if too many messages; better to fail early than
  back up for long periods of time
- The less queueing the better; do you really need a queue?
- Beanstalk really nice
- Have a look at Darner on-disk queue: https://github.com/wavii/darner


### Third session (after lunch): Request routing; back-pressure, circuit
breakers and failure scenarios

- Timeout of 10ms, retry request
- Duplicate all requests; use first response
- Dirty session cookies for read/write consistency
- Serve-from-stale using Varnish (caching always helps)
- Long tail: using search as an example
- Use application healthchecks to avoid common upstream problems (e.g. database
  is failing); is this useful given the infrequency of healthchecks
- Change UI as last resort to relieve traffic
- Mongrel 2 has support for ZeroMQ as transport; HTTP requests from Mongrel (a
  work queue for HTTP), needs compatible web server
- Loads testing framework
- "HTTP multicasting"
- Use ESIs (Varnish), caching much easier using fragments of pages
- Don't serve ads if they're slower than the page's business functionality
- Content rendering slowed down by adverts
- Circuit breakers are traditionally manual
- Too much load to have applications check all dependencies during healthcheck
  (must be asynchronous to healthcheck request)
- Cache healthcheck endpoints (e.g. 10 second); also helps prevent DDoS (e.g.
  thundering herd when load balancer starts up)
- HACheck from Uber
- SmartStack: local HA proxy
- Choose healthchecks very careful
- How to define failure dependency/service coupling (should this be external to
  the application, probably not?)
  - Use application logic to determine service health
- EDML
- Healthchecks might check replication lag? Is database up?
- Apps should be able to determine their own health based on their dependent
  services
- Vulcan, SmartStack + HA, Synapse
- Avoid ZooKeeper unless of course you're already using it

### Fourth session: Application logging

- User auditing
- Use controller actions to infer action taken by user (e.g. Rails)
- Unique ID for tracing
- Add unique tracing ID as comment to MySQL queries; breaks query caching but
  that has high contention so don't use it
  - Shows up in slow query log
- Percona PT Fingerprint tool for genericising queries; hash generic query to
  find recurrences of same queries
- DjangoSampler
- github.com/fac/logging-agent (Ruby)
  - MySQL slow query parser
- NewRelic
- Heroku Logplex (including back pressure; tackles large-scale problems)
- Log all that you can with third party services
- Elasticsearch scroll cursor
- Dock values in Elasticsearch (foundation.no consultancy)
- Percolate API for Elasticsearch; tailing Lucene queries
- Graylog as Elasticsearch frontend (as alternative to Kibana)

### Fifth session: War stories

Too numerous to note.
