## HMCached: Hotspot-aware Hybrid Memory Management for In-Memory Key-Value Stores

&#160; &#160; &#160; &#160; HMCached is an in-memory K-V store built on a
hybrid DRAM/NVM system. HMCached utilizes an application-level data access counting mechanism to identify frequently-accessed
(hotspot) objects (i.e., K-V pairs) in NVM, and migrates them to fast DRAM to reduce the costly NVM accesses. We also propose an
NVM-friendly index structure to store the frequently-updated portion of object metadata in DRAM, and thus further mitigate the NVM
accesses. Moreover, we propose a benefit-aware memory reassignment policy to address the slab calcification problem in slab-based
K-V store systems, and significantly improve the benefit gain from the DRAM.

&#160; &#160; &#160; &#160; We implement the proposed system with Memcached (https://memcached.org/). 

HMCached Usage
------------

### 1. External Dependencies

* NUMA system: HMCached must run in a non-uniform memory access (NUMA) system with multiple NUMA nodes,
               and each node presents a type of memory mediums.
* NVM device: If without available NVM device, you can use HME for emulation, https://github.com/CGCL-codes/HME.
* numactl-devel
* libevent: http://www.monkey.org/~provos/libevent/ (libevent-dev)
* libseccomp (optional, linux): enables process restrictions for better
  security.

### 2. Compiling

```javascript
[user @node1 HMCached]$ cd dram_reassignment
[user @node1 dram_reassignment]$ make
[user @node1 HMCached]$ sudo cp libdram_reassignment.so /usr/lib
[user @node1 HMCached]$ cd HMCached
[user @node1 HMCached]$ autoreconf -fis 
[user @node1 HMCached]$ ./configure
[user @node1 HMCached]$ make
```
### 3. Running

The run mode of HMCached is similar to Memcached.

#### * Server-side:

The following operation is based on an assumption that memory in Node 0 is DRAM while memory in Node 1 is NVM.
```javascript
[user @node1 HMCached]$ numactl --cpunodebind=0 --membind=0 ./memcached -l 127.0.0.1 -p 11211
```
#### Command-line Options:
```javascript
-p, --port=<num>          TCP port to listen on (default: 11211)
-l, --listen=<addr>       interface to listen on (default: INADDR_ANY)
-h, --help                print this help and exit
-m, --dram-limit=<num>    item memory in megabytes (default: 64 MB)
-e, --nvm-limit=<num>     item memory in megabytes (default: 64 MB)
-x, --threshold-adjust-period=<num>     number of requests served by each slab class (default: 10^7)
-y, --dram-reassignment-period=<num>    number of requests served by all slab classes (default: 3*10^8)
```

#### * Client-side (e.g., running with telnet):
```javascript
[user @node1 home]$ telnet 127.0.0.1 11211
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
get foo
VALUE foo 0 2
hi
END
stats
STAT pid 8861
(etc)
```

### 4. Benchmarks

* Yahoo! Cloud Serving Benchmark, https://github.com/brianfrankcooper/YCSB
* Mutilate: high-performance memcached load generator, https://github.com/leverich/mutilate

Support or Contact
------------
HMCached is developed at SCTS&CGCL Lab (http://grid.hust.edu.cn/) by Zhiwei Li, Haikun Liu and Xiaofei Liao. 
For any questions, please contact Zhiwei Li(leezw@hust.edu.cn),
Haikun Liu (hkliu@hust.edu.cn) and Xiaofei Liao (xfliao@hust.edu.cn).
