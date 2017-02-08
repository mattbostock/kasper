---
layout: post
title:  "FOSDEM 2017, Brussels - Day one"
date:   2017-02-04 10:00:00
categories: talks
---

I went to [FOSDEM][] [again][] this year, which is an excellent two-day
conference for open source developers, run by volunteers, and is free to attend.

You can find my very rough notes from the talks I attended on the Saturday
below. I also have [notes from the Sunday][].

If you want to watch the recorded talks, see the [FOSDEM videos][].

[FOSDEM]: https://fosdem.org
[again]: {% post_url 2016-01-30-fosdem-day-one %}
[notes from the Sunday]: {% post_url 2017-02-05-fosdem-day-two %}
[FOSDEM videos]: https://video.fosdem.org

## Brandon Philips (CoreOS) - Containing Infrastructure: The Internet on Kubernetes

- 3.5B Internet users
- 29k software devs/IT practitioners
  - we're outnumbered
- most people are uploading their data to servers
  - our responsibility to look after it
- ~100M servers worldwide
  - 3 per person

## SDN/NFV room - OpenFV

https://www.opnfv.org/

# Raymond Knopp - 4G/5G

- 5G coming soon
- Going to introduce a lot of complexity to cellular networks
- http://telecominfraproject.com/ (Google, FB and others)
- ITU IMT2020 FG Vision
- C-RAN is where compute is

## Bert Vermeulen - Switchdev kernel subsystem

https://fosdem.org/2017/schedule/event/switchdev/

- What is an Ethernet switch? Forwards ethernet frames
- FDB forwarding database (learning, aging)
  - https://en.wikipedia.org/wiki/Forwarding_information_base
- need CPU, used to cause an interrupt for each frame received
- OpenWRT swconfig (small CPUs, builtin switch chip)
- DSA switch chip with mainline kernel supports 5 ports; bridging offloaded to hardware

## Ray Kinsella (Intel) - TLDK Overview

https://fosdem.org/2017/schedule/event/tldk/
https://fd.io/

- built on http://dpdk.org/
- On cellular network
  - TCP connections establishment is as important as througput - most TCP flows are small
    - 60% of flows are smaller than 4KB, only 9.5% are larger than 32KB
- 97% of traffic not P2P on non-cellular network
- some companies are using multiple TCP stacks, drvien by dev desire to take advantage of new TCP options
- run well-known kernel stack on top of netmap, OpenFastPath, DPDK
  - gives broad RFC compliance
  - BSD Socks API
  - kernel stacks assume they're running in the kernel - requires work to make them run in userspace
  - core locality - eliminate context switching by processing packets on same core as data stream
- what is TLDK?
  - high performance L4 implementation on top of DPDK
  - from scratch, not using existing kernel stack
  - optimised for performance
    - not same level of RFC compliance
  - aimed to be fastest TCP implementation on top of general purpose CPU
  - cache coherency - end-to-end affinity
  - avoid swapping cache lines between cores
  - avoids context switching (in userland)
  - support for common HW offloads
- socket-like API calls where possible
- scales linearly with physical cores
  - maxes out PCI at 5 cores using UDP packets of 64 bytes (36.4 Mpps)
- TCP transparent proxy
  - sends ACKs on behalf of the client
  - caches TCP packets close to the network
  - reduces retransmission and buffer bloat
- ray.kinsella@intel.com

## Ferruh Yigit (Intel) - How to write a simple DPDK forwarding application

https://fosdem.org/2017/schedule/event/dpdkapp/

- DPDK designed to address the problem of packet sizes versus line rate
  - small packets mean a lot of packets on interfaces with high line rates
    - does not make good use of CPU caches
- needs hugepages
- need DPDK-compatible NIC drivers
- EAL: Environment Abstraction Layer
  - abstracts the hardware
  - scans for devices
- DPDK manages memory itself (cannot use OS memory allocation APIs)
  - DPDK mempool: fixed size memory buffers
- use multiple queues for performance (process each queue on separate cores)

## Klaus Aehlig (Google) - Bazel

- build tool (like Make); derives files from source files
- core of Google tool for over a decade (Google uses extensions)
- optimised for Google's use case
  - a large single repository (AKA mono-repo)
- open source since 2015
- why Bazel?
  - aggressive caching while retaining correctness
    - as if built freshly from source
- declarative
  - separation of concerns
    - write code rather than choose correct compiling strategy
- how does it work?
  - load the BUILD files needed
  - generates action graph based on dependencies
  - execute actions if cache miss
  - client-server architecture to keep graph in memory
- example
  - WORKSPACE file indicates root of codebase (can be an empty file)
  - BUILD file declaring dependencies
  - CC, link options, host/target architecture taken care of elsehwere
- sandboxing to help detect incorrect builds
  - remote execution (build cluster)
    - enables shared caches
- Skylark language for extending BUILD language (Python-like)
  - can be used to support builds for other languages
- challenges of open-sourcing
  - Google-specific internal dependencies
    - open source dependencies or find replacements
  - focus on languages used at Google
  - no stable interfaces, easy to change APIs on large code base but not appropriate for an open-source project
  - hard-coded paths everywhere
  - process of open-sourcing still ongoing
    - Bazel 1.0 ('properly' open source - expected 2018)
    - public repository will be primary repository
      - clear interfaces between Bazel and Google's use
    - all design reviews in public
    - core team currently all at Google
    - working towards stable build language and APIs
    - remote execution API being improved upon

## Andrea Arcangeli (Red Hat, Inc) - 20 years of Linux Virtual Memory

- what are virtual pages?
  - map virtual memory to physical
  - virtual pages cost 'nothing'
  - practically unlimited on 64bit architectures
- x86 code is implemented as a radix tree
- Total:

    grep PageTables /proc/meminfo

- Page tables are 4KB in size
- 'fabric' of virtual memory = all data structures that abstract the hardware:
  - tasks
  - processes
  - virtual memory areas
  - mmap
- algorithms for these data structures are the virtual memory heuristics
  - no perfect solution
    - when's the right time to unmap pages (swapiness?)
- all free memory is used as a cache
  - we overcommit by default (though not excessively by default)
- page struct size = 64 bytes (one per page)
- mm_struct AKA MM
  - describes memory of the process
  - shared by threads
- vm_area_struct AKA VMA
  - Virtual Memory Area
    - created and teardown using mmap and munmap
- page reclaim clock algorithm
  - reclaiming required scanning all pages to look for a candidate to free
- clock algorithm replaced with Least Recently Used (LRU) list
  - two LRUs
    - active and inactive
    - introduced in 2001
    - common use case: backing up requires streaming I/O
    - grep -i active /proc/meminfo
- rmap obsoleted the pgtable scan clock algorithm
  - rmap = reverse mapping
  - provides a way to map all of the pagetables of any given physical page without having to scan them all
- there many other LRUs (e.g. every memory cgroup has its own LRU)
  - separate LRU for anonymous and file-backed mappings
- numactl (automatic NUMA balancing)
- 'transparent hugepages'
  - https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Performance_Tuning_Guide/s-memory-transhuge.html
- virtual memory in hypervisors
  -  how can we take advantage of virtual memory in hypervisors?
    - KVM philosophy
      - reuse Linux code as much as possible
      - kernel module + mmu/sched notifier
- numa balancing: /proc/sys/kernel/numa_balancing
- hugepages:
  - larger than 4KB pages
  - 512 times bigger -> 2MiB
  - benefits?
    - enlarge TLB size (essential for KVM)
    - speed up TLB misses (essential for KVM)
  - faster to allocate memory initially (minor)
- transparent hugepages (Red Hat specific?) hide some of the complexity of hugepages from the system administrator
- memory externalisation: use memory on a remote node
- http://www.orbitproject.eu/tutorials-documentation-how-to-use-the-results-of-this-projects/

## Misc notes

https://en.wikipedia.org/wiki/Multipath_TCP
