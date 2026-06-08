# Computer Organization and Design Ch5 Exercises 풀이 가이드

대상 PDF:

- `ComputerOrganizationAndDesign_copy_260414_160221.pdf`
- `ComputerOrganizationAndDesign_copy_260414_160221 (1).pdf` ~ `(16).pdf`

확인된 범위:

- Chapter 5 Exercises `5.1` ~ `5.29`
- 소문항 `5.1.1` ~ `5.29.6`
- 단독 문제 `5.4`, `5.22`, `5.23` 포함

이 문서는 세 부분으로 구성된다.

1. **개념**: 문제를 풀기 전에 알아야 하는 배경
2. **공식**: 계산 문제에 바로 쓰는 식과 용어 변환
3. **문제 표현별 풀이법**: 영어로 어떤 문장이 나오면 어떤 순서로 풀지

---

# Part 1. 개념

## 1. Locality와 Data Layout

### Temporal Locality

같은 데이터를 가까운 시간 안에 다시 접근하는 성질이다.

영어 표현:

- `temporal locality`
- `reused soon`
- `same variable is referenced repeatedly`

예:

- loop 안에서 같은 `B[I][0]`를 여러 번 사용
- 한 번 cache에 올라온 block을 짧은 시간 뒤 다시 접근

풀이 핵심:

- 같은 address가 반복되는지 본다.
- 반복 간격이 cache가 버티는 범위 안인지 본다.

### Spatial Locality

한 데이터를 접근한 뒤 주변 주소를 곧 접근하는 성질이다.

영어 표현:

- `spatial locality`
- `sequential access`
- `contiguously stored`
- `nearby data`

예:

- C의 row-major 배열에서 `A[i][j]`, `A[i][j+1]`, `A[i][j+2]`
- byte address stream `0, 1, 2, 3, ...`

풀이 핵심:

- memory에 연속 배치되는 방향과 loop가 도는 방향이 같은지 확인한다.
- C는 row-major, Matlab/Fortran은 column-major다.

### Row-Major와 Column-Major

C:

```text
addr(A[i][j]) = base + (i × number_of_columns + j) × element_size
```

Matlab/Fortran:

```text
addr(A[i][j]) = base + (j × number_of_rows + i) × element_size
```

주의:

- C에서 같은 row의 column이 연속이다.
- Matlab에서 같은 column의 row가 연속이다.
- 같은 코드라도 언어의 저장 방식이 바뀌면 spatial locality 판단이 바뀐다.

## 2. Cache 기본 개념

cache는 memory hierarchy의 upper level이다. CPU가 요청한 data가 cache에 있으면 hit, 없으면 miss다.

용어:

- `cache block = cache line = line`
- `entry = cache line slot`
- `hit ratio = hit rate`
- `miss ratio = miss rate`
- `miss penalty = miss latency = miss service time`

hit 조건:

```text
hit = valid bit is 1 AND tag matches
```

direct-mapped cache:

- memory block이 들어갈 cache 위치가 하나뿐이다.
- 구현이 쉽고 빠르다.
- 같은 index로 몰리면 conflict miss가 생긴다.

set-associative cache:

- block이 하나의 set 안 여러 way 중 하나에 들어간다.
- conflict miss가 줄지만 tag comparator와 replacement policy가 필요하다.

fully associative cache:

- block이 cache의 어느 line에도 들어갈 수 있다.
- index가 없다.
- 모든 tag를 비교해야 하므로 hardware cost가 크다.

## 3. Cache Address Breakdown

byte address는 보통 다음 세 부분으로 나뉜다.

```text
tag | index | offset
```

- `offset`: block 내부 byte/word 위치
- `index`: 어느 cache entry 또는 set인지
- `tag`: 그 entry에 실제로 어떤 memory block이 있는지 확인

문제에서 `Tag: 31-10, Index: 9-5, Offset: 4-0`처럼 주면 각 bit 수를 센다.

```text
offset bits = 5 bits
index bits = 5 bits
tag bits = 22 bits
```

## 4. Cache Implementation Bits

cache 이름의 크기는 보통 순수 data storage만 말한다. 하지만 실제 구현에는 tag, valid, dirty, replacement bit가 추가된다.

한 line의 구성:

```text
data bits + tag bits + valid bit + dirty bit + replacement bits
```

문제에서 write-back이라고 말하면 dirty bit를 포함한다. direct-mapped이고 write-back 언급이 없으면 보통 valid bit만 포함한다.

## 5. Write Policy와 Allocation Policy

### Write-through

write가 cache에 발생하면 lower level에도 즉시 write한다.

장점:

- lower memory가 최신 값을 유지한다.
- coherence와 recovery가 단순하다.

단점:

- write traffic이 많다.
- write buffer가 필요할 수 있다.

### Write-back

cache block이 수정되면 dirty bit만 set하고, 교체될 때 lower level에 write한다.

장점:

- 같은 block에 여러 write가 있으면 traffic이 줄어든다.

단점:

- dirty block replacement 시 write-back 필요
- coherence 구현이 복잡하다.

### Write-allocate

write miss 때 block을 cache로 가져온 뒤 write한다.

write-back과 자주 함께 쓰인다.

### No-write-allocate

write miss 때 cache에 가져오지 않고 lower level에 직접 write한다.

write-through와 자주 함께 쓰인다.

## 6. Buffer

buffer는 서로 다른 memory hierarchy level 사이의 지연 시간을 숨긴다.

대표:

- `write buffer`: write-through 또는 dirty block write-back을 임시 저장
- `victim buffer`: 교체된 block을 잠시 보관해 conflict miss 완화
- `stream buffer`: sequential prefetch data 저장

문제에서 `write buffer with capacity of one block`이 나오면 dirty block을 buffer에 넣고 새 block read를 먼저 시작할 수 있는지 판단한다.

## 7. Memory Stall, AMAT, CPI

cache miss는 CPU를 stall시킨다. performance 문제는 대개 다음 셋 중 하나다.

- AMAT를 구하는 문제
- stall CPI를 구하는 문제
- total CPI 또는 speedup을 구하는 문제

중요한 구분:

- `hit time`: hit일 때도 항상 드는 시간
- `miss penalty`: miss 때문에 추가되는 시간
- `miss latency`: 보통 miss penalty와 같은 의미

## 8. Block Size Trade-off

block이 커지면:

좋은 점:

- spatial locality를 더 많이 활용
- compulsory miss 감소 가능

나쁜 점:

- block 수가 줄어 conflict/capacity miss 증가 가능
- miss penalty 증가
- pollution 증가

문제에서 여러 block size와 miss rate가 주어지면, 각 후보의 stall cost를 비교한다.

## 9. 3C Miss Model

miss 종류:

- `compulsory miss = cold miss`: 처음 접근해서 발생
- `capacity miss`: cache 전체가 작아서 working set을 못 담음
- `conflict miss = collision miss`: direct/set-associative mapping 때문에 같은 set을 경쟁

판단법:

- 처음 보는 block이면 compulsory
- fully associative cache에서도 miss면 capacity 가능
- fully associative에서는 hit인데 direct/set-associative에서 miss면 conflict

## 10. Replacement Policy

### LRU

Least Recently Used. 가장 오래전에 사용된 block을 교체한다.

풀이:

- 각 set마다 사용 순서를 기록한다.
- hit가 나면 해당 block을 most recent로 갱신한다.
- miss이고 빈 칸 없으면 least recent를 제거한다.

### MRU

Most Recently Used. 가장 최근 사용 block을 교체한다.

streaming workload에서는 MRU가 유리할 수 있다.

### Optimal

미래를 알고 있다고 가정하고, 가장 늦게 다시 쓰일 block 또는 다시 안 쓰일 block을 교체한다.

풀이:

- 현재 set에 있는 후보들의 다음 사용 시점을 찾는다.
- 다음 사용이 가장 먼 block을 제거한다.

## 11. Multilevel Cache

L1은 작고 빠르며 hit time이 중요하다. L2/L3는 크고 느리며 miss rate를 줄이는 역할이 크다.

용어:

- `local miss rate`: 해당 level에 들어온 access 중 miss 비율
- `global miss rate`: 전체 instruction/access 기준으로 lower memory까지 간 비율

L2는 L1 miss에 대해서만 접근된다.

## 12. Dependability

storage reliability 문제에서 쓰는 개념:

- `MTTF`: Mean Time To Failure
- `MTTR`: Mean Time To Repair 또는 Replacement
- `MTBF`: Mean Time Between Failures
- `Availability`: 사용 가능한 시간 비율

availability는 MTTR이 작아질수록 1에 가까워진다.

## 13. Hamming Code, SEC, SEC-DED

SEC:

- Single Error Correcting
- 1-bit error를 위치까지 찾아 고칠 수 있다.

SEC-DED:

- Single Error Correcting, Double Error Detecting
- SEC parity에 overall parity를 추가한다.

핵심:

- minimum distance가 클수록 더 많은 error를 detect/correct 가능하다.

## 14. Virtual Memory와 TLB

virtual address는 page offset과 VPN으로 나뉜다.

```text
virtual address = VPN | page offset
```

physical address는 PPN과 page offset으로 나뉜다.

```text
physical address = PPN | page offset
```

TLB는 VPN to PPN mapping cache다.

TLB hit:

- TLB에서 VPN을 찾고 PA를 바로 만든다.

TLB miss:

- page table을 확인한다.
- page table valid면 TLB를 갱신한다.
- page table invalid면 page fault다.

page fault:

- disk에서 page를 가져온다.
- page table을 갱신한다.
- TLB도 갱신한다.

## 15. Page Table

single-level page table은 virtual page 수만큼 PTE가 필요하다. virtual address가 크면 매우 커진다.

multilevel page table은 실제 사용하는 부분만 allocate해서 공간을 줄인다.

inverted page table은 physical frame 수만큼 entry를 둔다.

## 16. Page Size와 Disk/B-tree

page size가 커지면:

장점:

- page table entry 수 감소
- TLB coverage 증가
- B-tree fanout 증가 가능
- sequential transfer 효율 증가

단점:

- internal fragmentation 증가
- page fault 때 불필요한 data 전송 증가
- 작은 random access에 불리할 수 있음

B-tree 문제에서는 page utility와 page access cost의 비율을 비교한다.

## 17. Virtual Machine

virtual machine은 guest OS가 자신만의 virtual hardware를 가진 것처럼 보이게 한다.

중요 overhead:

- privileged instruction trap
- guest OS trap
- VMM trap
- I/O trap
- page table virtualization

shadow page table:

- hypervisor가 guest page table의 shadow copy를 유지
- page table update를 intercept해야 함

nested page table:

- hardware가 guest VA to PA, guest PA to machine address를 두 단계로 walk
- TLB miss latency가 커질 수 있음

## 18. Cache Coherence와 Consistency

coherence:

- 같은 memory block에 대한 여러 processor의 view를 일관되게 유지
- 한 address에 대한 문제

consistency:

- 여러 address의 read/write 순서가 processor들에게 어떻게 보이는지
- 여러 address 사이의 ordering 문제

snooping protocol:

- cache들이 bus transaction을 관찰
- 다른 core가 write하면 자신의 shared copy를 invalidate하거나 update

## 19. CMP Shared vs Private Cache

private L2:

- hit latency 낮음
- 각 core에 가까움
- data duplication 가능
- shared data와 coherence traffic 증가 가능

shared L2:

- capacity pooling 가능
- shared data에 유리
- hit latency가 더 클 수 있음
- 많은 core에서 bandwidth bottleneck 가능

## 20. Software Cache Optimization

data layout을 access pattern에 맞춘다.

AoS:

```c
struct Entry { field1; field2; field3; };
Entry log[N];
```

한 entry의 여러 field를 함께 쓰면 좋다.

SoA:

```c
field1[N];
field2[N];
field3[N];
```

특정 field만 많은 entry에서 훑으면 좋다.

---

# Part 2. 공식

## 1. 단위

```text
1 byte = 8 bits
1 word = 문제에서 주어진 word 크기
보통 1 word = 4 bytes = 32 bits
1 KiB = 2^10 bytes
1 MiB = 2^20 bytes
1 GHz = 10^9 cycles/s
1 ns = 10^-9 s
```

## 2. Locality와 Block 수

```text
integers per block = block bytes / integer bytes
blocks needed = ceil(total bytes referenced / block bytes)
blocks needed = ceil(number of elements × element bytes / block bytes)
```

C row-major:

```text
addr(A[i][j]) = base + (i × columns + j) × element_size
```

Matlab/Fortran column-major:

```text
addr(A[i][j]) = base + (j × rows + i) × element_size
```

## 3. Cache Geometry

```text
blocks(lines) = cache data bytes / block bytes
sets = blocks / associativity
offset bits = log2(block bytes)
index bits = log2(sets)
tag bits = address bits - index bits - offset bits
```

direct-mapped:

```text
associativity = 1
sets = lines = entries
```

fully associative:

```text
sets = 1
index bits = 0
```

block address:

```text
block address = floor(byte address / block bytes)
set index = block address mod number of sets
```

word address 문제:

```text
word block address = floor(word address / words per block)
word offset bits = log2(words per block)
```

byte address 문제:

```text
byte offset bits = log2(block bytes)
```

## 4. Address Bit Field

문제에서 bit range가 주어질 때:

```text
bit count = high bit - low bit + 1
```

예:

```text
Tag 31-10    -> 22 bits
Index 9-5    -> 5 bits
Offset 4-0   -> 5 bits
```

## 5. Cache Implementation Bits

data bits:

```text
data bits per line = block bytes × 8
```

line당 총 bit:

```text
bits per line = data bits + tag bits + valid bit + dirty bit + replacement bits
```

dirty bit이 없으면:

```text
bits per line = data bits + tag bits + 1 valid bit
```

전체 구현 bit:

```text
total required bits = number of lines × bits per line
```

순수 data bit:

```text
data storage bits = number of lines × data bits per line
```

비율:

```text
ratio = total required bits / data storage bits
```

## 6. Hit Ratio, Miss Rate

```text
hit ratio = hits / accesses
miss rate = misses / accesses
miss rate = 1 - hit ratio
```

## 7. Memory Stall

용어 변환:

```text
access = reference = memory reference
miss penalty = miss latency = miss service time
accesses/instruction = references per instruction
```

program 전체:

```text
memory stall cycles = memory accesses/program × miss rate × miss penalty
```

instruction당:

```text
stall CPI = accesses/instruction × miss rate × miss penalty
stall CPI = references per instruction × miss rate × miss latency
```

I/D 분리:

```text
I-stall CPI = 1 × I-cache miss rate × I-miss penalty
D-stall CPI = data accesses/instruction × D-cache miss rate × D-miss penalty
Actual CPI = base CPI + I-stall CPI + D-stall CPI
```

## 8. AMAT

```text
AMAT = hit time + miss rate × miss penalty
AMAT = hit latency + miss ratio × miss latency
```

multilevel:

```text
AMAT = T_L1 + MR_L1 × (T_L2 + MR_L2_local × memory_time)
```

더 일반적으로:

```text
AMAT = T1 + MR1 × (T2 + MR2 × (T3 + ...))
```

## 9. CPI with Cache Misses

penalty가 ns이면 cycles로 변환:

```text
cycle time = 1 / clock rate
miss penalty cycles = miss penalty time / cycle time
```

clock rate:

```text
clock rate = 1 / cycle time
cycle time = 1 / clock rate
```

total CPI:

```text
total CPI = base CPI + memory stall CPI
```

L2 포함:

```text
CPI = base CPI
    + L1 misses/instruction × L2 hit penalty
    + memory misses/instruction × memory penalty
```

global/local:

```text
global L2 miss rate = L1 local miss rate × L2 local miss rate
L2 local miss rate = global L2 miss rate / L1 local miss rate
```

## 10. Bandwidth

```text
bandwidth = bytes transferred / cycles
bandwidth = bytes transferred / time
time = bytes / bandwidth
```

write-through bandwidth:

```text
write traffic/instruction = data writes/instruction × bytes per write
```

miss read bandwidth:

```text
read traffic/instruction = misses/instruction × block bytes
```

write-back dirty replacement:

```text
write-back traffic/instruction = data misses/instruction × dirty fraction × block bytes
```

bytes/cycle:

```text
bytes/cycle = bytes/instruction / CPI
```

## 11. Sequential Stream Miss Rate

byte stream `0,1,2,3,...` with block size B bytes:

```text
miss rate ≈ 1 / B
```

word stream with B-byte block and 4-byte word:

```text
words per block = B/4
miss rate ≈ 1 / words per block
```

stream buffer가 완벽히 next block을 미리 가져오면 compulsory miss만 남거나 거의 0에 가까워진다. 문제 조건에 따라 첫 block miss만 남는지 계산한다.

## 12. Block Size Optimization

각 block size B에 대해:

```text
stall CPI(B) = references per instruction × miss rate(B) × miss latency(B)
```

모든 후보의 references/instruction이 같으면 비교할 때 생략 가능:

```text
compare score(B) = miss rate(B) × miss latency(B)
```

constant miss latency:

```text
best B = B with lowest miss rate
```

## 13. Replacement Policy Trace

set index:

```text
set = block address mod number of sets
```

LRU:

```text
evict least recently used block in the set
```

MRU:

```text
evict most recently used block in the set
```

Optimal:

```text
evict block whose next use is farthest in the future
```

## 14. Dependability

```text
MTBF = MTTF + MTTR
Availability = MTTF / (MTTF + MTTR)
Unavailability = MTTR / (MTTF + MTTR)
```

## 15. Hamming / SEC-DED

SEC parity bits:

```text
2^r >= m + r + 1
```

SEC-DED:

```text
SEC-DED parity bits = r + 1 overall parity bit
```

minimum distance d:

```text
detectable errors = d - 1
correctable errors = floor((d - 1)/2)
```

syndrome:

```text
syndrome = binary index of wrong bit
```

## 16. B-tree Page Size

entries per page:

```text
entries = page bytes / entry bytes × fullness
```

utility:

```text
utility = log2(entries)
```

access cost:

```text
access cost = disk latency + page bytes / transfer rate
```

choose:

```text
best page size = max utility/cost
```

hot page threshold:

```text
disk cost per access = disk cost / disk accesses per second
DRAM cost per page = DRAM cost per MB × page size in MB
reuse threshold ≈ disk cost per access / DRAM cost per page
```

## 17. Virtual Memory

```text
offset bits = log2(page bytes)
VPN bits = virtual address bits - offset bits
PPN bits = physical address bits - offset bits
virtual pages = 2^(VPN bits)
page table entries = virtual pages
page table size = entries × PTE bytes
```

physical frames:

```text
physical frames = physical memory bytes / page bytes
```

inverted page table:

```text
entries = physical frames
```

## 18. TLB Trace

```text
VPN = floor(virtual address / page size)
offset = virtual address mod page size
```

TLB lookup:

```text
if VPN in valid TLB entry -> TLB hit
else -> TLB miss, consult page table
```

page table:

```text
if PTE valid -> page table hit, load mapping into TLB
else -> page fault, allocate/load physical page, update PTE and TLB
```

set-associative TLB:

```text
TLB set = VPN mod number of TLB sets
```

## 19. TLB Effective Access Time

```text
EAT = h(t_TLB + t_M) + (1-h)(t_TLB + t_walk + t_M)
```

page fault 포함:

```text
EAT_total = (1-p)EAT + p × fault service time
```

## 20. Virtual Machine CPI

per 10,000 instructions로 주어지면:

```text
extra CPI = events per 10,000 instructions × penalty / 10,000
```

privileged trap:

```text
trap CPI = privileged accesses/10000 × trap penalty
```

I/O:

```text
I/O CPI = I/O accesses/10000 × I/O access cycles
```

virtualized total:

```text
CPI = base CPI + guest trap CPI + VMM trap CPI + I/O CPI
```

## 21. Nested Page Table

native 4-level page table walk:

```text
native TLB miss memory references ≈ 4 page-table references + 1 data reference
```

nested page table with 4 guest levels and 4 nested levels:

```text
each guest PTE access needs nested translation
rough memory references can grow to 4 × (4 + 1) + 1 = 21
```

문제의 counting convention에 따라 final data reference를 포함하는지 확인한다.

## 22. CMP Cache CPI/AMAT 비교

L1 miss가 L2로 간다고 할 때:

```text
CPI penalty ≈ L1 miss rate × L2 hit latency + L2 misses/instruction × memory latency
```

또는 L2 local miss rate가 주어지면:

```text
CPI penalty ≈ L1 miss rate × (L2 hit latency + L2 local miss rate × memory latency)
```

## 23. Software Layout

cache miss per entry 추정:

```text
misses per entry ≈ distinct cache blocks touched per entry
```

AoS가 불리한 경우:

```text
only one or two fields are read from a large struct
```

SoA가 유리한 경우:

```text
scan one field across many records
```

---

# Part 3. 문제 표현별 풀이법

## 5.1 Matrix locality

### 5.1.1

영어 단서:

- `How many 32-bit integers can be stored in a 16-byte cache block?`

풀이:

```text
32-bit integer = 4 bytes
integers per block = 16 / 4 = 4
```

필요 개념: block size, word/integer size.

### 5.1.2

영어 단서:

- `Which variable references exhibit temporal locality?`
- C code, repeated loop references

풀이:

1. 각 reference의 address가 loop 동안 반복되는지 본다.
2. `B[I][0]`처럼 inner loop에서 같은 값이 계속 쓰이면 temporal locality가 강하다.
3. `A[I][J]`는 한 번 write 후 바로 재사용되는지 확인한다.
4. `A[J][I]`는 J가 변할 때 주소가 계속 바뀌므로 같은 원소 반복은 약하다.

### 5.1.3

영어 단서:

- `Which variable references exhibit spatial locality?`
- C row-major

풀이:

1. C는 row-major임을 적용한다.
2. `A[I][J]`에서 J가 inner loop로 증가하면 연속 주소라 spatial locality가 좋다.
3. `A[J][I]`는 row index가 바뀌므로 stride가 커져 spatial locality가 나쁘다.

### 5.1.4

영어 단서:

- `Matlab`
- `storing matrix elements within the same column contiguously`
- `How many 16-byte cache blocks are needed`

풀이:

1. Matlab은 column-major로 주소를 계산한다.
2. 참조되는 matrix element 개수를 센다.
3. 중복을 제거한 unique element 수를 구한다.
4. `blocks = ceil(unique_elements × 4 / 16)`을 사용한다.

### 5.1.5

영어 단서:

- `Which variable references exhibit temporal locality?`
- Matlab version

풀이:

1. 계산식은 같으므로 같은 element가 반복 접근되는 reference를 찾는다.
2. 저장 방식은 temporal locality 자체보다 spatial locality에 더 크게 영향 준다.
3. inner loop에서 고정되는 index가 있는 reference를 우선 확인한다.

### 5.1.6

영어 단서:

- `Which variable references exhibit spatial locality?`
- Matlab column-major

풀이:

1. column-major에서는 첫 번째 index가 연속으로 변할 때 spatial locality가 좋다.
2. C에서 좋았던 access가 Matlab에서는 나빠질 수 있고, 반대도 가능하다.
3. loop에서 변하는 index와 column-major 주소식을 대조한다.

## 5.2 Direct-mapped cache trace

공통 입력:

- word address references
- direct-mapped cache
- initially empty

### 5.2.1

영어 단서:

- `direct-mapped cache with 16 one-word blocks`
- `identify binary address, tag, index`
- `hit or miss`

풀이:

1. word address를 binary로 바꾼다.
2. one-word block이므로 word offset은 0 bit다.
3. 16 blocks이므로 index bits는 `log2(16)=4`.
4. tag는 나머지 high bits다.
5. cache table을 empty로 시작한다.
6. 각 reference마다 index 위치의 valid/tag를 확인해 hit/miss를 적는다.

### 5.2.2

영어 단서:

- `two-word blocks`
- `total size of 8 blocks`

풀이:

1. words per block = 2이므로 word offset bits는 1.
2. 8 blocks이면 index bits는 3.
3. `block address = floor(word address/2)`.
4. `index = block address mod 8`.
5. tag는 block address의 index보다 위 bit다.
6. 같은 방식으로 trace한다.

### 5.2.3

영어 단서:

- `optimize a cache design`
- `C1 has 1-word blocks`
- `C2 has 2-word blocks`
- `C3 has 4-word blocks`
- `all with total of 8 words of data`

풀이:

1. 각 cache의 block 수를 구한다.
   - C1: 8 blocks
   - C2: 4 blocks
   - C3: 2 blocks
2. 각 reference sequence를 세 cache로 trace한다.
3. miss count와 hit count를 비교한다.
4. miss rate가 가장 낮은 design을 선택한다.
5. 왜 그런지 spatial locality와 conflict miss로 설명한다.

## 5.3 Cache metadata bits

공통 조건:

- byte addressable
- address = 64 bits
- word = 64 bits = 8 bytes

### 5.3.1

영어 단서:

- `total number of bits required`
- `32 KiB cache`
- `two-word blocks`

풀이:

1. block bytes = 2 words × 8 bytes = 16 bytes.
2. lines = 32 KiB / 16.
3. offset bits = log2(16).
4. index bits = log2(lines) for direct-mapped if associativity is not otherwise stated.
5. tag bits = 64 - index - offset.
6. total bits = lines × (data bits per line + tag + valid).
7. write-back이면 dirty도 넣지만, 문제에서 말하지 않으면 valid만 기본으로 둔다.

### 5.3.2

영어 단서:

- `64 KiB cache`
- `16-word blocks`
- `How much bigger than 32 KiB cache`

풀이:

1. block bytes = 16 × 8 = 128 bytes.
2. lines = 64 KiB / 128.
3. offset/index/tag를 다시 계산한다.
4. total bits를 계산한다.
5. 5.3.1의 total bits와 나눠 증가 비율을 구한다.
6. data size는 2배지만 metadata 감소 때문에 total SRAM이 정확히 2배가 아닐 수 있음을 설명한다.

### 5.3.3

영어 단서:

- `larger data size`
- `might provide slower performance`

풀이:

1. capacity는 커졌지만 block size도 커졌음을 본다.
2. 큰 block은 miss penalty를 늘린다.
3. 큰 cache는 hit time이 늘 수 있다.
4. 불필요한 data를 가져와 pollution이 생길 수 있다.
5. 따라서 AMAT가 나빠질 수 있다고 설명한다.

### 5.3.4

영어 단서:

- `Generate a series of read requests`
- `lower miss rate on a 32 KiB two-way set associative cache`
- `than cache described in 5.3.1`

풀이:

1. 5.3.1의 cache는 direct-mapped라고 보고 같은 index로 충돌하는 두 block을 찾는다.
2. 두 block을 번갈아 접근하는 sequence를 만든다.
3. direct-mapped에서는 계속 conflict miss가 난다.
4. 2-way set associative에서는 같은 set 안 두 way에 둘 다 저장되어 hit가 된다.

## 5.4 Alternative index function

영어 단서:

- `different indexing function`
- `(Block address[63:54] XOR Block address[53:44])`
- `Is it possible to use this to index a direct-mapped cache?`

풀이:

1. 1024 blocks이면 index bits는 10 bits가 필요하다.
2. 제시된 XOR도 10-bit 결과를 만들 수 있는지 확인한다.
3. direct-mapped는 deterministic하게 한 block address를 하나의 cache index로 보내면 된다.
4. 가능하다고 답할 수 있다.
5. 단, lookup 때 같은 XOR function을 hardware로 계산해야 하며 tag에는 index로 쓰지 않은 주소 정보를 충분히 저장해야 한다.
6. 기존 low-order index보다 hardware가 복잡하고 locality/conflict 특성이 달라진다고 설명한다.

## 5.5 Address field and trace

공통:

```text
Tag 31-10, Index 9-5, Offset 4-0
```

### 5.5.1

영어 단서:

- `cache block size (in words)`

풀이:

1. offset bits = 5.
2. block size = 2^5 bytes = 32 bytes.
3. 1 word = 4 bytes이면 32/4 = 8 words.

### 5.5.2

영어 단서:

- `How many entries`

풀이:

1. index bits = 5.
2. entries = 2^5 = 32.

### 5.5.3

영어 단서:

- `ratio between total bits required`
- `over the data storage bits`

풀이:

1. data bits per line = 32 bytes × 8 = 256.
2. tag bits = 22.
3. valid bit = 1.
4. bits per line = 256 + 22 + 1.
5. ratio = bits per line / 256.
6. dirty bit 포함 여부는 문제 조건에 따라 별도 언급한다.

### 5.5.4

영어 단서:

- `byte-addressed cache references`
- `list tag, index, offset`
- `hit or miss`
- `which bytes were replaced`

풀이:

1. 각 byte address를 32-bit binary 또는 hex bit field로 나눈다.
2. offset = bits 4-0.
3. index = bits 9-5.
4. tag = bits 31-10.
5. cache를 power-on empty, valid=0으로 시작한다.
6. miss이면 해당 block의 시작 byte 주소를 구한다.
7. block range는 `[block_start, block_start+31]`.
8. 기존 valid line이 있으면 그 block range가 replaced bytes다.

### 5.5.5

영어 단서:

- `hit ratio`

풀이:

```text
hit ratio = hits / total references
```

5.5.4 trace의 hit 수를 세어 계산한다.

### 5.5.6

영어 단서:

- `final state of the cache`
- `<index, tag, data>`

풀이:

1. 5.5.4 trace가 끝난 뒤 valid line만 모은다.
2. 각 line의 index, stored tag, block data range를 적는다.
3. data는 문제에서 byte values가 없으면 보통 address range 또는 memory block identifier로 표현한다.

## 5.6 Write policies and exclusive cache

### 5.6.1

영어 단서:

- `write-through, non-write allocate`
- `write-back, write allocate`
- `possible buffers needed`

풀이:

1. L1 write-through이므로 L1 to L2 write buffer가 필요할 수 있다.
2. L2 write-back이므로 dirty eviction을 위한 L2 to memory write-back buffer가 필요하다.
3. miss read와 write-back이 겹치면 victim/write buffer가 성능에 중요하다.

### 5.6.2

영어 단서:

- `handling an L1 write-miss`
- `dirty block`

풀이:

1. L1은 no-write-allocate이므로 L1에는 block을 가져오지 않는다.
2. write를 L2로 전달한다.
3. L2는 write-allocate/write-back이므로 L2 miss면 block을 가져와 수정하고 dirty로 표시한다.
4. L2에서 victim이 dirty면 memory로 write-back한다.
5. 필요한 buffer 순서를 설명한다.

### 5.6.3

영어 단서:

- `multilevel exclusive cache`
- `block can only reside in one of L1 and L2`

풀이:

1. exclusive cache에서는 같은 block이 L1과 L2에 동시에 있으면 안 된다.
2. L1 write miss 시 block 위치를 L2에서 찾는다.
3. L2 hit이면 block을 L2에서 제거하거나 ownership을 L1으로 이동한다.
4. L1 victim은 L2로 내려간다.
5. L2 victim dirty이면 memory로 write-back한다.
6. inclusive/non-exclusive와 다른 점을 명확히 설명한다.

## 5.7 Bandwidth for write policies

공통 데이터:

- data reads per 1000 instructions
- data writes per 1000 instructions
- I-cache miss rate
- D-cache miss rate
- block size
- CPI = 2

### 5.7.1

영어 단서:

- `write-through, write-allocate`
- `read and write bandwidths`
- `bytes per cycle`
- `each miss generates one block`

풀이:

1. instruction fetch miss traffic을 계산한다.
2. data read/write miss traffic을 계산한다.
3. write-through이므로 모든 data write가 lower level write traffic을 만든다.
4. write-allocate이므로 write miss도 block read를 만든다.
5. bytes per 1000 instructions를 구한다.
6. cycles per 1000 instructions = 1000 × CPI.
7. bytes/cycle = bytes / cycles.

### 5.7.2

영어 단서:

- `write-back, write-allocate`
- `30% of replaced data cache blocks are dirty`
- `minimal read and write bandwidths`

풀이:

1. miss마다 block read가 필요하다.
2. write traffic은 모든 write가 아니라 dirty replacement 때만 발생한다.
3. dirty write-back bytes = data cache replacements × dirty fraction × block size.
4. instruction miss block은 dirty write-back과 무관한 read traffic이다.
5. bytes/cycle로 나눈다.

## 5.8 Streaming workload

### 5.8.1

영어 단서:

- `streaming workloads`
- `512 KiB working set`
- `address stream 0,1,2,3,...`
- `64 KiB direct-mapped cache`
- `32-byte block`
- `3C model`

풀이:

1. sequential byte stream이면 32-byte block마다 첫 access만 miss다.
2. miss rate = 1/32.
3. working set이 cache보다 커도 재사용이 거의 없으므로 capacity보다 compulsory 중심이다.
4. cache size에 둔감하고 block size에 민감하다고 설명한다.

### 5.8.2

영어 단서:

- `block size is 16, 64, 128 bytes`
- `What kind of locality`

풀이:

1. 각 block size B에 대해 sequential byte stream miss rate = 1/B.
2. block size가 커질수록 miss rate가 감소한다.
3. 이는 spatial locality를 활용하는 것이다.

### 5.8.3

영어 단서:

- `Prefetching`
- `stream buffer`
- `two-entry stream buffer`
- `cache block can be loaded before computation on previous block completed`

풀이:

1. 첫 cache block은 demand miss일 가능성이 크다.
2. 이후 sequential next blocks는 stream buffer에 미리 들어온다.
3. buffer hit이면 cache hit처럼 처리한다.
4. steady-state miss rate는 거의 0 또는 첫 compulsory miss만 남는다고 조건에 맞춰 계산한다.

## 5.9 Optimal block size

공통:

- miss rates for B = 8,16,32,64,128
- references per instruction = 1.35

### 5.9.1

영어 단서:

- `miss latency of 20 × B cycles`

풀이:

1. 각 B마다 latency = 20B.
2. score = 1.35 × miss rate × latency.
3. score가 가장 작은 B 선택.

### 5.9.2

영어 단서:

- `miss latency of 24 + B cycles`

풀이:

1. 각 B마다 latency = 24+B.
2. score = 1.35 × miss rate × latency.
3. 최소 score 선택.

### 5.9.3

영어 단서:

- `constant miss latency`

풀이:

1. latency가 모든 후보에서 같으면 score는 miss rate에만 비례한다.
2. miss rate가 가장 낮은 B를 선택한다.

## 5.10 L1 capacity, AMAT, CPI, L2

공통:

- memory access time = 70 ns
- data accesses = 36% of instructions
- every instruction has instruction fetch
- base CPI = 1.0

### 5.10.1

영어 단서:

- `L1 hit time determines cycle times`
- `clock rates`

풀이:

```text
cycle time = L1 hit time
clock rate = 1 / cycle time
```

P1, P2 각각 계산한다.

### 5.10.2

영어 단서:

- `Average Memory Access Time`

풀이:

```text
AMAT = L1 hit time + L1 miss rate × main memory access time
```

P1, P2 각각 계산한다.

### 5.10.3

영어 단서:

- `base CPI of 1.0`
- `instruction access or data access causes a cache miss`

풀이:

1. miss penalty cycles = 70 ns / cycle time.
2. I-stall CPI = 1 × L1 miss rate × penalty.
3. D-stall CPI = 0.36 × L1 miss rate × penalty.
4. total CPI = 1 + I-stall + D-stall.
5. faster processor는 execution time까지 비교해야 한다.
   - `CPU time per instruction = CPI × cycle time`

### 5.10.4

영어 단서:

- `addition of an L2 cache`
- `L2 miss rate indicated is local miss rate`
- `AMAT`

풀이:

```text
AMAT = L1 hit time + L1 miss rate × (L2 hit time + L2 local miss rate × memory time)
```

L2를 넣기 전 AMAT와 비교한다.

### 5.10.5

영어 단서:

- `total CPI for P1 with addition of L2`

풀이:

1. L2 hit penalty cycles = L2 hit time / P1 cycle time.
2. memory penalty cycles = memory time / P1 cycle time.
3. L1 misses/instruction = `(1 + 0.36) × L1 miss rate`.
4. memory misses/instruction = L1 misses/instruction × L2 local miss rate.
5. CPI = base + L1 misses/instruction × L2 hit penalty + memory misses/instruction × memory penalty.

### 5.10.6

영어 단서:

- `what would the L2 miss rate need to be`
- `faster than P1 without L2`

풀이:

1. P1 without L2의 total CPI 또는 time/instruction을 기준값으로 둔다.
2. L2 local miss rate를 unknown `x`로 둔다.
3. P1 with L2 CPI 식을 `x`로 만든다.
4. `with L2 time < without L2 time` 부등식을 푼다.

### 5.10.7

영어 단서:

- `faster than P2 without L2`

풀이:

1. P2 without L2의 `CPI × cycle time`을 기준값으로 둔다.
2. P1 with L2의 `CPI(x) × P1 cycle time`을 만든다.
3. 부등식으로 x의 최대 허용 miss rate를 구한다.

## 5.11 Associative cache trace

공통:

- word address sequence
- LRU/MRU/optimal
- binary word address, tag, index, offset, hit/miss

### 5.11.1

영어 단서:

- `three-way set associative`
- `two-word blocks`
- `total size of 48 words`

풀이:

1. block size = 2 words.
2. total blocks = 48/2 = 24.
3. 3-way이므로 sets = 24/3 = 8.
4. word offset bits = log2(2)=1.
5. index bits = log2(8)=3.
6. tag bits는 word address width에서 나머지.
7. 각 set에 3 way, 각 way에 valid/tag/data를 그린다.

### 5.11.2

영어 단서:

- `Trace the behavior`
- `true LRU replacement policy`

풀이:

1. 5.11.1의 geometry를 사용한다.
2. 각 word address에 대해 block address, offset, index, tag를 구한다.
3. set 안 3 way의 tag를 검사한다.
4. hit이면 LRU order를 갱신한다.
5. miss이면 빈 way 또는 LRU way에 넣는다.
6. 매 reference 뒤 각 way의 tag 상태를 기록한다.

### 5.11.3

영어 단서:

- `fully associative`
- `one-word blocks`
- `total size of eight words`

풀이:

1. block size = 1 word.
2. blocks = 8.
3. fully associative이므로 index bits = 0.
4. offset bits = 0.
5. tag = 전체 word address.
6. 8 entries를 그린다.

### 5.11.4

영어 단서:

- `Trace`
- `fully associative`
- `true LRU`

풀이:

1. 모든 line의 tag를 비교한다.
2. hit/miss를 기록한다.
3. miss이면 빈 line 또는 LRU line을 교체한다.
4. index가 없음을 주의한다.

### 5.11.5

영어 단서:

- `fully associative`
- `two-word blocks`
- `total size of eight words`

풀이:

1. block size = 2 words.
2. blocks = 8/2 = 4.
3. fully associative이므로 sets=1, index bits=0.
4. offset bits = 1.
5. tag = word block address.

### 5.11.6

영어 단서:

- `Trace`
- `LRU replacement`

풀이:

1. 5.11.5 geometry를 사용한다.
2. block address = floor(word address/2).
3. tag = block address.
4. 4 entries를 LRU로 관리한다.

### 5.11.7

영어 단서:

- `using MRU`

풀이:

1. 5.11.6과 같은 geometry.
2. miss이고 full이면 most recently used entry를 교체한다.
3. LRU 결과와 hit 수를 비교한다.

### 5.11.8

영어 단서:

- `optimal replacement policy`
- `lowest miss rate`

풀이:

1. miss 시 cache 안 후보들의 다음 사용 시점을 찾는다.
2. 다음 사용이 가장 먼 block을 교체한다.
3. 다시 사용되지 않는 block이 있으면 그것을 우선 교체한다.
4. 전체 hit/miss trace를 만든다.

## 5.12 Multilevel cache

### 5.12.1

영어 단서:

- `only a first level cache`
- `second level direct-mapped`
- `second level eight-way set associative`
- `main memory access time is doubled`

풀이:

1. clock rate 2 GHz에서 cycle time을 구한다.
2. memory time 100 ns를 cycles로 바꾼다.
3. L1 only:
   ```text
   CPI = base CPI + L1 misses/instruction × memory penalty
   ```
4. L2 direct:
   ```text
   CPI = base + L1 miss rate × L2 hit cycles + global miss rate × memory penalty
   ```
5. L2 8-way도 같은 식으로 계산한다.
6. memory access time doubled이면 memory penalty만 2배로 바꿔 다시 계산한다.
7. absolute CPI와 percent change를 모두 적는다.

### 5.12.2

영어 단서:

- `add a third level cache`
- `takes 50 cycles`
- `13% miss rate`
- `Would this provide better performance?`

풀이:

1. 기존 L2 direct CPI를 기준값으로 둔다.
2. L3 추가 식:
   ```text
   CPI = base + L1miss × L2hit + L2global_or_L2miss × L3hit + final_memory_miss × memoryPenalty
   ```
3. 문제의 13%가 L3 local miss rate인지 문맥상 해석해 적용한다.
4. CPI가 줄면 better.
5. 장점: memory miss 감소.
6. 단점: 추가 hit latency, area, power, complexity.

### 5.12.3

영어 단서:

- `off-chip second level cache`
- `global miss rate of 4%`
- `each additional 512 KB lowered global miss rates by 0.7%`
- `match performance`

풀이:

1. 목표 CPI는 표의 on-chip direct-mapped L2 CPI다.
2. off-chip L2 hit time = 50 cycles.
3. cache size가 512KB에서 시작하고, 512KB 추가마다 global miss rate가 0.7%씩 감소한다고 둔다.
4. size를 변수 n으로 두고 global miss rate를 식으로 만든다.
5. CPI(size)를 목표 CPI 이하로 만드는 최소 size를 찾는다.

## 5.13 Dependability

### 5.13.1

영어 단서:

- `Calculate the MTBF`

풀이:

```text
MTBF = MTTF + MTTR
```

단위를 맞춘다. years와 days가 섞이면 둘 중 하나로 통일한다.

### 5.13.2

영어 단서:

- `Calculate availability`

풀이:

```text
Availability = MTTF / (MTTF + MTTR)
```

### 5.13.3

영어 단서:

- `MTTR approaches 0`

풀이:

1. 식에서 MTTR=0에 가까워지면 availability는 1에 가까워진다.
2. 현실적으로 repair/replacement 시간이 완전히 0은 불가능하다고 설명한다.

### 5.13.4

영어 단서:

- `MTTR gets very high`
- `Does this imply low availability?`

풀이:

1. MTTR이 커지면 availability가 낮아진다.
2. 하지만 MTTF도 매우 크면 availability가 여전히 높을 수 있다.
3. 비율 문제임을 설명한다.

## 5.14 SEC/DED Hamming

### 5.14.1

영어 단서:

- `minimum number of parity bits`
- `128-bit word`
- `SEC/DED`

풀이:

1. SEC용 r을 찾는다.
   ```text
   2^r >= m + r + 1
   ```
2. m=128을 대입한다.
3. SEC/DED는 overall parity 1 bit를 추가한다.
4. total parity = r+1.

### 5.14.2

영어 단서:

- `protect each 64 bits with 8 parity bits`
- `cost/performance ratio`

풀이:

1. 64-bit 단위 SEC/DED parity overhead를 구한다.
2. 128-bit 단위 parity overhead와 비교한다.
3. cost는 parity bit 수 또는 parity/data 비율.
4. performance는 correctable error 수인데 둘 다 single-error correcting이면 동일한 correction capability로 볼 수 있다.
5. parity overhead가 작은 쪽이 cost/performance가 좋다.

### 5.14.3

영어 단서:

- `SEC code`
- `protects 8 bit words with 4 parity bits`
- `read value 0x375`
- `is there an error? correct`

풀이:

1. 0x375를 binary로 바꾼다.
2. parity bit 위치를 정한다.
3. syndrome을 계산한다.
4. syndrome이 0이면 error 없음.
5. syndrome이 nonzero이면 해당 bit를 flip한다.
6. corrected data bits를 추출한다.

## 5.15 Page size, B-tree, hot pages

### 5.15.1

영어 단서:

- `best page size`
- `entries now become 128 bytes`

풀이:

1. 각 page size에 대해 entries = page bytes / 128 × 0.7.
2. utility = log2(entries).
3. access cost는 기존 table의 disk 조건을 사용해 latency + transfer.
4. utility/cost가 최대인 page size 선택.

### 5.15.2

영어 단서:

- `pages are half full`

풀이:

1. fullness를 0.7에서 0.5로 바꾼다.
2. 5.15.1과 같은 방식으로 utility/cost 계산.
3. 최대값 선택.

### 5.15.3

영어 단서:

- `modern disk`
- `3 ms latency`
- `100 MB/s transfer rate`
- `future servers are likely to have larger pages`

풀이:

1. access cost = 3 ms + page bytes / 100 MB/s.
2. utility/cost를 다시 계산한다.
3. transfer bandwidth가 좋아지고 latency가 지배적이면 큰 page의 추가 transfer cost가 상대적으로 작아진다.
4. 그래서 future server에서 larger page가 유리해질 수 있음을 설명한다.

### 5.15.4

영어 단서:

- `keep using the same page size`
- `avoiding software rewrite`
- `other factors can be changed`

풀이:

1. page size 대신 entry size, compression, fill factor, caching policy, DRAM hot page threshold, disk speed 등을 조정할 수 있다.
2. cost trend를 고려한다.
3. DRAM 가격 하락은 hot pages를 memory에 유지하기 쉽게 한다.
4. disk latency 개선은 제한적이므로 page size를 유지하려면 prefetch/cache/compression이 중요하다고 설명한다.

## 5.16 TLB and page table trace

공통:

- virtual address stream
- page size
- TLB entries
- LRU/direct/two-way

### 5.16.1

영어 단서:

- `4 KiB pages`
- `4-entry fully associative TLB`
- `true LRU replacement`
- `list TLB hit/miss, page table hit/miss, page fault, updated TLB`

풀이:

1. offset bits = log2(4KiB)=12.
2. VPN = virtual address >> 12.
3. TLB에서 VPN tag를 찾는다.
4. TLB hit이면 PA page를 얻고 LRU time을 갱신한다.
5. TLB miss이면 page table의 VPN entry를 본다.
6. PTE valid이면 page table hit, TLB에 insert한다.
7. PTE invalid이면 page fault, next largest page number를 할당하고 PTE/TLB를 갱신한다.
8. TLB full이면 LRU entry를 교체한다.

### 5.16.2

영어 단서:

- `16 KiB pages instead of 4 KiB`
- `advantages/disadvantages larger page size`

풀이:

1. offset bits = log2(16KiB)=14.
2. VPN을 다시 계산한다.
3. 같은 trace를 반복한다.
4. 장점: TLB coverage 증가, page table entry 감소, sequential 효율.
5. 단점: internal fragmentation, page fault cost 증가, 불필요한 data loading.

### 5.16.3

영어 단서:

- `two-way set associative TLB`

풀이:

1. 4-entry 2-way이면 sets = 2.
2. TLB set = VPN mod 2.
3. 각 set 안 2 way에서만 LRU replacement를 수행한다.
4. fully associative와 충돌 차이를 비교한다.

### 5.16.4

영어 단서:

- `direct mapped TLB`

풀이:

1. 4-entry direct-mapped이면 index = VPN mod 4.
2. 해당 entry 하나만 확인한다.
3. tag mismatch면 miss이고 기존 entry를 교체한다.
4. conflict miss가 증가할 수 있음을 설명한다.

### 5.16.5

영어 단서:

- `why a CPU must have a TLB`
- `if there were no TLB`

풀이:

1. TLB가 없으면 매 memory access마다 page table walk가 필요하다.
2. instruction fetch와 data access 모두 translation overhead를 가진다.
3. multilevel page table이면 여러 memory reference가 추가된다.
4. 따라서 high performance를 위해 TLB가 필수라고 설명한다.

## 5.17 Page table size

공통:

- VA size = 32 bits
- page size = 8 KB
- PTE size = 4 bytes

### 5.17.1

영어 단서:

- `5 applications`
- `utilize half of the memory available`
- `total page table size`

풀이:

1. offset bits = log2(8KB)=13.
2. VPN bits = 32-13=19.
3. full single-level entries = 2^19.
4. 한 process page table size = entries × 4 bytes.
5. 문제의 `half of memory available`이 physical memory resident entries만 의미하는지, full virtual table을 의미하는지 확인해 서술한다.
6. 5 applications이면 process 수만큼 곱한다.

### 5.17.2

영어 단서:

- `two level page table`
- `up to 256 entries at first level`
- `main page table entry is 6 bytes`
- `minimum and maximum`

풀이:

1. first level entries 최대 256.
2. first level이 가리키는 second-level table의 coverage를 계산한다.
3. minimum은 실제 사용하는 virtual space가 적고 필요한 second-level table만 allocate하는 경우.
4. maximum은 first-level entry가 모두 second-level table을 가리키는 경우.
5. first-level table size와 second-level tables size를 더한다.

### 5.17.3

영어 단서:

- `virtually indexed, physically tagged cache`
- `page size`
- `16 KiB direct-mapped cache`
- `2 64-bit words per block`

풀이:

1. block size = 2×8=16 bytes.
2. 16KiB direct-mapped lines = 16KiB/16 = 1024.
3. index bits = 10, offset bits = 4.
4. VIPT에서 index+offset bits가 page offset bits 안에 들어가야 synonym problem을 피한다.
5. page offset bits = 13.
6. index+offset = 14이면 page offset보다 크므로 direct-mapped 16KiB는 문제 가능.
7. data size를 늘리려면 associativity를 늘려 index bits를 줄인다.

## 5.18 Page table optimization

공통:

- VA = 43 bits
- physical DRAM = 16 GB
- page size = 4 KB
- PTE = 4 bytes

### 5.18.1

영어 단서:

- `single-level page table`
- `how many PTEs`
- `how much physical memory`

풀이:

1. offset bits = 12.
2. VPN bits = 43-12.
3. PTEs = 2^VPNbits.
4. page table memory = PTEs × 4 bytes.

### 5.18.2

영어 단서:

- `multilevel page table`
- `segment tables allowed unlimited size`
- `how many levels`
- `memory references if missing in TLB`

풀이:

1. 한 page table page가 몇 PTE를 담는지 계산한다.
   ```text
   PTEs per page = 4KB / 4B = 1024 = 2^10
   ```
2. VPN bits를 10-bit 단위로 나눠 필요한 level 수를 구한다.
3. unlimited upper table이면 top level이 한 번에 남은 bits를 가리킬 수 있다.
4. TLB miss translation은 level 수만큼 page table memory reference가 필요하고, 실제 data access까지 포함하면 +1을 고려한다.

### 5.18.3

영어 단서:

- `segments are limited to 4 KiB page size`
- `Is 4 bytes large enough for all page table entries`

풀이:

1. PTE가 physical page number 또는 next-level page table pointer를 담아야 한다.
2. physical frames = 16GB / 4KB = 2^22.
3. physical frame number에는 22 bits 필요.
4. valid/protection/dirty/reference bits까지 32 bits 안에 들어가는지 판단한다.
5. upper-level pointer도 physical page frame을 가리키므로 같은 조건을 적용한다.

### 5.18.4

영어 단서:

- `How many levels`
- `segments limited to 4 KiB page size`

풀이:

1. 각 level table page가 2^10 entries를 담는다.
2. VPN bits = 31.
3. 10 bits씩 cover하면 4 levels가 필요하다.
4. top level은 남은 1 bit만 사용할 수 있다.

### 5.18.5

영어 단서:

- `inverted page table`
- `how many PTEs`
- `hash table implementation`
- `common case and worst case memory references`

풀이:

1. inverted page table entries = physical frames.
2. physical frames = physical memory / page size.
3. common case hash lookup은 hash bucket access + PTE check 정도로 작다.
4. worst case는 collision chain을 모두 따라갈 수 있다.
5. 일반 page table보다 space는 줄지만 lookup time이 hash/collision에 의존한다고 설명한다.

## 5.19 TLB valid/protection

### 5.19.1

영어 단서:

- `valid bit set to zero`

풀이:

1. mapping이 현재 유효하지 않거나 page가 unmapped일 수 있다.
2. context switch 후 stale entry를 무효화했을 수 있다.
3. page table update, page eviction, protection change 때문에 invalid일 수 있다.

### 5.19.2

영어 단서:

- `instruction writes to VA page 30`
- `software managed TLB faster than hardware managed`

풀이:

1. TLB table에서 VA page 30이 있는지 찾는다.
2. 없으면 TLB miss다.
3. page table walk 또는 software handler가 mapping을 찾는다.
4. software managed TLB는 OS가 replacement/format을 최적화하거나 simple hardware보다 효율적일 때 유리할 수 있다.

### 5.19.3

영어 단서:

- `writes to VA page 200`

풀이:

1. TLB entry에서 VA page 200을 찾는다.
2. protection이 RO이면 write는 protection fault를 발생시킨다.
3. modified bit 처리도 확인한다.
4. writable이면 modified bit를 set한다.

## 5.20 Replacement policy comparison

공통:

- 2-way set associative
- 4 one-word blocks
- word address sequence

기본 geometry:

```text
total blocks = 4
ways = 2
sets = 2
block size = 1 word
set index = address mod 2
```

### 5.20.1

영어 단서:

- `Assuming LRU`
- `which accesses are hits`

풀이:

1. 각 address의 set을 구한다.
2. set마다 2 way를 LRU로 관리한다.
3. hit access 번호 또는 address를 표시한다.

### 5.20.2

영어 단서:

- `Assuming MRU`

풀이:

1. 같은 trace를 사용한다.
2. miss 시 most recent block을 제거한다.
3. hit 목록을 LRU와 비교한다.

### 5.20.3

영어 단서:

- `random replacement`
- `flipping a coin`

풀이:

1. miss이고 set full일 때 coin result로 victim을 고른다.
2. random이므로 결과는 coin sequence에 따라 달라진다.
3. 자신의 coin sequence와 hit count를 함께 제시한다.

### 5.20.4

영어 단서:

- `optimal replacement policy`

풀이:

1. miss 시 현재 set의 두 block 중 미래에 더 늦게 쓰이는 것을 제거한다.
2. 다음 사용이 없는 block을 가장 먼저 제거한다.
3. hit access 목록을 기록한다.

### 5.20.5

영어 단서:

- `why difficult to implement optimal`

풀이:

1. optimal policy는 미래 access sequence를 알아야 한다.
2. 실제 hardware는 미래를 모르므로 구현 불가능하다.
3. LRU 같은 heuristic을 사용한다고 설명한다.

### 5.20.6

영어 단서:

- `decision upon each memory reference whether cached`
- `impact on miss rate`

풀이:

1. streaming처럼 재사용 없는 block은 cache하지 않으면 pollution을 줄인다.
2. 재사용될 block만 cache하면 miss rate가 줄 수 있다.
3. 하지만 미래 reuse 예측이 필요하다.

## 5.21 Virtual machine overhead CPI

### 5.21.1

영어 단서:

- `no accesses to I/O`
- `VMM performance impact doubles/cut in half`
- `limit performance degradation to 10%`

풀이:

1. privileged OS accesses per 10000 instructions를 instruction당으로 바꾼다.
2. virtualized trap penalty를 더한다.
3. extra CPI = events/10000 × penalty.
4. CPI = base CPI + extra CPI.
5. VMM penalty가 2배/1/2일 때 다시 계산한다.
6. 10% degradation 조건:
   ```text
   CPI <= base CPI × 1.10
   ```
7. penalty를 unknown으로 두고 푼다.

### 5.21.2

영어 단서:

- `I/O accesses`
- `non-virtualized system`
- `virtualized system`
- `half the I/O accesses`

풀이:

1. non-virtualized CPI = base + I/O accesses/10000 × I/O access time.
2. virtualized CPI는 privileged trap overhead와 VMM overhead를 추가한다.
3. I/O accesses가 절반이면 I/O CPI 항만 절반으로 줄인다.
4. 차이를 비교한다.

## 5.22 Virtual memory vs virtual machines

영어 단서:

- `Compare and contrast`
- `goals`
- `pros and cons`
- `cases where desired`

풀이:

1. virtual memory의 목표:
   - address space abstraction
   - protection
   - relocation
   - physical memory보다 큰 address space
2. virtual machine의 목표:
   - whole machine abstraction
   - isolation
   - multiple OS
   - migration/testing/consolidation
3. 공통점:
   - indirection과 mapping table 사용
   - protection/isolation 제공
4. 차이:
   - VM은 OS 전체를 virtualize하고, virtual memory는 process address를 virtualize한다.
5. 장단점과 사례를 표로 쓰면 좋다.

## 5.23 Non-native ISA virtualization

영어 단서:

- `emulate non-native ISAs`
- `QEMU`
- `difficulties`
- `run faster than native ISA?`

풀이:

1. ISA translation이 필요하다.
2. instruction semantics, endian, exception, privileged instruction, memory model 차이가 어렵다.
3. dynamic binary translation overhead가 크다.
4. host hardware가 훨씬 빠르거나 JIT optimization이 좋으면 native old machine보다 빠를 수 있다.
5. 같은 세대 native hardware보다 빠르기는 어렵다고 균형 있게 설명한다.

## 5.24 Cache controller with write buffer

### 5.24.1

영어 단서:

- `request that hits in the cache while a block is being written back`

풀이:

1. cache hit이면 processor request를 처리해도 된다.
2. write buffer의 write-back은 background로 계속 진행한다.
3. 단, request address가 write buffer의 block과 충돌하는지 확인한다.
4. 같은 block이면 ordering/forwarding이 필요할 수 있다.

### 5.24.2

영어 단서:

- `request that misses in the cache while write buffer busy`

풀이:

1. miss 처리를 위해 새 block을 읽어야 한다.
2. write buffer가 memory port를 사용 중이면 기다리거나 memory arbitration이 필요하다.
3. write buffer가 1 block capacity라서 또 dirty victim이 나오면 buffer space가 생길 때까지 stall해야 한다.
4. read priority를 줄지 write-back completion을 기다릴지 정책을 설명한다.

### 5.24.3

영어 단서:

- `Design a finite state machine`
- `enable use of write buffer`

풀이:

1. 기본 FSM 상태를 둔다: Idle, Compare Tag, Allocate/Read Miss, Write Back.
2. dirty victim이 있으면 memory에 바로 쓰지 않고 WriteBufferFill 상태로 보낸다.
3. 새 block read를 시작한다.
4. write buffer background write 상태를 추가한다.
5. buffer full일 때 dirty miss가 나면 stall 상태를 추가한다.
6. hit while buffer busy, miss while buffer busy 전이를 명확히 표시한다.

## 5.25 Coherence and consistency

### 5.25.1

영어 단서:

- `possible values`
- `correct cache coherence protocol`
- `one more possible value if no coherency`

풀이:

1. X[0], X[1] 초기값을 둔다.
2. 각 processor의 operations가 block 단위 coherence를 유지한다고 가정한다.
3. legal interleavings로 가능한 final values를 나열한다.
4. coherence가 없으면 lost update 또는 stale copy로 추가 값이 가능하다고 제시한다.

### 5.25.2

영어 단서:

- `snooping protocol`
- `valid operation sequence`

풀이:

1. read miss, write miss, invalidate, ownership acquisition을 순서대로 적는다.
2. write 전에는 block을 exclusive/modified 상태로 가져와야 한다.
3. 다른 cache의 shared copy는 invalidate된다.
4. 두 processor가 완료되는 valid bus transaction sequence를 작성한다.

### 5.25.3

영어 단서:

- `best-case and worst-case numbers of cache misses`

풀이:

1. best case는 operations 순서를 locality가 좋게 배치해 ownership transfer를 최소화한다.
2. worst case는 processors가 번갈아 write해 invalidation과 miss를 많이 만든다.
3. compulsory miss와 coherence miss를 구분해 count한다.

### 5.25.4

영어 단서:

- `memory consistency`
- `possible values of C and D`
- `ensures both consistency assumptions`

풀이:

1. A와 B의 write/read ordering을 consistency assumptions에 맞춰 가능한 interleaving만 허용한다.
2. C=B, D=A가 읽을 수 있는 값을 순서 제약으로 계산한다.
3. sequential consistency라면 모든 processor의 program order가 유지되는 interleaving만 고려한다.

### 5.25.5

영어 단서:

- `if assumptions are not maintained`

풀이:

1. write visibility가 processor마다 다르게 보일 수 있다.
2. program order가 깨져 한쪽 write는 보이고 다른 write는 안 보이는 값을 예로 든다.
3. 5.25.4에서 불가능했던 C,D pair를 하나 제시한다.

### 5.25.6

영어 단서:

- `write policies and write allocation policies`
- `make protocol implementation simpler`

풀이:

1. write-through은 memory가 최신이므로 coherence가 단순하다.
2. no-write-allocate는 write miss 때 cache state 변화가 적다.
3. write-back/write-allocate는 dirty ownership 관리가 필요해 복잡하다.
4. protocol simplicity 관점에서 write-through 조합이 유리하다고 설명한다.

## 5.26 CMP private/shared L2

### 5.26.1

영어 단서:

- `Which cache design is better`
- `Use data`

풀이:

1. private/shared 각각 L2 hit latency와 misses per instruction을 사용한다.
2. penalty 또는 CPI contribution:
   ```text
   L2 cost = L1 miss rate × L2 hit latency + L2 misses/instruction × memory latency
   ```
3. benchmark A, B 각각 계산한다.
4. 더 작은 cost를 선택한다.

### 5.26.2

영어 단서:

- `off-chip bandwidth becomes bottleneck`
- `first off-chip link doubles`

풀이:

1. memory latency 또는 off-chip penalty를 2배로 둔다.
2. private/shared cost를 다시 계산한다.
3. memory miss가 더 적은 design이 유리해질 가능성이 크다.
4. private은 duplication 때문에 off-chip traffic이 커질 수 있고 shared는 pooling으로 miss를 줄일 수 있음을 설명한다.

### 5.26.3

영어 단서:

- `pros and cons of shared vs private L2`
- `single-threaded, multi-threaded, multiprogrammed`
- `on-chip L3`

풀이:

1. single-threaded: private latency 장점.
2. multi-threaded shared data: shared capacity/data sharing 장점.
3. multiprogrammed: shared cache contention 가능, private isolation 장점.
4. L3가 있으면 L2 private + L3 shared 구조가 절충안이 된다.

### 5.26.4

영어 단서:

- `non-blocking L2 cache`
- `shared or private`

풀이:

1. non-blocking cache는 여러 outstanding misses를 허용한다.
2. shared L2는 많은 core의 miss가 모이므로 MLP 활용이 크다.
3. private L2도 이득이 있지만 core당 miss stream이 적으면 효과가 제한될 수 있다.
4. bottleneck 위치를 기준으로 설명한다.

### 5.26.5

영어 단서:

- `double number of cores every 18 months`
- `released in three years`
- `same per-core performance`
- `off-chip memory bandwidth`

풀이:

1. 3 years = 36 months.
2. 18 months마다 2배이므로 36 months는 두 번 doubling.
3. cores는 4배.
4. per-core bandwidth를 유지하려면 off-chip bandwidth도 4배 필요하다.

### 5.26.6

영어 단서:

- `entire memory hierarchy`
- `improve number of concurrent misses`

풀이:

1. non-blocking caches, MSHR 증가.
2. prefetching.
3. banked/interleaved memory.
4. out-of-order execution.
5. larger queues, memory-level parallelism.
6. cache hierarchy tuning을 제시한다.

## 5.27 Web server log data layout

### 5.27.1

영어 단서:

- `topK_sourceIP(int hour)`
- `Which fields accessed`
- `64-byte cache blocks`
- `misses per entry`

풀이:

1. function이 hour로 filter하고 sourceIP를 count하므로 hour field와 sourceIP field가 필요하다.
2. struct layout에서 이 두 field가 어떤 cache block에 들어가는지 본다.
3. entry 하나를 scan할 때 touched cache blocks 수를 계산한다.
4. prefetching이 없으므로 sequential scan miss는 block당 첫 entry에서 발생한다.
5. average misses per entry = misses / entries.

### 5.27.2

영어 단서:

- `reorganize data structure`
- `improve cache utilization`

풀이:

1. sourceIP와 hour만 별도 arrays로 분리하는 SoA를 제안한다.
2. 필요한 fields만 packed하면 cache block당 더 많은 useful entries가 들어간다.
3. cold fields는 따로 둔다.

### 5.27.3

영어 단서:

- `another log processing function`
- `prefer a different layout`
- `both functions important`

풀이:

1. 여러 field를 함께 쓰는 함수 예를 든다.
2. 그 경우 AoS가 더 유리할 수 있다.
3. 둘 다 중요하면 hot fields만 columnar로 분리하거나 hybrid layout을 사용한다.
4. code snippet으로 before/after를 보여준다.

## 5.28 SPEC cache miss data

### 5.28.1

영어 단서:

- `Cache Performance for SPEC CPU2000`
- `miss rates broken down by miss types`
- `cold, capacity, conflict`

풀이:

1. 주어진 external table에서 benchmark pair의 miss rates를 찾는다.
2. cold/capacity/conflict 항목을 cache associativity별로 옮긴다.
3. missing data가 있으면 직접 simulation이 아니라 table lookup 문제임을 명시한다.

### 5.28.2

영어 단서:

- `Select set associativity`
- `64 KiB L1 shared by both benchmarks`
- `if directly mapped, select L2 associativity`

풀이:

1. 두 benchmark의 miss rate를 함께 고려한다.
2. 평균, worst-case, workload importance 중 기준을 정한다.
3. L1 associativity는 hit time 증가와 miss 감소 사이 trade-off로 고른다.
4. L1이 direct-mapped로 고정되면 L2 associativity를 높여 conflict miss를 줄인다.

### 5.28.3

영어 단서:

- `higher set associativity actually increases miss rate`
- `construct reference stream`

풀이:

1. table에서 associativity 증가에도 miss rate가 증가한 예를 찾는다.
2. 이유는 replacement behavior나 changed set mapping이 불리해질 수 있기 때문이다.
3. reference stream을 만들 때, 낮은 associativity에서는 우연히 보존되는 block이 높은 associativity의 LRU에서는 밀려나도록 구성한다.
4. trace로 증명한다.

## 5.29 Shadow paging vs nested page table

### 5.29.1

영어 단서:

- `create process`
- `TLB miss`
- `page fault`
- `context switch`
- `shadow page table and nested page table`

풀이:

1. shadow paging:
   - create process 때 shadow table 생성/초기화.
   - TLB miss는 shadow table 기준으로 해결.
   - page fault나 guest page table update는 hypervisor가 intercept해 shadow를 갱신.
   - context switch 때 shadow/TLB 관리 필요.
2. NPT:
   - guest page table과 nested page table을 hardware가 함께 walk.
   - create process 때 guest table은 guest OS, nested mapping은 hypervisor.
   - TLB miss latency가 길다.
   - page fault는 guest fault와 nested fault를 구분한다.

### 5.29.2

영어 단서:

- `x86-based four-level page table`
- `native versus nested`
- `memory references needed to service a TLB miss`

풀이:

1. native 4-level walk는 page table entry 4개를 읽고 최종 data를 읽는다.
2. nested에서는 guest PTE를 읽기 위한 guest physical address마다 nested translation이 필요하다.
3. 일반 계산:
   ```text
   nested references ≈ guest_levels × (nested_levels + 1) + final data reference
   ```
4. 4-level/4-level이면 보통 21 references로 계산한다.
5. 문제에서 final data access 제외 convention이면 20이라고 명시한다.

### 5.29.3

영어 단서:

- `which metrics are more important`
- `TLB miss rate`
- `TLB miss latency`
- `page fault rate`
- `page fault handler latency`

풀이:

1. shadow paging은 page fault/page table update intercept overhead가 중요하다.
2. shadow는 TLB miss latency는 native에 가까울 수 있다.
3. NPT는 hardware walk가 길어 TLB miss latency가 중요하다.
4. 둘 다 TLB miss rate는 중요하지만 이유가 다르다.

### 5.29.4

영어 단서:

- `native execution CPI of 1`
- `shadow page tables versus NPT`
- `page table virtualization overhead`

풀이:

1. shadow overhead:
   ```text
   page faults per instruction × shadowing page fault overhead
   ```
2. NPT overhead:
   ```text
   TLB misses per instruction × NPT TLB miss latency
   ```
3. per 1000 instructions로 주어졌으면 1000으로 나눈다.
4. CPI = 1 + overhead CPI.

### 5.29.5

영어 단서:

- `reduce page table shadowing induced overhead`

풀이:

1. batch updates.
2. paravirtualization.
3. avoid frequent guest page table writes.
4. large pages.
5. hardware assist for dirty/accessed bits.
6. better TLB retention/context tagging.

### 5.29.6

영어 단서:

- `reduce NPT-induced overhead`

풀이:

1. larger pages to reduce page table depth and TLB miss rate.
2. nested TLB or combined TLB caching VA to MA.
3. page-walk cache.
4. reduce TLB misses with ASID/VPID.
5. improve hardware page walker and memory-level parallelism.

---

# 전체 문제 누락 체크

포함된 문제:

```text
5.1.1 5.1.2 5.1.3 5.1.4 5.1.5 5.1.6
5.2.1 5.2.2 5.2.3
5.3.1 5.3.2 5.3.3 5.3.4
5.4
5.5.1 5.5.2 5.5.3 5.5.4 5.5.5 5.5.6
5.6.1 5.6.2 5.6.3
5.7.1 5.7.2
5.8.1 5.8.2 5.8.3
5.9.1 5.9.2 5.9.3
5.10.1 5.10.2 5.10.3 5.10.4 5.10.5 5.10.6 5.10.7
5.11.1 5.11.2 5.11.3 5.11.4 5.11.5 5.11.6 5.11.7 5.11.8
5.12.1 5.12.2 5.12.3
5.13.1 5.13.2 5.13.3 5.13.4
5.14.1 5.14.2 5.14.3
5.15.1 5.15.2 5.15.3 5.15.4
5.16.1 5.16.2 5.16.3 5.16.4 5.16.5
5.17.1 5.17.2 5.17.3
5.18.1 5.18.2 5.18.3 5.18.4 5.18.5
5.19.1 5.19.2 5.19.3
5.20.1 5.20.2 5.20.3 5.20.4 5.20.5 5.20.6
5.21.1 5.21.2
5.22
5.23
5.24.1 5.24.2 5.24.3
5.25.1 5.25.2 5.25.3 5.25.4 5.25.5 5.25.6
5.26.1 5.26.2 5.26.3 5.26.4 5.26.5 5.26.6
5.27.1 5.27.2 5.27.3
5.28.1 5.28.2 5.28.3
5.29.1 5.29.2 5.29.3 5.29.4 5.29.5 5.29.6
```
