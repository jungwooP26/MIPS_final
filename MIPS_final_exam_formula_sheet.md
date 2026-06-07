# 컴퓨터구조 기말 계산 공식집

범위:

- Ch1 51~61
- Ch3 43~51
- Ch4 69~107
- Ch4.5 전체
- Ch5 전체
- Ch6 전체

페이지는 PDF 뷰어 번호 기준이다. Ch3은 내부 표기가 4쪽 커서 `PDF 43~51 = 내부 47~55`이며, 특히 `PDF 50 = 내부 54`다.

## 공통 단위

- `1 byte = 8 bits`
- `1 KiB = 2^10 bytes`
- `1 MiB = 2^20 bytes`
- `1 GB = 10^9 bytes` 또는 문제에 따라 `1 GiB = 2^30 bytes`
- `1 GHz = 10^9 cycles/s`
- `1 ms = 10^-3 s`
- `1 us = 10^-6 s`
- `1 ns = 10^-9 s`
- `Speedup = old execution time / new execution time`

## Ch1

### 처리량

`Ideal throughput = unit 수 × unit당 처리량`

`MIPS = instruction count / (time × 10^6)`

`MIPS = clock rate / (CPI × 10^6)`

`FLOPS = floating-point operations / second`

`TOPS = operations / second / 10^12`

### 강의 계산

`18,432 FP32 units / 144 blocks = 128 units/block`

`29 TFLOPS / 0.9 TFLOPS = 32.22배`

`641 TOPS / 2 TOPS = 320.5배`

## Ch3

### Floating-point 일반식

exponent `e` bits, fraction `f` bits:

`bias = 2^(e-1)-1`

`value(normal) = (-1)^s × 1.fraction × 2^(E-bias)`

`min normal = 2^(1-bias)`

`min subnormal = 2^(1-bias-f)`

`max finite = (2-2^-f) × 2^bias`

FP16 (`e=5`, `f=10`):

- bias 15
- max 65,504
- min normal `2^-14 ≈ 6.10×10^-5`
- min subnormal `2^-24 ≈ 5.96×10^-8`

### Model memory

`Memory = parameter count × bytes/parameter`

- FP32: 4 bytes
- FP16/BF16: 2 bytes
- FP8/INT8: 1 byte

### Loss scaling

`scaled gradient = scale × gradient`

`gradient = scaled gradient / scale`

### MAC, dot, matrix

`MAC: acc = acc + A×B`

`dot(a,b) = Σ a_i b_i`

길이 N dot product:

- N MACs
- 약 2N FLOPs

matrix A(M×K), B(K×N):

`MACs = M×N×K`

`FLOPs ≈ 2MNK`

정사각 matrix:

`MACs = N^3`

`FLOPs ≈ 2N^3`

### 처리량

`MAC/s = MAC units × clock × MAC/cycle × utilization`

`OPS/s = MAC/s × 2` if one MAC is counted as multiply + add.

## Ch4

### Forwarding

EX hazard:

```text
EX/MEM.RegWrite
and EX/MEM.Rd != 0
and EX/MEM.Rd == ID/EX.Rs or Rt
```

`ForwardA/B = 10`

MEM hazard:

```text
MEM/WB.RegWrite
and MEM/WB.Rd != 0
and MEM/WB.Rd == ID/EX.Rs or Rt
and no EX/MEM match
```

`ForwardA/B = 01`

No forwarding:

`ForwardA/B = 00`

Priority:

`EX/MEM > MEM/WB`

### Load-use

```text
ID/EX.MemRead
and (
 ID/EX.Rt == IF/ID.Rs
 or ID/EX.Rt == IF/ID.Rt
)
```

기본 5-stage pipeline에서는 1 stall.

### Pipeline cycle

`Ideal cycles = N + pipeline stages - 1`

5-stage:

`Ideal cycles = N + 4`

`Total cycles = N + 4 + stalls + flush penalties`

`CPI = total cycles / retired instructions`

### Branch target

`Target = PC + 4 + (sign-extended immediate << 2)`

강의 예:

`40 + 4 + 7×4 = 72`

### Branch prediction CPI

`Misprediction rate = 1 - accuracy`

`Branch stall CPI = branch frequency × misprediction rate × penalty`

`Effective CPI = base CPI + branch stall CPI`

반복 loop branch가 호출당 N번 평가될 때 steady-state 근사:

- 1-bit predictor miss rate `≈ 2/N`
- strongly-taken 2-bit predictor miss rate `≈ 1/N`

### EPC

강의 구현:

`stored EPC = faulting PC + 4`

`faulting PC = EPC - 4`

### ILP

`IPC = instructions / cycles`

`CPI = cycles / instructions`

`IPC ≈ 1/CPI`

`Instructions/s = clock rate × IPC`

4 GHz, IPC 4:

`16 BIPS`, `CPI = 0.25`

## Ch4.5 SIMD

### Lane

`lanes = register width bits / element width bits`

128-bit:

- INT8 16
- INT16 8
- FP32 4
- FP64 2

256-bit:

- INT8 32
- INT16 16
- FP32 8
- FP64 4

512-bit:

- INT8 64
- INT16 32
- FP32 16
- FP64 8

### FMA

`FLOPs/instruction = lanes × 2`

`Peak OPS/s = cores × lanes × ops/lane/instruction × instructions/cycle × clock`

AVX-512:

- FP32 FMA = 32 FLOPs/instruction
- FP64 FMA = 16 FLOPs/instruction

### Tail

`vector iterations = floor(N/L)`

`tail = N mod L`

### Alignment

`alignment bytes = register bits / 8`

### 강의 계산

`24 CPU cores × 16 FP32 lanes = 384 lanes`

`46 SM × 128 FP32 units = 5,888 units`

`5,888/384 = 15.33배`

`184 tensor cores × 256 MAC = 47,104 MAC/cycle`

`3,000 GB/s / 100 GB/s = 30배`

OpenMAX dot product:

`54,311,234`

## Ch5

### Hit와 miss

`Hit ratio = hits/accesses`

`Miss ratio = misses/accesses = 1-hit ratio`

### Bandwidth

`Bandwidth = bytes transferred / cycles`

bus:

`Bandwidth = transfers/s × bus width(bits) / 8`

DDR:

`Bandwidth = clock × 2 × bus width / 8`

강의:

`16/17 = 0.94 B/cycle`

`16/20 = 0.8 B/cycle`

`16/65 = 0.246 B/cycle`

### Disk

`RPS = RPM/60`

`Average rotational latency = 0.5/RPS`

`Transfer time = bytes/transfer rate`

`Disk access = queue + seek + rotation + transfer + controller`

15,000 RPM:

- RPS 250
- one rotation 4 ms
- average rotation 2 ms

강의:

`4 + 2 + 0.005 + 0.2 = 6.2 ms`

### Cache geometry

`blocks = cache bytes / block bytes`

`sets = cache bytes / (block bytes × associativity)`

`offset bits = log2(block bytes)`

`index bits = log2(sets)`

`tag bits = address bits - index bits - offset bits`

`block address = floor(byte address/block bytes)`

`set index = block address mod sets`

### Cache implementation bits

`Total bits = lines × (data bits + tag bits + valid + dirty + replacement bits)`

강의 FSM cache:

`1024 × (128+18+1+1) = 151,552 bits = 18,944 bytes`

FastMATH 16 KiB cache 그림:

- 64 B block -> offset 6
- 256 entries -> index 8
- tag 18
- line data 512 bits

valid만 포함:

`256 × (512+18+1) = 135,936 bits = 16,992 bytes`

### Write-through CPI

`Effective CPI = base CPI + store frequency × write penalty`

강의:

`1 + 0.1×100 = 11`

### Memory stall

`Stall cycles = accesses × miss rate × miss penalty`

`I-stall CPI = I miss rate × penalty`

`D-stall CPI = data accesses/instruction × D miss rate × penalty`

`Actual CPI = base CPI + I-stall + D-stall`

가중 cache miss rate:

`overall miss rate = (m_I + f_D m_D)/(1+f_D)`

- `m_I`: instruction-cache miss rate
- `m_D`: data-cache miss rate
- `f_D`: data accesses/instruction

강의:

`2 + 0.02×100 + 0.36×0.04×100 = 5.44`

### AMAT

`AMAT = hit time + miss rate × miss penalty`

multilevel:

`AMAT = T1 + MR1 × (T2 + MR2 × (T3 + ...))`

강의:

`1 + 0.05×20 = 2 ns`

### Multilevel cache

`Global L2 miss rate = L1 local miss × L2 local miss`

`L2 local miss = global L2 miss / L1 miss`

`CPI = base + L1 misses×L2 hit penalty + global memory misses×memory penalty`

강의:

- cycle `= 1/4GHz = 0.25 ns`
- memory penalty `=100/0.25=400 cycles`
- L1 only CPI `=1+0.02×400=9`
- with L2 CPI `=1+0.02×20+0.005×400=3.4`
- speedup `=9/3.4=2.65`
- L2 local miss `=0.005/0.02=25%`

### Blocking

double tile working set:

`one tile bytes = B^2×8`

three tiles:

`working set ≈ 3B^2×8`

`B_max = floor(sqrt(usable cache bytes/(3×element bytes)))`

강의 960² DGEMM:

`blocked speedup over unoptimized = 12.0/0.8 = 15`

### Dependability

`MTBF = MTTF + MTTR`

`Availability = MTTF/(MTTF+MTTR)`

`Unavailability = MTTR/(MTTF+MTTR)`

### Hamming

SEC parity bits:

`2^r >= m+r+1`

minimum distance `d`:

- detect up to `d-1` bits
- correct up to `floor((d-1)/2)` bits

SEC-DED:

- SEC parity r bits
- overall parity 1 bit 추가

### Virtual memory

`offset bits = log2(page bytes)`

`VPN bits = VA bits - offset`

`PPN bits = PA bits - offset`

`virtual pages = 2^(VPN bits)`

`page table size = entries × PTE bytes`

32-bit VA, 4 KiB pages:

- offset 12
- VPN 20
- entries `2^20`
- 4-byte PTE이면 4 MiB

### TLB effective time

단순 sequential model:

`EAT = h(t_TLB+t_M) + (1-h)(t_TLB+t_walk+t_M)`

page fault 포함:

`EAT_total = (1-p)EAT + p×fault service time`

### Bank

`bank index = address unit mod number of banks`

같은 cycle에 같은 bank이면 conflict 가능.

## Ch6

### Perceptron

`y = f(Σw_i x_i)`

N inputs:

- N MACs
- 약 2N FLOPs

### Dense layer

`parameters = (N_in+1) × N_out`

`MACs = N_in × N_out`

### Convolution output

`Output = floor((N+2P-K)/S)+1`

### Convolution work

`MACs = Hout×Wout×Cout×Kh×Kw×Cin`

`FLOPs ≈ 2×MACs`

3×3 kernel:

- 9 MAC/output
- 18 FLOPs/output

### Amdahl

`T_new = T_affected/S + T_unaffected`

`Speedup = T_old/T_new`

`Speedup = 1/((1-F)+F/S)`

`Maximum speedup = 1/(1-F)`

Case 1 maximum: 5

Case 2 maximum: 1.25

### Model memory

`Memory = parameters × bytes/parameter`

FP32:

- 1.5 B → 6 GB
- 175 B → 700 GB

### Execution

`Ideal time = FLOPs/sustained FLOPS`

`Speedup = CPU time/GPU time`

### CUDA indexing

`global_id = blockIdx.x×blockDim.x + threadIdx.x`

`blocks = ceil(N/blockSize)`

2D:

`x = blockIdx.x×blockDim.x + threadIdx.x`

`y = blockIdx.y×blockDim.y + threadIdx.y`

### Warp

`warps/block = ceil(threads/block / 32)`

`warp efficiency = active threads/(warps×32)`

`cycles/warp instruction = warp width / SIMD width`

V100 FP32:

`32/16 = 2 cycles`

V100 FP64:

`32/8 = 4 cycles`

### Shared convolution tile

naive reads:

`B^2K^2`

shared tile reads:

`(B+K-1)^2`

K=3, B=16:

- naive 2304
- shared 324
- 약 7.11배 감소

### V100

resident warps:

`80 SM × 64 warps = 5,120 warps`

resident thread contexts:

`5,120×32 = 163,840`

FP32 units:

`80×4×16 = 5,120`

peak:

`5,120×1.245GHz×2 = 12.7488 TFLOPS`

memory transfer:

`time = bytes/bandwidth`

9 GB at 900 GB/s:

`0.01 s = 10 ms`

`16 GB/900 GB/s ≈ 17.78 ms`

4096-bit interface:

`transfer rate = 900×10^9×8/4096 ≈ 1.758 GT/s`

`occupancy = active warps/maximum warps per SM`

### Compute와 memory

`Compute time = FLOPs/compute throughput`

`Memory time = bytes/memory bandwidth`

`Execution time >= max(compute time, memory time)`

`Arithmetic intensity = FLOPs/bytes`
