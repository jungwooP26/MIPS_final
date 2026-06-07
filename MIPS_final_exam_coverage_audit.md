# 컴퓨터구조 기말 범위 완전성 감사

## 1. 감사 결론

- 범위는 PDF 뷰어 페이지 번호를 기준으로 확정했다.
- 총 230개 PDF 페이지를 텍스트와 추출 도식으로 다시 대조했다.
- 기존 상세본에서 빠져 있던 결론, 일부 구현 세부, 실제 processor 예, 함정 항목을 보완했다.
- 계산 공식집에는 슬라이드 직접 공식뿐 아니라 해당 개념에서 자연스럽게 출제 가능한 유도식도 추가했다.
- 상세본은 슬라이드 문장 전체를 번역한 문서가 아니라, 각 슬라이드의 시험에 필요한 핵심 명제와 수치가 모두 남도록 구성했다.

## 2. 페이지 번호 대조

| 파일 | PDF 범위 | 내부 표기 | 비고 |
|---|---:|---:|---|
| Ch1 | 51~61 | 51~61 | 일치 |
| Ch3 | 43~51 | 47~55 | 내부 번호가 +4 |
| Ch4 | 69~107 | 69~107 | 일치 |
| Ch4.5 | 1~22 | 일부 누락/재사용 | PDF 기준 |
| Ch5 | 1~105 | 대부분 동일 | 일부 slide header가 Chapter 6 |
| Ch6 | 1~44 | 일부 누락/재사용 | 일부 slide header가 Chapter 4 |

Ch3 대응:

| PDF | 내부 | 핵심 |
|---:|---:|---|
| 43 | 47 | IEEE 754와 AI format 필요성 |
| 44 | 48 | FP32/BF16/FP16/FP8 |
| 45 | 49 | AI의 approximation 허용 |
| 46 | 50 | performance/accuracy |
| 47 | 51 | overflow, underflow, mixed precision |
| 48 | 52 | MAC |
| 49 | 53 | dot, matrix, parallel MAC |
| 50 | 54 | finite range/precision 결론 |
| 51 | 55 | ISA arithmetic 결론 |

## 3. 장별 슬라이드 완전성

### Chapter 1: 11쪽

- 51~55: multicore, RTX 4090 규모, supercomputer, mobile, datacenter
- 56~58: CPU와 AI chip, matrix 중심 AI, GPU/TPU/NPU
- 59~60: MIPS/FLOPS/TOPS, CPU/GPU 수치 비교와 역할
- 61: cost/performance, abstraction, ISA, execution time, power, parallelism

### Chapter 3: 9쪽

- 43~44: FP32 구조와 AI용 low precision format
- 45~46: approximation, speed/memory/accuracy trade-off
- 47: gradient overflow/underflow, mixed precision, loss scaling
- 48~49: MAC, dot product, matrix multiply, parallel MAC array
- 50~51: bit interpretation, finite range/precision, ISA arithmetic 결론

### Chapter 4: 39쪽

- 69~75: forwarding 검출, mux control, double hazard, datapath
- 76~82: load-use detector, stall/bubble, hazard datapath, scheduling
- 83~93: branch flush, ID-stage resolution, branch data hazard, 1/2-bit predictor, BTB
- 94~105: exception/interrupt, CP0, EPC/Cause, vector, pipeline flush, precise/imprecise
- 106~107: ILP, IPC/CPI/BIPS, static/dynamic multiple issue

### Chapter 4.5: 22쪽

- 1~7: SIMD 정의, media data, Flynn taxonomy, lane, SSE2, UADD8
- 8~13: assembly, inline, autovectorization, library, OpenMAX, intrinsic
- 14~15: AoS/SoA와 alignment
- 16~18: scalar/AVX-512/assembly DGEMM
- 19~22: CPU SIMD, GPU, Tensor Core, HBM, CPU/GPU 역할

### Chapter 5: 105쪽

- 1~16: locality, hierarchy, SRAM/DRAM/flash/disk, bandwidth
- 17~35: direct mapping, tag/valid, trace, block size, write policy, main memory
- 36~53: CPI/AMAT, associativity, replacement, multilevel cache, advanced CPU/software
- 54~63: blocking DGEMM, dependability, Hamming SEC-DED
- 64~81: VM/VMM, virtual memory, page table, replacement, TLB, protection
- 82~92: hierarchy 공통 원리, 3C, design trade-off, FSM cache controller
- 93~101: coherence, consistency, real cache/TLB, multiple issue, DGEMM 성능
- 102~105: byte/word, stride, associativity/core 수, AMAT 한계, segment/VMM 함정, 결론

### Chapter 6: 44쪽

- 1~15: perceptron, MLP, convolution/averaging filter, CNN, LeNet, AlexNet
- 16~22: Amdahl, single/multicore/SIMD, parameter/FLOPs, CPU/GPU time
- 23~30: shared memory, graphics pipeline, shader/GPGPU/CUDA, grid/block/thread
- 31~35: overlapping convolution reads, global/shared/private memory, host/device
- 36~44: V100 sub-core, register, warp, latency hiding, SM/chip/HBM, peak summary

## 4. 계산식 완전성

### AI arithmetic

- floating-point bias, normal/subnormal/max 범위
- model memory와 precision별 감소율
- loss scaling
- MAC, dot product, matrix MAC/FLOP
- throughput, TOPS, 성능비

### Pipeline

- forwarding boolean과 우선순위
- load-use boolean
- `N+4+stall+flush`
- branch target
- branch predictor miss rate/CPI
- EPC 보정
- IPC/CPI/BIPS

### SIMD/GPU

- lane와 tail
- FMA FLOPs와 peak throughput
- alignment
- Tensor Core MAC
- Amdahl
- convolution output/MAC/FLOP
- CUDA index/block/warp
- shared tile read reduction
- warp efficiency/occupancy
- V100 peak와 bandwidth

### Memory hierarchy

- hit/miss
- bandwidth와 disk access
- block/index/tag/offset/set
- cache storage bit
- write CPI, stall CPI, AMAT, recursive AMAT
- local/global/weighted miss rate
- associativity trace
- blocking tile
- MTTF/MTTR/MTBF/availability
- Hamming parity/distance/syndrome
- VPN/PPN/page table/TLB effective time
- bank mapping

## 5. 슬라이드 자체의 주의점

1. Ch3은 PDF 번호와 내부 번호가 4쪽 다르다.
2. Ch4.5, Ch5, Ch6은 기존 교재 slide를 합치며 Chapter header가 현재 file과 다른 페이지가 있다.
3. Ch5 PDF 50의 `Extra penalty = 500 cycles`는 100 ns/0.25 ns 및 CPI 식과 모순된다. 계산은 400 cycles가 일관된다.
4. Ch4.5 PDF 6의 `4 × 32-bit double precision`은 128-bit SSE2 구조상 `4 × FP32 single precision`의 오타다.
5. Ch6 CPU/GPU 시간 표는 nominal peak FLOPS만으로 모든 행을 재현하는 표가 아니다. 표의 estimated time을 별도 측정/가정으로 취급한다.

## 6. 공부 순서

1. 상세 정리본으로 개념과 판정 순서를 이해한다.
2. 공식집을 보지 않고 각 공식을 직접 적는다.
3. forwarding, cache trace, Hamming, Amdahl, convolution, V100 문제를 수치만 바꿔 반복한다.
4. 마지막에는 이 감사표의 장별 범위를 보며 설명하지 못하는 항목이 없는지 확인한다.
