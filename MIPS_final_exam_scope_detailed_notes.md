# 컴퓨터구조 기말 범위 상세 정리

## 0. 정리 범위

- Chapter 1: PDF 51~61쪽만
- Chapter 3: PDF 43~51쪽만
- Chapter 4: PDF 69~107쪽만
- Chapter 4.5 SIMD: 전체
- Chapter 5: 전체
- Chapter 6: 전체

### 페이지 번호 기준

이 문서에서 `PDF n쪽`은 PDF 뷰어가 표시하는 실제 파일 페이지 번호다. 슬라이드 아래쪽에 인쇄된 내부 번호와 혼동하지 않는다.

| 파일 | 시험 범위 PDF 번호 | 슬라이드 내부 표기 | 판정 |
|---|---:|---:|---|
| Ch1 | 51~61 | 51~61 | 일치 |
| Ch3 | 43~51 | 47~55 | PDF 번호보다 내부 번호가 4 큼 |
| Ch4 | 69~107 | 69~107 | 일치 |
| Ch4.5 | 1~22 전체 | 일부 번호 누락, 일부는 Chapter 3/4 표기 | PDF 번호 사용 |
| Ch5 | 1~105 전체 | 대부분 일치하나 일부가 Chapter 6로 표기 | PDF 번호 사용 |
| Ch6 | 1~44 전체 | 일부 번호/Chapter 표기가 재사용됨 | PDF 번호 사용 |

따라서 사용자가 예로 든 `Ch3 PDF 50쪽 = 내부 표기 54쪽`이 맞다. Ch3의 AI 추가 부분은 **PDF 43쪽, 내부 표기 47쪽**에서 시작한다. 기존 정리도 PDF 43쪽부터 사용했으므로 시작 범위는 맞았으며, 이번 개정에서 두 번호를 명시해 혼동 가능성을 제거했다.

Temp와 Downloads에 중복된 Ch1, Ch3, Ch4.5 PDF는 각각 SHA-256이 동일해 같은 파일임도 확인했다.

Chapter 1과 Chapter 3은 교수님이 추가한 AI 관련 슬라이드가 시작되는 PDF 페이지부터 정리했다. Chapter 4는 중간고사 범위를 제외하고 forwarding 검출이 시작되는 PDF 69쪽부터 multiple issue 개요가 끝나는 PDF 107쪽까지만 정리했다.

계산 문제 대비를 위해 각 단원에서 다음을 구분했다.

- `강의 공식`: 슬라이드에 직접 제시된 공식 또는 수치
- `유도 공식`: 슬라이드의 개념으로 문제를 풀 때 필요한 공식
- `판정 규칙`: hazard, hit/miss, exception처럼 조건을 검사하는 문제
- `강의 예제`: 슬라이드 수치를 그대로 계산
- `주의`: 단위나 조건 때문에 자주 틀리는 부분

---

# Chapter 1. AI 시대의 컴퓨터 구조

## 1.1 Multicore CPU

PDF 51쪽은 Intel 10세대 Core i9 10-core CPU를 예로 든다.

- multicore CPU는 한 chip 안에 여러 general-purpose core를 둔다.
- 각 core는 branch, operating system, browser, database처럼 control flow가 복잡한 일을 독립적으로 처리한다.
- core 수가 늘었다고 모든 프로그램이 core 수만큼 빨라지는 것은 아니다.
- 작업을 독립적인 부분으로 분할할 수 있어야 병렬 실행이 가능하다.
- serial part, synchronization, memory bandwidth가 실제 speedup을 제한한다.

계산형 문제에서는 보통 다음 관계를 사용한다.

`이상적인 처리량 = core 수 × core당 처리량`

실제 처리량은 병렬화 가능한 비율과 병목 때문에 이보다 작다. 정확한 전체 speedup은 Chapter 6의 Amdahl's Law로 계산한다.

## 1.2 GPU 규모

PDF 52쪽의 RTX 4090 예:

- 76 billion transistors
- 18,432 FP32 multipliers
- 144 processing blocks

유도 계산:

`processing block당 FP32 multiplier 수 = 18,432 / 144 = 128`

이 수치는 GPU가 소수의 복잡한 core보다 대량의 단순 arithmetic unit을 사용한다는 점을 보여준다.

주의:

- 18,432개의 multiplier가 항상 매 cycle 모두 유효한 결과를 내는 것은 아니다.
- 실제 성능은 clock, instruction mix, occupancy, memory bandwidth, branch divergence에 영향을 받는다.
- hardware unit 수와 동시에 실행 가능한 software thread 수는 같은 값이 아니다.

## 1.3 GPU가 사용되는 규모

PDF 53~55쪽은 GPU 병렬성이 여러 규모에서 사용됨을 보여준다.

- supercomputer: 다수 CPU와 GPU를 연결해 과학 계산과 AI training 수행
- mobile SoC: CPU, GPU, NPU를 한 chip에 통합
- datacenter: 수많은 accelerator를 network로 연결

규모가 커질수록 단순 연산 능력뿐 아니라 다음이 중요하다.

- memory capacity와 bandwidth
- accelerator 사이 communication
- workload 분배
- power와 cooling
- fault tolerance

## 1.4 CPU와 AI chip 비교

### CPU

- 적은 수의 강력한 core
- branch prediction, out-of-order execution, cache hierarchy
- 복잡하고 순서 의존적인 작업에 강함
- 낮은 latency가 중요한 작업에 적합
- 일반적으로 8~32개 정도의 고성능 core를 예로 듦

### GPU, TPU, NPU

- 대량의 단순 arithmetic unit
- 같은 연산을 많은 data에 반복하는 작업에 강함
- 높은 throughput 지향
- matrix multiplication, convolution, tensor operation에 적합

핵심 구분:

- latency: 한 작업이 끝날 때까지 걸리는 시간
- throughput: 단위 시간에 완료하는 작업의 수
- CPU는 latency와 flexibility에 강하고, AI chip은 throughput에 강하다.

## 1.5 AI에서 행렬곱이 중요한 이유

### Image

- pixel 값은 행렬 또는 tensor로 표현된다.
- convolution layer와 fully connected layer는 반복적인 multiply-accumulate로 구현된다.

### Language model

- token은 embedding vector로 변환된다.
- attention과 feed-forward layer에서 큰 matrix multiplication이 반복된다.

핵심 연산:

`C[i,j] = Σ_k A[i,k] × B[k,j]`

한 `C[i,j]`는 A의 한 row와 B의 한 column의 dot product다.

`N × N` 정사각 matrix multiplication의 대략적인 연산량:

- output element 수: `N²`
- output 하나당: `N` multiplication과 약 `N` addition
- 총 MAC 수: `N³`
- multiplication과 addition을 각각 1 FLOP로 세면 약 `2N³ FLOPs`

## 1.6 GPU, TPU, NPU

### GPU

- graphics용으로 시작했지만 programmable parallel processor로 발전
- AI training과 research에 널리 사용
- 비교적 general-purpose한 parallel accelerator

### TPU

- Google의 tensor 전용 accelerator
- matrix/tensor operation에 특화
- 대규모 datacenter training에 사용

### NPU

- mobile phone과 PC에 통합
- 낮은 전력으로 inference 수행
- camera, speech, on-device AI에 적합

구분 문제:

- training: weight를 갱신하므로 큰 compute, memory, precision 요구
- inference: 학습된 weight로 결과만 계산하며 low precision을 더 적극적으로 사용 가능

## 1.7 MIPS, FLOPS, TOPS

### MIPS

`MIPS = instruction count / (execution time × 10^6)`

또는 일반 CPU 공식과 연결하면:

`MIPS = clock rate / (CPI × 10^6)`

한계:

- ISA마다 instruction의 일이 다르다.
- 같은 CPU도 program에 따라 CPI가 다르다.
- AI의 핵심인 low-precision matrix operation 처리량을 잘 표현하지 못한다.

### FLOPS

`FLOPS = floating-point operation 수 / second`

단위:

- MFLOPS = `10^6 FLOPS`
- GFLOPS = `10^9 FLOPS`
- TFLOPS = `10^12 FLOPS`

반드시 precision을 같이 본다.

- FP64 FLOPS
- FP32 FLOPS
- FP16 FLOPS

서로 다른 precision의 수치는 직접 비교하면 안 된다.

### TOPS

`TOPS = total operation 수 / second / 10^12`

AI chip에서는 INT8, INT4 등의 low-precision operation을 포함한 처리량을 나타내는 경우가 많다.

주의:

- 제조사마다 MAC를 1 operation 또는 2 operations로 세는 방식이 다를 수 있다.
- sparsity를 적용한 peak TOPS인지 dense TOPS인지 확인해야 한다.
- peak 성능과 실제 application 성능은 다르다.

## 1.8 CPU와 GPU 수치 비교

슬라이드 수치:

- CPU: Intel i9-13900K, 약 $400
- GPU: RTX 4070, 약 $600
- CPU FP32: 약 0.9 TFLOPS
- GPU FP32: 약 29 TFLOPS
- CPU INT8: 약 2 TOPS
- GPU INT8: 약 641 TOPS

계산:

`FP32 성능비 = 29 / 0.9 ≈ 32.22`

따라서 약 32배다.

`INT8 성능비 = 641 / 2 = 320.5`

따라서 약 320배다.

주의:

- FP32 TFLOPS와 INT8 TOPS는 서로 다른 precision이므로 서로 나누면 의미가 없다.
- 같은 precision, 같은 operation counting 방식에서 비교해야 한다.
- 가격 대비 성능도 `성능 / 가격`으로 별도 계산할 수 있지만, workload가 같아야 의미가 있다.

## 1.9 CPU가 여전히 필요한 이유

- complex logic: branch와 unpredictable control flow
- low latency: input, OS response, control
- general purpose: 다양한 program 수행
- orchestration: data 준비, GPU kernel 실행, 결과 처리

실제 AI system:

`CPU가 control + GPU/TPU/NPU가 대량 matrix arithmetic`

## 1.10 Chapter 1 결론

PDF 61쪽의 핵심:

- technology 발전으로 cost/performance가 계속 개선된다.
- hardware와 software 모두 abstraction의 계층으로 구성된다.
- ISA는 hardware와 software 사이의 interface다.
- 성능의 가장 직접적인 기준은 execution time이다.
- power가 성능 향상의 주요 제한 요소다.
- power와 단일 core 성능의 한계를 넘기 위해 parallelism을 활용한다.

---

# Chapter 3. AI를 위한 수 표현과 MAC

## 2.1 FP32 복습

FP32 구성:

- sign: 1 bit
- exponent: 8 bits
- fraction 또는 mantissa field: 23 bits
- 총 32 bits = 4 bytes

정규화 수의 개념식:

`value = (-1)^sign × 1.fraction × 2^(exponent-bias)`

FP32 bias는 127이다.

슬라이드는 FP32의 대략적인 범위를 `1.2×10^-38`에서 `3.4×10^38` 정도로 설명한다.

일반적인 IEEE 계열 floating-point 유도식:

- exponent field가 `e` bits이면 `bias = 2^(e-1)-1`
- fraction field가 `f` bits이면 hidden 1을 포함한 precision은 `p=f+1` binary digits
- 최소 positive normal: `2^(1-bias)`
- 최소 positive subnormal: `2^(1-bias-f)`
- 최대 finite normal: `(2-2^-f) × 2^bias`

FP16은 `e=5`, `f=10`, `bias=15`이므로:

- 최대 finite: `(2-2^-10)×2^15 = 65,504`
- 최소 positive normal: `2^-14 ≈ 6.10×10^-5`
- 최소 positive subnormal: `2^-24 ≈ 5.96×10^-8`

슬라이드의 `FP16 min ≈ 6×10^-8`은 최소 normal이 아니라 최소 positive subnormal에 해당한다.

AI에서 FP32의 문제:

- weight 하나당 4 bytes
- memory capacity 사용량 증가
- memory bandwidth 증가
- 더 많은 transistor switching으로 power 증가
- AI가 항상 FP32 수준의 precision을 요구하지는 않음
- AI pattern recognition은 과학 계산처럼 약 7 decimal digits를 항상 요구하지 않음

## 2.2 AI number format 비교

| Format | 구성 | 크기 | 핵심 특징과 대표 용도 |
|---|---:|---:|---|
| FP32 | E8, M23 | 4 bytes | 넓은 range/precision, training baseline |
| BF16 | E8, M7 | 2 bytes | FP32와 exponent 폭이 같아 range 유지, large-model training |
| FP16 | E5, M10 | 2 bytes | BF16보다 fraction이 많지만 range가 좁음, training/inference |
| FP8 예 | E4, M3 | 1 byte | 매우 compact, 최신 AI chip inference |
| INT8 | integer 8 bits | 1 byte | on-device inference, scale/zero-point 필요 가능 |

핵심:

- exponent bit 수는 range에 큰 영향을 준다.
- fraction bit 수는 precision에 큰 영향을 준다.
- BF16은 FP16보다 fraction은 적지만 exponent가 넓어 training에서 overflow/underflow에 더 강하다.

## 2.3 Model memory 계산

`Model memory = parameter 수 × parameter당 bytes`

예:

`1 billion parameters × 4 bytes = 4 GB`

`1 billion parameters × 2 bytes = 2 GB`

`1 billion parameters × 1 byte = 1 GB`

동일 model에서:

- FP16/BF16 memory = FP32의 `1/2`
- FP8/INT8 memory = FP32의 `1/4`

주의:

- 이 계산은 weight만 계산한 최소치다.
- training에서는 gradient, optimizer state, activation, master weight 때문에 훨씬 많은 memory가 필요하다.
- GB를 `10^9 bytes`로 계산하는지 GiB를 `2^30 bytes`로 계산하는지 확인한다.

## 2.4 Speed와 memory 비교

슬라이드의 단순 비교:

- FP32: speed 1, memory 4
- FP16: 약 2배 speed, memory 2
- INT8: 약 4배 speed, memory 1

사용 예:

- smartphone NPU: INT8로 low-power real-time inference
- cloud GPU: FP16으로 training 가속
- datacenter: FP16/INT8을 혼합해 cost와 energy 절감

계산형 문제:

`speedup = old execution time / new execution time`

`memory reduction ratio = old bytes / new bytes`

예:

FP32 model이 8 GB라면:

- FP16: 약 4 GB
- INT8: 약 2 GB

주의:

- bit 수가 절반이라고 실제 program이 무조건 정확히 2배 빨라지는 것은 아니다.
- hardware가 해당 format을 지원해야 한다.
- memory-bound인지 compute-bound인지에 따라 speedup이 달라진다.

## 2.5 Overflow와 underflow

### Overflow

- 표현 가능한 최대값보다 큰 값
- 결과가 `Inf`가 되거나 이후 연산에서 `NaN`이 발생할 수 있음

슬라이드 예:

- FP16 최대값 약 65,504
- gradient 70,000은 FP16 범위를 넘으므로 overflow

판정:

`|value| > max representable value`이면 overflow 가능

### Underflow

- 너무 작은 값이 subnormal이 되거나 0으로 round됨
- gradient가 0이 되면 weight가 갱신되지 않음

슬라이드 예:

- FP16의 매우 작은 값 기준을 약 `6 × 10^-8`로 설명
- gradient `10^-9`가 0으로 round될 수 있음

주의:

- FP16에는 subnormal도 있으므로 최소 normal과 최소 nonzero 값을 구분해야 한다.
- 시험에서 슬라이드가 제시한 threshold를 주면 그 값을 기준으로 판정한다.

## 2.6 Mixed precision과 loss scaling

### Mixed precision

- FP16/BF16으로 빠르게 matrix arithmetic 수행
- 중요한 누적 또는 master weight는 FP32로 유지

### Loss scaling

1. loss 또는 gradient에 scale factor `S`를 곱한다.
2. FP16 계산에서 작은 gradient가 0으로 사라지는 것을 방지한다.
3. weight update 전 gradient를 다시 `S`로 나눈다.

공식:

`scaled gradient = S × original gradient`

`original gradient = scaled gradient / S`

예:

원래 gradient가 `10^-9`, scale이 `2^16 = 65,536`이면:

`scaled gradient = 10^-9 × 65,536 ≈ 6.5536 × 10^-5`

이는 FP16에서 훨씬 안전하게 표현될 수 있다.

주의:

- scale이 너무 크면 반대로 overflow가 발생할 수 있다.
- dynamic loss scaling은 overflow 여부를 보고 scale을 조절한다.
- PyTorch와 TensorFlow 같은 framework가 mixed precision과 loss scaling을 지원한다.

## 2.7 MAC

`accumulator = accumulator + A × B`

MAC 한 번은:

- multiplication 1회
- addition 1회

FLOP counting에서:

- MAC를 2 FLOPs로 세는 경우가 많다.
- hardware 표기에서는 MAC를 1 operation으로 세는 경우도 있으므로 문제 조건 확인이 필수다.

## 2.8 Dot product

`dot(a,b) = Σ_i a_i b_i`

길이가 `N`이면:

- multiplication: `N`
- 일반적인 addition: `N-1`
- accumulator가 이미 있다고 보면 `N MACs`
- 약 `2N FLOPs`

## 2.9 Matrix multiplication

`C[i,j] = Σ_k A[i,k]B[k,j]`

`M × K` matrix A와 `K × N` matrix B:

- output 크기: `M × N`
- MAC 수: `M × N × K`
- FLOPs: 약 `2MNK`

정사각 `N × N`:

- MAC 수: `N³`
- FLOPs: 약 `2N³`

## 2.10 처리량 계산

`MAC/s = MAC unit 수 × clock frequency × cycle당 MAC 수 × utilization`

peak에서는 utilization을 1로 둔다.

`TOPS = operations/s / 10^12`

FMA 또는 MAC를 2 operations로 세면:

`OPS/s = MAC/s × 2`

슬라이드의 병렬 MAC 규모 예:

- CPU core: cycle당 약 1~2 MAC
- RTX 4070 GPU: cycle당 약 2,600 MAC
- Google TPU v4: cycle당 약 275,000 MAC

주의:

- CPU, GPU, TPU 수치는 precision이 같아야 비교 가능하다.
- 실제 utilization은 memory, dependency, branch, occupancy에 의해 1보다 작다.

## 2.11 Chapter 3 결론

PDF 50~51쪽, 내부 표기 54~55쪽의 핵심:

- bit 자체에는 고정된 의미가 없고 instruction이 signed, unsigned, floating-point 등으로 해석한다.
- 모든 computer number representation은 range와 precision이 유한하다.
- integer와 floating-point operation 모두 overflow/underflow 가능성을 고려해야 한다.
- floating point는 real number의 정확한 저장이 아니라 제한된 range와 precision을 가진 approximation이다.
- ISA는 signed/unsigned integer와 floating-point arithmetic을 지원한다.
- 슬라이드는 MIPS의 자주 쓰이는 core instruction 54개가 SPECINT의 100%, SPECFP의 97%를 차지한다고 요약한다.

---

# Chapter 4. Forwarding부터 Multiple Issue까지

## 3.1 Forwarding이 필요한 이유

생산 instruction의 결과가 WB까지 기다리지 않아도 이미 EX 또는 MEM stage에서 생성되었다면, 이를 다음 instruction의 EX input으로 직접 보낸다.

RAW dependency 예:

```asm
sub $2, $1, $3
and $12, $2, $5
```

`and`가 `$2`를 register file에서 읽을 때는 아직 이전 값일 수 있다. `sub`의 결과를 EX/MEM에서 ALU input으로 forwarding해야 한다.

## 3.2 Register 번호 전달

forwarding unit이 비교하는 source:

- 현재 EX stage instruction의 source:
  - `ID/EX.RegisterRs`
  - `ID/EX.RegisterRt`
- 앞 instruction의 destination:
  - `EX/MEM.RegisterRd`
  - `MEM/WB.RegisterRd`

기본 dependency 비교:

- 1a: `EX/MEM.Rd == ID/EX.Rs`
- 1b: `EX/MEM.Rd == ID/EX.Rt`
- 2a: `MEM/WB.Rd == ID/EX.Rs`
- 2b: `MEM/WB.Rd == ID/EX.Rt`

단순히 번호만 같다고 forwarding하면 안 된다.

추가 조건:

- 생산 instruction이 실제 register write를 해야 함
- destination이 `$zero`가 아니어야 함

## 3.3 Forwarding control

| Mux value | Source |
|---|---|
| `00` | ID/EX의 register file 값 |
| `10` | EX/MEM의 직전 ALU result |
| `01` | MEM/WB의 write-back 값 |

### EX hazard

첫 번째 operand:

```text
if EX/MEM.RegWrite
and EX/MEM.RegisterRd != 0
and EX/MEM.RegisterRd == ID/EX.RegisterRs
then ForwardA = 10
```

두 번째 operand:

```text
if EX/MEM.RegWrite
and EX/MEM.RegisterRd != 0
and EX/MEM.RegisterRd == ID/EX.RegisterRt
then ForwardB = 10
```

### MEM hazard

첫 번째 operand:

```text
if MEM/WB.RegWrite
and MEM/WB.RegisterRd != 0
and MEM/WB.RegisterRd == ID/EX.RegisterRs
and no newer EX/MEM match
then ForwardA = 01
```

두 번째 operand도 `Rs` 대신 `Rt`로 같은 방식이다.

## 3.4 Double data hazard

```asm
add $1, $1, $2
add $1, $1, $3
add $1, $1, $4
```

세 번째 instruction이 EX에 있을 때:

- 첫 번째 add 결과는 MEM/WB에 있을 수 있음
- 두 번째 add 결과는 EX/MEM에 있음

둘 다 `$1`을 destination으로 가지지만 가장 최신값은 두 번째 add다.

우선순위:

`EX/MEM forwarding > MEM/WB forwarding`

즉 MEM hazard는 같은 operand에 EX hazard가 없을 때만 선택한다.

## 3.5 Forwarding 판정 문제 풀이 순서

1. cycle별로 각 instruction이 어느 stage에 있는지 적는다.
2. EX stage instruction의 `Rs`, `Rt`를 찾는다.
3. EX/MEM의 destination과 비교한다.
4. RegWrite와 destination `!= 0`을 검사한다.
5. match하면 `10`.
6. EX/MEM match가 없을 때 MEM/WB와 비교한다.
7. match하면 `01`, 없으면 `00`.

주의:

- store의 write data도 forwarding이 필요할 수 있다.
- instruction format에 따라 실제 destination field가 `rd`가 아니라 `rt`일 수 있다. pipeline register의 `RegisterRd`라는 표기는 최종 destination 번호를 의미하는 식으로 사용된다.

## 3.6 Load-use hazard

```asm
lw  $2, 20($1)
and $4, $2, $5
```

load data는 MEM stage가 끝나야 나오지만, 바로 다음 instruction은 같은 cycle의 EX 시작에서 operand가 필요하다.

따라서 시간을 거꾸로 forwarding할 수 없고 1 cycle stall이 필요하다.

검출 조건:

```text
ID/EX.MemRead
and (
  ID/EX.RegisterRt == IF/ID.RegisterRs
  or
  ID/EX.RegisterRt == IF/ID.RegisterRt
)
```

여기서 load의 destination은 `rt`다.

주의:

- 다음 instruction이 `rt`를 실제 source로 사용하지 않는 형식이면 불필요한 stall을 피해야 한다.
- 강의의 단순 hazard detector는 기본 조건을 제시한 것이다.

## 3.7 Stall 구현

load-use hazard 검출 시:

1. PC write를 막는다.
2. IF/ID pipeline register write를 막는다.
3. ID/EX로 들어갈 control signal을 모두 0으로 만든다.
4. EX에 bubble 또는 nop이 들어간다.
5. consumer instruction은 ID에서 한 cycle 더 머문다.
6. 다음 cycle에 load data를 MEM/WB에서 EX로 forwarding한다.

핵심:

- instruction 자체를 삭제하는 것이 아니다.
- PC와 IF/ID를 freeze한다.
- control 0인 bubble만 앞으로 보낸다.

## 3.8 Pipeline cycle 계산

이상적인 5-stage pipeline에서 `N`개 instruction의 완료 cycle:

`Total cycles = N + 4`

stall과 flush가 있으면:

`Total cycles = N + 4 + stall cycles + flush penalty cycles`

평균 CPI:

`CPI = total cycles / retired instruction count`

긴 program에서 pipeline fill/drain을 무시하면:

`CPI ≈ 1 + stall cycles per instruction`

주의:

- bubble은 retired instruction 수에 포함하지 않는다.
- branch 때문에 잘못 fetch되어 flush된 instruction도 retired instruction 수에 포함하지 않는다.

## 3.9 Stall과 scheduling

compiler는 independent instruction을 load와 consumer 사이에 이동해 stall을 숨길 수 있다.

조건:

- data dependency를 깨면 안 됨
- memory alias 관계를 바꾸면 안 됨
- exception과 observable behavior를 보존해야 함

## 3.10 Branch hazard

branch outcome을 늦게 결정할수록 이미 fetch된 잘못된 instruction 수가 많아진다.

MEM에서 branch를 결정하면 다수 instruction을 flush해야 한다.

delay를 줄이는 방법:

- branch target adder를 ID에 배치
- register comparator를 ID에 배치
- branch outcome을 ID에서 결정

대가:

- ID stage가 길어질 수 있음
- branch operand forwarding과 hazard detection이 추가로 필요

## 3.11 Branch target 계산

MIPS PC-relative branch:

`Branch target = PC + 4 + (sign-extended immediate << 2)`

강의 예:

- branch instruction address: 40
- immediate: 7
- `PC + 4 = 44`
- `7 << 2 = 28`
- target: `44 + 28 = 72`

주의:

- immediate는 instruction 수 단위이고 실제 byte offset을 만들기 위해 2 bit left shift한다.
- 기준은 현재 PC가 아니라 `PC + 4`다.
- 음수 immediate는 sign extension 후 shift한다.

## 3.12 Branch operand data hazard

강의의 ID-stage branch comparison 기준:

### 2번째 또는 3번째 앞 ALU instruction 결과

- forwarding으로 해결 가능
- 별도 stall이 필요하지 않을 수 있음

### 바로 앞 ALU instruction 결과

- branch가 ID에서 값을 필요로 하는 시점에 결과가 아직 충분히 앞에 나오지 않음
- 1 stall 필요

### 2번째 앞 load 결과

- 1 stall 필요

### 바로 앞 load 결과

- 2 stalls 필요

시험에서는 producer 종류와 branch까지의 거리를 함께 본다.

## 3.13 Dynamic branch prediction

Branch History Table 또는 Branch Prediction Buffer:

- branch PC의 일부 bit로 index
- 최근 taken/not-taken 결과 저장
- 예측에 따라 fall-through 또는 target에서 fetch
- 틀리면 pipeline flush 후 state 갱신

### 1-bit predictor

- 직전 결과를 다음 결과로 예측
- loop branch에서 마지막 iteration에 taken에서 not taken으로 바뀌며 1번 miss
- 다음에 같은 loop에 들어오면 첫 iteration은 taken인데 state가 not taken이라 다시 miss
- 반복적으로 실행되는 inner loop는 두 번 miss하기 쉬움

loop branch가 한 번의 loop 실행에서 `N`번 평가되고 같은 loop가 반복 호출되는 steady state라면:

- 1-bit predictor: 대략 호출당 2회 misprediction
- misprediction rate: 약 `2/N`

첫 실행의 초기 state에 따라 첫 호출은 달라질 수 있다.

### 2-bit saturating counter

일반적인 네 state:

- Strongly Not Taken
- Weakly Not Taken
- Weakly Taken
- Strongly Taken

taken이면 counter 증가, not taken이면 감소하며 0~3에서 saturate한다.

예측:

- 0, 1: not taken
- 2, 3: taken

방향을 바꾸려면 반대 결과가 두 번 필요하므로 loop 종료 한 번으로 다음 실행의 prediction까지 바로 뒤집히지 않는다.

loop의 대부분이 taken이고 predictor가 strongly taken으로 안정된 경우:

- 2-bit predictor: 보통 loop 종료에서 호출당 1회 misprediction
- misprediction rate: 약 `1/N`

## 3.14 Branch prediction 성능 계산

유도 공식:

`Branch stall CPI = branch frequency × misprediction rate × misprediction penalty`

`Effective CPI = base CPI + Branch stall CPI`

예:

- branch frequency = 20% = 0.2
- predictor accuracy = 90%, 따라서 misprediction rate = 0.1
- penalty = 3 cycles

`추가 CPI = 0.2 × 0.1 × 3 = 0.06`

base CPI가 1이면 effective CPI는 1.06이다.

주의:

- accuracy와 misprediction rate를 혼동하지 않는다.
- penalty가 branch 한 개당인지 잘못 예측한 branch 한 개당인지 확인한다.

## 3.15 Branch Target Buffer

prediction이 taken이어도 target address가 늦게 계산되면 penalty가 남는다.

BTB:

- PC로 검색하는 target address cache
- branch가 fetch되는 시점에 target 제공
- predictor가 taken이고 BTB hit이면 target instruction을 즉시 fetch 가능

BTB 문제에서는 두 조건을 구분한다.

- direction prediction hit
- target address BTB hit

## 3.16 Exception과 interrupt

### Exception

CPU 내부에서 발생:

- undefined opcode
- arithmetic overflow
- syscall

### Interrupt

외부 I/O controller 등에서 발생

둘 다 정상 control flow를 바꾸지만 발생 원인이 다르다.

## 3.17 MIPS exception 처리

- CP0가 관리
- EPC: exception 관련 PC 저장
- Cause: 원인 저장
- handler address로 jump

강의 단순화:

- Cause 0: undefined opcode
- Cause 1: overflow
- handler: `0x80000180`

Vectored interrupt 방식은 cause별로 서로 다른 handler entry를 사용한다.

슬라이드 예:

- undefined opcode: `0xC0000000`
- overflow: `0xC0000020`
- 다음 vector: `0xC0000040`

각 vector entry는 직접 처리하거나 실제 긴 handler로 jump할 수 있다.

handler의 핵심 동작:

1. Cause를 읽어 알맞은 handler를 선택한다.
2. restartable이면 원인을 수정하고 EPC로 복귀한다.
3. restart 불가능하면 program을 종료하고 EPC와 Cause를 이용해 error를 보고한다.

## 3.18 Pipeline exception

EX stage의 add가 overflow한 경우:

1. faulting add가 register를 write하지 못하게 control을 막는다.
2. add보다 앞선 instruction은 정상 완료시킨다.
3. add와 그 이후 instruction은 flush한다.
4. Cause와 EPC를 기록한다.
5. handler로 control을 이동한다.

이는 branch misprediction flush와 유사한 hardware를 재사용할 수 있다.

## 3.19 EPC 계산

강의 구현은 실제로 `PC + 4`를 EPC에 저장하고 handler가 보정한다고 설명한다.

faulting instruction address가 `0x4C`이면:

- 저장값: `0x50`
- faulting instruction으로 돌아가려면 `EPC - 4 = 0x4C`

주의:

- 실제 ISA 또는 문제에서 EPC가 faulting PC를 직접 저장한다고 정의할 수도 있다.
- 시험에서는 강의 datapath의 정의를 따른다.

## 3.20 Restartable exception

- faulting instruction의 architectural effect가 commit되지 않아야 한다.
- handler가 문제를 해결한다.
- EPC를 이용해 instruction을 다시 fetch하고 처음부터 실행한다.

## 3.21 Precise와 imprecise exception

### Precise exception

- faulting instruction보다 앞선 instruction은 모두 완료
- faulting instruction과 이후 instruction은 완료되지 않음
- architectural state가 명확한 program order 경계를 가짐

동시에 여러 exception이 있으면 가장 오래된 instruction의 exception을 먼저 처리한다.

### Imprecise exception

- 어떤 instruction이 완료되었는지 handler가 분석해야 함
- hardware는 단순해질 수 있지만 software가 매우 복잡
- multiple-issue out-of-order processor에서는 실용적이지 않음

## 3.22 ILP

Instruction-Level Parallelism은 여러 instruction을 겹쳐 실행하는 정도다.

향상 방법:

- deeper pipeline
- multiple issue

### IPC와 CPI

`IPC = completed instructions / cycles`

`CPI = cycles / completed instructions`

이상적인 steady state에서:

`IPC = 1 / CPI`

예:

- 4-way issue peak IPC = 4
- peak CPI = `1/4 = 0.25`

### BIPS

`Instructions/s = clock frequency × IPC`

4 GHz, IPC 4:

`4 × 10^9 cycles/s × 4 instructions/cycle = 16 × 10^9 instructions/s`

즉 16 BIPS다.

## 3.23 Static과 dynamic multiple issue

### Static multiple issue

- compiler가 함께 issue할 instruction을 packet/slot으로 구성
- compiler가 dependency와 hazard를 피함
- hardware가 비교적 단순

### Dynamic multiple issue

- CPU가 runtime에 instruction stream을 검사
- issue 가능한 instruction을 선택
- runtime dependency와 latency 변화에 대응
- hardware가 복잡하고 power cost가 큼

---

# Chapter 4.5. SIMD 전체

## 4.1 SIMD 정의

SIMD:

- Single Instruction
- Multiple Data
- 같은 operation을 여러 data element에 동시에 적용

SISD:

- 한 instruction이 한 data element 처리

예:

- scalar로 8쌍을 더하면 add instruction 8개
- 8 lane SIMD이면 vector add 1개
- 이상적 instruction count reduction은 8배

주의:

- instruction 수가 8분의 1이라고 execution time이 무조건 8분의 1은 아니다.
- load/store, loop overhead, alignment, tail 처리, dependency가 남는다.

SIMD가 특히 유용한 원래 배경:

- audio data에는 16-bit element가 흔함
- graphics/video에는 8-bit element가 흔함
- word보다 작은 data를 한 register에 여러 개 pack해 data-level parallelism을 얻음

대표 ISA 발전:

- Intel MMX 64-bit
- SSE/SSE2 계열 128-bit
- AVX 256-bit
- AVX-512 512-bit
- ARM NEON 128-bit
- ARM SVE/SVE2는 최대 2048-bit까지 scalable

## 4.2 Flynn taxonomy

- SISD: scalar CPU
- SIMD: vector/media extension
- MISD: 드물며 fault-tolerant system 예
- MIMD: multicore와 multiprocessor

GPU는 programming model 관점에서 많은 thread를 실행하는 MIMD/SPMD 특성도 있고, warp 내부 실행은 SIMD/SIMT 성격을 가진다.

## 4.3 Lane 수 계산

`lane 수 = SIMD register width / element width`

### 128-bit

- INT8: `128/8 = 16`
- INT16: `128/16 = 8`
- FP32: `128/32 = 4`
- FP64: `128/64 = 2`

### 256-bit

- INT8: 32
- INT16: 16
- FP32: 8
- FP64: 4

### 512-bit

- INT8: 64
- INT16: 32
- FP32: 16
- FP64: 8

시험 풀이:

1. register width와 element width의 단위를 bit로 통일한다.
2. 나눈 값이 한 instruction이 동시에 처리하는 element 수다.
3. FMA라면 lane당 multiplication과 addition을 각각 세어 FLOPs를 계산한다.

SSE2 슬라이드의 핵심은 128-bit register에서 FP64 2개 또는 FP32 4개를 동시에 처리한다는 것이다. 슬라이드의 `4 × 32-bit double precision` 문구는 `4 × 32-bit single precision`의 오타로 해석해야 한다.

슬라이드 설명상 SSE2는 128-bit register 4개를 추가하고 AMD64/EM64T에서는 8개로 확장된다.

## 4.4 Vector instruction throughput

이상적:

`vector results/instruction = lane 수`

`vector arithmetic operations/instruction = lane 수 × lane당 operation 수`

FP32 AVX-512 add:

- 16 lanes
- 16 additions

FP32 AVX-512 FMA:

- 16 lanes
- lane당 multiply 1 + add 1
- 32 FLOPs/instruction

FP64 AVX-512 FMA:

- 8 lanes
- 16 FLOPs/instruction

## 4.5 ARM UADD8

`UADD8 R0, R1, R2`:

- 32-bit register를 8-bit lane 4개로 나눔
- 각 lane을 unsigned byte로 해석
- 네 쌍의 addition을 병렬 수행
- lane 사이 carry는 전달되지 않는 packed arithmetic로 이해한다.

## 4.6 SIMD 사용 방법

### Assembly

- instruction을 직접 선택
- 정확한 control
- architecture 종속성과 유지보수 비용

### Inline assembly

- C/C++ 코드 안에 assembly 삽입
- compiler가 stack과 주변 코드를 관리
- compiler/architecture 종속
- clobber와 operand constraint를 정확히 써야 함

### Automatic vectorization

- compiler가 scalar loop를 vector instruction으로 변환
- portability가 좋음
- alias와 dependency를 compiler가 증명해야 함

`restrict`는 pointer가 같은 memory를 가리키지 않는다는 정보를 주어 vectorization을 돕는다.

슬라이드 예의 `n & ~3`은 `n`을 4의 배수로 내림해 vector loop 범위를 만든다. 남은 `n mod 4`개는 별도 tail 처리가 필요하다.

### Optimized library

- OpenMAX 같은 검증된 SIMD library 사용
- 가장 간단하게 높은 성능 확보 가능
- OpenMAX는 Khronos Group의 royalty-free cross-platform API standard
- ARM은 ARMv7 NEON에 최적화한 OpenMAX DL implementation을 제공

### Intrinsic

- C 함수처럼 보이지만 특정 vector instruction으로 compile
- type checking과 register allocation을 compiler가 담당
- assembly보다 읽기 쉽지만 ISA 종속적

## 4.7 Dot product 계산

OpenMAX 예:

`dot = Σ source1[i] × source2[i]`

슬라이드 배열:

`source1 = [42, 23, 983, 7456, 124, 11111, 4554, 10002]`

`source2 = [242, 423, 9832, 746, 1124, 1411, 2254, 1298]`

각 곱:

`[10164, 9729, 9664856, 5562176, 139376, 15677621, 10264716, 12982596]`

합:

`dot = 54,311,234`

길이 8:

- 8 multiplications
- 7 additions 또는 accumulator 기준 8 MACs

## 4.8 AoS와 SoA

### AoS

```text
r0,g0,b0,a0, r1,g1,b1,a1, ...
```

같은 field가 떨어져 있어 R 값만 vector로 읽으려면 gather 또는 shuffle이 필요하다.

### SoA

```text
r0,r1,r2,..., g0,g1,g2,...
```

같은 field가 연속되어 contiguous vector load가 가능하다.

계산형 관점:

- FP32 16개를 AVX-512로 읽으면 64 bytes
- cache line이 64 bytes라면 aligned load 한 번으로 정확히 한 cache line을 읽을 수 있다.

## 4.9 Alignment

슬라이드 기준:

- SSE 128-bit = 16-byte alignment
- AVX 256-bit = 32-byte alignment
- AVX-512 512-bit = 64-byte alignment가 자연스러운 기준
- C/C++에서는 `__attribute__((aligned(32)))` 또는 `alignas(32)`로 alignment를 요구할 수 있음

일반 공식:

`alignment bytes = register width bits / 8`

주의:

- 현대 CPU는 unaligned load를 지원하지만 cache-line 또는 page boundary를 가로지르면 penalty가 커질 수 있다.
- 시험에서는 슬라이드에서 지정한 alignment 조건을 우선한다.

## 4.10 Tail 처리

element 수 `N`이 lane 수 `L`의 배수가 아니면:

`vector iteration 수 = floor(N/L)`

`tail element 수 = N mod L`

tail은 scalar loop 또는 mask instruction으로 처리한다.

## 4.11 DGEMM 기본 연산

`C[i,j] += A[i,k] × B[k,j]`

정사각 `N × N`:

- output element: `N²`
- 각 output에 `N` MACs
- 총 MACs: `N³`
- 약 `2N³ FLOPs`

## 4.12 AVX-512 DGEMM

슬라이드 코드는 한 번에 double 8개를 처리한다.

- `__m512d`: 512-bit
- FP64 lane 수: `512/64 = 8`
- C element 8개 load
- B scalar 하나를 8 lane에 broadcast
- A element 8개와 vector FMA
- C element 8개 store

한 vector FMA:

- 8 multiplications
- 8 additions
- 16 FLOPs

이상적 scalar instruction count 감소는 lane 수에 가깝지만 memory와 loop overhead가 남는다.

## 4.13 CPU SIMD 처리량

슬라이드 예:

`24 cores × 16 FP32 lanes = 384 FP32 lanes/cycle`

GPU:

`46 SM × 128 FP32 units = 5,888 FP32 units`

unit 수 비:

`5,888 / 384 ≈ 15.33`

슬라이드는 약 15배로 설명한다.

주의:

- lane 수 또는 unit 수 비교일 뿐 실제 application speedup이 아니다.
- CPU와 GPU clock, utilization, memory, instruction issue rate가 다르다.

일반 peak 식:

`Peak OPS/s = cores × lanes/core × operations/lane/instruction × instructions/cycle × clock`

## 4.14 Tensor core 계산

슬라이드 예:

`184 Tensor Cores × 256 MAC operations = 47,104 MAC operations/cycle`

Tensor Core는 NVIDIA Volta 세대인 2017년에 도입되었고, 슬라이드는 4×4 matrix multiply-accumulate를 한 cycle에 수행하는 2D SIMD/MAC array로 설명한다.

MAC를 2 arithmetic operations로 세면:

`47,104 × 2 = 94,208 operations/cycle`

주의:

- tensor core generation과 data type에 따라 tile size와 throughput이 다르다.
- 슬라이드가 제시한 256 MAC/core/cycle 가정을 그대로 사용한다.

## 4.15 Memory bandwidth 비교

슬라이드:

- GPU HBM: 최대 약 3 TB/s
- CPU DDR5: 약 100 GB/s

동일 단위로 환산:

`3 TB/s ≈ 3,000 GB/s`

비율:

`3,000 / 100 = 30`

약 30배다.

주의:

- decimal TB/GB 기준의 단순 계산이다.
- theoretical peak와 sustained bandwidth는 다르다.

## 4.16 CPU SIMD와 GPU의 역할

AVX-512의 장점:

- 결과가 몇 cycle 안에 나와 low latency에 유리
- scalar와 SIMD code를 한 program에서 유연하게 혼합
- CPU cache의 data를 사용해 별도 accelerator transfer가 없음

한계:

- 512-bit, FP32 16 lanes 정도의 width ceiling
- 넓은 SIMD 사용 시 높은 power density로 clock이 낮아질 수 있음
- core 수가 GPU의 수천 arithmetic unit보다 적음

GPU의 장점:

- branch prediction과 복잡한 out-of-order logic보다 throughput에 자원을 집중
- 수천 개의 단순 core
- HBM의 매우 높은 bandwidth

결론은 대체 관계가 아니라 보완 관계다. CPU SIMD는 low-latency mixed workload, GPU/Tensor Core는 high-throughput parallel workload에 적합하다.

---

# Chapter 5. Memory Hierarchy 전체

## 5.1 Locality

### Temporal locality

최근 사용한 item을 곧 다시 사용할 가능성:

- loop instruction
- induction variable
- accumulator

### Spatial locality

최근 주소 근처를 곧 사용할 가능성:

- sequential instruction
- array traversal

memory hierarchy는 locality를 이용해 작은 fast memory에 자주 쓰는 block을 둔다.

## 5.2 기본 용어와 비율

`Hit ratio = hits / total accesses`

`Miss ratio = misses / total accesses`

`Miss ratio = 1 - Hit ratio`

`Miss penalty`: lower level에서 block을 가져와 access를 완료하는 추가 시간

`Block 또는 line`: 계층 사이에서 이동하는 단위

## 5.3 Memory technology

슬라이드의 대략적 비교:

- SRAM: 0.5~2.5 ns, 매우 비쌈
- DRAM: 50~70 ns, SRAM보다 느리고 저렴
- disk: 5~20 ms, 매우 느리지만 저렴하고 큼

단위:

- `1 ms = 10^-3 s`
- `1 us = 10^-6 s`
- `1 ns = 10^-9 s`
- `1 ms = 10^6 ns`

예:

DRAM 50 ns와 disk 5 ms의 latency 비:

`5 ms / 50 ns = 5×10^-3 / 50×10^-9 = 100,000`

disk가 약 10만 배 느리다.

ideal memory는 SRAM의 access time과 disk의 capacity/cost를 동시에 가지는 것이지만 실제 한 technology로 달성할 수 없다. 따라서 SRAM cache, DRAM main memory, flash/disk를 계층화한다. DRAM generation 표의 핵심 추세는 capacity 증가와 장기적인 cost/GB 감소다.

## 5.4 DRAM

- capacitor charge로 bit 저장
- refresh 필요
- row 단위 access
- row buffer는 같은 row의 여러 word access를 빠르게 함
- burst mode는 연속 word의 address overhead를 줄임
- banking은 여러 bank access를 겹쳐 bandwidth를 높임
- DDR은 rising/falling edge 모두에서 transfer
- QDR은 input과 output에 각각 DDR 경로를 두어 양방향 bandwidth를 높임

일반 bus peak bandwidth:

`Bandwidth = transfer rate × bus width(bits) / 8`

DDR에서 clock당 2 transfer가 명시되면:

`Bandwidth = clock × 2 × bus width / 8`

## 5.5 Memory bandwidth 계산

`Bandwidth = transferred bytes / elapsed cycles`

### 4-word-wide memory

- address: 1 cycle
- DRAM: 15 cycles
- transfer: 1 cycle
- 총: `1 + 15 + 1 = 17 cycles`
- 4 words = 16 bytes

`Bandwidth = 16 / 17 ≈ 0.94 B/cycle`

### 4-bank interleaved memory

- address: 1
- first access latency: 15
- word transfer: `4 × 1`
- 총: `1 + 15 + 4 = 20 cycles`

`Bandwidth = 16 / 20 = 0.8 B/cycle`

주의:

- wide memory와 banked memory의 bus/transfer 가정이 다르다.
- 문제에서 address, access, transfer가 overlap되는지 확인한다.

## 5.6 Flash

- nonvolatile semiconductor storage
- disk보다 100~1000배 빠름
- NOR: random access, embedded instruction memory
- NAND: 높은 density, block access, SSD/USB
- write endurance 제한
- wear leveling으로 write를 여러 block에 분산

## 5.7 Disk access time

`Average disk access time = queue + seek + rotational latency + transfer + controller`

sector에는 sector ID, 512 B 또는 4096 B data, ECC, synchronization field와 gap이 포함된다.

### Rotational latency

`revolutions per second = RPM / 60`

`one revolution time = 1 / (RPM/60)`

평균 rotational latency는 half rotation:

`Average rotational latency = 0.5 / (RPM/60)`

15,000 RPM:

- `15,000/60 = 250 revolutions/s`
- 한 바퀴: `1/250 s = 4 ms`
- 평균 half rotation: `2 ms`

### Transfer time

`Transfer time = data size / transfer rate`

512 B, 100 MB/s:

`512 / 100,000,000 s = 5.12 us = 0.00512 ms`

### 전체 예

`4 + 2 + 0.005 + 0.2 ≈ 6.205 ms`

슬라이드는 6.2 ms로 round한다.

실제 seek가 1 ms이면:

`1 + 2 + 0.005 + 0.2 ≈ 3.205 ms`

약 3.2 ms다.

disk 성능에서 추가로 알아둘 핵심:

- 제조사 average seek는 가능한 seek 전체의 평균이고 locality와 OS scheduling으로 실제 평균이 더 작을 수 있다.
- controller는 physical sector를 배치하고 host에는 logical sector interface를 제공한다.
- SCSI, ATA, SATA가 대표 interface다.
- drive cache가 sector를 prefetch하면 seek와 rotation을 일부 피할 수 있다.

## 5.8 Direct-mapped cache

`Memory block address = floor(byte address / block size bytes)`

`Cache index = block address mod number of cache blocks`

direct mapped에서는 한 memory block이 들어갈 cache entry가 정확히 하나다.

hit 조건:

- valid bit = 1
- stored tag = address tag

둘 중 하나라도 아니면 miss다.

cache가 초기화될 때 valid bit는 0이므로 첫 access는 compulsory miss다.

## 5.9 Cache address field 계산

주어진 값:

- address width `m` bits
- cache data capacity `C` bytes
- block size `B` bytes
- associativity `A`

공식:

`number of blocks = C / B`

`number of sets = C / (B × A)`

`block offset bits = log2(B)`

`index bits = log2(number of sets)`

`tag bits = m - index bits - offset bits`

주의:

- byte-addressed system에서 block offset은 byte 기준이다.
- word offset과 byte offset을 따로 나누어 표시할 수 있지만 합은 `log2(block bytes)`다.
- fully associative는 set가 1개이므로 index 0 bits다.

PDF 26쪽의 1024-entry, 1-word/block direct-mapped 예:

- byte offset 2 bits
- index 10 bits
- tag `32-10-2 = 20 bits`

## 5.10 Address mapping 예

64 blocks, 16 bytes/block, direct mapped, address 1200:

`block address = floor(1200/16) = 75`

`index = 75 mod 64 = 11`

32-bit byte address:

- offset: `log2(16) = 4 bits`
- index: `log2(64) = 6 bits`
- tag: `32 - 4 - 6 = 22 bits`

따라서:

`tag 22 | index 6 | offset 4`

## 5.11 Cache trace 문제

access마다:

1. block address 계산
2. set/index 계산
3. 현재 valid와 tag 비교
4. hit/miss 기록
5. miss면 replacement policy에 따라 entry 갱신
6. write면 write policy와 dirty bit 처리

direct-mapped 8-entry, 1 word/block에서 word address를 사용하는 경우:

`index = word address mod 8`

tag는 index로 사용하지 않은 상위 bit다.

강의 access sequence:

`22, 26, 22, 26, 16, 3, 16, 18`

- 22: miss, index 6
- 26: miss, index 2
- 반복한 22와 26: hit
- 16: miss, index 0
- 3: miss, index 3
- 반복한 16: hit
- 18: miss, index 2이며 기존 26을 교체

주의:

- 주소가 byte address인지 word address인지 먼저 확인한다.
- slide 102의 함정처럼 byte 36과 word 36은 전혀 다른 mapping이다.

## 5.12 Block size trade-off

block 증가:

- spatial locality로 compulsory miss 감소 가능
- 같은 cache capacity에서 block 수 감소
- capacity/conflict pressure 증가 가능
- pollution 증가 가능
- transfer data 증가로 miss penalty 증가

따라서 block이 클수록 무조건 좋은 것은 아니다.

miss block 전체가 오기 전에 CPU를 재개하는 기법:

- critical-word-first: 요청한 word부터 전송
- early restart: 요청 word가 오면 나머지 block 전송 중에도 CPU 재개

## 5.13 Cache miss 처리

- pipeline stall
- lower level에서 block fetch
- instruction miss면 instruction fetch restart
- data miss면 load/store access 완료 후 재개

## 5.14 Write-through

write hit:

- cache update
- lower memory도 update

memory write가 100 cycles, store frequency 10%, base CPI 1:

`Effective CPI = 1 + 0.1 × 100 = 11`

write buffer가 있으면 CPU는 buffer가 차지 않은 한 계속 실행할 수 있다.

주의:

- write buffer가 full이면 stall
- write buffer가 모든 latency를 제거하는 것은 아니다.

## 5.15 Write-back

write hit:

- cache만 update
- dirty bit = 1

dirty victim replacement:

1. victim block을 lower memory에 write
2. 새 block을 read

장점:

- 같은 block에 여러 번 write해도 lower level write를 한 번으로 줄일 수 있음

대가:

- dirty bit 필요
- miss 처리 FSM 복잡

## 5.16 Write miss policy

- write allocate: block을 가져와 cache에서 write
- no-write-allocate/write around: lower level에만 write

흔한 조합:

- write-through + no-write-allocate
- write-back + write-allocate

문제에서는 hit/miss 각각의 동작을 별도로 추적한다.

## 5.17 Main memory organization

1-word-wide memory에서 4-word block:

- address: 1 cycle
- 각 word DRAM: `4 × 15`
- 각 word transfer: `4 × 1`

`Miss penalty = 1 + 4×15 + 4×1 = 65 cycles`

`Bandwidth = 16 bytes / 65 cycles ≈ 0.246 B/cycle`

약 0.25 B/cycle다.

## 5.18 Memory stall cycles

일반식:

`Memory stall cycles = memory accesses × miss rate × miss penalty`

instruction당:

`Stall CPI = misses per instruction × miss penalty`

split cache:

`I-stall CPI = I-cache miss rate × I-miss penalty`

`D-stall CPI = data accesses/instruction × D-cache miss rate × D-miss penalty`

`Actual CPI = Base CPI + I-stall CPI + D-stall CPI`

전체 cache access 기준 가중 miss rate:

`Overall miss rate = (I misses + D misses) / (I accesses + D accesses)`

instruction당 data access가 `f_D`이고 I-cache/D-cache miss rate가 각각 `m_I`, `m_D`이면:

`Overall miss rate = (m_I + f_D m_D) / (1 + f_D)`

Intrinsity FastMATH 예:

- 12-stage pipeline
- cycle마다 instruction access, load/store이면 data access 추가
- split I/D cache, 각각 16 KiB
- 256 blocks, block당 16 words = 64 bytes
- I miss 0.4%, D miss 11.4%, weighted average 약 3.2%

그림의 direct-mapped cache address:

- 32-bit byte address
- 64-byte block -> offset 6 bits
- 256 entries -> index 8 bits
- tag 18 bits
- line data 512 bits

valid만 포함한 최소 storage:

`256 × (512 + 18 + 1) = 135,936 bits = 16,992 bytes`

## 5.19 Cache performance 강의 예

주어진 값:

- I miss rate = 2%
- D miss rate = 4%
- penalty = 100 cycles
- base CPI = 2
- load/store frequency = 36%

I-cache:

`0.02 × 100 = 2 cycles/instruction`

D-cache:

`0.36 × 0.04 × 100 = 1.44 cycles/instruction`

Actual CPI:

`2 + 2 + 1.44 = 5.44`

ideal cache CPU와의 성능비:

`5.44 / 2 = 2.72`

ideal CPU가 2.72배 빠르다.

주의:

- speedup은 느린 시간/빠른 시간이다.
- CPI는 execution time에 비례하므로 같은 instruction count와 clock이면 CPI 비로 계산 가능하다.

## 5.20 AMAT

`AMAT = Hit time + Miss rate × Miss penalty`

예:

- hit time = 1 ns
- miss rate = 5%
- miss penalty = 20 ns 또는 20 cycles at 1 ns

`AMAT = 1 + 0.05 × 20 = 2 ns`

주의:

- miss penalty가 hit time을 포함하는지 추가 penalty인지 문제 정의를 확인한다.
- 일반 교재 공식은 추가 penalty로 사용한다.

multilevel recursive AMAT:

`AMAT = T_L1 + MR_L1 × (T_L2 + MR_L2 × (T_L3 + ...))`

각 `MR`은 해당 level에 들어온 access를 기준으로 한 local miss rate다.

## 5.21 Set associativity

`set index = block address mod number of sets`

`number of sets = total blocks / ways`

한 set 안의 모든 way tag를 동시에 비교한다.

- direct mapped: 1 comparator
- A-way: A comparators
- fully associative: 모든 entry와 비교

슬라이드의 64 KiB D-cache, 16-word block simulation:

- 1-way: 10.3%
- 2-way: 8.6%
- 4-way: 8.3%
- 8-way: 8.1%

associativity 증가로 conflict miss가 줄지만 diminishing return이 나타난다.

PDF 46쪽의 4-way 예가 총 1024 block, 1 word/block, 32-bit byte address라면:

- sets `=1024/4=256`
- index 8 bits
- byte offset 2 bits
- tag `=32-8-2=22 bits`
- hit 판정을 위해 선택된 set의 tag 4개를 병렬 비교

## 5.22 Associativity trace

강의 access sequence:

`0, 8, 0, 6, 8`

4-block direct mapped:

- 0과 8이 같은 index에서 충돌
- 모든 access가 miss

2-way:

- 세 번째 0은 hit
- replacement 후 마지막 8은 miss

fully associative:

- 세 번째 0 hit
- 마지막 8도 hit

계산 시 replacement policy가 명시되지 않으면 강의 예에서 사용한 ordering/LRU 가정을 확인한다.

## 5.23 Replacement

우선순위:

1. invalid entry가 있으면 사용
2. 없으면 LRU 또는 주어진 policy

LRU:

- 가장 오래 사용하지 않은 entry replacement
- associativity가 높을수록 exact LRU hardware가 복잡

Random:

- 구현이 단순
- 높은 associativity에서 LRU와 비슷할 수 있음

## 5.24 Multilevel cache

### Local miss rate

`Local miss rate at Li = Li misses / Li accesses`

### Global miss rate

`Global miss rate at Li = Li misses / CPU memory accesses`

L2 global miss:

`L1 local miss rate × L2 local miss rate`

## 5.25 Multilevel cache 강의 예

주어진 값:

- base CPI = 1
- clock = 4 GHz
- cycle time = `1/4GHz = 0.25 ns`
- L1 miss/instruction = 2%
- memory = 100 ns

memory penalty:

`100 / 0.25 = 400 cycles`

L1만 있을 때:

`CPI = 1 + 0.02 × 400 = 9`

L2 추가:

- L2 access = 5 ns = 20 cycles
- global memory miss = 0.5%

`CPI = 1 + 0.02 × 20 + 0.005 × 400`

`= 1 + 0.4 + 2 = 3.4`

성능 향상:

`9 / 3.4 ≈ 2.65`

약 2.6배다.

추가 계산:

`L2 local miss rate = 0.005 / 0.02 = 0.25 = 25%`

주의:

- 슬라이드 p.50의 "Extra penalty = 500 cycles" 문구는 `100 ns / 0.25 ns = 400 cycles` 및 바로 아래 CPI 식과 일치하지 않는다.
- 계산에서는 슬라이드의 CPI 식처럼 400 cycles를 사용해야 일관된다.

설계 우선순위:

- L1: CPU clock에 직접 영향을 주므로 최소 hit time
- L2/L3: main memory까지 가는 global miss를 줄이는 낮은 miss rate
- 일반적으로 L1은 더 작고 block도 L2보다 작게 설계될 수 있음

## 5.26 Out-of-order CPU와 cache miss

- dependent instruction은 기다림
- independent instruction은 계속 실행 가능
- pending store는 load/store unit에 남고 dependent instruction은 reservation station에서 대기
- 단순 AMAT만으로 actual performance를 정확히 예측하기 어려움
- non-blocking cache는 hit-under-miss, miss-under-miss를 지원

## 5.27 Blocking

목적:

- block을 cache에 둔 동안 최대한 재사용
- 큰 stride를 줄임
- capacity miss와 bandwidth demand 감소

정사각 matrix multiply:

`총 MACs = N³`

blocking은 arithmetic count를 크게 바꾸지 않지만 memory access locality를 개선한다.

tile size `B`에서 double matrix tile 하나의 data:

`B² × 8 bytes`

A, B, C tile 세 개가 cache에 들어가야 한다고 단순 가정하면:

`Working set ≈ 3B² × 8 bytes`

cache capacity에 맞게 `B`를 정할 수 있다.

세 tile이 모두 cache에 들어간다는 단순 조건:

`3B² × element_bytes <= usable cache bytes`

따라서 가능한 tile 한 변의 상한:

`B <= floor(sqrt(usable cache bytes / (3 × element_bytes)))`

슬라이드 DGEMM 그래프의 GFLOPS:

| 구현 | 32² | 160² | 480² | 960² |
|---|---:|---:|---:|---:|
| Unoptimized | 1.7 | 1.5 | 1.3 | 0.8 |
| AVX | 6.4 | 3.5 | 2.3 | 2.5 |
| AVX + unroll | 14.6 | 6.6 | 4.7 | 5.1 |
| AVX + unroll + blocked | 13.6 | 12.7 | 11.7 | 12.0 |

작은 matrix에서는 unroll만으로도 빠르지만, 큰 matrix에서는 blocking이 locality를 유지해 성능 저하를 크게 줄인다. 960²에서 blocked/unoptimized 성능비는 `12.0/0.8 = 15배`다.

## 5.28 Dependability

- fault: component의 failure 원인
- fault가 항상 즉시 system service failure를 만들지는 않음
- service accomplishment: specification대로 service 제공
- service interruption: specification에서 벗어난 상태
- restoration을 거쳐 service accomplishment로 복귀

`MTBF = MTTF + MTTR`

`Availability = MTTF / (MTTF + MTTR)`

unavailability:

`1 - Availability = MTTR / (MTTF + MTTR)`

예:

- MTTF = 10,000 hours
- MTTR = 10 hours

`Availability = 10000/10010 ≈ 0.999001 = 99.9001%`

availability 향상:

- MTTF 증가
- MTTR 감소

## 5.29 Hamming distance

두 bit pattern에서 다른 bit 위치 수다.

- minimum distance 2: single-bit error detection
- minimum distance 3: single-error correction
- distance 4 + overall parity: SEC-DED

## 5.30 Hamming parity bit 수

`m` data bits를 SEC로 보호할 parity bits `r`:

`2^r >= m + r + 1`

이유:

- no error 상태 1개
- data와 parity를 포함한 각 bit error 위치를 구분해야 함

예: data 8 bits

- r=3: `8 < 8+3+1=12`, 부족
- r=4: `16 >= 8+4+1=13`, 가능

SEC-DED는 전체 parity bit 하나를 추가한다.

## 5.31 Hamming encoding

- bit position을 1부터 번호 지정
- 1, 2, 4, 8, ... 위치는 parity
- 나머지는 data
- parity bit `p_i`는 position 번호의 binary 표현에서 해당 bit가 1인 위치를 검사

8 data bit 예의 배치:

`p1, p2, d1, p4, d2, d3, d4, p8, d5, d6, d7, d8`

- p1: position 번호의 bit0이 1인 위치
- p2: position 번호의 bit1이 1인 위치
- p4: position 번호의 bit2가 1인 위치
- p8: position 번호의 bit3가 1인 위치

minimum Hamming distance가 `d_min`이면:

- 최대 검출 bit 수: `d_min - 1`
- 최대 교정 bit 수: `floor((d_min - 1)/2)`

## 5.32 Syndrome와 SEC-DED 판정

syndrome `H`:

- `0000`: SEC parity check상 error 위치 없음
- `1010`: binary 10, bit 10 error

overall parity `pn`과 결합:

- H even/zero, pn even: no error
- H nonzero, pn odd: correctable single-bit error
- H zero, pn odd: overall parity bit error
- H nonzero, pn even: double-bit error detected, correction 불가

ECC DRAM 예:

- 64 data bits + 8 ECC bits
- overhead: `8/64 = 12.5%`
- 전체 전송 폭 기준 ECC 비율: `8/72 ≈ 11.11%`

## 5.33 Virtual machine

- VMM이 virtual CPU, memory, I/O를 physical resource에 mapping
- guest는 user mode에서 native execution
- privileged instruction은 trap하여 VMM이 처리
- timer interrupt도 VMM이 virtualize

VMM은 guest의 virtual CPU, memory, I/O를 physical resource에 mapping한다. guest OS가 host OS와 달라도 가능하다. user/system mode와 privileged instruction trap이 virtualization의 핵심 ISA 지원이다.

virtualization은 guest 사이 isolation을 높이고 resource sharing을 돕지만 overhead가 있다. 슬라이드 예는 IBM VM/370, VMware, Microsoft Virtual PC다.

## 5.34 Virtual memory address translation

`Virtual address = VPN | page offset`

`Physical address = PPN | page offset`

page offset은 translation에서 변하지 않는다.

공식:

`page offset bits = log2(page size bytes)`

`VPN bits = virtual address bits - offset bits`

`PPN bits = physical address bits - offset bits`

## 5.35 Page table 계산

`number of virtual pages = 2^(VPN bits)`

`Page table size = number of virtual pages × PTE size`

강의 그림:

- virtual address 32 bits
- page size 4 KiB = `2^12`
- offset = 12 bits
- VPN = 20 bits
- physical address 30 bits
- PPN = `30 - 12 = 18 bits`

single-level page table entry가 4 bytes라면:

`2^20 × 4 bytes = 4 MiB`

process마다 4 MiB page table이 필요할 수 있어 multilevel page table이 사용된다.

## 5.36 Page fault

page가 physical memory에 없을 때:

1. faulting virtual address로 PTE 탐색
2. disk 위치 확인
3. victim page 선택
4. dirty victim이면 disk write
5. requested page read
6. page table 갱신
7. process runnable
8. faulting instruction restart

page fault는 millions of cycles이므로:

- fully associative placement
- LRU 근사 replacement
- write-back

이 필요하다.

PTE의 reference/use bit는 page access 시 1로 설정되고 OS가 주기적으로 0으로 지워 LRU를 근사한다. dirty bit는 page write 시 설정되며, dirty victim만 disk에 write-back한다. disk write latency 때문에 virtual memory에서 write-through는 비현실적이다.

## 5.37 TLB

TLB는 PTE cache다.

슬라이드의 typical range:

- 16~512 PTE entries
- hit time 0.5~1 cycle
- miss penalty 10~100 cycles
- miss rate 0.01%~1%

TLB hit:

- VPN tag match
- PPN을 즉시 얻음

TLB miss:

- page table에서 PTE를 가져옴
- page가 memory에 있으면 TLB refill 후 restart
- page가 없으면 page fault

TLB miss와 page fault는 다르다.

## 5.38 TLB effective time

단순 sequential model의 유도식:

- TLB access `t_T`
- memory access `t_M`
- page-table walk 추가 시간 `t_W`
- TLB hit ratio `h`

`EAT = h(t_T + t_M) + (1-h)(t_T + t_W + t_M)`

page fault가 포함되면:

`EAT_total = (1-p)EAT + p(Page fault service time)`

page fault probability `p`가 매우 작아도 service time이 매우 커서 영향이 클 수 있다.

주의:

- TLB와 cache를 병렬 lookup하는 구조에서는 단순식을 그대로 쓰지 않는다.
- 문제에서 sequential/parallel access 가정을 확인한다.

## 5.39 TLB와 cache

physical-tagged cache:

- address translation 필요
- physical tag와 cache tag 비교

virtual indexing을 병렬화하려면 cache index bit가 page offset 안에 있어야 alias 문제를 줄일 수 있다.

memory protection:

- supervisor/kernel mode와 user mode 구분
- page table과 I/O/interrupt control은 privileged state
- user mode에서 privileged instruction을 실행하면 trap
- process 사이 shared page는 허용하되 page permission으로 잘못된 access를 차단

## 5.40 3C miss

- compulsory: 첫 access
- capacity: 전체 cache가 작아 발생
- conflict: set mapping 충돌

설계 변화:

- cache size 증가 → capacity miss 감소, hit time 증가 가능
- associativity 증가 → conflict miss 감소, hit time/energy 증가 가능
- block 증가 → compulsory miss 감소 가능, miss penalty와 pollution 증가

## 5.41 Cache implementation bit 계산

강의 FSM cache:

- data capacity 16 KiB
- block 16 bytes = 128 data bits
- 1024 blocks
- address 32 bits
- direct mapped
- offset 4 bits
- index 10 bits
- tag 18 bits
- valid 1, dirty 1

line당 저장 bit:

`128 + 18 + 1 + 1 = 148 bits`

전체:

`148 × 1024 = 151,552 bits`

bytes:

`151,552 / 8 = 18,944 bytes`

약 18.5 KiB다.

즉 16 KiB data cache도 tag와 state bit를 포함하면 실제 storage는 더 크다.

## 5.42 Cache controller FSM

강의의 blocking, direct-mapped, write-back, write-allocate cache:

interface 핵심:

- CPU 쪽 address/write/read data는 32 bits
- memory 쪽 block transfer data는 128 bits
- request의 `Valid`와 completion의 `Ready` handshake 사용
- main memory access는 여러 cycle이 걸림

### Idle

- CPU request 대기

### Compare Tag

- valid와 tag 검사
- hit면 read/write 완료
- miss이며 victim clean이면 Allocate
- miss이며 victim dirty이면 Write-Back

### Write-Back

- victim old block을 memory에 기록
- memory ready까지 state 유지
- 완료 후 Allocate

### Allocate

- requested block을 memory에서 읽음
- cache data/tag/valid 갱신
- Compare Tag로 돌아가 원래 access 재시도

cycle 계산 문제에서는 memory `Ready`가 올 때까지 해당 state cycle을 더한다.

FSM에서:

- next state는 current state와 current input의 함수
- control output은 current state의 함수
- current state는 state register에 저장

## 5.43 Cache coherence

coherence가 보장해야 하는 것:

1. processor가 자신이 쓴 값을 다시 읽으면 그 값을 봄
2. 충분한 시간이 지난 뒤 다른 processor도 최신 write를 봄
3. 같은 location에 대한 write 순서를 모든 processor가 동일하게 봄

## 5.44 Invalidating snooping

write하려는 cache:

- exclusive ownership 획득
- bus에 invalidate broadcast
- 다른 cache copy invalid

다른 core의 이후 read:

- cache miss
- owner 또는 memory에서 최신 data 획득

trace 문제에서는 각 step마다:

- cache state
- cache data
- memory data
- bus transaction

을 갱신한다.

coherence protocol의 역할:

- migration: shared memory의 data를 사용하는 core의 local cache로 이동해 bandwidth 절감
- replication: read-shared data를 여러 cache에 복제해 contention 감소
- snooping: 각 cache가 shared bus transaction을 감시
- directory: cache와 memory가 block sharing state를 directory에 기록

## 5.45 Memory consistency

coherence는 한 location, consistency는 여러 memory operation의 관찰 순서를 다룬다.

강의의 단순 가정:

- write는 모든 processor가 본 뒤 complete
- processor가 write와 다른 access를 reorder하지 않음
- read는 reorder될 수 있지만 write는 reorder하지 않는 model

`P가 X write 후 Y write`하면 다른 processor가 새 Y를 봤다면 새 X도 봐야 한다.

## 5.46 Multiple issue 지원 cache

- multi-banked cache
- bank conflict가 없으면 cycle당 여러 access
- requested word first
- non-blocking cache
- hit under miss
- miss under miss
- prefetch

bank 문제의 기본:

`bank index = block 또는 word address mod number of banks`

같은 cycle access가 같은 bank로 mapping되면 conflict가 발생할 수 있다.

### 실제 processor cache/TLB 예

ARM Cortex-A53과 Intel Core i7 모두 split L1 I/D cache, unified lower-level cache를 사용한다.

- 두 processor 모두 64-byte cache block
- A53 L1: I 2-way, D 4-way, 16~64 KiB each
- Core i7 L1: I 4-way, D 8-way, 32 KiB each/core
- A53 L2: 128 KiB~2 MiB, 16-way, 약 12 cycles
- Core i7 L2: 256 KiB/core, 8-way, 약 10 cycles
- Core i7 L3: 8 MiB shared, 16-way, 약 35 cycles
- virtual address는 둘 다 48 bits
- physical address는 A53 40 bits, Core i7 44 bits
- 둘 다 instruction/data TLB를 나누고 hardware가 TLB miss를 처리
- Core i7은 L1 TLB와 512-entry L2 TLB의 2-level 구조를 사용

## 5.47 Chapter 5 함정

- byte address와 word address 구분
- cache capacity는 data만 의미하는지 total implementation bits인지 구분
- block size 단위를 word와 byte 중 무엇으로 주었는지 확인
- local miss rate와 global miss rate 구분
- AMAT와 CPU CPI 문제 구분
- TLB miss와 page fault 구분
- write-back miss에서 dirty victim write 시간을 빠뜨리지 않기
- row-major array에서 column 방향 큰 stride는 locality가 나쁨
- out-of-order CPU는 AMAT만으로 performance를 정확히 예측하기 어려움
- shared L2/L3의 associativity가 core 수보다 너무 작으면 core 간 conflict miss가 늘 수 있음
- segment로 address range를 늘리면 segment 크기 제한과 복잡한 address arithmetic 문제가 생김
- virtualization을 고려하지 않은 ISA에서는 non-privileged instruction이 hardware resource에 접근해 VMM 구현이 어려울 수 있음

슬라이드의 byte/word 예:

- 32-byte direct-mapped cache, 4-byte block -> 8 lines
- byte address 36: block `floor(36/4)=9`, index `9 mod 8=1`
- word address 36: byte address `36×4=144`, block `144/4=36`, index `36 mod 8=4`

## 5.48 Chapter 5 결론

- fast memory는 작고, large memory는 느리다.
- locality와 caching으로 fast하고 large한 memory의 illusion을 만든다.
- 전체 계층은 대략 `L1 -> L2/L3 -> DRAM -> disk/flash`다.
- 각 level에서 placement, lookup, replacement, write policy라는 같은 질문이 반복된다.
- multiprocessor에서는 hierarchy 성능뿐 아니라 coherence와 consistency가 필수다.

---

# Chapter 6. Parallel Computing과 GPU

## 6.1 Perceptron

`y = f(w1x1 + w2x2 + ... + wNxN)`

또는:

`y = f(w · x)`

길이 N:

- N multiplications
- N-1 additions
- accumulator 관점 N MACs
- 약 2N FLOPs

multi-layer perceptron은 각 layer에서 matrix-vector 또는 matrix-matrix multiplication을 반복한다.

dense layer의 input 수가 `N_in`, output perceptron 수가 `N_out`이면:

- weight 수: `N_in × N_out`
- bias 포함 parameter 수: `(N_in + 1) × N_out`
- inference MAC 수: `N_in × N_out`

perceptron 수와 connection 수가 늘수록 계산량이 선형으로 증가한다.

## 6.2 2D convolution

일반 개념:

`g[m,n] = Σ_k Σ_l f[k,l] × h[m-k,n-l]`

deep learning 구현에서는 kernel flip 여부에 따라 실제로 cross-correlation을 사용하는 경우가 많지만, 계산 구조는 neighborhood의 weighted sum이다.

CNN convolutional layer에서는 learned kernel weight를 image의 여러 위치에 반복 적용해 feature map을 만든다. 같은 weight를 공간 전체에서 재사용하는 weight sharing이 parameter 수와 locality에 중요하다.

## 6.3 Convolution output 크기

유도 공식:

입력 크기 `N`, kernel `K`, padding `P`, stride `S`:

`Output size = floor((N + 2P - K)/S) + 1`

2D에서는 height와 width에 각각 적용한다.

예:

- N=32
- K=3
- P=1
- S=1

`floor((32+2-3)/1)+1 = 32`

same convolution이 된다.

padding 0이면:

`32-3+1 = 30`

## 6.4 Convolution operation 수

single-channel:

`MACs = Hout × Wout × Kh × Kw`

multi-channel:

`MACs = Hout × Wout × Cout × Kh × Kw × Cin`

FLOPs:

`FLOPs ≈ 2 × MACs`

3×3 kernel 한 output:

- 9 MACs
- 약 18 FLOPs

## 6.5 Impulse와 averaging filter

3×3 averaging kernel의 각 coefficient가 `1/9`다.

input이 한 위치만 1인 2D impulse이면 convolution output은 해당 위치 주변에 kernel 모양이 나타난다.

kernel 합:

`9 × 1/9 = 1`

따라서 전체 brightness/DC component를 보존하는 smoothing filter다.

## 6.6 LeNet과 AlexNet

LeNet:

- convolution
- average pooling
- sigmoid/tanh
- fully connected
- MNIST 60K training examples

AlexNet:

- 더 큰 model과 dataset
- max pooling
- ReLU
- dropout
- GPU로 CPU 대비 50배 speedup 사례
- 7 hidden layers, 약 650K units, 약 60M parameters
- 두 GPU에서 약 일주일 training

이 수치는 특정 구현과 hardware의 측정값이며 GPU가 항상 모든 CNN에서 50배라는 뜻은 아니다.

## 6.7 Amdahl's Law

original time:

`T_original = T_affected + T_unaffected`

개선 후:

`T_new = T_affected / improvement factor + T_unaffected`

speedup:

`Speedup = T_original / T_new`

fraction 형태:

`Speedup = 1 / ((1-F) + F/S)`

- F: 개선 가능한 비율
- S: 해당 부분 speedup 또는 processor 수의 이상적 효과

최대 speedup:

`S -> infinity`일 때 `1/(1-F)`

## 6.8 Amdahl 강의 예 Case 1

- total = 100 s
- parallel part = 80 s
- serial part = 20 s

4 processors:

`T = 80/4 + 20 = 40 s`

`Speedup = 100/40 = 2.5`

40 processors:

`T = 80/40 + 20 = 22 s`

`Speedup ≈ 4.545`

400 processors:

`T = 80/400 + 20 = 20.2 s`

`Speedup ≈ 4.950`

limit:

`100/20 = 5`

## 6.9 Amdahl 강의 예 Case 2

- parallel part = 20 s
- serial part = 80 s

4 processors:

`T = 20/4 + 80 = 85 s`

`Speedup ≈ 1.176`

40 processors:

`T = 20/40 + 80 = 80.5 s`

`Speedup ≈ 1.242`

400 processors:

`T = 20/400 + 80 = 80.05 s`

`Speedup ≈ 1.249`

limit:

`100/80 = 1.25`

핵심: processor 수보다 parallel fraction이 먼저 중요하다.

## 6.10 Single core, multicore, SIMD

### Single core

- output pixel을 순차 계산

### Multicore

- 서로 독립적인 output row/pixel을 core에 분배
- shared data synchronization과 bandwidth 고려

### SIMD

- 한 core에서 여러 x 위치를 vector lane으로 동시 계산
- loop increment가 `SIMD_WIDTH`
- tail 처리 필요

multicore와 SIMD는 함께 사용할 수 있다.

`총 이상적 data parallelism = core 수 × SIMD lane 수`

## 6.11 Parameter memory

`Memory = parameter count × bytes/parameter`

FP32는 4 bytes:

- 60 K → 240 KB ≈ 0.24 MB
- 61 M → 244 MB
- 138 M → 552 MB
- 25 M → 100 MB
- 110 M → 440 MB
- 1.5 B → 6 GB
- 175 B → 700 GB

슬라이드 표의 값과 일치한다.

슬라이드는 2012~2020 사이 대표 AI model의 compute demand가 대략 `10^9`배 증가했다고 강조한다.

슬라이드 전체 표:

| Model | Parameters | Inference FLOPs | FP32 memory |
|---|---:|---:|---:|
| LeNet-5 | 60 K | 341 K | 0.24 MB |
| AlexNet | 61 M | 724 M | 244 MB |
| VGG-16 | 138 M | 15.5 G | 552 MB |
| ResNet-50 | 25 M | 4.1 G | 100 MB |
| BERT-Base | 110 M | 22.5 G | 440 MB |
| GPT-2 | 1.5 B | 약 300 G | 6 GB |
| GPT-3 | 175 B | 약 350 T | 700 GB |

주의:

- decimal 단위로 계산한 근사값
- activation, KV cache, gradient, optimizer state는 포함하지 않은 weight 중심 값

## 6.12 FLOPs와 parameter의 차이

- parameter: model storage와 memory capacity에 영향
- FLOPs: 한 inference/training step의 arithmetic work

parameter가 많다고 FLOPs가 반드시 같은 비율로 증가하지 않는다.

architecture, sequence length, image resolution, reuse에 따라 달라진다.

## 6.13 Execution time과 throughput

이상적:

`Execution time = required FLOPs / sustained FLOPS`

speedup:

`Speedup = CPU time / GPU time`

예:

ResNet-50:

`4 ms / 0.5 ms = 8`

VGG-16:

`15/2 = 7.5`

슬라이드는 약 7배로 표시한다.

BERT:

`22/3 ≈ 7.33`

GPT-2:

`300/20 = 15`

GPT-3:

`97 hours = 349,200 seconds`

`349,200/20 = 17,460`

약 17,500배다.

주의:

- `FLOPs/peak FLOPS`는 이상적 lower bound다.
- 슬라이드의 실행시간은 estimate이며 memory, utilization, batching을 포함한 단순 비교다.
- 특히 GPT-3 행은 `FLOPs / 1 TFLOPS` 또는 `FLOPs / 82 TFLOPS`만으로 표의 97시간/20초를 재현할 수 없다. 표의 estimated time과 nominal peak FLOPS를 서로 다른 가정의 수치로 취급해야 한다.

## 6.14 Shared-memory multiprocessor

- 모든 processor가 하나의 physical address space 공유
- shared variable은 lock 또는 synchronization 필요
- memory bandwidth와 coherence가 bottleneck

speedup 계산에서 synchronization와 communication overhead가 있으면:

`T_parallel = T_work/P + T_sync + T_communication + T_serial`

## 6.15 GPU 발전

1. fixed-function graphics pipeline
2. programmable shader
3. GPGPU
4. CUDA를 통한 general-purpose parallel programming

CUDA는 2007년에 도입되었고 graphics texture/shader 형태에 묶이지 않은 programming model을 제공했다.

fixed-function pipeline의 대표 단계:

- vertex transform
- rasterization
- texture mapping
- pixel coloring

초기 shader/GPGPU의 한계:

- data를 texture로 표현
- computation을 shader로 표현
- random memory access 제한
- pixel 사이 communication이 어려움

## 6.16 CUDA software hierarchy

- Grid: kernel launch 전체
- Block: 협력 가능한 thread group
- Thread: 한 data element 또는 작은 task

1D global thread index:

`global_id = blockIdx.x × blockDim.x + threadIdx.x`

필요 block 수:

`number of blocks = ceil(N / threads per block)`

정수 계산:

`blocks = (N + blockSize - 1) / blockSize`

2D:

`x = blockIdx.x × blockDim.x + threadIdx.x`

`y = blockIdx.y × blockDim.y + threadIdx.y`

boundary check:

`if x < W and y < H`

## 6.17 Convolution mapping

가장 단순한 mapping:

- thread 하나가 output element 하나 계산
- block이 output tile 담당
- grid가 전체 output 담당

output element가 독립적으로 계산 가능해야 대량 병렬화가 쉽다.

## 6.18 Redundant memory access

3×3 convolution에서 인접 output thread는 input neighborhood를 겹쳐 읽는다.

naive:

`B × B output tile`이 각 output당 `K²` input을 읽으면:

`naive reads = B²K²`

shared tile:

필요 input 영역은:

`(B + K - 1)²`

K=3:

`shared tile reads = (B+2)²`

이상적 read reduction:

`B² × 9 / (B+2)²`

예 B=16:

- naive: `16²×9 = 2304`
- shared tile: `18² = 324`
- reduction: `2304/324 ≈ 7.11배`

## 6.19 CUDA memory hierarchy

### Global memory

- 모든 thread 접근
- 크지만 high latency
- coalesced access 중요

### Shared memory

- block 내 공유
- 작고 빠름
- input tile reuse
- synchronization 필요

### Register/private state

- thread별
- register가 가장 빠름
- register pressure가 커지면 occupancy 감소 또는 spill 가능

주의:

CUDA의 "local memory"는 이름과 달리 register spill 시 device memory에 위치할 수 있어 느리다.

host CPU memory와 CUDA device global memory는 별도 address space로 그려져 있다. accelerator를 사용할 때는 data transfer time도 포함해야 한다.

`Total time = host work + host-device transfer + kernel execution + device-host transfer`

## 6.20 Hardware hierarchy와 software hierarchy

software:

`grid -> block -> thread`

hardware:

`GPU -> SM -> warp -> lane`

block은 한 SM에 배정되고, block의 thread는 warp 단위로 나뉜다.

## 6.21 Warp

- NVIDIA warp = 32 threads
- 같은 instruction을 서로 다른 data에 실행
- thread별 register state 보유

`warps per block = ceil(threads per block / 32)`

예:

- 256 threads/block
- `256/32 = 8 warps/block`

thread 수가 250이면:

`ceil(250/32) = 8 warps`

마지막 warp의 6 lanes는 inactive일 수 있다.

warp lane utilization:

`Warp efficiency = active threads / (number of warps × 32)`

250 threads이면:

`250/(8×32) = 97.65625%`

## 6.22 Warp instruction execution

슬라이드의 V100 sub-core:

- FP32 unit은 16-wide
- warp는 32 threads
- 따라서 한 warp의 FP32 instruction을 2 clocks에 나누어 실행

`cycles per warp instruction = warp width / SIMD width`

`= 32/16 = 2 cycles`

FP64 unit은 8-wide라면:

`32/8 = 4 cycles`

슬라이드 설명과 일치한다.

## 6.23 Latency hiding

한 warp가 memory를 기다리면 warp selector가 다른 ready warp를 실행한다.

조건:

- 충분한 active warp
- register와 shared memory resource가 occupancy를 제한하지 않아야 함
- independent work가 있어야 함

warp context 수는 동시에 arithmetic을 수행하는 lane 수가 아니라 즉시 전환 가능한 resident state 수다.

## 6.24 V100 SM 구조

슬라이드 기준:

- 4 sub-cores/warp selectors
- SM당 최대 64 warp contexts
- warp당 32 threads
- 최대 resident thread contexts:

`64 × 32 = 2,048 threads/SM`

- sub-core당 register 64 KB
- SM 전체 register 256 KB
- shared memory + L1 128 KB
- sub-core당 최대 16 warp를 interleave하고 4개 sub-core 전체로 64 warp context
- sub-core 실행 자원에는 16-wide FP32, 16-wide INT, 8-wide FP64, tensor unit, load/store unit이 포함

주의:

- 실제 동시에 arithmetic unit에서 실행되는 thread 수와 resident context 수를 구분한다.
- 2,048개는 latency hiding을 위해 resident할 수 있는 최대 context 설명이다.

## 6.25 V100 chip 전체 resident context

- 80 SM
- 64 warps/SM

`80 × 64 = 5,120 resident warps`

thread contexts:

`5,120 × 32 = 163,840 threads`

이는 최대 interleaved/resident context 수이지 163,840개의 FP32 ALU가 있다는 뜻은 아니다.

## 6.26 V100 peak FP32 계산

슬라이드:

- clock = 1.245 GHz
- 80 SM
- SM당 4 sub-core
- sub-core당 16 FP32 mul-add lanes

FP32 lanes:

`80 × 4 × 16 = 5,120`

FMA를 2 FLOPs로 계산:

`5,120 × 1.245×10^9 × 2`

`= 12.7488×10^12 FLOPS`

`≈ 12.7 TFLOPS`

주의:

- mul-add가 매 cycle 가능한 peak 가정
- 실제 성능은 utilization과 memory에 의해 낮다.

## 6.27 V100 memory

슬라이드:

- HBM 16 GB
- bandwidth 900 GB/s
- 4096-bit interface
- L2 6 MB

단순 transfer time:

`time = bytes / bandwidth`

예: 9 GB를 peak 900 GB/s로 읽으면:

`9/900 s = 0.01 s = 10 ms`

실제는 protocol, access pattern, read/write 혼합으로 더 오래 걸릴 수 있다.

전체 16 GB를 peak 900 GB/s로 한 번 읽는 최소 시간:

`16/900 s ≈ 17.78 ms`

4096-bit interface에서 900 GB/s를 만들기 위한 effective transfer rate:

`900×10^9 bytes/s × 8 / 4096 bits ≈ 1.758×10^9 transfers/s`

즉 약 1.758 GT/s다.

resource 제한을 포함한 occupancy 개념식:

`active blocks/SM = min(thread limit, register limit, shared-memory limit, architectural block limit)`

`occupancy = active warps / maximum warps per SM`

## 6.28 Compute-bound와 memory-bound

유도 개념:

`Compute time = FLOPs / compute throughput`

`Memory time = transferred bytes / memory bandwidth`

단순 lower bound:

`Execution time >= max(Compute time, Memory time)`

Arithmetic intensity:

`AI = FLOPs / transferred bytes`

- AI가 낮으면 memory-bound 가능성이 큼
- AI가 높으면 compute-bound 가능성이 큼

convolution tiling과 matrix blocking은 data reuse를 늘려 arithmetic intensity를 높인다.

## 6.29 Chapter 6 함정

- parameter count와 FLOPs를 같은 것으로 취급하지 않기
- MAC와 FLOP counting 방식 확인
- peak FLOPS와 measured FLOPS 구분
- resident thread와 동시에 실행 중인 ALU lane 구분
- block과 warp를 같은 개념으로 보지 않기
- shared memory는 block 내부에서만 공유
- warp divergence와 memory access pattern이 실제 성능 저하
- Amdahl 문제에서 개선되지 않는 serial part를 반드시 더하기

## 6.30 Chapter 6 결론

- AI 연산은 perceptron, convolution, matrix multiply처럼 독립적인 MAC가 매우 많아 data parallelism이 크다.
- multicore, SIMD, GPU thread를 계층적으로 결합해 parallelism을 확장한다.
- GPU는 많은 warp context를 보관하고 ready warp를 선택해 latency를 숨긴다.
- 성능은 arithmetic unit 수만으로 결정되지 않으며 memory bandwidth, reuse, occupancy, divergence, transfer가 함께 결정한다.
- V100 예는 `5,120 FP32 lanes`, `12.7 TFLOPS peak`, `900 GB/s HBM`, `163,840 resident thread contexts`의 의미를 서로 구분하는 것이 핵심이다.

---

# 7. 범위 전체 계산 문제 풀이 체크리스트

## 7.1 단위

- GHz를 Hz로 변환했는가?
- ns, us, ms, s를 통일했는가?
- bit와 byte를 구분했는가?
- GB와 GiB 조건을 확인했는가?
- percent를 소수로 바꾸었는가?

## 7.2 성능비

`Speedup = old time / new time`

시간이 작을수록 빠르다. 성능 수치가 직접 주어지면:

`Performance ratio = new performance / old performance`

## 7.3 Pipeline

- producer 결과가 어느 stage 끝에 나오는가?
- consumer가 어느 stage 시작에 필요한가?
- EX/MEM과 MEM/WB 중 최신 결과는 무엇인가?
- load-use라면 bubble이 필요한가?
- branch가 ID에서 비교되는가?
- flush된 instruction을 retired count에 넣지 않았는가?

## 7.4 Cache

1. byte/word address 확인
2. block address 계산
3. sets 계산
4. tag/index/offset 계산
5. valid/tag로 hit 판정
6. replacement policy 적용
7. dirty/write policy 적용
8. miss penalty와 CPI/AMAT 계산

## 7.5 Virtual memory

- page size로 offset bit 계산
- VPN과 PPN 분리
- TLB miss인지 page fault인지 판정
- dirty victim write-back 포함
- page table size 계산 시 PTE bytes 포함

## 7.6 SIMD/GPU

- lane 수 = register bits / element bits
- FMA를 2 FLOPs로 세는지 확인
- warp 수는 ceiling 사용
- block 수는 ceiling 사용
- resident context와 arithmetic unit 수 구분
- bandwidth time은 bytes/bandwidth

## 7.7 AI arithmetic

- model memory = parameter × bytes
- matrix MACs = M×N×K
- FLOPs ≈ 2×MACs
- convolution MACs = Hout×Wout×Cout×Kh×Kw×Cin
- precision이 다른 성능 수치를 직접 비교하지 않기
