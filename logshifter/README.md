logshifter
=====

A simple log pipe designed to maintain consistently high input consumption rates, preferring to
drop old messages rather than block the input producer.

The primary design goals are:

* Minimal blocking of the input producer
* Asynchronous dispatch of log events to an output
* Sacrifice delivery rate for input processing consistency
* Fixed maximum memory use as a factor of configurable buffer sizes
* Pluggable and configurable outputs

logshifter is useful for capturing and redirecting output of heterogenous applications which
emit logs to stdout rather than to a downstream aggregator (e.g. syslog) directly.


Usage
---
Here are some small examples to verify logshifter is writing events, using the default configuration
(which writes to syslog):

    # single shot
    echo hello world > >(/tmp/logshifter)

    # 10 messages @ 1 message/second
    for i in {1..10}; do echo logshifter message ${i}; sleep 1; done > >(/tmp/logshifter -verbose)

    # 30 messages @ 1 message/second with stats reporting every 2 seconds
    for i in {1..30}; do echo logshifter message ${i}; sleep 1; done > >(/tmp/logshifter -verbose -statsfilename /tmp/logshifter.stats -statsinterval 2s)

Configuration
---
An optional configuration file can be supplied to logshifter with the `-config` flag. The file
is a list of key value pairs in the format `k = v`, one per line. The possible options and their
defaults are described in the [Config type](config.go). Keys are case insensitive.

Statistics
---
Periodic stats can be emitted to a file using the `-statsfilename` argument.  The stats are written
in JSON format on an interval specified by `-statsinterval` (which is a string in a format expected
by the golang [time.ParseDuration](http://golang.org/pkg/time/#ParseDuration) function).

Note that enabling statistics will introduce extra processing overhead.

Building
---
Since logshifter lives inside the origin-server repository, use the `go` tools with a relative
directory. Change your working directory to `origin-server/logshifter`. To test:

    go test -v .

And to build:

    go build -o /tmp/logshifter .
