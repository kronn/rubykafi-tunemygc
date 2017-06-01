# Ruby Kafi 51.0

<!-- .slide: class="master01" -->

---

# Agenda

<ol>
<li class="fragment">Ruby GC
<li class="fragment">TuneMyGC
<li class="fragment">???
<li class="fragment">Profit
</ol>

<!-- .slide: class="master01" -->

---

# Garbage Collection

<!-- .slide: class="master01 intro" -->

----

![Tweet about garbage collection](gc.png)

https://twitter.com/sadserver/status/868120790599741444

<!-- .slide: class="master02" -->

----

# Ruby's GC

<ul>
<li class=fragment>Lazy
<li class=fragment>Generational
<li class=fragment>Incremental
<li class=fragment>Mark and Sweep GC
</li>

<!-- .slide: class="master02" -->

----

# Mark & Sweep GC

active/used ("living") objects are marked, the others are swept away.

<p class=fragment>This requires to "Stop the (Ruby) World"

<!-- .slide: class="master02" -->

----

# Halting Problem

<p class=fragment>Not named after Roger Halting
<p class=fragment>Ruby stops everything for a GC run
<p class=fragment>**all** execution stops
<p class=fragment>`:-|`


<!-- .slide: class="master02" -->

----

# Generational GC
## Ruby 2.1+

<p class=fragment>most objects die young
<p class=fragment>minor/major GC run
<p class=fragment>faster marking phase
<p class=fragment>`:-D`

<!-- .slide: class="master02" -->

----

# Lazy GC
## Ruby 1.9.3+

<p class=fragment>first part of Incremental GC
<p class=fragment>sweep in smaller stages
<p class=fragment>reduces sweep pause
<p class=fragment>`:-D`

<!-- .slide: class="master02" -->

----

# Incremental GC
## Ruby 2.2+

<p class=fragment>applies to major GC marking
<p class=fragment>white/grey/black marks + write-barrier
<p class=fragment>reduces major GC pause
<p class=fragment>`:-D`


<!-- .slide: class="master02" -->

----

# Introspectable GC
## Ruby 2.1+

<p class=fragment>`GC.stat`
<p class=fragment>`:-D`


<!-- .slide: class="master02" -->

----

# Configurable GC

<pre>
RUBY_GC_HEAP_INIT_SLOTS
RUBY_GC_HEAP_FREE_SLOTS
RUBY_GC_HEAP_GROWTH_FACTOR
RUBY_GC_HEAP_GROWTH_MAX_SLOTS
RUBY_GC_HEAP_OLDOBJECT_LIMIT_FACTOR

RUBY_GC_MALLOC_LIMIT
RUBY_GC_MALLOC_LIMIT_MAX
RUBY_GC_MALLOC_LIMIT_GROWTH_FACTOR 

RUBY_GC_OLDMALLOC_LIMIT
RUBY_GC_OLDMALLOC_LIMIT_MAX
RUBY_GC_OLDMALLOC_LIMIT_GROWTH_FACTOR
</pre>

<p class=fragment>`ENV`-vars

<!-- .slide: class="master02" -->

---

# TuneMyGC

<!-- .slide: class="master03 intro" -->

----

# TuneMyGC

## https://tunemygc.com

<!-- .slide: class="master03" -->

----

# Hitobito

with the specs as workload

<!-- .slide: class="master03" -->

----

# 6:02 minutes

<pre>
real    6m2.248s
user    3m24.900s
sys     0m6.532s
6m, 2s: time bundle exec rake spec
</pre>

<!-- .slide: class="master03" -->

----

# tunemygc client installation

<pre>
~/Code/work/hitobito
{0} $ bundle exec tunemygc -r viehweger+hitobito@puzzle.ch
[tunemygc] not enabled
Application hitobito registered. Use
RUBY_GC_TOKEN=34c62b5504405b56d0de49051dfa11d1
in your environment.
</pre>

<!-- .slide: class="master03" -->

---

# ???

<!-- .slide: class="master04 intro" -->

----

# run specs again
## with api-token

`RUBY_GC_TOKEN=34c62b5504405b56d0de49051dfa11d1 RUBY_GC_TUNE=200 bundle exec
rake spec`

This might be slower due to instrumentation.


<!-- .slide: class="master04" -->

----

# get URL from the log

`grep 'tunemygc.com' log/test.log`

https://tunemygc.com/configs/88cba0bea8461e46f652079bb4fbc289

<!-- .slide: class="master04" -->

----

# configure your environment
## depending on the measured workload

<p class=fragment>as a Jenkins-ENV-setting
<p class=fragment>or OpenShift ENV-vars
<p class=fragment>or just your local machine

<!-- .slide: class="master04" -->

----

# Recommendations

<pre>
export RUBY_GC_HEAP_INIT_SLOTS=540609
export RUBY_GC_HEAP_FREE_SLOTS=610495
export RUBY_GC_HEAP_GROWTH_FACTOR=1.03
export RUBY_GC_HEAP_GROWTH_MAX_SLOTS=91521
export RUBY_GC_HEAP_OLDOBJECT_LIMIT_FACTOR=2.0

export RUBY_GC_MALLOC_LIMIT=37139624
export RUBY_GC_MALLOC_LIMIT_MAX=66851323
export RUBY_GC_MALLOC_LIMIT_GROWTH_FACTOR=1.32

export RUBY_GC_OLDMALLOC_LIMIT=45373934
export RUBY_GC_OLDMALLOC_LIMIT_MAX=81673081
export RUBY_GC_OLDMALLOC_LIMIT_GROWTH_FACTOR=1.2
</pre>

<!-- .slide: class="master04" -->

----

# 5:57 minutes

<pre>
real     5m57.221s
user     3m24.444s
sys      0m6.032s
5m, 57s: time bundle exec rake spec
</pre>

<p class=fragment>~ 5 second win
<p class=fragment>1.4 % faster
<p class=fragment>`:-)`

<!-- .slide: class="master04" -->

----

# post-optimization GC analysis

`grep 'tunemygc.com' log/test.log`

https://tunemygc.com/configs/346c9c88ef16df5e5780f801dc758ddb

<!-- .slide: class="master04" -->

---

# Profit

<!-- .slide: class="master05 intro" -->

----

# Consistent Gain

## the 4-5 seconds are repeatable

<!-- .slide: class="master05" -->

----

# Speed for Memory

## memory usage increased slightly

<!-- .slide: class="master05" -->

----

# Sometimes more speed translates to money

Yes, we're in it for the fun, but success is part of it, right?

<!-- .slide: class="master05" -->

----

# Every Workload is different

## so let's be scientific about it

<!-- .slide: class="master05" -->

----

# Defaults might be fine

## but we want proof, right?

<!-- .slide: class="master05" -->

----

# Measure, Tune, Measure

The wrong settings can make things slower or use too much memory.

<!-- .slide: class="master05" -->

---

# Merci vielmol

<!-- .slide: class="master01" -->

----

# Links

* https://blog.heroku.com/incremental-gc
* http://atdot.net/~ko1/activities/rubyconf2013-ko1_pub.pdf
* https://tunemygc.com/apps/34c62b5504405b56d0de49051dfa11d1/deployments
* https://www.youtube.com/watch?v=r0UjXixkBV8

<!-- .slide: class="master01" -->
