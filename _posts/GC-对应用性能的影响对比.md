---
title: GC 对应用性能的影响对比
date: 2018-11-14 10:21:22
categories: 编程
tags:
- JVM
---
本片文章记录在系统负载越来越高时，不同 GC 影响 Java Web 应用的响应时间（Latency）和吞吐量（Throughput）的测试结果。<!-- more -->

---

Number of Threads: 8

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 173.5/sec | 59ms | 137ms
Parallel Collector | 146.3/sec | 128ms | 142ms
Concurrent Mark Sweep Collector | 153.8/sec | 72ms | 87ms
Garbage-First Garbage Collector  | 157.8/sec | 88ms | 90ms

---

Number of Threads: 16

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 159.8/sec | 170ms | 215ms
Parallel Collector | 170.6/sec | 192ms | 249ms
Concurrent Mark Sweep Collector | 233.6/sec | 136ms | 157ms
Garbage-First Garbage Collector  | 167.2/sec | 153ms | 209ms

---

Number of Threads: 32

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 171.1/sec | 328ms | 363ms
Parallel Collector | 220.4/sec | 274ms | 332 ms
Concurrent Mark Sweep Collector | 263.4/sec | 211ms | 276ms
Garbage-First Garbage Collector  | 196.8/sec | 249ms | 391ms

---

Number of Threads: 64

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 215.5/sec | 437ms | 611ms
Parallel Collector | 266.8/sec | 393ms | 468ms
Concurrent Mark Sweep Collector | 304.2/sec | 346ms | 479ms
Garbage-First Garbage Collector  | 291.6/sec | 365ms | 479ms

---

Number of Threads: 128

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 265.1/sec | 753ms | 898ms
Parallel Collector | 297.7/sec | 619ms | 755ms
Concurrent Mark Sweep Collector | 255.8/sec | 783ms | 1277ms
Garbage-First Garbage Collector  | 298.8/sec | 642ms | 785ms

---

Number of Threads: 256

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 354.7/sec | 903ms | 1074ms
Parallel Collector | 360.0/sec | 977ms | 1124ms
Concurrent Mark Sweep Collector | 373.1/sec | 604ms | 721ms
Garbage-First Garbage Collector  | 349.4/sec | 951ms | 1087ms

---

Number of Threads: 512

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 374.3/sec | 1858ms | 2006ms
Parallel Collector | 436.7/sec | 1699ms | 1878ms
Concurrent Mark Sweep Collector | 413.8/sec | 1566ms | 1896ms
Garbage-First Garbage Collector  | 410.5/sec | 1645ms | 1862ms

---

Number of Threads: 1024

Garbage Collection | Throughput | 90% Line | 95% Line
- | - | - | - | - | -
Serial Collector | 406.3/sec | 3240ms | 3440ms
Parallel Collector | 434.2/sec | 3109ms | 4723ms
Concurrent Mark Sweep Collector | 451.5/sec | 2475ms | 2793ms
Garbage-First Garbage Collector  | 447.8/sec | 2499ms | 4990ms