# Ch9: Memory Hierarchy
- accessing memory is much slower than performing other datapath actions
  - memory is around 100ns, CPU clock speeds are under 1 ns
- `access time`: delay between submitting a request to the memory and getting the data
- `cycle time`: time gap needed between two successive requests to the memory system
- register file is much faster than physical memory
  - uses a faster type of RAM (SRAM)
  - register file is much smaller in terms of memory footprint
## Concept of a Cache
- ideally, we want to have size of large memory but speed of small memory
  - implement small, fast memory using SRAM
  - large, slow memory using DRAM
- main memory - physical memory visible to the instruction set
- cache - hidden storage
  - store information brought from memory in the cache
  - much smaller than main memory, but much faster
- CPU looks in the cache for data it wants from main memory; if it is not in there, then it retreives it from main memory
![](./images/ch9/0.png)
## Principle of Locality
- program typically accesses a small region of memory regardless of how large the program size is
  - region may change over time, but changes are gradual
- 2 dimensions of locality
  - spatial: probability of program accessing adjacent memory locations
    - program instructions are in contiguous memory locations
  - temporal: probability of program accessing same location in the future it accessing currently
    - example: looping code
## Basic Terms
- analogy: toolbox and tool tray
  - look at tool tray first - if not in tool tray, look in toolbox
    - tool tray is much faster to look at
- `Hit` - CPU finding contents of memory address in cache
  - saves trip to deeper levels of memory hierarchy
  - hit rate is probability of successful lookup in cache by CPU
- `miss` - CPU fails to find memory address in cache
  - miss rate = 1 - hit rate
- `miss penalty` - time penalty associated with servicing a miss; analagous to time to go to garage and fetch tool from tool box
- `EMAT(effective memory access time) - effective access time of CPU, 2 components:
  - time to lookup the cache to see if memory location is there (hit time/cache access time)
  - upon miss in the cache, time to go to deeper levels of memory to fetch missing memory location (miss penalty)
  - CPU will always incur the cache access time, and will add the second if there is a miss in the cache
    - miss penalty depends on organization of cache and main memory design
  - $EMAT = T_c + m * T_m$
    - $T_c$ = cache access time
    - $T_m$ = miss penalty
    - $m$ = miss rate
## Multilevel Memory Hierarchy
- modern processers have multiple levels of caches
  - first level cache is optimized for speed to keep pace with clock cycle times of processor (smaller)
  - speed of second level cache only affects miss penalty of first level cache and doesn't affect clock cycle time of processor
    - focuses on reducing miss rate, so is much larger
  - size of cache becomes larger and access times increase the further away we move from CPU
- EMAT equation for multilevel caches:
  - $EMAT_i = T_i + m_i * EMAT_{i+1}$
- `memory hierarchy`: all storage containing either instructions/data that a processor accesses directly and indirectly
  - indirect = storage is not visible to ISA
![](./images/ch9/0.png)
## Cache Organization
- 3 facets to organization: placement, algorithm for lookup, and validity
- placement: where do we place the data in the cache?
  - use mapping function with memory address associated with cache index
- algorithm for lookup: how do we find data in cache?
  - need to add metadata to each entry to identify cache entry contents
- validity: how do we know if data in cache is valid?
  - need valid bit
## Direct-Mapped Cache Organization
- 1 to 1 between memory location and cache location
  - given a memory address, there is exactly one place to put the contents in the cache
- example: 16 entry memory, 8 entry cache
  - obviously, since cache is initially empty, lookups into memory addresses will always miss-`compulsory misses`
  - however, since two memory location map to 1 location on the cache, when storing and looking up, the previous data must be evicted 
  - `conflict miss` - when there are empty spots in the cache and a direct mapping, but still misses
### Cache Lookup
- CPU looks up into cache to see if data is in the cache
  - only looks in physical memory if a miss occurs
- to go from memory address to cache index, simply do
  - memory address % cache size
  - or simply need least significant bits of address for cache index (example cache has 8 entries, look up mem address 8 - 1000 in binary, index is 000)
- since each entry in cache has multiple mappings to memory addresses, need some way of knowing which address the cache entry is for
  - use the most significant bits as the `tag`
  - example: if we are looking up memory address 11, and we look at location 3 in the cache and the tag is 0, then we know that it is a miss because it corresponds to 0011 (3) and not 1011, which is 11.
- also need to add a valid bit for each entry in the cache
### Fields of Cache entry
- valid field, tag field, and data
- memory address generated by CPU has two parts
  - cache tag and cache index
    - LSB are index, MSB as tag