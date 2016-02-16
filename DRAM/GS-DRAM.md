#Gather-Scatter DRAM

GS-DRAM first shuffles the data in a DRAM row to reduce _chip conflicts_, and instead of sending different addresses to chips it uses _pattern ID_ and a simple translator in each chip.
It targets power-of-2 strides.

#### _in-memory-databases_
  - layout: row store, column store
  - Access pattern: transactions, analytics 
Execution usually targets both, there for inefficient accesses.

#### Two adverse effect:
  - high latency
  - unnecessary memory BW and cache space consumption

#### Prior work 
increases the cost of memory interface and tag store

#### Challenges:
  - _chip conflicts_ -> shuffling
  - different data from different chips (read different columns with a single read command) -> pattern ID

#### Column ID-based data shuffling
Mapping should:
  - minimize chip conflicts
  - communication should be easy with column command
  - reassembling them should be straight-forward and fast

They used column ID (LSB bits) shuffling, each LSB bit have one stage of shuffling similar to butterfly network.
Each nth bit swaps groups of columns. With this shuffling not a single column has chip conflict (and all 2-stride accesses).

Low latency (based on stages), each stages maximum one processor cycle.


#### Pattern ID
MC provides pattern ID with each column read. Then each DRAM chip do translation based on its _ChipID_ to find which column to access.
Pattern 0 is default access 

Requires two bitwise operations, one mux and one register for storing chipID.
They using spare pins for read command which is just 2 for DDR4.

#### On-chip cache support
GS-DRAM breaks the contiguity of caches. Each cache line will not be completed. To solve this they extend tag store to store pattern ID bits as well.

Patterns may overlap, so before all fetches, MC must check for this row address dirty cache lines. Also, modifying a value should also invalidate all others.

They also never used multiple pattern IDs. They used just two by assigning particular pages with a bit that says this page can you pattern ID A or pattern ID 0.

### ISA support
Added opcode to enable application communicate pattern ID.

### Software stack support
shuffling flag and pattern ID for applications. New malloc implementation.

#### Other notes
They simply changed multiple row buffer hits to one single read.

Cons:
  - Anything that changes DRAM structure is impractical in industry.
  - chipID register
    - when to program?
    - what if is wrong?
    - more verification
  - more pins for patterID
    - just impractical 
  - caches:
    - pattern ID bits to tag store of cache line
    - complexity if cache designs
  - ISA change
  - application Change (automatically is future work)
  - OS pages system changes + TLB
  - self-developed workloads


