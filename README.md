![vmpooler](https://raw.github.com/sschneid/vmpooler/master/lib/vmpooler/public/img/logo.gif)

# vmpooler

vmpooler provides configurable 'pools' of instantly-available (running) virtual machines.


## Usage

At [Puppet Labs](http://puppetlabs.com) we run acceptance tests on thousands of disposable VMs every day.  Dynamic cloning of VM templates initially worked fine for this, but added several seconds to each test run and was unable to account for failed clone tasks.  By pushing these operations to a backend service, we were able to both speed up tests and eliminate test failures due to underlying infrastructure failures.


## Installation

### Prerequisites

vmpooler requires the following Ruby gems be installed:

- [json](http://rubygems.org/gems/json)
- [rbvmomi](http://rubygems.org/gems/rbvmomi)
- [redis](http://rubygems.org/gems/redis)
- [sinatra](http://rubygems.org/gems/sinatra)

It also requires that a [Redis](http://redis.io/) server exists somewhere, as this is the datastore used for vmpooler's inventory and queueing services.

### Configuration

The following YAML configuration sets up two pools, `debian-7-i386` and `debian-7-x86_64`, which contain 5 running VMs each:

```
---
:vsphere:
  server: 'vsphere.company.com'
  username: 'vmpooler'
  password: 'swimsw1msw!m'

:redis:
  server: 'redis.company.com'

:config:
  logfile: '/var/log/vmpooler.log'

:pools:
  - name: 'debian-7-i386'
    template: 'Templates/debian-7-i386'
    folder: 'Pooled VMs/debian-7-i386'
    pool: 'Pooled VMs/debian-7-i386'
    datastore: 'vmstorage'
    size: 5
  - name: 'debian-7-x86_64'
    template: 'Templates/debian-7-x86_64'
    folder: 'Pooled VMs/debian-7-x86_64'
    pool: 'Pooled VMs/debian-7-x86_64'
    datastore: 'vmstorage'
    size: 5
```

See the provided YAML configuration example, [vmpooler.yaml.example](vmpooler.yaml.example), for additional configuration options and parameters.

### Template set-up

Template set-up is left as an exercise to the reader.  Somehow, either via PXE, embedded bootstrap scripts, or some other method -- clones of VM templates need to be able to set their hostname, register themselves in your DNS, and be resolvable by the vmpooler application after completing the clone task and booting up.


## API and Dashboard

vmpooler provides an API and web front-end (dashboard) on port `:4567`.  See the provided YAML configuration example, [vmpooler.yaml.example](vmpooler.yaml.example), to specify an alternative port to listen on.

### API

vmpooler provides a REST API for VM management.  See the [API documentation](API.md) for more information.

### Dashboard

A dashboard is provided to offer real-time statistics and historical graphs.  It looks like this:

![dashboard](https://raw.github.com/sschneid/vmpooler/gh-pages/img/screenshots/dashboard.png)

[Graphite](http://graphite.wikidot.com/) is required for historical data retrieval.  See the provided YAML configuration example, [vmpooler.yaml.example](vmpooler.yaml.example), for details.


## Build status

[![Build Status](https://travis-ci.org/puppetlabs/vmpooler.png?branch=master)](https://travis-ci.org/puppetlabs/vmpooler)


## License

vmpooler is distributed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).  See the [LICENSE](LICENSE) file for more details.
