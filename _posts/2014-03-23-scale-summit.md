---
layout: post
title:  "Scale Summit 2014: Performance and scalability unconference"
date:   2014-03-23 23:05:00
categories: talks ideas
---

I attended [Scale Summit](http://www.scalesummit.org/) 2014 in London on Friday. It was fantastic opportunity to share ideas and experiences in performance and stability in web operations.


Scale Summit is an '[unconference](http://en.wikipedia.org/wiki/Unconference)', meaning that the topics discussed were decided on the day according to what the participants wanted to learn or share.

Sessions ran in parallel so I had to choose the topics I was most interested in. Below are the (messy) notes that I took.

I'd definitely recommend Scale Summit to anyone considering going in future; thanks to [Michael Brunton-Spall](https://twitter.com/bruntonspall) and the organisers for a brilliant event and excellent bacon sandwiches.

* * *

### First Session: How to scale collection of metrics

- Use case:
  - Difficulty of procuring a solution
  - [Graphite](http://graphite.readthedocs.org/en/latest/overview.html) on-premise (existing 40 boxes for monitoring)

- [Carbon relay](http://graphite.readthedocs.org/en/latest/carbon-daemons.html#carbon-relay-py) for scaling or archival data

- How to alert on Graphite trends?
  - Nagios checks
  - Etsy's [Kale](http://codeascraft.com/2013/06/11/introducing-kale/)/[Skyline](https://github.com/etsy/skyline) for analysis; can trigger false alerts

- Graphite very popular

- [Ganglia](http://ganglia.sourceforge.net/) for OS metrics? Alternative: Sensu with Diamond into RabbitMQ (federation, pushed to central [FusionIO](http://www.fusionio.com/) box)
  - OpsGD heartbeart
  - Chaos monkey to kill Sensu once an hour on each node

- Use case: Nagios reporting on Ganglia

- [Grafana](http://grafana.org/) helps with load (SVG means less load than PNG)

- Put [varnish](https://www.varnish-cache.org/) in front of Graphite to help with load for large ops teams (low TTL of a few seconds)

- [Riemann](http://riemann.io/)? Featurs include realtime anayltics and events timeout (freshness)

- How to store data at resolution of one second or less?

- Peak traffic event: Log everything for future capacity planning next time it occurs
  - [Amazon Redshift](http://aws.amazon.com/redshift/) useful for storing lots of metrics (delta compression if data is sorted properly)
  - For ad-hoc querying: dump metrics into ElasticSearch (inc. outputting graphs) i.e. 3TB logs/day

- Use case: [Graylog2](http://graylog2.org/): Alert based on streams from ElasticSearch

- Buffer carbon writes to reduce disk I/O (or use a ramdisk and copy occasionally to disk)

- 768GB RAM, 24 SSDs for Ganglia

- Experience: never enough data to find root cause of an outage (it if moves, graph it)

- [Arista](http://www.aristanetworks.com/) for monitoring ports (congestion on network interface) at sub-second resolution (which becomes necessary for diagnosing issues)

- Metric coverage? As part of application deployment pipeline:
  - Determine application requirements (log a heartbeat, status page)
  - List metrics on status page

- Collection of AWS CloudWatch data (e.g [ELB](http://aws.amazon.com/elasticloadbalancing/) latency)

### Second Session: Handling traffic spikes and DDoS

#### Load testing is useful:

- Usually something unexpected breaks (memcache, ELB)
- [Tsung](http://tsung.erlang-projects.org/) cluster (Erlang/XML) from Amazon EC2 instances
  - 30/40,000 requests per second

#### Tips for dealing with load

- Cache at every layer (even a TTL of 1 second means we're only send 1 req/second to origin)

- Terminate SSL at the CDN to avoid extra load incurred

- Request collapsing/coalescing - take multiple requests and funnel just one to the backend

- [Amazon CloudFront](http://aws.amazon.com/cloudfront/) based on [Squid](http://www.squid-cache.org/)? Request collapsing supported in Squid from 2.7

- Varnish

- Ensure that cookies/query strings aren't cache busting

- Dynamic image resizers should sign their  URLs; i.e. to prevent someone setting abritrary image dimensions

- Watch web server access logs; logs should cease once downstream caches are warmed up

#### Suggestion of request cap on Amazon EC2

- Concurrent users:

  - On EC2, Jetty can handle 200,000 requests each, suggesiton that AWS truncates it to 2,000 each (suggestion that NAT gateway preventing connections?)

- Question if Amazon ELBs and EC2 are slow to scale up (packets per second limit?)
  - Use [HA-Proxy](http://haproxy.1wt.eu/) instead of ELB
  - Use smaller EC2 instances; cap regardless of instance size

#### Avoiding and mitigating DDoS attacks

- Be careful with UDP (DNS)

- Detect a signature after the attack has started and filter on that signature

- DDoS: have more transit than your attacker can fill

- Varnish: Filter out Max-Age 0 set by browser (Chrome? mobile phones?)

- Mobile does it to bypass network provider cache?

#### How to test load (stress testing)

- Use javascript to load test against yourself
  - Put javascript on the site that does background requests (hit URLs, slowly ramp up, use clients' browsers as distributed botnet)

- Difficulty of closing connections for disappearing clients

- [Gor](https://github.com/buger/gor) or [Varnish replay](https://www.varnish-cache.org/docs/2.1/reference/varnishreplay.html) for replaying live traffic to other environments

- [Siege](http://www.joedog.org/siege-home/), [Gattling](http://gatling-tool.org/) for load testing
  - Tune load testing tool for requests per connections, per handshake
  - [Avalanche](http://www.spirent.com/Ethernet_Testing/Software/Avalanche) appliance
  - [Soaster](http://www.soasta.com/)
  - [Loads](https://github.com/mozilla-services/loads) from Mozilla

### Post-Lunch Third Session: Managing open source projects

- Can you scope the feature functionality in advance?
  - Take it as it comes
  - It may turn out that a fork does your thing better

- Optimise for sharing with others (make sure your documentation is good enough, don't assume knowledge)
  - Mailing lists and IRC a good way to share information (newcomers to contribute to documentation with their new knowledge)
  - Perl community does this well (maintainers change frequently, e.g. Catalyst has 450 committers)

- Modularity makes the process easier - everyone can agree on a common core
  - Divergence from core goals can be done using plugins

- Respond quickly for new commiters to encourage them (e.g. within 24 hours; at least to comment, not necessarily merge)

- Encourage reviewing of pull requests between contributors; don't be a bottleneck

- Difficulty of merging code designed for paying customers (e.g. clients of the company you work for); can that feature be made public? $POLITICS

- Code in the open is a donation economy; no obligation to maintain ("Sorry, the code I wrote a year ago was actually a terrible idea")

- Github PRs can show contribution guidelines

- An open source _project_ needs maintainer(s)

- Maintaining an open source community can be a full time job, should be shared

- CI systems help reduce time
  - Document those tests to reduce the barrier to using them
  - Travis is free, integrates well with Github

- Maintainers need to have some kind of vision to garden/guide a project
  - Prevent a project from being unmaintainable

- GCC was forked to EGCS that lived for ~5 years, changes were merged back into GCC

- Advice for maintainers and what their role is

- Open sourcing for the fun of it is not sustainable; you as a maintainer/author must have a use for it at least for a period
  - Communicate your intentions; what should project users expect?

- Finding a canonical (or most active/useful) fork is difficult
  - The community decides who holds the 'master fork'

- Repositories (RubyGems, PyPi): Packages with a canonical name can be abandoned; confusing user experience

- How to communicate?
  - Project web site
    - __Include a description of what your project does__
      - Don't just compare to a similar project; what does that other project do?
    - How to contribute (readme for contributors)
    - How to install
  - Building a community encourages ownership
  - Public issue tracking very important = documentation
    - Can I Google for this issue?
  - Default should be public where possible (equality of access)
  - GPG key for vulnerability disclosure
  - Public activity (as opposed to on Pivotal) implies the project is active
  - Gitter: Chatrooms for Github repositories
    - IRC is good for contributors but not so much for users
      - One channel for multiple projects by the same author/company
    - Libralist, Google Groups, Gmane (have a backup of the mailing list)
  - Have a domain for a medium-large projects
  - Documentation
    - [ReadTheDocs](https://readthedocs.org/)
    - [Viewdocs](http://progrium.viewdocs.io/viewdocs): allows for keeping docs in sync
    - [Github Pages](http://pages.github.com/) are good but don't allow for keeping source and documentation in sync (uses gh-pages separate repository)
    - /latest/ points to master or release branch
    - Always update documentation at the same time as code changes (both in same PR where possible)
      - Requesting unit tests from newbies is difficult; fixing up documentation is a necessity
      - Write tests for their contribution if necessary; let them see the tests after

- Value of newcomers is that they can contribute to documentation in a way that you can't (fresh perspective)

- How manage documentation style?
  - Rewrite it for them, explain you're rewriting it to match your style

- Try to avoid the benchmark trolls (version 0.x was faster, can't explain why)

- Clear explanation when closing a PR due to poor quality or incompatible direction

- ['Love your idiots'](http://shadow.cat/blog/matt-s-trout/love-your-idiots/); encourage newbies to communicate with each other and later contribute


### Fourth Session: [AWS Cloud Formation](http://aws.amazon.com/cloudformation/) and Cloud Orchestration

- Functions in JSON ARRGGHHHHH

- Partition platform into features
  - JSON files like MVC (different views)
    - Allow for featuring flagging
  - Template JSON files using Ruby, stored in source control
    - Can use API instead?

- Declarative nature of Cloud Formation useful (no need to worry about order)

- Cloud Formation JSON provides Dev/Prod parity

- Use new instances for new version deployments

- How to compare template vs what's in the environment?
  - Can diff live running document with the template

- Declarative description of environment using Puppet
  - `puppet apply` with AWS API credentials
  - [Puppet](http://puppetlabs.com/) resources for AWS; IAM users, VPCs etc (not so much VM provisioning)
  - Lots of work to implement providers for Puppet

- Dry run mode much needed

- Pin versions in case AWS release new features/versions in the time between your Staging and Production deployments

- Custom resources fill in the pieces

- Configure Cloud Formation using [Ansible](http://www.ansible.com/home)

- JSON templating using [Ginger](http://blogaholicdesignsginger3column.blogspot.co.uk/2010/08/how-to-install-ginger-template.html) or CPP (preprocessing)

- [AWS OpsWorks](http://aws.amazon.com/opsworks/) - more difficult to audit changes; machine centric
  - Can spin up primitives (subnets, security groups etc)
  - Chef with an orchestration layer
  - Receive notifications (great for discovery)

- [Heat](https://wiki.openstack.org/wiki/Heat)
  - Orchestration for OpenStack

- [Eucalyptus](https://www.eucalyptus.com/)?

- [Asgard](https://github.com/Netflix/asgard)?

- Triggers for scaling actions often conflict with versioned templates (i.e. I've scaled up but config specifies less servers and I don't want to scale down)

- [Troposphere](https://github.com/cloudtools/troposphere) for generating Cloud Formation configurations
  - Also CloudCast
  - Ruby DSLs

- [Juju](https://juju.ubuntu.com/)

- [CloudInit](http://cloudinit.readthedocs.org/en/latest/) once instances are up (not just Amazon)

- Bake everything into AMI (less secrets) vs bare box plus provisioning
  - Bare box not ideal if Puppet repo is down
  - Baked in means faster boot times

- Use [Packer](http://www.packer.io/) to build AMIs
- Package Puppet modules as .dpkg or RPMs

- [SmartStack](http://nerds.airbnb.com/smartstack-service-discovery-cloud/): service discovery

- Possible to use AWS API for discovery but doesn't translate to bare metal
  - List neighbouring [Docker](https://www.docker.io/) containers

- Use HA-Proxy on each machine

- Serf, [Zookeeper](http://zookeeper.apache.org/), [ETCD](https://github.com/coreos/etcd)
  - Zookeeper looks overcomplicated for Production

### Fifth Session - Linux Containers

- When to use container vs a VM?
  - Rubygem dependency hell
  - Not paying for extra instances

- One container per machine useful for bare metal
  - __Unified deployment for hybrid cloud/bare metal__

- [Zones](http://en.wikipedia.org/wiki/Solaris_Containers) on [Illumos](http://wiki.illumos.org/display/illumos/illumos+Home) OS
  - Zones allow greater visibility of processes inside

- Docker for CI
  - Docker containers as CI slaves
  - 4 OSes in one Docker container
  - Faster than provisioning a new VM
  - Allows for CI without an Internet connection

- What are containers?
  - Lightweight VM
    - Separated network stack, IP, etc
    - All done in host kernel
    - Like chroot with own network stack

- [Vagrant LXC](https://github.com/fgrehm/vagrant-lxc)
  - Much faster than a VM

- Build on a Docker container for testing
  - Can be done using LXC (Butterfs)

- Messos/Marathon for autoscaling
  - Still maturing

- One service per container, with supporting services (syslog, sshd)

- Should sshd be installed?
  - Probably in case we need to troubleshoot something

- Containers with persistent data
  - Attach disks
  - Can blow away a container and keep data

- Native performance

- How to test Docker containers? Are they valid/working as expected?
  - Blackbox testing
    - Generate new container
    - Run tests against new container
  - Can we catch Docker container configuration errors earlier?

- Can share UNIX sockets between containers instead of using TCP/IP

- [Fig](https://github.com/orchardup/fig) - new project ("host your own Heroku")

- [Drone](https://drone.io/); CI based on Docker

- Service discovery
  - [Synapse](https://github.com/airbnb/synapse) (SmartStack)
  - HApache with [Redis](http://redis.io/)
  - DNS for discovery
  - [Serf](http://www.serfdom.io/)

- Zombie processes have been an issue
  - Containers that won't die; mostly fixed
  - Docker doesn't use LXC anymore by default (libcontainer instead)

- Useful for partitioning an EC2 instance

- Performance impact on networking if pushing large throughput (also on disks if using a separate FS in the container)
    - AUFS is the default but will perform poorly; no need to use it

- Create immutable Docker containers without Puppet (it's already configured); changes mean a new container image
  - Puppet apply when building container

- Very useful for testing
  - Testing clusters

- Very useful in production for immutable server pattern

- Still immature

- Opinionated

- Difficult when revoking SSH keys etc (tricky for MySQL)
  - Don't bake your users in?

- Have to build new images for security updates

### CIs

- Possible CI solutions:
  - [Jenkins](http://jenkins-ci.org/)
  - [Buildbox](https://buildbox.io/)
  - [Travis](https://travis-ci.org/)
  - [Teamcity](http://www.jetbrains.com/teamcity/) (nicer than Jenkins?)
  - [Go](http://www.thoughtworks.com/products/go-continuous-delivery) from Thoughtworks
  - [Bamboo](https://www.atlassian.com/software/bamboo) (worse than Jenkins?)
  - Drone (similar to Travis)

- Post receive hooks better than SCM polling

- [Netflix plugin](https://wiki.jenkins-ci.org/display/JENKINS/Job+DSL+Plugin) for writing jobs in Groovy

