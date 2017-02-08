---
layout: post
title:  "FOSDEM 2017, Brussels - Day two"
date:   2017-02-05 10:00:00
categories: talks
---

I went to [FOSDEM][] [again][] this year, which is an excellent two-day
conference for open source developers, run by volunteers, and is free to attend.

You can find my very rough notes from the talks I attended on the Sunday
below. I also have [notes from the Saturday][].

If you want to watch the recorded talks, see the [FOSDEM videos][].

[FOSDEM]: https://fosdem.org
[again]: {% post_url 2016-01-30-fosdem-day-one %}
[notes from the Saturday]: {% post_url 2017-02-04-fosdem-day-one %}
[FOSDEM videos]: https://video.fosdem.org

## gomobile

https://fosdem.org/2017/schedule/event/go_mobile/

- React native: https://facebook.github.io/react-native/

## Fabian Stäber - Implementing tail -f in Go

https://fosdem.org/2017/schedule/event/go_tail/

- implementing `tail -f` much harder than expected
- if you move (rotate) the file (e.g. logrotate), the file handler points to the original file
- two logrotate configs:
  - move file and create a new one
  - copy the old file and truncate the original copy
- check if:
  - file was truncated
    - `file.Stat()`
  - file was moved
    - `os.SameFile()`
  - close and re-open as necessary
- can we do this without polling (checking file once a second)
  - github.com/fsnotify/fsnotify
    - used by mtail
- subtle problems with fsnotify
  - inotify on Linux
    - fsnotify watches the directory where the log file is located
    - truncate is a 'write' event
  - kevent on FreeBSD
    - does not support watching a directory
    - truncate is a 'chmod' event

## Michael Schubert (Kinvolk) - gobpf - Utilising eBPG from Go

https://fosdem.org/2017/schedule/event/go_bpf/
https://kinvolk.io/blog/2016/11/introducing-gobpf---using-ebpf-from-go/

- gobpf is a library to use eBPF programs from Go
  - https://github.com/iovisor/gobpf
- original use case - packet filtering (Berkley Packet Filter)
- why eBPF?
  - attach to kernel events
  - safety guarantee through the bpf verifier (checks it doesn't block the
    kernel, invalid memory access etc)
  - fast, runs in kernel
- bpf syscall
- programs can be attached to:
  - sockets (execute for each packet)
  - kernel tracepoints: https://www.kernel.org/doc/Documentation/trace/tracepoints.txt
  - kprobes (set of handlers placed on a cetain instruction address)
  - uprobes (user-space)
- eBPF maps
  - `BPF_MAP_TYPE_PROG_ARRAY` holds file descriptors to other eBPF programs
- eBPF man page is out of date - look at source code
- BGP_MAP_TYPE_PERF_EVENT_ARRAY
  - ring buffer; array of file descriptors (one per cpu) containing per events
  - allows sending lots of events very fast
  - user space can read async from mmaped file

## Ron Evans - High-performance IoT Using Go and Gobot

https://fosdem.org/2017/schedule/event/go_gobot/

- Roy Amara quote on underestimating/overestimating long-term/short-term effects of technology
- Why Go for robotics?
  - concurrency
  - portability
    - cross compilation
    - static binaries
  - performance
    - slowest stop-the-world GC in Go 1.8 is 100 microseconds
    - C10K -> 10M IOT devices
- Gobot is a framework
  - hardware-oriented development ('full stack robotics')
- demo
  - hello world == blinking LED
- the microcontroller can't run Go yet
  - can use microcontroller as a peripheral (use 'firmata' to drive the microcontroller)
    - https://github.com/hybridgroup/gobot/tree/master/platforms/firmata
- Gobot 1.2 has an event stream, run a lambda on an event
- 'Miniluminado'
  - solar-powered monitoring system
    - MQTT for communication between drone, sensor station and base station
    - Intel Edison for sensor station
    - WindRiver Linux on NUC for FTT server as gateway
      - https://www.windriver.com/products/linux/
    - base station: Intel Arduino 101
    - drone as repair system
      - Parrot minidrone + DS3 controller
        - Parrot has bluetooth LE interface, ARM chip
      - to carry replacement part
- robeaux.io
  - https://github.com/hybridgroup/robeaux
  - dashboard for controlling robotic devices
  - built in React

## Santiago M. Mola (sourced) - go-git: A pure Go implementation of Git

https://fosdem.org/2017/schedule/event/go_git/
https://github.com/src-d/go-git

- Git implementation in pure Go
- full read-feature parity with libgit2
- used at sourced to help analyse +17M Git repositories
- usual options
  - git CLI
  - libgit2 (C with bindings for other languages
  - JGit (Java)
- github.com/libgit2/git2go (libgit2 bindings) - uses cgo
- github.com/go-gitea/git (CLI wrapper) - fast but inflexible, shells out
- idiomatic APIs for plumbing and porcelain (high and low level)
- has storage backends
  - virtual filesystem
  - Git transports supported (SSH, file, git, HTTP)... or your own transport
- fork redundancy
  - +70TB for storing HEAD/master branch of GitHub non-forks
    - +240TB including forks
  - solution: store forks together; share all objects; fork is a namespaced set of references
  - detecting forks?
    - name is too error-prone
      - same name != same project
    - GitHub API only works on GitHub if you used the GitHub interface to fork
    - detect common ancestry in commits
      - compare initial commit hash
- the 'many small files' problem
  - efficient archiving format for this use case (siva)
    - single filer per repository
    - custom storage backend for go-git
    - https://blog.sourced.tech/post/siva
- users of go-git
  - github.com/gitql/gitql: a SQL interface to Git
  - github.com/mcuadros/go-stable: gopkg.in-like for your public and private packages

## Frank Wessels (Minio) - High performance and scaling techniques in Golang using Go Assembly

https://fosdem.org/2017/schedule/event/go_scaling/

- BLAKE2B and SHA256 acceleration
- Minio can work with FS backend of 'XL' backend (working directly with raw disk devices)
  - XL backend needs to hash to help detect bitrot
- Plan9 Go assembly language
  - pseudo-assembly
  - actual instructions generated are platform specific
  - usually logical, not always
  - data flows from left to right
    - `MOV R1 R2` == `R2 = R1`
  - pseudo-registers
  - can use opcodes directly if you need
- benefits
  - fast complication^W compilation
  - enhances execution speed
  - no need for cgo dependency
- can take advantage of SIMD (Intel) or NEON (ARM) capabilities
- look for examples in go/src/
- how to integrate
  - use file extensions
    - `_amd64.s`
    - `arm64.s`
- implement your algorithm in Go first as a baseline
  - translate it step-by-step in assembly
  - check your results as you make progress
  - start simple
    - load arguments, write results
    - gradually add more
  - look at commit history of github.com/minio/blake2b-simd
- asm2plan9s tool developed my Minio
  - generates byte sequences for Go assembly
  - uses YASM
- BLAKE2B hash
  - cryptographic, collision-resistant hash
  - focus on speed, simplicity, security
  - SHA3 competition contender
  - three flavours; AVX2, AVX and SSE
  - assembly over pure Go gave 3.94x speedup
- distributed sync package
  - github.com/minio/dsync

## Francesc Campoy (Google) - The state of Go: What to expect in Go 1.8

- Go 1.6 is one year old
- Go 1.7 is 6 months old
- Go 1.8 due for release in February
- language change:
  - struct tags now ignored when converting types
    - must have same frequency of fields (order matters)
- ports to other platforms
  - 32-bit MIPS; big and little-endian (requires floating-point unit)
- Go 1.8 supports OS X 10.8+
  - Go 1.9 will not
- `go fix`
  - fixes import paths
- `go vet` now more precise
  - stricter when it can be; reduced false positives
- Single Static Assignment (SSA) now used everywhere
  - 20-30% speedup on 32-bit ARM systems
- `go build` now faster than 1.7, not yet as fast as Go 1.4
- default GOPATH \o/
  - `$HOME/go` on Unix
- `go bug`
  - starts filing Go bug report with debug info (environment, version)
- detection of concurrent map access
- mutex contention profiling
  - `-mutexprofile` to report on how long goroutines are locked in a mutex
  - `sync.RWMutex` not yet supported
- garbage collection
  - sub-millisecond GC pause times
- defer is now faster
  - use defer but it's the first thing to remove if you have a performance issue
  - keep it out of hot code paths
- cgo now faster (defer was removed from cgo code)
- sorting: `sort.Slice`
  - pass it a slice and an anonymous function
  - `sort.SliceStable`
- plugins
  - only on Linux currently
  - example: code hot-swapping (e.g. plugins for a game or desktop app)
    - https://github.com/campoy/golang-plugins
- `Shutdown` method on `http.Server`
  - stop receiving new requests but don't drop existing requests
  - check for `http.ErrServerClosed` error from `http.ListenAndServe()`
- HTTP/2 PUSH now supported using Pusher interface
  - use type assertion on `http.ResponseWriter`
- more context support
  - database/sql
  - `net.Resolver`
- misc minor changes
  - https://tip.golang.org/doc/go1.8
- Women Who Go: now has 16 chapters
- Go meetups: https://go-meetups.appspot.com
- Go 1.8 release party on February 16th
