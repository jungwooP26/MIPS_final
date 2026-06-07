# 컴퓨터구조 기말 범위 전 페이지 한국어 해설

## 읽는 방법

- 이 파일은 요약본이 아니라 지정된 기말 범위 **230개 PDF 페이지를 페이지별로 모두 해설한 문서**다.
- `PDF 쪽`은 PDF 뷰어의 실제 페이지 번호다.
- `내부 쪽`은 슬라이드 아래에 인쇄된 번호다.
- Chapter 3은 `PDF 43~51 = 내부 47~55`로 4쪽 차이가 난다.
- 그림만 있는 페이지는 앞뒤 슬라이드와 도식의 변화까지 설명했다.
- 같은 그림을 단계적으로 보여 주는 animation slide도 생략하지 않고 각 페이지에서 새로 강조되는 내용을 적었다.
- 원문의 문장을 기계적으로 직역하기보다, 모든 정보가 남으면서 시험에서 이해하고 설명할 수 있는 한국어로 풀었다.

## 범위

| 장 | PDF 범위 | 페이지 수 |
|---|---:|---:|
| Chapter 1 | 51~61 | 11 |
| Chapter 3 | 43~51 | 9 |
| Chapter 4 | 69~107 | 39 |
| Chapter 4.5 SIMD | 1~22 | 22 |
| Chapter 5 | 1~105 | 105 |
| Chapter 6 | 1~44 | 44 |
| 합계 |  | 230 |

---

# Chapter 1: AI 시대의 컴퓨터 구조

## Ch1 PDF 51쪽 / 내부 51쪽 - Multicore CPU 예

- Intel 10세대 Core i9의 10-core CPU die 사진이다. 한 chip 안에 Core 1~10이 반복 배치된 모습을 보여 준다.
- CPU core는 각각 복잡한 instruction과 control flow를 처리할 수 있는 범용 실행 장치다. 여러 core가 서로 독립적인 task나 thread를 동시에 처리하면 multicore parallelism을 얻는다.
- die의 전부가 core인 것은 아니다. 큰 영역은 cache, memory interface, interconnect, I/O와 기타 control logic가 차지한다.
- core가 10개라고 어떤 program이나 정확히 10배 빨라지는 것은 아니다. 병렬화 가능한 부분, serial part, synchronization, memory bandwidth가 실제 speedup을 제한한다.
- 계산 관점에서 이상적인 총 처리량은 `core 수 × core당 처리량`이지만, 실제 speedup은 Chapter 6의 Amdahl's Law로 판단한다.

## Ch1 PDF 52쪽 / 내부 52쪽 - NVIDIA RTX 4090

- 2022년 RTX 4090은 약 760억 transistor와 18,432개의 FP32 multiplier를 144개 processing block에 구성한 대규모 parallel processor다.
- CPU의 적은 수의 복잡한 core와 달리 GPU는 같은 단순 산술을 많은 data에 적용하기 위해 매우 많은 arithmetic unit을 둔다.
- 단순 계산으로 block당 FP32 multiplier는 `18,432 / 144 = 128개`다.
- 그림에서 작은 반복 block이 매우 많이 보이는 것은 GPU가 throughput 중심으로 설계됐음을 뜻한다.
- FP32 unit 수는 peak hardware 규모다. 실제 사용률은 memory bandwidth, instruction mix, occupancy, dependency, branch divergence에 따라 달라진다.

## Ch1 PDF 53쪽 / 내부 53쪽 - GPU 가속 Supercomputer

- Oak Ridge National Laboratory의 Frontier supercomputer 사례다. 2022년 가을 세계 1위였던 대규모 계산 시스템을 보여 준다.
- 구성 수치는 9,472개의 64-core AMD CPU, 총 606,208 CPU core, 37,888개의 Radeon GPU, 약 21 MW 전력이다.
- CPU는 program control과 general-purpose 처리를 담당하고 GPU는 대량의 수치 계산을 가속한다.
- 한 GPU 내부의 parallelism뿐 아니라 수만 개 GPU를 network로 연결하는 system-level parallelism까지 사용한다.
- 규모가 커지면 계산 성능 외에도 communication, workload distribution, memory hierarchy, cooling, power, fault tolerance가 핵심 문제가 된다.

## Ch1 PDF 54쪽 / 내부 54쪽 - Mobile Parallel Processing

- Apple A15 Bionic SoC의 die 사진이다. 약 150억 transistor 안에 6-core CPU, multi-core GPU, NPU, cache, memory interface가 통합돼 있다.
- CPU는 2개의 큰 high-performance core와 4개의 작은 energy-efficient core로 구성된다. workload 성격에 따라 성능과 전력을 조절한다.
- 5개 GPU block은 graphics와 data-parallel computation을 담당한다.
- NPU block은 neural-network inference를 낮은 전력으로 처리한다.
- mobile system은 CPU, GPU, NPU를 하나의 chip에서 함께 사용해 성능, latency, battery life 사이의 균형을 맞춘다.

## Ch1 PDF 55쪽 / 내부 55쪽 - Datacenter

- Google datacenter의 accelerator rack 또는 TPU system 사진이다. 많은 compute board와 network가 한 시스템으로 연결돼 있다.
- datacenter AI는 단일 accelerator가 아니라 다수 CPU, GPU/TPU, memory, storage, network를 결합해 large-model training과 inference를 수행한다.
- 대규모 model에서는 accelerator 계산량뿐 아니라 model parameter를 저장할 memory capacity와 accelerator 사이 data 이동 bandwidth가 중요하다.
- distributed training에서는 parameter와 gradient를 여러 장치가 교환하므로 communication이 전체 시간을 제한할 수 있다.
- 이 페이지는 parallelism이 mobile SoC부터 datacenter까지 모든 규모에서 사용된다는 흐름을 완성한다.

## Ch1 PDF 56쪽 / 내부 56쪽 - CPU와 AI Chip 비교

- AI workload는 같은 단순 계산을 엄청난 횟수 반복하므로 수천 개 core가 같은 작업을 동시에 수행하는 parallel chip이 유리하다.
- CPU는 복잡한 task를 독립적으로 처리하고, strict ordering과 branch가 많은 작업에 강하다. 전형적인 고성능 core 수는 약 8~32개다.
- GPU/TPU/NPU의 각 core는 단순한 수학을 처리하지만 수천~수만 개가 병렬 동작한다. 반복적이고 data-parallel한 작업에서 높은 throughput을 낸다.
- 핵심 차이는 CPU가 low-latency와 control 중심이고 AI chip이 throughput과 arithmetic density 중심이라는 점이다.
- 모든 workload가 AI chip에 적합한 것은 아니다. dependency가 강하거나 unpredictable branch가 많으면 CPU가 더 효율적일 수 있다.

## Ch1 PDF 57쪽 / 내부 57쪽 - AI의 핵심이 Matrix Multiplication인 이유

- image는 수백만 pixel number로 이루어진 matrix/tensor로 저장된다. deep-learning layer는 image에 반복적인 matrix operation을 적용한다.
- LLM에서는 word와 sentence를 의미를 담은 numerical embedding vector로 바꾸고, attention과 feed-forward layer에서 큰 matrix multiplication을 반복한다.
- deep-learning model은 정해진 복잡한 matrix operation의 집합으로 볼 수 있다. model이 커질수록 matrix arithmetic도 증가한다.
- matrix multiplication은 `C[i,j] = Σ_k A[i,k]B[k,j]`이며, output 하나는 row와 column의 dot product다.
- `M×K`와 `K×N` matrix의 곱은 `MNK MAC`, multiply와 add를 각각 세면 약 `2MNK FLOPs`가 필요하다.

## Ch1 PDF 58쪽 / 내부 58쪽 - GPU, TPU, NPU

- GPU는 원래 graphics용이지만 수천 core와 matrix operation 능력으로 AI training과 research에 가장 널리 사용된다. RTX와 datacenter A100이 예다.
- TPU는 Google이 AI를 위해 설계한 tensor accelerator다. matrix/tensor operation에 특화되어 GPT 규모의 model training에 사용된다.
- NPU는 smartphone과 PC에 내장되어 매우 낮은 power로 inference를 수행한다. camera와 voice recognition이 대표 용도다.
- GPU는 비교적 general-purpose parallel processor, TPU는 datacenter tensor 특화, NPU는 low-power on-device inference 특화로 구분한다.
- training은 weight update와 높은 memory/compute가 필요하고, inference는 학습된 weight로 결과만 계산해 low precision을 더 적극적으로 사용할 수 있다.

## Ch1 PDF 59쪽 / 내부 59쪽 - MIPS, FLOPS, TOPS

- MIPS는 초당 몇 million instruction을 실행하는지 나타낸다. general CPU의 add, compare, jump 같은 instruction 수를 세지만 ISA와 program에 따라 instruction의 일이 달라 AI 성능 비교에는 부적합하다.
- `MIPS = instruction count / (execution time × 10^6)` 또는 `clock rate / (CPI × 10^6)`이다. 예로 i7-13700K가 300,000+ MIPS로 제시된다.
- FLOPS는 초당 floating-point operation 수다. science, graphics, neural network에 관련되며 CPU i9-13900K 약 0.9 TFLOPS, RTX 4070 약 29 TFLOPS가 제시된다.
- TOPS는 초당 trillion operation 수로 INT8/INT4 같은 low-precision operation까지 포함해 modern AI chip의 대규모 parallel workload를 표현한다.
- 예시는 CPU 약 2 TOPS, RTX 4070 약 641 INT8 TOPS다. precision과 MAC counting 방식이 다르면 수치를 직접 비교하면 안 된다.

## Ch1 PDF 60쪽 / 내부 60쪽 - CPU와 GPU 성능 및 역할

- 약 $400의 i9-13900K와 약 $600의 RTX 4070을 비교한다.
- FP32 성능비는 `29 / 0.9 ≈ 32.2배`, INT8 처리량 비는 `641 / 2 = 320.5배`다.
- GPU가 AI math에 빠르다고 CPU가 불필요한 것은 아니다. CPU는 IF/ELSE, loop, unpredictable code flow 같은 complex logic를 처리한다.
- CPU는 keyboard input, OS response처럼 low latency가 필요한 작업과 browser, database 등 general-purpose program을 수행한다.
- AI system에서 CPU는 data 준비, GPU 호출, 결과 해석과 다음 단계 결정을 담당하고 GPU는 bulk matrix arithmetic을 수행한다. 둘은 항상 협력한다.

## Ch1 PDF 61쪽 / 내부 61쪽 - Chapter 1 결론

- technology 발전으로 cost/performance가 지속적으로 개선된다.
- hardware와 software는 여러 abstraction layer로 구성되며, ISA가 hardware/software interface 역할을 한다.
- 가장 직접적인 performance measure는 execution time이다. MIPS 같은 간접 지표만으로 판단하면 안 된다.
- power가 성능 향상의 제한 요소가 되었고, 단일 core만 빠르게 만드는 방식에는 한계가 있다.
- 따라서 multicore, SIMD, GPU 같은 parallelism을 활용해 performance를 높이는 것이 현대 computer architecture의 핵심 방향이다.

# Chapter 3: AI를 위한 수 표현과 MAC

> 주의: 이 장은 PDF 번호와 내부 번호가 4쪽 다르다.

## Ch3 PDF 43쪽 / 내부 47쪽 - IEEE 754와 AI가 새 Format을 요구한 이유

- FP32는 sign 1 bit, exponent 8 bits, mantissa/fraction 23 bits로 총 32 bits, 즉 수 하나당 4 bytes다.
- sign은 양수/음수, exponent는 수의 크기 범위, fraction은 유효 숫자의 정밀도를 담당한다.
- 표현 범위는 대략 `1.2×10^-38`부터 `3.4×10^38`이며 science와 general computing의 표준으로 사용돼 왔다.
- AI model에는 수십억 weight가 있어 FP32를 사용하면 memory capacity와 bandwidth가 크게 필요하다. 10억 parameter만 해도 weight가 약 4 GB다.
- AI pattern recognition은 항상 FP32의 약 7 decimal digit precision을 요구하지 않는다. bit 수를 줄이면 memory, switching transistor, power, heat와 operation cost를 낮출 수 있다.
- 그래서 AI용 format은 일부 precision을 포기하고 speed와 memory 효율을 얻는다.

## Ch3 PDF 44쪽 / 내부 48쪽 - FP32, BF16, FP16, FP8

- FP32는 E8/M23, 4 bytes로 full precision baseline이며 model을 처음부터 training할 때 사용된다.
- BF16은 E8/M7, 2 bytes다. exponent가 FP32와 같아 range는 넓고 decimal detail만 줄어 large-model training에 유리하다.
- FP16은 E5/M10, 2 bytes다. BF16보다 fraction precision은 높지만 exponent range가 좁아 overflow/underflow를 조심해야 한다.
- FP8 예시는 sign 1, E4/M3, 1 byte다. 매우 compact해 최신 AI chip inference에 쓰이지만 approximation 허용이 전제다.
- 같은 model의 weight memory는 FP16/BF16이 FP32의 절반, FP8이 4분의 1이다.
- exponent bit는 range, fraction bit는 precision을 주로 결정한다.

## Ch3 PDF 45쪽 / 내부 49쪽 - AI가 반올림을 허용할 수 있는 이유

- 선명한 4K 사진과 약간 흐린 사진 모두 친구의 얼굴을 알아보는 데 충분하다는 비유를 사용한다.
- AI classification도 작은 수치 오차가 있어도 전체 pattern을 올바르게 인식할 가능성이 높다.
- FP32는 수당 4 bytes로 매우 정밀한 decimal을 저장하며 weight를 세밀하게 조정하는 training baseline에 적합하다.
- FP16은 2 bytes로 약간 덜 정밀하지만 training과 inference에서 약 2배 speed, 2배 memory 절약의 예가 제시된다.
- INT8은 1 byte의 integer로 on-device inference에 쓰이며 약 4배 speed와 4배 memory 절약의 예가 제시된다.
- 실제 speedup은 hardware support와 memory/compute bottleneck에 따라 달라지고, accuracy 손실도 model과 quantization 방법에 따라 달라진다.

## Ch3 PDF 46쪽 / 내부 50쪽 - Performance와 Accuracy의 Trade-off

- 표의 baseline은 FP32 speed 1×, memory 4 단위, 가장 높은 accuracy, model training이다.
- FP16은 약 2× faster, memory 2 단위, accuracy가 FP32와 매우 가깝고 training/inference에 사용된다.
- INT8은 약 4× faster, memory 1 단위, accuracy가 조금 낮을 수 있으며 on-device inference에 적합하다.
- smartphone NPU는 INT8로 battery를 적게 사용하면서 real-time AI를 수행하고, cloud GPU는 FP16으로 training을 가속한다.
- datacenter는 FP16/INT8을 섞어 cost와 energy를 줄일 수 있다.
- 수십억 operation에서 작은 rounding error가 평균화될 수 있다는 점이 scientific computing과 다르다. 과학 계산은 각 digit의 정확성이 훨씬 중요할 수 있다.

## Ch3 PDF 47쪽 / 내부 51쪽 - Overflow, Underflow, Mixed Precision

- training은 작은 gradient로 수백만 weight를 계속 update한다. gradient가 표현 범위를 벗어나면 학습이 망가진다.
- gradient explosion은 값이 너무 커져 overflow가 발생하는 현상이다. FP16 최대 약 65,504인데 gradient가 70,000이면 Inf가 되고 이후 weight가 NaN/Inf로 오염될 수 있다.
- gradient vanishing/underflow는 너무 작은 값이 0으로 round되는 현상이다. FP16 최소 positive subnormal은 약 `5.96×10^-8`이고, `10^-9` 같은 gradient는 0이 되어 layer가 학습하지 않을 수 있다.
- solution은 mixed precision과 loss scaling이다. 중요한 master weight는 FP32로 유지하고 빠른 math는 FP16으로 수행한다.
- gradient에 scale `S`를 곱해 FP16에서 표현 가능한 크기로 만든 뒤 weight update 전에 다시 `S`로 나눈다.
- `scaled gradient = S×gradient`이며 scale이 너무 크면 overflow가 생기므로 dynamic loss scaling이 필요하다.

## Ch3 PDF 48쪽 / 내부 52쪽 - MAC

- MAC은 Multiply-Accumulate이며 `accumulator = accumulator + A×B`다.
- 두 수를 곱하고 running total에 더하는 단순 연산을 대규모로 반복한다.
- neuron의 `output = Σ(weight×input)`은 MAC chain이다. GPT 한 번의 forward pass에도 엄청난 수의 MAC이 필요하다.
- CPU는 core당 cycle당 소수 MAC, GPU는 수천 MAC unit, TPU/NPU는 chip 전체를 MAC array 중심으로 설계한다.
- modern AI chip 성능을 TOPS 또는 MAC/s로 나타내는 이유가 여기에 있다.
- operation counting에서 MAC를 1 operation으로 볼 수도 있고 multiply와 add를 나눠 2 operations/FLOPs로 볼 수도 있으므로 문제 조건을 확인해야 한다.

## Ch3 PDF 49쪽 / 내부 53쪽 - 한 MAC에서 AI Chip까지

- 1단계는 `acc += A×B` 한 번이다.
- 2단계 dot product는 `acc = Σ(a_i×b_i)`로 두 array에 MAC chain을 적용한다.
- 3단계 matrix multiplication `C=A×B`는 많은 dot product를 parallel하게 계산한다.
- 4단계 deep learning은 여러 layer의 matrix multiplication을 연결한 neural network다.
- 슬라이드의 대략적인 cycle당 MAC 규모는 CPU core 1~2, RTX 4070 약 2,600, Google TPU v4 약 275,000이다.
- 같은 clock이라면 병렬 MAC unit 수가 많을수록 peak throughput이 커지지만, memory 공급과 utilization이 충분해야 한다.

## Ch3 PDF 50쪽 / 내부 54쪽 - 수 표현 결론 1

- bit pattern 자체에는 고정된 의미가 없다. 어떤 instruction을 적용하는지에 따라 signed integer, unsigned integer, floating-point 등으로 해석된다.
- computer representation은 bit 수가 유한하므로 표현 가능한 range와 precision도 유한하다.
- program은 rounding, overflow, underflow 가능성을 고려해야 한다.
- 같은 32-bit pattern도 integer instruction과 floating-point instruction이 완전히 다른 값으로 해석할 수 있다.
- 이 페이지는 AI format도 결국 bit pattern을 목적에 맞게 다르게 해석하는 설계라는 점과 연결된다.

## Ch3 PDF 51쪽 / 내부 55쪽 - 수 표현 결론 2

- ISA는 signed/unsigned integer와 floating-point arithmetic을 지원한다.
- floating point는 real number를 exact하게 저장하는 것이 아니라 제한된 range와 precision으로 근사한다.
- arithmetic operation은 overflow와 underflow를 일으킬 수 있다.
- MIPS ISA의 자주 쓰이는 core instruction 54개가 SPECINT에서는 100%, SPECFP에서는 97%를 차지한다는 수치가 제시된다.
- 자주 쓰이는 작은 instruction 집합이 실제 workload 대부분을 처리하고, 나머지 instruction은 상대적으로 덜 사용된다는 결론이다.

# Chapter 4: Forwarding부터 Multiple Issue까지

## Ch4 PDF 69쪽 / 내부 69쪽 - Forwarding 필요 조건 검출

- EX stage의 ALU source register 번호는 `ID/EX.RegisterRs`, `ID/EX.RegisterRt`에 들어 있다.
- 바로 앞 instruction의 destination은 `EX/MEM.RegisterRd`, 더 앞 instruction의 destination은 `MEM/WB.RegisterRd`에 있다.
- EX/MEM의 Rd가 현재 Rs 또는 Rt와 같으면 직전 결과를 forwarding해야 한다. 조건 1a/1b다.
- MEM/WB의 Rd가 현재 Rs 또는 Rt와 같으면 더 이전 결과를 forwarding해야 한다. 조건 2a/2b다.
- 핵심은 data value뿐 아니라 register number도 pipeline register를 따라 전달해야 dependency를 비교할 수 있다는 것이다.

## Ch4 PDF 70쪽 / 내부 70쪽 - RegWrite와 `$zero` 조건

- register 번호가 같다는 사실만으로 forwarding하면 안 된다. producer instruction이 실제 register write를 해야 한다.
- 그래서 EX/MEM과 MEM/WB의 `RegWrite` control signal을 함께 검사한다.
- destination이 `$zero`, 즉 register 0이면 write 결과가 보존되지 않으므로 forwarding하지 않는다.
- 완전한 기본 조건은 `RegWrite=1`, `Rd!=0`, `Rd==현재 source`의 세 부분이다.
- store나 branch처럼 register를 쓰지 않는 instruction을 false dependency로 처리하지 않는 것이 목적이다.

## Ch4 PDF 71쪽 / 내부 71쪽 - Forwarding Path 그림

- register file에서 나온 두 ALU operand 앞에 각각 multiplexer가 추가된다. control은 `ForwardA`, `ForwardB`다.
- EX/MEM의 최근 ALU result를 현재 ALU input으로 되돌리는 path가 있다.
- MEM/WB의 write-back data를 현재 ALU input으로 되돌리는 path도 있다. 이 값은 memory load data이거나 더 이전 ALU result일 수 있다.
- forwarding unit은 ID/EX의 Rs/Rt와 EX/MEM, MEM/WB의 Rd를 비교해 mux를 선택한다.
- forwarding은 register file에 write한 뒤 다시 read할 때까지 기다리지 않고 pipeline 내부의 최신 value를 직접 사용하게 한다.

## Ch4 PDF 72쪽 / 내부 72쪽 - ForwardA/ForwardB Mux Control

- `ForwardA=00`이면 첫 ALU operand를 ID/EX, 즉 register file에서 가져온다.
- `ForwardA=10`이면 EX/MEM의 직전 ALU result를 사용한다.
- `ForwardA=01`이면 MEM/WB의 memory data 또는 이전 ALU result를 사용한다.
- ForwardB도 두 번째 operand에 대해 같은 `00`, `10`, `01` 의미를 가진다.
- 보통 `11`은 사용하지 않는 조합이다. 시험에서는 dependency를 찾은 뒤 source의 최신 위치에 맞춰 A와 B를 따로 결정한다.

## Ch4 PDF 73쪽 / 내부 73쪽 - Double Data Hazard

- `add $1,$1,$2`, `add $1,$1,$3`, `add $1,$1,$4`처럼 같은 register를 연속 갱신하면 마지막 instruction이 두 producer와 모두 match할 수 있다.
- EX/MEM에는 두 번째 add의 더 최신 `$1`, MEM/WB에는 첫 번째 add의 오래된 `$1`이 있다.
- 두 조건이 모두 참이어도 가장 최근 program-order value인 EX/MEM을 선택해야 한다.
- 따라서 EX hazard가 MEM hazard보다 우선순위가 높다.
- 이 priority를 넣지 않으면 오래된 값을 forwarding해 잘못된 결과를 계산한다.

## Ch4 PDF 74쪽 / 내부 74쪽 - 수정된 MEM Hazard 조건

- MEM/WB forwarding은 MEM/WB가 write하고, Rd가 0이 아니고, 현재 source와 같을 때 후보가 된다.
- 여기에 같은 source에 대한 EX/MEM match가 **없다**는 조건을 추가한다.
- Rs 조건이 성립하면 `ForwardA=01`, Rt 조건이 성립하면 `ForwardB=01`이다.
- 논리적으로 `MEM match AND NOT EX match`로 표현한다.
- 이 식이 double hazard에서 newer EX/MEM result를 우선하는 정확한 forwarding control이다.

## Ch4 PDF 75쪽 / 내부 75쪽 - Forwarding을 포함한 전체 Datapath

- 5-stage datapath 전체에 forwarding unit, ALU input mux, destination register-number path가 통합된 그림이다.
- IF/ID에서 instruction의 register field를 읽고 ID/EX에 source 번호와 data를 보낸다.
- EX/MEM과 MEM/WB는 destination 번호와 RegWrite를 forwarding unit으로 되돌린다.
- ALU input mux는 register value, EX/MEM result, MEM/WB write-back value 중 하나를 선택한다.
- forwarding으로 대부분 ALU-ALU RAW hazard를 stall 없이 해결하지만, load-use처럼 value가 늦게 나오는 경우는 해결하지 못한다.

## Ch4 PDF 76쪽 / 내부 76쪽 - Load-Use Hazard

- `lw $2,20($1)` 바로 뒤의 `and $4,$2,$5`가 load된 `$2`를 사용하는 timeline이다.
- load data는 MEM stage가 끝나야 나오지만, and는 같은 cycle의 EX stage 시작에 operand를 필요로 한다.
- forwarding path가 있어도 아직 생성되지 않은 data를 과거로 보낼 수는 없다.
- 따라서 consumer를 한 cycle 늦추는 stall이 필요하다.
- 그림은 load-use hazard가 기본 5-stage pipeline에서 1-cycle bubble을 요구하는 이유를 시간 위치로 보여 준다.

## Ch4 PDF 77쪽 / 내부 77쪽 - Load-Use Hazard Detection

- hazard는 consumer가 ID stage에 있을 때 검사한다.
- ID/EX의 instruction이 load인지 `ID/EX.MemRead`로 확인한다.
- load destination은 `ID/EX.RegisterRt`이며, 이를 IF/ID의 consumer source `RegisterRs`, `RegisterRt`와 비교한다.
- 조건은 `ID/EX.MemRead AND (ID/EX.Rt==IF/ID.Rs OR ID/EX.Rt==IF/ID.Rt)`다.
- 참이면 pipeline을 stall하고 bubble을 삽입한다. 실제 설계에서는 instruction format상 Rt가 source가 아닌 경우를 구분해 불필요한 stall을 줄일 수 있다.

## Ch4 PDF 78쪽 / 내부 78쪽 - Pipeline Stall 방법

- ID/EX에 들어갈 control value를 모두 0으로 만들어 EX, MEM, WB에서 아무 state도 바꾸지 않는 nop을 만든다.
- PC update를 막아 같은 consumer instruction 주소를 유지한다.
- IF/ID register update도 막아 consumer를 ID에서 다시 decode하게 한다.
- 그 뒤의 instruction도 다시 fetch되므로 instruction을 잃지 않는다.
- 한 cycle 뒤 load data가 준비되면 MEM/WB에서 consumer의 EX stage로 forwarding할 수 있다.

## Ch4 PDF 79쪽 / 내부 79쪽 - 삽입된 Bubble의 이동

- timeline에서 `and`가 원래 차례에 실행되지 않고 control 0인 nop/bubble로 바뀐다.
- 실제 consumer `and`는 ID stage에 한 cycle 더 머문 뒤 다음 cycle에 EX로 진행한다.
- load는 stall과 관계없이 MEM, WB로 계속 전진한다.
- bubble도 일반 instruction처럼 EX, MEM, WB stage를 이동하지만 register나 memory를 변경하지 않는다.
- 이후 instruction의 시작도 한 cycle씩 밀리므로 전체 실행 cycle이 1 증가한다.

## Ch4 PDF 80쪽 / 내부 80쪽 - Stall의 정확한 Timeline

- 더 정확한 그림에서는 load 뒤의 `and`가 ID에 고정되고, 그 다음 `or`는 IF에 고정된다.
- PC와 IF/ID를 freeze하기 때문에 같은 instruction이 중복 실행되는 것이 아니라 같은 위치에서 기다린다.
- ID/EX에는 별도의 bubble이 들어가 load 뒤를 따라간다.
- stall이 풀리면 and가 EX, or가 ID로 동시에 전진한다.
- 이 그림은 “consumer를 nop으로 바꾼다”가 아니라 “consumer는 정지시키고 nop control만 앞으로 보낸다”는 차이를 강조한다.

## Ch4 PDF 81쪽 / 내부 81쪽 - Hazard Detection이 포함된 Datapath

- hazard detection unit이 `ID/EX.MemRead`, `ID/EX.Rt`, `IF/ID.Rs/Rt`를 입력으로 받는다.
- hazard 시 `PCWrite=0`, `IF/IDWrite=0`으로 PC와 IF/ID를 동결한다.
- control signal 앞 mux가 0을 선택해 ID/EX에 nop control을 넣는다.
- forwarding unit은 그대로 존재해 stall 다음 cycle에 준비된 load data를 EX operand로 보낸다.
- 이 datapath는 forwarding과 stall이 서로 대체 관계가 아니라 함께 필요하다는 것을 보여 준다.

## Ch4 PDF 82쪽 / 내부 82쪽 - Stall과 Performance

- stall은 instruction completion을 늦춰 performance를 낮추지만 correct result를 위해 필요하다.
- compiler는 pipeline 구조를 알고 independent instruction을 load와 consumer 사이로 옮겨 stall을 숨길 수 있다.
- reordering은 data dependency, memory alias, exception과 observable behavior를 바꾸지 않아야 한다.
- hardware forwarding/hazard detection과 software scheduling을 함께 사용하면 pipeline utilization을 높일 수 있다.
- 긴 program에서는 `CPI ≈ 1 + stall cycles per instruction`으로 stall 영향을 계산할 수 있다.

## Ch4 PDF 83쪽 / 내부 83쪽 - Branch Hazard

- branch outcome을 MEM stage에서 결정하면 그동안 sequential path의 여러 instruction이 이미 IF, ID, EX에 들어간다.
- branch가 taken이면 잘못 들어온 instruction의 control을 0으로 만들어 flush해야 한다.
- 그림의 branch 뒤 instruction들은 architectural effect 없이 제거되고 target instruction을 다시 fetch한다.
- branch decision이 늦을수록 flush되는 instruction 수와 penalty가 커진다.
- deeper pipeline과 multiple issue processor에서 branch prediction이 특히 중요한 이유다.

## Ch4 PDF 84쪽 / 내부 84쪽 - Branch Delay 줄이기

- branch target adder와 register comparator를 ID stage로 옮겨 outcome을 더 일찍 결정한다.
- 예제는 address 40의 `beq $1,$3,7`이다. `PC+4=44`, offset `7×4=28`, target은 72다.
- MIPS branch target 공식은 `PC+4+(sign-extended immediate<<2)`다.
- ID에서 비교하려면 branch source 값을 ID까지 forwarding할 수 있어야 하고 별도의 hazard detection도 필요하다.
- ID logic가 늘어 stage delay가 길어질 수 있다는 trade-off가 있다.

## Ch4 PDF 85쪽 / 내부 85쪽 - Taken Branch, Clock 3

- clock 3에서 branch가 ID stage에 있고 comparator가 `$1`과 `$3`를 비교한다.
- branch target adder가 72를 계산한다.
- taken으로 판정되면 현재 IF에 들어온 fall-through instruction `sub $10,$4,$8`은 잘못된 path이므로 `IF.Flush`로 제거한다.
- PC source mux가 sequential PC+4가 아니라 target 72를 선택하도록 바뀐다.
- branch 자체는 이후 stage로 이동하지만 register/memory write를 하지 않는다.

## Ch4 PDF 86쪽 / 내부 86쪽 - Taken Branch, Clock 4

- 다음 clock에는 target 72의 `lw $4,50($7)`이 instruction memory에서 fetch된다.
- 앞 cycle에 잘못 fetch된 instruction 자리에는 bubble이 보인다.
- branch는 EX로 진행하고 그보다 앞선 instruction은 정상적으로 MEM/WB를 수행한다.
- ID에서 branch를 해결했기 때문에 taken branch penalty를 한 cycle 수준으로 줄일 수 있다.
- target instruction이 즉시 fetch되려면 target 계산과 PC selection이 clock timing 안에 끝나야 한다.

## Ch4 PDF 87쪽 / 내부 87쪽 - Branch Operand Hazard: Forwarding 가능

- branch compare register가 2번째 또는 3번째 앞 ALU instruction의 destination인 경우를 다룬다.
- 해당 ALU result가 EX/MEM 또는 MEM/WB에 도달해 있으므로 ID-stage comparator로 forwarding할 수 있다.
- 예에서 `add $1,$2,$3`의 결과를 `beq $1,$4,target`이 사용한다.
- 필요한 value가 준비돼 있다면 별도 stall 없이 branch를 비교할 수 있다.
- 일반 ALU forwarding은 EX input으로 가지만 branch용 forwarding은 ID comparator까지 가야 한다.

## Ch4 PDF 88쪽 / 내부 88쪽 - Branch Operand Hazard: 1 Stall

- branch가 바로 앞 ALU instruction의 result를 비교하거나, 두 번째 앞 load result를 비교하면 value가 아직 ID comparator에 제때 도착하지 않는다.
- 이 경우 branch를 ID에서 한 cycle stall한다.
- 그림에서는 `lw $1,addr` 뒤에 다른 instruction 하나가 있고 `beq $1,$4,target`이 와도 1 stall이 필요하다.
- stall 후 load value가 MEM/WB 쪽에서 ID comparator로 forwarding될 수 있다.
- branch stall 수는 producer 종류와 branch까지의 instruction distance를 함께 판단해야 한다.

## Ch4 PDF 89쪽 / 내부 89쪽 - 바로 앞 Load 뒤 Branch: 2 Stalls

- `lw $1,addr` 바로 다음 `beq $1,$0,target`은 load data가 가장 늦게 준비되는 worst case다.
- branch는 ID stage에서 두 cycle 기다려야 한다.
- 첫 stall 동안 load는 EX에서 MEM으로, 두 번째 stall 동안 MEM에서 data를 얻는다.
- 그 뒤 load data를 branch comparator에 forwarding해 비교한다.
- 따라서 “바로 앞 ALU -> branch 1 stall”, “두 번째 앞 load -> 1 stall”, “바로 앞 load -> 2 stalls”를 구분한다.

## Ch4 PDF 90쪽 / 내부 90쪽 - Dynamic Branch Prediction

- deeper/superscalar pipeline에서는 branch penalty가 커서 runtime prediction을 사용한다.
- Branch Prediction Buffer 또는 Branch History Table은 최근 branch PC로 index하고 taken/not-taken outcome을 저장한다.
- branch fetch 시 table을 확인해 이전과 같은 outcome을 예상하고 fall-through 또는 target에서 fetch를 시작한다.
- prediction이 틀리면 잘못된 instruction을 flush하고 predictor state를 갱신한다.
- table index에 PC 일부만 사용하면 서로 다른 branch가 같은 entry를 공유하는 aliasing이 생길 수 있다.

## Ch4 PDF 91쪽 / 내부 91쪽 - 1-Bit Predictor의 약점

- 1-bit predictor는 직전 outcome을 다음에도 그대로 예측한다.
- 대부분 taken인 inner loop에서 마지막 iteration의 not-taken을 taken으로 잘못 예측한다.
- state가 not-taken으로 바뀌므로 다음에 inner loop가 다시 시작될 때 첫 taken도 잘못 예측한다.
- 그래서 반복 호출되는 inner loop는 보통 호출당 두 번 misprediction한다.
- branch가 한 loop에서 N번 평가된다면 steady-state miss rate는 대략 `2/N`이다.

## Ch4 PDF 92쪽 / 내부 92쪽 - 2-Bit Predictor

- 네 state는 strongly/weakly taken과 strongly/weakly not-taken이다.
- taken outcome이면 counter를 증가시키고 not-taken이면 감소시키며 끝 state에서 saturate한다.
- upper two state는 taken, lower two state는 not-taken을 예측한다.
- prediction 방향을 바꾸려면 반대 outcome이 두 번 연속 필요하다.
- loop 종료의 not-taken 한 번으로 taken prediction이 바로 뒤집히지 않아 보통 호출당 한 번만 miss한다. 대략 `1/N`이다.

## Ch4 PDF 93쪽 / 내부 93쪽 - Branch Target Buffer

- direction을 taken으로 맞혀도 target address를 계산하는 데 시간이 걸리면 taken branch penalty가 남는다.
- BTB는 branch PC로 index하는 target-address cache다.
- fetch 단계에서 BTB hit이고 predictor도 taken이면 target instruction을 즉시 fetch할 수 있다.
- BTB miss이면 branch임을 알아도 target을 계산할 때까지 기다려야 한다.
- 시험에서는 direction prediction과 target BTB hit를 별도 조건으로 볼 수 있다.

## Ch4 PDF 94쪽 / 내부 94쪽 - Exception과 Interrupt

- 둘 다 예상 밖 사건으로 normal control flow를 바꾸지만 ISA마다 용어 사용이 조금 다르다.
- exception은 CPU 내부에서 발생한다. undefined opcode, arithmetic overflow, syscall이 예다.
- interrupt는 external I/O controller 같은 CPU 외부 장치에서 발생한다.
- 처리 과정은 현재 program state를 보존하고 handler로 control을 옮긴 뒤 가능한 경우 복귀하는 것이다.
- performance를 크게 희생하지 않으면서 precise하게 처리하는 것이 pipeline 설계의 어려운 부분이다.

## Ch4 PDF 95쪽 / 내부 95쪽 - MIPS Exception 처리

- MIPS에서는 System Control Coprocessor CP0가 exception을 관리한다.
- EPC에는 offending 또는 interrupted instruction과 관련된 PC를 저장한다.
- Cause register에는 문제 원인을 저장한다. 강의 단순 모델은 0을 undefined opcode, 1을 overflow로 둔다.
- control은 common handler address `0x80000180`으로 이동한다.
- handler는 EPC와 Cause를 읽어 어떤 instruction에서 무슨 일이 생겼는지 판단한다.

## Ch4 PDF 96쪽 / 내부 96쪽 - Vectored Interrupt

- common handler 대신 cause마다 서로 다른 vector address로 바로 이동하는 방식이다.
- 예시는 undefined opcode `0xC0000000`, overflow `0xC0000020`, 다음 vector `0xC0000040`이다.
- 각 vector entry의 간격은 짧아서 간단한 처리 instruction이나 실제 긴 handler로 가는 jump를 둔다.
- 장점은 Cause를 읽고 긴 분기 chain을 거치지 않고 원인별 entry에 빠르게 도착하는 것이다.
- 대신 vector table과 원인별 address 관리가 필요하다.

## Ch4 PDF 97쪽 / 내부 97쪽 - Handler의 동작

- Cause를 읽어 해당 문제를 처리하는 code로 이동한다.
- restartable exception이면 원인을 수정하고 EPC를 사용해 program으로 돌아간다.
- 예를 들어 page fault라면 page를 memory로 가져온 뒤 faulting instruction을 다시 실행할 수 있다.
- restart할 수 없으면 program을 종료하고 EPC, Cause 등으로 error를 보고한다.
- handler는 architectural state를 보존하고 필요한 register를 save/restore해야 한다.

## Ch4 PDF 98쪽 / 내부 98쪽 - Pipeline에서 Exception

- EX stage의 `add $1,$2,$1`이 overflow하는 상황을 예로 든다.
- faulting add가 `$1`을 잘못 overwrite하지 못하도록 RegWrite를 차단한다.
- add보다 앞선 instruction은 완료시키고 add와 그 이후 younger instruction은 flush한다.
- Cause와 EPC를 기록하고 handler address로 PC를 바꾼다.
- 동작이 branch misprediction과 비슷하므로 flush와 PC redirect hardware를 상당 부분 재사용할 수 있다.

## Ch4 PDF 99쪽 / 내부 99쪽 - Exception Datapath

- 기존 pipeline datapath에 Cause register와 EPC register가 추가된 그림이다.
- exception source에 따라 Cause 값을 기록하고 fault 관련 PC를 EPC에 저장한다.
- PC mux에는 normal PC+4/branch target뿐 아니라 handler `0x80000180` 선택이 추가된다.
- IF.Flush, ID.Flush, EX.Flush control로 faulting instruction과 younger instruction의 side effect를 제거한다.
- exception detection stage에 따라 어느 pipeline register를 flush해야 precise state가 되는지 결정한다.

## Ch4 PDF 100쪽 / 내부 100쪽 - Restartable Exception

- pipeline은 faulting instruction을 flush하고 handler를 실행한 뒤 해당 instruction으로 돌아가 처음부터 다시 실행할 수 있다.
- EPC는 원인을 일으킨 instruction을 식별해야 한다.
- 강의 datapath에서는 실제로 `PC+4`를 저장하므로 handler가 4를 빼서 faulting PC로 보정한다.
- faulting instruction의 register/memory effect가 commit되지 않아야 restart가 가능하다.
- 문제에서 EPC 정의가 별도로 주어지면 그 정의를 따르되, 이 강의 범위에서는 `stored EPC=faulting PC+4`를 사용한다.

## Ch4 PDF 101쪽 / 내부 101쪽 - Exception Example 시작

- instruction sequence는 0x40 sub, 0x44 and, 0x48 or, 0x4C add, 0x50 slt, 0x54 lw 순서다.
- 0x4C의 add에서 overflow exception이 발생한다고 가정한다.
- older sub/and/or는 정상적으로 완료돼야 하고, faulting add와 younger slt/lw는 완료되면 안 된다.
- handler는 `0x80000180`의 `sw $25,1000($0)`, `0x80000184`의 `sw $26,1004($0)` 등으로 state를 저장한다.
- 다음 두 페이지가 exception detection과 flush의 cycle별 변화를 보여 준다.

## Ch4 PDF 102쪽 / 내부 102쪽 - Exception 검출 Cycle

- add가 EX stage에서 overflow를 일으키는 cycle의 datapath다.
- add의 write control을 막고 EX.Flush를 발생시켜 faulting result가 EX/MEM 이후 state를 바꾸지 못하게 한다.
- ID에 있던 slt와 IF에 있던 lw는 younger instruction이므로 ID.Flush와 IF.Flush로 제거한다.
- Cause와 EPC가 기록되고 PC가 handler address로 redirect된다.
- add보다 앞선 or/and instruction은 MEM/WB를 계속 진행해 precise exception 조건을 만족한다.

## Ch4 PDF 103쪽 / 내부 103쪽 - Handler Fetch와 Bubble

- 다음 cycle에는 handler의 첫 instruction `sw $25,1000($0)`이 fetch된다.
- faulting add와 younger instruction이 있던 stage에는 bubble이 이동한다.
- older `or $13,...` 등은 정상적으로 WB까지 완료한다.
- pipeline이 비워진 뒤 handler instruction이 일반 pipeline을 따라 실행된다.
- 그림은 exception이 program-order 경계를 깨끗하게 만든 뒤 handler로 넘어가는 precise 처리 과정을 보여 준다.

## Ch4 PDF 104쪽 / 내부 104쪽 - Multiple Exceptions

- pipeline에는 여러 instruction이 겹쳐 있으므로 한 시점에 여러 exception이 감지될 수 있다.
- simple approach는 program order상 가장 오래된 instruction의 exception을 먼저 처리하고 younger instruction을 flush하는 것이다.
- older instruction은 완료, faulting과 younger instruction은 미완료 상태로 만드는 것을 precise exception이라 한다.
- multiple issue와 out-of-order completion에서는 실행 완료 순서가 program order와 달라 precise exception 유지가 어렵다.
- reorder/commit 구조가 필요한 이유와 연결되는 개념이다.

## Ch4 PDF 105쪽 / 내부 105쪽 - Imprecise Exception

- hardware가 pipeline을 정지하고 현재 state와 여러 exception cause를 저장한 뒤 handler에게 분석을 맡기는 방식이다.
- handler가 어떤 instruction이 exception을 냈고 어떤 instruction을 complete/flush해야 하는지 판단해야 한다.
- 일부 operation을 software가 수동으로 완료해야 할 수도 있다.
- hardware는 단순해지지만 handler software가 매우 복잡해진다.
- 복잡한 multiple-issue out-of-order processor에는 실용적이지 않아 현대 CPU는 일반적으로 precise architectural state를 제공하려 한다.

## Ch4 PDF 106쪽 / 내부 106쪽 - Instruction-Level Parallelism

- pipelining은 여러 instruction의 서로 다른 stage를 동시에 실행해 ILP를 얻는다.
- 더 깊은 pipeline은 stage당 일을 줄여 clock cycle을 짧게 할 수 있다.
- multiple issue는 pipeline resource를 복제해 한 cycle에 여러 instruction을 시작한다.
- CPI가 1보다 작아질 수 있으므로 IPC가 직관적인 지표가 된다. 이상적 관계는 `IPC=1/CPI`다.
- 4 GHz, 4-way issue의 peak는 16 BIPS, IPC 4, CPI 0.25다. 실제는 dependency와 resource conflict 때문에 낮다.

## Ch4 PDF 107쪽 / 내부 107쪽 - Static과 Dynamic Multiple Issue

- static multiple issue에서는 compiler가 함께 issue할 instruction을 issue slot/packet으로 묶고 hazard를 피하도록 scheduling한다.
- hardware는 비교적 단순하지만 compiler가 정확한 latency와 dependency를 알아야 한다.
- dynamic multiple issue에서는 CPU가 runtime instruction stream을 검사해 매 cycle issue 가능한 instruction을 선택한다.
- compiler reordering도 도움을 주지만 최종 hazard와 variable latency는 hardware가 처리한다.
- dynamic 방식은 적응력이 높지만 dependency tracking, scheduling, precise exception 때문에 hardware와 power cost가 크다.

# Chapter 4.5: SIMD 전체

## Ch4.5 PDF 1쪽 - 표지

- Special Chapter의 주제는 SIMD, 즉 Single Instruction Multiple Data다.
- 한 instruction으로 여러 data element에 같은 연산을 적용해 data-level parallelism을 얻는 방식이다.
- 이후 페이지는 SIMD의 개념, register lane, 사용 방법, data layout, matrix multiply, GPU와의 관계를 다룬다.

## Ch4.5 PDF 2쪽 - SIMD 개요

- SIMD는 하나의 instruction이 여러 data를 동시에 처리한다.
- Intel의 MMX, SSE, SSE2, SSE3, SSE4와 ARM NEON이 대표적인 multimedia/vector extension이다.
- 슬라이드의 “2단계: Multimedia 명령 가속”은 subword data를 pack해 audio, image, video operation을 빠르게 하는 발전 단계를 뜻한다.
- scalar instruction 여러 개를 vector instruction 하나로 줄일 수 있어 instruction overhead와 execution time을 낮춘다.

## Ch4.5 PDF 3쪽 / 내부 3쪽 - Subword Data

- modern media codec와 graphics accelerator는 machine word보다 작은 data를 대량 처리한다.
- audio application에는 16-bit data가 흔하고 graphics/video에는 8-bit data가 흔하다.
- 32-bit 또는 128-bit register에 이런 작은 element를 여러 개 담아 동시에 계산하면 hardware를 효율적으로 사용할 수 있다.
- 예를 들어 32-bit register는 8-bit element 4개를 packed lane으로 처리할 수 있다.

## Ch4.5 PDF 4쪽 - SISD와 SIMD

- SISD는 instruction 하나가 data element 하나를 처리한다. 8쌍을 더하려면 ADD가 8번 필요하다.
- SIMD는 instruction 하나가 여러 data element를 처리한다. ARM NEON의 `VADD.I16 Q0,Q1,Q2`는 16-bit element 8쌍을 한 번에 더한다.
- 이상적으로 instruction 수와 arithmetic throughput이 8배 개선될 수 있다.
- Flynn taxonomy는 SISD, SIMD, MISD, MIMD 네 종류다. fault-tolerant system은 MISD 예, multicore는 MIMD 예로 설명된다.
- SIMD의 핵심은 서로 다른 data에 같은 operation을 적용하는 DLP다.

## Ch4.5 PDF 5쪽 - Register Width와 Lane

- lane은 SIMD register를 element width로 나눈 고정 크기 slot이다.
- `lane 수 = register width / element width`다.
- 128-bit는 INT8 16, INT16 8, FP32 4, FP64 2 lanes다.
- 256-bit AVX2는 각각 32, 16, 8, 4 lanes이고 512-bit AVX-512는 64, 32, 16, 8 lanes다.
- Intel은 MMX 64-bit, SSE 128-bit, AVX 256-bit, AVX-512 512-bit로 발전했다. ARM은 VFP, NEON 128-bit, 최대 2048-bit scalable SVE/SVE2로 발전했다.
- width가 넓으면 instruction당 parallelism이 늘지만 chip area와 power도 증가한다.

## Ch4.5 PDF 6쪽 / 내부 6쪽 - SSE2

- SSE2는 128-bit SIMD register를 사용하며 AMD64/EM64T에서는 register 수가 8개로 확장됐다고 설명한다.
- 한 128-bit register는 FP64 2개 또는 FP32 4개를 담아 동시에 연산할 수 있다.
- 슬라이드의 “4×32-bit double precision”은 크기상 “4×32-bit single precision”의 오타다.
- 같은 instruction으로 register 안의 모든 operand를 동시에 처리하는 것이 Single-Instruction Multiple-Data다.

## Ch4.5 PDF 7쪽 / 내부 7쪽 - ARM UADD8

- `UADD8 R0,R1,R2`는 R1과 R2를 각각 8-bit unsigned lane 4개로 나눈다.
- 대응하는 byte 네 쌍을 독립적으로 더해 R0의 네 lane에 저장한다.
- 각 lane은 bit 31~24, 23~16, 15~8, 7~0으로 구분된다.
- lane 사이 carry는 일반 32-bit add처럼 전달되지 않는 packed arithmetic로 이해한다.
- scalar byte add 네 번을 SIMD instruction 한 번으로 대체하는 예다.

## Ch4.5 PDF 8쪽 / 내부 8쪽 - Assembly 구현

- NEON unit을 직접 사용하는 가장 명시적인 방법은 assembly code를 작성하는 것이다.
- 예제 `vadd.i32 q0,q0,q0`는 q0의 각 32-bit lane을 자기 자신과 더해 element를 두 배로 만든다.
- GNU assembler와 ARM RVCT assembler의 syntax 예가 함께 제시된다.
- instruction과 register를 정확히 control할 수 있지만 architecture와 assembler 문법에 종속된다.
- 슬라이드의 portability 표현은 같은 NEON architecture 범위에서 직접 의도를 유지한다는 뜻으로 보고, 다른 ISA로의 source portability는 낮다고 이해해야 한다.

## Ch4.5 PDF 9쪽 / 내부 9쪽 - Inline Assembly

- C 함수 안에 `asm volatile` block으로 NEON instruction을 삽입한다.
- compiler가 surrounding C code와 stack을 관리하지만 operand constraint와 clobber를 programmer가 지정해야 한다.
- 예제는 pixel source/destination, intensity를 register에 연결하고 `vdup.8`, `vld1.8` 같은 instruction으로 byte image를 처리한다.
- gcc/clang 등 compiler syntax에 종속되어 portability가 제한된다.
- 잘못된 constraint나 clobber는 compiler optimization과 충돌해 오동작을 만들 수 있다.

## Ch4.5 PDF 10쪽 / 내부 10쪽 - Automatic Vectorization

- compiler가 ordinary C/C++ loop를 분석해 자동으로 NEON vector instruction으로 변환한다.
- assembly나 intrinsic 없이 source portability를 유지하면서 SIMD performance를 얻을 수 있다.
- compiler가 pointer alias와 loop dependency가 없음을 증명해야 하며, programmer hint가 필요할 수 있다.
- `__restrict`는 pa와 pb가 같은 memory를 가리키지 않는다고 알려 vectorization을 돕는다.
- `n & ~3`은 n을 4의 배수로 내림한다. vector loop 뒤에 남는 `n mod 4` tail은 별도 처리해야 한다.

## Ch4.5 PDF 11쪽 / 내부 11쪽 - Optimized Library

- 검증된 NEON optimized library를 호출하는 것이 가장 쉬운 SIMD 사용 방법이다.
- OpenMAX는 Khronos Group이 배포하는 royalty-free cross-platform API standard다.
- ARM은 ARMv7 NEON에 최적화한 OpenMAX Development Layer implementation을 제공했다.
- programmer는 저수준 register와 instruction 대신 library interface를 사용한다.
- library가 workload와 data format에 맞으면 직접 assembly를 쓰지 않고도 높은 performance를 얻을 수 있다.

## Ch4.5 PDF 12쪽 / 내부 12쪽 - OpenMAX Dot Product

- 두 `OMX_S16` array source1과 source2, 길이 8의 dot product를 `omxSP_DotProd_S16`으로 계산한다.
- `len = sizeof(source1)/sizeof(OMX_S16)`이므로 element 수 8을 얻는다.
- 계산은 `Σ source1[i]×source2[i]`다.
- 각 곱은 10,164, 9,729, 9,664,856, 5,562,176, 139,376, 15,677,621, 10,264,716, 12,982,596이다.
- 합은 `54,311,234`다. 8 multiplication과 accumulator 기준 8 MAC 또는 7 addition이 필요하다.

## Ch4.5 PDF 13쪽 / 내부 13쪽 - Intrinsic

- intrinsic은 C/C++ function call처럼 보이지만 compile 시 low-level SIMD instruction sequence로 바뀐다.
- inline assembly와 비슷한 hardware 기능을 표현하면서 type checking과 automatic register allocation을 제공한다.
- assembly보다 읽고 유지하기 쉽고 compiler optimization과 결합하기 좋다.
- 여전히 특정 ISA의 intrinsic name과 vector type에 종속된다.
- 성능과 programmability 사이의 실용적인 중간 지점이다.

## Ch4.5 PDF 14쪽 - AoS

- SIMD efficiency는 data가 memory에 어떻게 배치되는지에 크게 좌우된다.
- AoS의 `Pixel {r,g,b,a}` array는 `r0,g0,b0,a0,r1,g1,...` 순서다.
- 모든 R 값만 한 vector에 넣으려면 서로 떨어진 address에서 gather하거나 shuffle해야 한다.
- contiguous SIMD load가 어렵고 cache line에서 필요 없는 G/B/A data도 함께 읽는다.
- object 단위 접근은 편하지만 field-wise vector operation에는 불리하다.

## Ch4.5 PDF 15쪽 - SoA와 Alignment

- SoA는 R array, G array, B array, A array를 따로 둬 `r0,r1,r2,...`처럼 같은 field를 연속 배치한다.
- 한 SIMD load가 여러 R value를 한 번에 모든 lane에 채울 수 있어 vectorization과 cache use가 좋아진다.
- SSE는 16-byte, AVX는 32-byte boundary alignment가 중요하다. AVX-512의 자연 alignment는 64 bytes다.
- unaligned access는 가능해도 cache-line/page boundary를 넘으면 penalty가 커질 수 있다.
- C/C++의 `__attribute__((aligned(32)))`, `alignas(32)` 같은 방법으로 alignment를 보장할 수 있다.

## Ch4.5 PDF 16쪽 / 내부 16쪽 - Scalar DGEMM

- 세 nested loop로 double matrix multiply를 수행한다.
- `cij = C[i+j*n]`을 register variable에 두고 inner k loop에서 `cij += A[i+k*n]×B[k+j*n]`을 반복한다.
- 마지막에 C element를 memory에 한 번 저장해 반복 load/store를 줄인다.
- 정사각 n×n matrix는 `n^3 MAC`, 약 `2n^3 FLOPs`가 필요하다.
- 아직 vector instruction과 blocking을 사용하지 않은 baseline code다.

## Ch4.5 PDF 17쪽 / 내부 17쪽 - AVX-512 Intrinsic DGEMM

- outer i loop를 `i+=8`로 진행해 FP64 element 8개를 한 번에 처리한다.
- `__m512d`는 512-bit vector로 double 8개를 담는다.
- C 8개를 load하고, B scalar 하나를 모든 lane에 broadcast한다.
- A의 연속 8개 element와 B vector를 `_mm512_fmadd_pd`로 곱해 C vector에 누적한다.
- 한 vector FMA는 8 multiply와 8 add, 총 16 FLOPs를 수행한다.
- 끝에 C 8개를 store한다. alignment와 n의 tail 처리가 필요하다.

## Ch4.5 PDF 18쪽 / 내부 18쪽 - AVX-512 Assembly DGEMM

- `vmovapd`는 aligned C vector 8개를 zmm1에 load한다.
- `vbroadcastsd`는 B scalar를 zmm0의 모든 FP64 lane에 복제한다.
- `vfmadd231pd`는 memory의 A vector, zmm0, zmm1로 parallel multiply-add를 수행한다.
- pointer와 loop counter를 update하고 compare/jump로 k loop를 반복한다.
- 마지막 `vmovapd`가 계산된 C vector 8개를 memory에 저장한다.
- intrinsic code가 실제 vector load, broadcast, FMA, pointer arithmetic instruction으로 변환되는 모습을 보여 준다.

## Ch4.5 PDF 19쪽 - SIMD에서 GPU로

- CPU SIMD가 data parallelism의 기본이며 GPU와 AI chip은 이를 대규모로 확장한 형태다.
- AVX-512는 core당 FP32 16 lanes다. 24-core CPU이면 최대 `24×16=384 FP32 lanes`다.
- 슬라이드는 GPU SM 하나를 32-thread warp 네 묶음, 총 128-wide 실행 자원으로 단순화해 설명한다.
- RTX 4070의 46 SM에 128 FP32 unit을 곱하면 `46×128=5,888 units`다.
- unit 수 비는 `5,888/384≈15.3`이지만 실제 speedup은 clock, issue rate, memory와 utilization에 따라 달라진다.

## Ch4.5 PDF 20쪽 - Tensor Core

- Tensor Core는 vector의 1D SIMD를 matrix operation으로 확장한 2D SIMD/MAC array로 설명된다.
- NVIDIA Volta 세대인 2017년에 도입됐으며 슬라이드는 4×4 matrix multiply-accumulate를 한 cycle에 수행한다고 설명한다.
- RTX 4070의 184 Tensor Core에 core당 256 MAC를 곱하면 `47,104 MAC/cycle`이다.
- multiply와 add를 각각 operation으로 세면 `94,208 operations/cycle`로 볼 수 있다.
- CPU SIMD는 general-purpose, GPU/Tensor Core는 data-parallel matrix math에 더 특화됐다는 차이가 있다.

## Ch4.5 PDF 21쪽 - AVX-512의 장점과 한계

- AVX-512는 결과 latency가 몇 cycle로 짧아 latency-sensitive code에 유리하다.
- scalar code와 SIMD code를 같은 program에서 자유롭게 섞고 CPU cache의 data를 바로 사용하므로 별도 PCIe transfer가 없다.
- 한계는 512-bit, FP32 16 lanes의 width ceiling과 넓은 vector hardware의 area/power cost다.
- AVX-512를 강하게 사용하면 power density 때문에 CPU clock이 낮아질 수 있다.
- 24 core×16 lanes=384로 GPU의 5,888 unit보다 약 15배 적다는 비교가 다시 제시된다.

## Ch4.5 PDF 22쪽 - GPU의 장점과 결론

- GPU는 branch prediction과 복잡한 out-of-order logic보다 수천 개의 simple core와 throughput에 transistor를 집중한다.
- HBM bandwidth는 최대 약 3 TB/s로 제시되며 CPU DDR5 약 100 GB/s보다 약 30배 높다.
- large-model weight와 activation을 공급하려면 arithmetic unit뿐 아니라 memory bandwidth가 중요하다.
- CPU SIMD와 GPU는 경쟁해 하나가 다른 하나를 없애는 관계가 아니다.
- CPU SIMD는 low-latency mixed workload, GPU는 high-throughput parallel compute에 적합하므로 서로 보완한다.

# Chapter 5: Memory Hierarchy

## Ch5 PDF 1쪽 - 표지

- 주제는 “Large and Fast: Exploiting Memory Hierarchy”다.
- 빠른 memory는 작고 비싸며, 큰 memory는 느리고 저렴한 물리적 trade-off를 locality와 caching으로 완화한다.
- cache, DRAM, disk, virtual memory, TLB, coherence까지 하나의 hierarchy 원리로 연결한다.

## Ch5 PDF 2쪽 / 내부 2쪽 - Principle of Locality

- program은 한 시점에 전체 address space 중 작은 부분만 집중적으로 접근한다.
- temporal locality는 최근 접근한 item을 곧 다시 사용할 가능성이다. loop instruction, induction variable, accumulator가 예다.
- spatial locality는 최근 접근 주소 근처를 곧 사용할 가능성이다. sequential instruction과 array traversal이 예다.
- memory hierarchy는 이 두 성질을 이용해 자주 쓰는 작은 working set을 빠른 upper level에 둔다.

## Ch5 PDF 3쪽 / 내부 3쪽 - Locality 활용

- 전체 data는 disk 같은 크고 느린 storage에 저장할 수 있다.
- 최근 접근했거나 그 근처에 있는 item을 disk에서 더 작은 DRAM main memory로 복사한다.
- DRAM에서 더 최근에 자주 사용하는 item은 CPU 가까운 SRAM cache로 복사한다.
- upper level로 갈수록 작고 빠르고 비싸며, lower level로 갈수록 크고 느리고 저렴하다.

## Ch5 PDF 4쪽 / 내부 4쪽 - Memory Hierarchy 용어

- block 또는 line은 level 사이에서 이동하는 data 단위이며 여러 word를 포함할 수 있다.
- requested data가 upper level에 있으면 hit이고 `hit ratio=hits/accesses`다.
- 없으면 miss이며 lower level에서 block을 복사해야 한다. 추가 시간이 miss penalty다.
- `miss ratio=misses/accesses=1-hit ratio`다.
- miss가 끝나면 data는 upper level에서 CPU에 제공되므로 이후 근처 접근이 hit가 될 수 있다.

## Ch5 PDF 5쪽 / 내부 5쪽 - Memory Technology

- SRAM latency는 약 0.5~2.5 ns, cost는 $500~$1000/GB로 빠르지만 비싸다.
- DRAM은 약 50~70 ns, $3~$6/GB로 SRAM보다 느리고 저렴하다.
- magnetic disk는 약 5~20 ms, $0.01~$0.02/GB로 매우 크고 싸지만 극도로 느리다.
- ideal memory는 SRAM speed와 disk capacity/cost를 모두 갖는 것이지만 한 technology로는 불가능하다.
- 계층을 구성해 CPU가 대부분 빠른 level을 보는 것처럼 만든다.

## Ch5 PDF 6쪽 / 내부 6쪽 - DRAM Technology

- DRAM bit는 capacitor의 charge로 저장한다.
- 한 transistor가 charge에 접근하지만 시간이 지나면 charge가 사라져 주기적 refresh가 필요하다.
- refresh는 contents를 읽고 다시 write하는 과정이다.
- DRAM은 row 단위로 access/refresh하므로 row organization과 row buffer가 performance에 중요하다.
- SRAM보다 cell이 단순하고 density가 높지만 refresh와 sensing 때문에 느리다.

## Ch5 PDF 7쪽 / 내부 7쪽 - Advanced DRAM Organization

- DRAM bit는 rectangular array로 구성되고 access 시 entire row를 읽는다.
- burst mode는 한 row에서 successive word를 각 address 없이 연속 공급해 latency를 amortize한다.
- DDR은 clock rising edge와 falling edge 모두에서 data를 transfer한다.
- QDR은 input과 output에 별도 DDR path를 사용해 양방향 throughput을 높인다.
- locality가 좋아 같은 open row를 반복 접근하면 row-buffer hit로 더 빠르게 처리할 수 있다.

## Ch5 PDF 8쪽 / 내부 8쪽 - DRAM 세대 변화

- 1980년 64 Kibibit부터 2018년 16 Gibibit까지 chip capacity가 크게 증가한 표다.
- 장기적으로 cost/GB는 수백만 달러 수준에서 한 자릿수 달러까지 급격히 감소했다.
- 세대가 발전하며 density와 capacity가 증가했지만 latency 개선은 CPU performance 증가보다 느렸다.
- 그래서 memory wall을 완화할 cache hierarchy와 bandwidth 기술이 더 중요해졌다.
- 연도별 숫자 암기보다 capacity 증가와 cost/bit 하락의 추세가 핵심이다.

## Ch5 PDF 9쪽 / 내부 9쪽 - DRAM Performance Factor

- row buffer는 여러 word를 parallel하게 읽고 refresh할 수 있게 한다.
- synchronous DRAM은 clock에 맞춰 동작하고 consecutive burst에서 매번 address를 보내는 overhead를 줄인다.
- DRAM banking은 여러 bank를 서로 겹쳐 access해 bandwidth를 높인다.
- 이 기법들은 첫 access latency를 완전히 없애기보다 여러 transfer를 overlap하거나 amortize해 throughput을 개선한다.

## Ch5 PDF 10쪽 / 내부 10쪽 - Memory Bandwidth 증가

- 4-word-wide memory는 address 1, DRAM 15, transfer 1 cycle로 총 17 cycles에 16 bytes를 보낸다.
- bandwidth는 `16/17≈0.94 B/cycle`이다.
- 4-bank interleaved memory 예는 address 1, first latency 15, 네 word transfer 4로 총 20 cycles다.
- bandwidth는 `16/20=0.8 B/cycle`이다.
- wide memory와 interleaving은 구조와 overlap 가정이 다르므로 숫자만 보고 일반적인 우열로 해석하면 안 된다.

## Ch5 PDF 11쪽 / 내부 11쪽 - Flash Storage

- flash는 power가 없어도 data를 유지하는 nonvolatile semiconductor storage다.
- disk보다 약 100~1000배 빠르고, 작고, power가 낮고, mechanical shock에 강하다.
- cost/GB는 disk보다 높고 DRAM보다 낮은 중간 수준이다.
- SSD와 embedded storage가 memory hierarchy에서 disk를 대체하거나 보완한다.

## Ch5 PDF 12쪽 / 내부 12쪽 - NOR와 NAND Flash

- NOR flash는 random read/write access가 가능해 embedded instruction memory에 사용된다.
- NAND flash는 density가 높고 block 단위 access를 하며 cost/GB가 낮아 USB와 media storage, SSD에 쓰인다.
- flash cell은 수천 회 수준의 program/erase 후 wear가 누적될 수 있다.
- 그래서 RAM처럼 매 access write하거나 단순 disk replacement로 쓰기 어렵다.
- wear leveling은 write를 덜 사용한 physical block으로 remap해 수명을 늘린다.

## Ch5 PDF 13쪽 / 내부 13쪽 - Disk Storage

- rotating magnetic platter와 read/write head를 사용하는 nonvolatile storage의 구조 그림이다.
- data는 track과 sector에 기록되고 head를 원하는 track으로 이동한 뒤 platter 회전을 기다려 읽는다.
- mechanical movement 때문에 semiconductor memory보다 latency가 매우 크다.
- 대신 capacity가 크고 cost/GB가 매우 낮다.

## Ch5 PDF 14쪽 / 내부 14쪽 - Disk Sector와 Access

- sector에는 sector ID, 512 B 또는 4096 B data, ECC, synchronization field와 gap이 포함된다.
- access time은 queue delay, seek, rotational latency, data transfer, controller overhead로 구성된다.
- seek는 head를 track으로 이동하는 시간이고 rotation은 sector가 head 아래 올 때까지 기다리는 시간이다.
- ECC는 media defect와 recording error를 감지/교정한다.
- 전체 latency는 단순 transfer time보다 mechanical delay가 훨씬 큰 경우가 많다.

## Ch5 PDF 15쪽 / 내부 15쪽 - Disk Access 계산

- 조건은 512 B sector, 15,000 RPM, average seek 4 ms, 100 MB/s transfer, controller 0.2 ms다.
- `15,000/60=250 rotations/s`, 한 바퀴는 4 ms, 평균 rotation은 반 바퀴인 2 ms다.
- transfer time은 `512/100 MB/s≈0.005 ms`다.
- 전체는 `4+2+0.005+0.2≈6.2 ms`다.
- 실제 average seek가 locality와 scheduling으로 1 ms라면 전체는 약 3.2 ms가 된다.

## Ch5 PDF 16쪽 / 내부 16쪽 - Disk Performance 문제

- 제조사 average seek는 가능한 모든 seek를 평균한 값이지만 실제 workload locality와 OS scheduling은 더 짧은 seek를 만들 수 있다.
- smart controller는 physical sector placement를 관리하고 host에는 logical sector interface를 제공한다.
- SCSI, ATA, SATA가 대표 disk interface다.
- drive 내부 cache가 앞으로 필요할 sector를 prefetch하면 seek와 rotational delay를 피할 수 있다.
- quoted specification과 application에서 관측한 latency가 다를 수 있다.

## Ch5 PDF 17쪽 / 내부 17쪽 - Cache Memory

- cache는 CPU에 가장 가까운 memory hierarchy level이다.
- 이전 access sequence를 바탕으로 다음 requested data가 cache에 있는지, 있다면 어디에 있는지를 빠르게 판단해야 한다.
- 핵심 질문은 placement, lookup, replacement, write policy다.
- 이후 direct-mapped cache가 가장 단순한 답을 제공한다.

## Ch5 PDF 18쪽 / 내부 18쪽 - Direct-Mapped Cache

- memory block이 갈 cache location은 하나뿐이다.
- 공식은 `cache index = block address mod number of cache blocks`다.
- block 수가 2의 거듭제곱이면 block address의 low-order bit를 index로 바로 사용한다.
- lookup이 빠르고 comparator가 하나만 필요하지만 같은 index를 공유하는 block이 반복 교체되는 conflict miss가 생긴다.

## Ch5 PDF 19쪽 / 내부 19쪽 - Tag와 Valid Bit

- index만으로는 현재 entry에 어떤 memory block이 있는지 알 수 없어 address의 high-order bit를 tag로 저장한다.
- access tag와 stored tag가 같아야 requested block이다.
- entry에 유효한 data가 있는지를 valid bit로 표시한다. 1은 present, 0은 invalid다.
- 초기에는 valid=0이므로 tag bit가 우연히 같아도 hit가 아니다.
- hit 조건은 `valid AND tag match`다.

## Ch5 PDF 20쪽 / 내부 20쪽 - Cache Example 초기 상태

- 8-block, 1-word/block direct-mapped cache의 빈 상태다.
- index는 000~111의 3 bits다.
- 모든 valid가 N이므로 아직 어떤 access도 hit할 수 없다.
- word address를 직접 사용하므로 이 예에서는 `index=word address mod 8`이다.
- 다음 페이지부터 access sequence에 따라 tag와 data가 채워진다.

## Ch5 PDF 21쪽 / 내부 21쪽 - Word Address 22

- word address 22는 binary `10110`이다.
- low 3 bits `110`이 cache index 6이고 upper bits `10`이 tag다.
- index 6이 invalid이므로 miss가 발생한다.
- cache[6]에 tag 10과 `Mem[10110]` data를 넣고 valid를 Y로 바꾼다.

## Ch5 PDF 22쪽 / 내부 22쪽 - Word Address 26

- word address 26은 binary `11010`이다.
- low 3 bits `010`이 index 2, upper bits `11`이 tag다.
- index 2가 invalid이므로 miss다.
- cache[2]에 tag 11과 `Mem[11010]`을 저장한다.
- 이제 index 6의 block 22와 index 2의 block 26이 동시에 cache에 있다.

## Ch5 PDF 23쪽 / 내부 23쪽 - 22와 26 재접근

- address 22는 index 6의 valid가 Y이고 tag 10이 match해 hit다.
- address 26도 index 2의 tag 11이 match해 hit다.
- 같은 block의 반복 access가 temporal locality로 hit가 되는 예다.
- hit에서는 lower memory access 없이 cache data를 즉시 사용한다.

## Ch5 PDF 24쪽 / 내부 24쪽 - 16, 3, 16

- address 16은 binary `10000`, index 0, tag 10이며 처음에는 miss 후 cache[0]에 들어간다.
- address 3은 `00011`, index 3, tag 00이며 miss 후 cache[3]에 들어간다.
- 다시 address 16을 접근하면 cache[0]의 tag 10이 유지돼 hit다.
- 서로 다른 index를 사용하는 block은 서로 conflict하지 않는다.

## Ch5 PDF 25쪽 / 내부 25쪽 - Address 18의 Conflict

- address 18은 binary `10010`, index 2, tag 10이다.
- cache[2]에는 이전 address 26의 tag 11이 있으므로 valid여도 tag mismatch로 miss다.
- direct-mapped cache는 다른 선택지가 없어 address 26 block을 address 18 block으로 교체한다.
- 이후 26을 다시 access하면 conflict miss가 발생한다.
- valid bit만 보지 않고 tag 비교가 반드시 필요한 이유다.

## Ch5 PDF 26쪽 / 내부 26쪽 - Address Subdivision

- 32-bit byte address, 1024-entry direct-mapped cache, 1 word=4 B/block의 구조다.
- byte offset은 `log2(4)=2 bits`, index는 `log2(1024)=10 bits`, tag는 `32-10-2=20 bits`다.
- index로 한 entry를 선택하고 stored 20-bit tag와 address tag를 비교한다.
- valid와 tag match의 AND가 hit signal이다.
- block 안에는 word 하나뿐이므로 byte offset이 그 word의 byte를 선택한다.

## Ch5 PDF 27쪽 / 내부 27쪽 - Larger Block Mapping

- cache는 64 blocks, block size는 16 bytes다.
- byte address 1200의 memory block address는 `floor(1200/16)=75`다.
- cache block은 `75 mod 64=11`이다.
- 32-bit address에서 offset 4 bits, index 6 bits, tag 22 bits다.
- byte address를 먼저 block address로 나눈 뒤 modulo해야 한다.

## Ch5 PDF 28쪽 / 내부 28쪽 - Block Size Trade-off

- 큰 block은 nearby data를 함께 가져와 spatial locality를 활용하므로 compulsory miss를 줄일 수 있다.
- cache total size가 고정이면 block이 커질수록 block 수가 줄어 capacity/conflict pressure가 증가한다.
- 필요 없는 data가 cache를 차지하는 pollution이 생길 수 있다.
- transfer data가 커져 miss penalty도 증가한다.
- critical-word-first와 early restart는 requested word를 먼저 제공해 긴 block transfer의 체감 penalty를 줄인다.

## Ch5 PDF 29쪽 / 내부 29쪽 - Cache Miss 처리

- hit이면 CPU가 normal pipeline execution을 계속한다.
- miss이면 CPU pipeline을 stall하고 next memory level에서 block을 가져온다.
- instruction-cache miss는 instruction fetch를 다시 시작한다.
- data-cache miss는 load/store data access를 완료한 뒤 instruction을 계속한다.
- miss handling은 cache controller와 lower-level memory protocol이 담당한다.

## Ch5 PDF 30쪽 / 내부 30쪽 - Write-Through

- write hit에서 cache만 update하면 main memory와 값이 달라진다.
- write-through는 cache와 memory를 모두 update해 consistency를 단순화한다.
- base CPI 1, store 10%, memory write 100 cycles이면 `CPI=1+0.1×100=11`로 매우 느려진다.
- write buffer에 pending write를 넣으면 CPU는 buffer가 차지 않은 동안 계속 실행할 수 있다.
- buffer가 full이면 CPU가 stall하므로 write latency가 완전히 사라지는 것은 아니다.

## Ch5 PDF 31쪽 / 내부 31쪽 - Write-Back

- write hit에서는 cache block만 update하고 dirty bit를 1로 설정한다.
- dirty block이 replacement될 때 lower memory에 한 번 write-back한다.
- 같은 block에 여러 write가 있어도 lower level write를 줄일 수 있다.
- replacement 시 dirty victim write와 new block read가 모두 필요해 miss handling이 복잡하다.
- write buffer를 사용하면 old block write와 new block fetch 일부를 overlap할 수 있다.

## Ch5 PDF 32쪽 / 내부 32쪽 - Write Miss Policy

- write miss에서 block을 먼저 cache로 가져오는 것이 write allocate다.
- block을 가져오지 않고 lower memory에 바로 쓰는 것이 write around 또는 no-write-allocate다.
- write-through는 write around를 사용할 수 있으며 initialization처럼 block 전체를 곧 write할 workload에 유리할 수 있다.
- write-back은 보통 write allocate를 사용해 이후 write를 cache에 모은다.
- 시험에서는 write hit/miss와 write policy 조합을 분리해 trace해야 한다.

## Ch5 PDF 33쪽 / 내부 33쪽 - Intrinsity FastMATH Cache

- embedded MIPS processor이며 12-stage pipeline, 매 cycle instruction access와 필요 시 data access를 한다.
- split cache로 16 KiB I-cache와 16 KiB D-cache를 둔다.
- 각 cache는 256 blocks, block당 16 words이므로 block size는 64 bytes다.
- D-cache는 write-through 또는 write-back을 지원한다.
- SPEC2000 miss rate는 I-cache 0.4%, D-cache 11.4%, access 수를 반영한 weighted average 3.2%다.

## Ch5 PDF 34쪽 / 내부 34쪽 - FastMATH Cache 구조

- 32-bit address는 tag 18 bits, index 8 bits, block/byte offset 6 bits로 나뉜다.
- 8-bit index가 256 entry 중 하나를 선택하고 18-bit tag와 valid를 비교한다.
- 한 block은 16 words, 즉 64 bytes=512 data bits다.
- block offset 중 상위 4 bits가 16 words 중 하나를 mux로 선택하고 하위 2 bits가 byte 위치다.
- valid만 포함한 총 storage는 `256×(512+18+1)=135,936 bits=16,992 bytes`로 data capacity 16 KiB보다 크다.

## Ch5 PDF 35쪽 / 내부 35쪽 - Cache를 지원하는 Main Memory

- DRAM main memory와 cache는 fixed-width clocked bus로 연결되고 bus clock은 CPU보다 느릴 수 있다.
- 1-word-wide DRAM에서 4-word block을 읽으면 address 1 cycle, 각 word DRAM access `4×15`, transfer `4×1`이다.
- miss penalty는 `1+4×15+4×1=65 bus cycles`다.
- 16 bytes를 65 cycles에 보내므로 bandwidth는 `16/65≈0.25 B/cycle`이다.
- wider memory와 interleaving은 이 긴 block fill time을 줄이는 방법이다.

## Ch5 PDF 36쪽 / 내부 36쪽 - Cache Performance 측정

- CPU time은 ideal program execution cycles와 memory stall cycles로 나눌 수 있다.
- cache hit time은 기본 execution에 포함되고, 주된 추가 stall은 miss에서 발생한다.
- `Memory stall cycles = memory accesses/program × miss rate × miss penalty`다.
- instruction당 형태는 `stall CPI = misses/instruction × miss penalty`다.
- I-cache와 D-cache를 분리하면 두 stall CPI를 각각 계산해 base CPI에 더한다.

## Ch5 PDF 37쪽 / 내부 37쪽 - Cache Performance 계산 예

- I miss rate 2%, D miss rate 4%, miss penalty 100 cycles, base CPI 2, load/store 36%다.
- I-cache stall은 `0.02×100=2 cycles/instruction`이다.
- D-cache stall은 `0.36×0.04×100=1.44`다.
- actual CPI는 `2+2+1.44=5.44`다.
- ideal-cache CPU와 같은 clock/instruction count라면 performance ratio는 `5.44/2=2.72`, 즉 ideal CPU가 2.72배 빠르다.

## Ch5 PDF 38쪽 / 내부 38쪽 - AMAT

- hit time도 0이 아니므로 miss rate만으로 memory performance를 판단할 수 없다.
- `AMAT = hit time + miss rate×miss penalty`다.
- clock 1 ns, hit time 1 cycle, miss penalty 20 cycles, miss rate 5%이면 `1+0.05×20=2 ns`다.
- 평균적으로 access당 2 cycles가 걸린다는 뜻이다.
- miss penalty가 hit time을 포함한 total인지 추가 penalty인지 문제 정의를 확인해야 한다.

## Ch5 PDF 39쪽 / 내부 39쪽 - Cache Performance 요약

- CPU base CPI가 낮아질수록 같은 miss stall이 전체 실행 시간에서 더 큰 비율을 차지한다.
- clock rate가 올라가면 같은 absolute memory latency가 더 많은 CPU cycle로 환산된다.
- processor가 빨라져도 DRAM latency가 비례해 빨라지지 않아 memory wall이 커진다.
- system performance를 평가할 때 cache behavior를 무시할 수 없다.
- peak core performance만 높이고 memory hierarchy를 개선하지 않으면 실제 speedup이 제한된다.

## Ch5 PDF 40쪽 / 내부 40쪽 - Associative Cache

- fully associative cache에서는 memory block이 어떤 entry에도 들어갈 수 있다.
- 모든 entry tag를 동시에 search해야 해 entry당 comparator가 필요하고 비싸다.
- n-way set associative는 block number로 set 하나를 고른 뒤 set 안의 n entry를 병렬 비교한다.
- `set = block number mod number of sets`다.
- associativity가 커질수록 placement 선택이 늘어 conflict miss가 줄지만 hit time, hardware cost와 energy가 증가한다.

## Ch5 PDF 41쪽 / 내부 41쪽 - Placement와 Search 비교

- 8-entry direct-mapped는 block number가 entry 하나를 정하고 comparator 하나만 사용한다.
- set-associative는 먼저 set을 고른 뒤 그 set의 여러 way tag를 search한다.
- fully associative는 전체 8 entry를 모두 search한다.
- 같은 block이 들어갈 수 있는 위치가 1개, n개, 전체로 늘어나는 대신 comparator 수도 증가한다.
- 그림의 search arrow 수가 lookup hardware cost를 나타낸다.

## Ch5 PDF 42쪽 / 내부 42쪽 - Associativity Spectrum

- 총 8 entry cache를 1-way, 2-way, 4-way, 8-way로 구성한 표다.
- 1-way는 8 sets×1 way로 direct-mapped다.
- 2-way는 4 sets×2 ways, 4-way는 2 sets×4 ways다.
- 8-way는 1 set×8 ways로 fully associative와 같다.
- total data capacity는 같고 set 수와 way 수만 달라진다.

## Ch5 PDF 43쪽 / 내부 43쪽 - Direct-Mapped Trace

- 4-block cache에서 access sequence `0,8,0,6,8`을 비교한다.
- direct-mapped에서 0과 8은 모두 index 0으로 mapping돼 서로를 계속 교체한다.
- 6은 index 2에 들어가지만 0/8 conflict에는 영향을 주지 않는다.
- sequence의 다섯 access가 모두 miss다.
- cache total space가 남아 있어도 fixed index 때문에 생기는 전형적인 conflict miss다.

## Ch5 PDF 44쪽 / 내부 44쪽 - 2-Way와 Fully Associative Trace

- 2-way, 4 total blocks이면 2 sets인데 0,8,6은 모두 set 0으로 mapping된다.
- 0과 8을 함께 보관해 세 번째 0은 hit지만 6을 넣으며 replacement가 필요해 마지막 8은 miss다.
- fully associative는 0,8,6을 서로 다른 entry에 보관할 수 있다.
- fully associative의 세 번째 0과 마지막 8은 모두 hit다.
- associativity가 conflict miss를 줄이지만 replacement policy가 필요해진다.

## Ch5 PDF 45쪽 / 내부 45쪽 - Associativity의 효과

- 64 KiB D-cache, 16-word block, SPEC2000 simulation의 miss rate다.
- 1-way 10.3%, 2-way 8.6%, 4-way 8.3%, 8-way 8.1%다.
- 1-way에서 2-way로 갈 때 큰 개선이 있지만 그 이후 개선 폭은 작다.
- associativity 증가에는 diminishing return이 있다.
- 따라서 miss rate 감소와 hit time/area/power 증가 사이에서 적절한 way 수를 선택한다.

## Ch5 PDF 46쪽 / 내부 46쪽 - 4-Way Set-Associative 조직

- 예는 총 1024 block, 1 word/block을 4-way로 구성해 256 sets를 만든다.
- 32-bit byte address에서 byte offset 2 bits, set index 8 bits, tag 22 bits다.
- index가 네 way의 같은 set row를 동시에 선택한다.
- 네 tag comparator의 match를 OR해 hit를 만들고, 4-to-1 mux로 matching way data를 선택한다.
- direct-mapped보다 comparator와 mux가 늘지만 conflict miss가 줄어든다.

## Ch5 PDF 47쪽 / 내부 47쪽 - Replacement Policy

- direct-mapped는 placement가 하나라 replacement 선택이 없다.
- set-associative에서는 우선 invalid entry가 있으면 그것을 사용한다.
- set이 가득 차면 LRU 또는 random policy로 victim을 선택한다.
- LRU는 가장 오래 사용하지 않은 entry를 교체하며 2-way는 간단하지만 high associativity에서는 정확한 구현이 어렵다.
- high associativity에서는 random도 LRU와 비슷한 performance를 보일 수 있어 구현 비용을 줄인다.

## Ch5 PDF 48쪽 / 내부 48쪽 - Multilevel Cache

- L1은 CPU에 붙은 작고 빠른 primary cache다.
- L1 miss는 더 크고 느리지만 main memory보다 빠른 L2가 처리한다.
- L2 miss는 main memory가 처리하며 high-end system에는 L3도 있다.
- 각 level은 아래 level의 latency를 숨기는 cache 역할을 한다.
- L1은 hit time, lower-level cache는 miss rate와 capacity를 더 중시한다.

## Ch5 PDF 49쪽 / 내부 49쪽 - L1만 있는 계산 예

- base CPI 1, clock 4 GHz, miss/instruction 2%, main memory 100 ns다.
- cycle time은 `1/4 GHz=0.25 ns`다.
- memory miss penalty는 `100/0.25=400 cycles`다.
- effective CPI는 `1+0.02×400=9`다.
- memory miss 때문에 ideal CPI 1 processor가 실제로는 instruction당 9 cycles를 사용한다.

## Ch5 PDF 50쪽 / 내부 50쪽 - L2 추가

- L2 access time 5 ns이므로 `5/0.25=20 cycles`다.
- main memory까지 가는 global miss rate는 instruction당 0.5%다.
- CPI는 `1+0.02×20+0.005×400=3.4`다.
- L1-only CPI 9와 비교하면 `9/3.4≈2.6배` 빨라진다.
- 슬라이드의 “Extra penalty=500 cycles” 문구는 계산식과 모순이다. 100 ns/0.25 ns는 400 cycles이며 아래 CPI도 400을 사용한다.
- L2 local miss rate는 `0.005/0.02=25%`다.

## Ch5 PDF 51쪽 / 내부 51쪽 - Multilevel 설계 고려

- L1은 processor clock과 직접 연결되므로 minimal hit time이 가장 중요하다.
- L2는 L1 miss를 받고 main memory access를 막는 것이 목적이므로 low miss rate가 중요하다.
- L2 hit time은 L1 hit time보다 전체 performance에 덜 민감하다.
- 일반적으로 L1은 단일 큰 cache보다 작고, L1 block도 L2 block보다 작을 수 있다.
- level마다 최적화 목표가 다르다.

## Ch5 PDF 52쪽 / 내부 52쪽 - Advanced CPU와 Cache Miss

- out-of-order CPU는 cache miss를 기다리는 동안 independent instruction을 실행할 수 있다.
- pending store는 load/store unit에 남고 dependent instruction은 reservation station에서 기다린다.
- miss와 무관한 instruction은 functional unit을 계속 사용한다.
- miss의 실제 영향은 program data-flow와 memory-level parallelism에 따라 달라진다.
- 단순 AMAT만으로 out-of-order performance를 정확히 분석하기 어려워 system simulation을 사용한다.

## Ch5 PDF 53쪽 / 내부 53쪽 - Software와 Memory Access Pattern

- miss rate는 hardware cache specification뿐 아니라 program의 memory access pattern에 달려 있다.
- algorithm이 data를 어떤 순서로 방문하는지, compiler가 loop와 data layout을 어떻게 최적화하는지가 중요하다.
- contiguous row traversal은 spatial locality가 좋고 큰 stride의 column traversal은 나쁠 수 있다.
- 같은 arithmetic을 해도 access order를 바꾸면 cache performance가 크게 달라진다.

## Ch5 PDF 54쪽 / 내부 54쪽 - Blocking의 목적

- 목표는 data가 cache에서 replacement되기 전에 최대한 많이 재사용하는 것이다.
- unblocked DGEMM inner loop는 `C[i,j]`를 유지하며 A row/column과 B column을 순회한다.
- matrix가 cache보다 크면 A와 B를 다시 읽어 capacity miss가 많아진다.
- matrix를 작은 tile로 나눠 cache에 맞추면 같은 tile을 여러 arithmetic에 재사용할 수 있다.
- blocking은 MAC 수를 바꾸지 않고 data movement를 줄인다.

## Ch5 PDF 55쪽 / 내부 55쪽 - Unoptimized DGEMM Access Pattern

- C, A, B matrix에서 loop가 진행될 때 old/new access 영역을 그림으로 보여 준다.
- C의 한 element를 계산하는 동안 A와 B를 긴 방향으로 순회해 working set이 커질 수 있다.
- 특히 column-major/row-major layout과 loop 순서가 맞지 않으면 큰 stride가 생긴다.
- matrix가 커질수록 이전에 읽은 block이 재사용 전에 cache에서 밀려난다.
- 다음 blocked pattern과 비교해 locality 차이를 이해하는 페이지다.

## Ch5 PDF 56쪽 / 내부 56쪽 - Blocked DGEMM Code

- `BLOCKSIZE=32`로 A, B, C의 작은 submatrix를 처리하는 `do_block` 함수를 사용한다.
- outer `sj,si,sk` loop가 matrix를 32×32 tile로 나눈다.
- 한 tile 조합에서 C tile을 반복 update해 A/B tile을 cache에 있는 동안 재사용한다.
- double tile 하나는 `B^2×8 bytes`, 세 tile working set은 대략 `3B^2×8`이다.
- usable cache capacity 안에 세 tile이 들어가도록 block size를 정하는 것이 기본 원리다.

## Ch5 PDF 57쪽 / 내부 57쪽 - Blocked Access Pattern

- unoptimized는 matrix의 넓은 영역을 길게 훑지만 blocked는 작은 사각형 영역을 집중적으로 반복한다.
- A, B, C의 현재 tile이 cache에 머무는 동안 많은 MAC가 수행된다.
- temporal locality와 spatial locality가 모두 향상된다.
- arithmetic intensity가 높아지고 lower memory traffic이 감소한다.
- block이 너무 크면 cache에 안 들어가고 너무 작으면 loop overhead와 reuse 기회가 줄어든다.

## Ch5 PDF 58쪽 / 내부 58쪽 - Dependability

- fault는 component의 문제이며 system service failure로 이어질 수도 있고 아닐 수도 있다.
- service accomplishment는 specification대로 service가 제공되는 상태다.
- service interruption은 service가 specification에서 벗어난 상태다.
- failure 후 restoration을 거쳐 정상 service로 돌아간다.
- dependable memory hierarchy는 error detection/correction과 repair를 통해 interruption을 줄인다.

## Ch5 PDF 59쪽 / 내부 59쪽 - Dependability Measure

- reliability는 평균 failure까지 시간인 MTTF로 나타낸다.
- repair 시간은 MTTR이다.
- `MTBF=MTTF+MTTR`이다.
- `Availability=MTTF/(MTTF+MTTR)`다.
- availability를 높이려면 fault avoidance/tolerance/forecasting으로 MTTF를 늘리거나 diagnosis/repair process로 MTTR을 줄인다.

## Ch5 PDF 60쪽 / 내부 60쪽 - Hamming SEC Code

- Hamming distance는 두 bit pattern에서 서로 다른 bit 위치 수다.
- minimum distance 2 code는 single-bit error를 detect할 수 있다. parity code가 예다.
- minimum distance 3은 single-bit error correction과 double-bit error detection이 가능하다.
- 일반적으로 distance d는 최대 d-1 bit error detect, `floor((d-1)/2)` bit error correct가 가능하다.
- SEC는 Single Error Correction을 뜻한다.

## Ch5 PDF 61쪽 / 내부 61쪽 - SEC Encoding

- bit position을 왼쪽부터 1로 번호 매긴다.
- 1,2,4,8처럼 power-of-two position에 parity bit p1,p2,p4,p8을 둔다.
- 나머지 position 3,5,6,7,9,10,11,12에 d1~d8을 둔다.
- 각 parity는 position number의 binary 표현에서 해당 bit가 1인 position을 검사한다.
- p1은 1,3,5,7,9,11, p2는 2,3,6,7,10,11 식으로 overlapping group을 검사해 error 위치를 binary로 표현한다.

## Ch5 PDF 62쪽 / 내부 62쪽 - SEC Decoding

- 수신 word에서 parity check를 다시 계산해 syndrome을 만든다.
- syndrome `0000`이면 SEC parity 기준 error가 없다.
- syndrome `1010`은 binary 10이므로 bit position 10이 뒤집혔음을 뜻한다.
- single-bit error이면 해당 position bit를 반전해 correct한다.
- 여러 bit error에서는 syndrome이 다른 위치를 가리킬 수 있어 SEC만으로 안전한 double-error correction은 할 수 없다.

## Ch5 PDF 63쪽 / 내부 63쪽 - SEC-DED

- 전체 word를 검사하는 additional parity `pn`을 더해 minimum distance 4를 만든다.
- H zero/even, pn even이면 no error다.
- H nonzero/odd, pn odd이면 correctable single-bit error다.
- H zero/even, pn odd이면 overall parity bit 자체의 error다.
- H nonzero/odd, pn even이면 double-bit error가 발생했으며 detect는 하지만 correct하지 않는다.
- ECC DRAM은 보통 64 data bits를 8 ECC bits로 보호한다.

## Ch5 PDF 64쪽 / 내부 64쪽 - Virtual Machine

- host computer가 guest operating system과 machine resource를 emulate한다.
- 여러 guest를 isolation해 한 guest의 security/reliability 문제가 다른 guest에 미치는 영향을 줄인다.
- physical CPU, memory, I/O를 공유해 resource utilization을 높인다.
- virtualization에는 performance overhead가 있지만 modern processor 성능과 ISA support로 실용적이다.
- IBM VM/370, VMware, Microsoft Virtual PC가 예다.

## Ch5 PDF 65쪽 / 내부 65쪽 - Virtual Machine Monitor

- VMM 또는 hypervisor가 virtual CPU, memory, I/O를 physical resource에 mapping한다.
- guest code는 가능한 한 native machine의 user mode에서 직접 실행한다.
- privileged instruction과 protected resource access는 trap되어 VMM이 처리한다.
- guest OS는 host OS와 달라도 가능하다.
- VMM이 real I/O device를 관리하고 guest에는 generic virtual I/O device를 emulate한다.

## Ch5 PDF 66쪽 / 내부 66쪽 - Timer Virtualization

- native system에서 timer interrupt가 오면 OS가 current process를 suspend하고 next process를 선택한다.
- VM system에서는 VMM이 current VM을 suspend하고 next VM을 선택한다.
- guest VM도 timer interrupt를 기대하므로 VMM이 virtual timer를 제공한다.
- physical timer interrupt가 발생하면 적절한 시점에 guest에게 virtual interrupt를 emulate한다.
- time과 scheduling 같은 privileged resource도 virtualize해야 한다.

## Ch5 PDF 67쪽 / 내부 67쪽 - Virtualization을 위한 ISA Support

- ISA는 user mode와 system/supervisor mode를 구분한다.
- privileged instruction은 system mode에서만 실행되며 user mode 실행 시 trap한다.
- page table, interrupt control, I/O register 같은 physical resource는 privileged instruction으로만 접근해야 한다.
- 이런 조건이 충족돼야 VMM이 모든 sensitive operation을 가로채 안전하게 emulate할 수 있다.
- x86 같은 ISA도 virtualization 지원을 추가해 발전했다.

## Ch5 PDF 68쪽 / 내부 68쪽 - Virtual Memory

- main memory를 secondary disk storage의 cache처럼 사용한다.
- CPU hardware와 OS가 함께 관리한다.
- 각 process는 private virtual address space를 받아 다른 process로부터 보호된다.
- CPU/OS가 virtual address를 physical address로 translate한다.
- virtual-memory block은 page, translation miss로 page가 memory에 없으면 page fault라 한다.

## Ch5 PDF 69쪽 / 내부 69쪽 - Address Translation

- fixed-size page 예로 4 KiB를 사용한다.
- virtual address는 virtual page number와 page offset으로 나뉜다.
- VPN은 page table/TLB를 통해 physical page number로 바뀐다.
- page offset은 page 내부 위치이므로 translation 전후 그대로 유지된다.
- 32-bit VA, 4 KiB page이면 offset 12 bits, VPN 20 bits다. 30-bit PA이면 PPN 18 bits다.

## Ch5 PDF 70쪽 / 내부 70쪽 - Page Fault Penalty

- page가 physical memory에 없으면 disk에서 가져와야 해 millions of clock cycles가 걸린다.
- hardware만으로 처리하기보다 OS code가 page fault를 처리한다.
- 이 huge penalty 때문에 page fault rate를 극도로 낮춰야 한다.
- virtual page는 physical frame 어느 곳에도 둘 수 있는 fully associative placement를 사용한다.
- OS는 sophisticated replacement algorithm과 locality를 활용한다.

## Ch5 PDF 71쪽 / 내부 71쪽 - Page Table

- page table은 virtual page가 physical memory 또는 disk 어디에 있는지 저장한다.
- CPU의 page-table register가 physical memory 안의 page table 시작을 가리킨다.
- VPN이 PTE array index가 된다.
- page가 memory에 있으면 PTE에 physical page number와 valid, referenced, dirty 등 status bit가 있다.
- memory에 없으면 PTE가 swap space의 disk location을 가리킬 수 있다.

## Ch5 PDF 72쪽 / 내부 72쪽 - Page Table Translation 그림

- 32-bit virtual address는 VPN 20 bits와 page offset 12 bits로 나뉜다.
- page-table register와 VPN으로 PTE를 찾아 valid와 physical page number를 읽는다.
- physical address는 18-bit PPN과 같은 12-bit offset을 결합한 30 bits다.
- valid=0이면 page가 memory에 없으므로 page fault path로 간다.
- PTE access 자체가 memory access라 translation마다 추가 latency가 생기며 TLB가 이를 줄인다.

## Ch5 PDF 73쪽 / 내부 73쪽 - Page와 Storage Mapping

- virtual page number가 page table의 한 entry를 선택한다.
- valid=1 entry는 physical memory frame을 가리킨다.
- valid=0 entry는 disk storage에 있는 page를 가리킬 수 있다.
- virtual page는 physical frame에 arbitrary하게 배치되는 fully associative mapping이다.
- 여러 virtual page가 physical memory와 disk 사이에 흩어져 있어도 process에는 연속 virtual address space로 보인다.

## Ch5 PDF 74쪽 / 내부 74쪽 - Page Replacement와 Write

- page fault rate를 줄이기 위해 least-recently-used에 가까운 page를 victim으로 고른다.
- PTE reference/use bit는 page access 때 1로 설정되고 OS가 주기적으로 0으로 지워 recent use를 근사한다.
- reference=0인 page는 최근 사용되지 않았을 가능성이 크다.
- disk write는 millions of cycles이고 page 전체 단위이므로 write-through는 비현실적이다.
- virtual memory는 write-back을 사용하고 dirty bit로 modified page만 disk에 기록한다.

## Ch5 PDF 75쪽 / 내부 75쪽 - TLB

- page table access 후 실제 data access까지 하면 memory reference가 두 번 필요하다.
- page-table access에도 locality가 있으므로 CPU 안에 PTE cache인 TLB를 둔다.
- typical TLB는 16~512 entries, hit 0.5~1 cycle, miss 10~100 cycles, miss rate 0.01~1%다.
- TLB miss는 hardware 또는 optimized software handler가 처리할 수 있다.
- TLB hit이면 VPN을 빠르게 PPN으로 바꿔 actual memory/cache access를 진행한다.

## Ch5 PDF 76쪽 / 내부 76쪽 - TLB와 Page Table 전체 구조

- virtual page number를 TLB tag들과 associative하게 비교한다.
- matching valid TLB entry에서 physical page address와 dirty/reference information을 얻는다.
- TLB miss이면 memory의 page table을 찾아 PTE를 가져온다.
- page-table valid가 1이면 TLB를 refill하고, 0이면 disk에서 page를 가져와야 한다.
- TLB, page table, physical memory, disk가 translation hierarchy를 이룬다.

## Ch5 PDF 77쪽 / 내부 77쪽 - TLB Miss

- page가 memory에 있는데 PTE만 TLB에 없으면 page table에서 PTE를 load하고 instruction을 retry한다.
- simple page table은 hardware가 처리할 수 있지만 complex multilevel structure는 software handler가 유리할 수 있다.
- software 방식은 special exception을 발생시키고 optimized handler가 TLB를 채운다.
- page가 memory에 없으면 단순 TLB miss가 아니라 page fault가 되어 OS가 disk I/O를 수행한다.
- TLB miss와 page fault를 혼동하면 penalty 계산을 크게 틀린다.

## Ch5 PDF 78쪽 / 내부 78쪽 - TLB Miss Handler

- TLB miss는 “page present지만 TLB에 없음” 또는 “page도 present하지 않음” 두 경우를 포함한다.
- destination register가 overwrite되기 전에 miss를 감지해 instruction side effect를 막아야 한다.
- exception handler가 memory의 PTE를 TLB로 복사하고 instruction을 restart한다.
- PTE valid가 0이면 refill 대신 page fault가 이어진다.
- handler가 끝나면 faulting instruction을 처음부터 다시 실행해 precise state를 유지한다.

## Ch5 PDF 79쪽 / 내부 79쪽 - Page Fault Handler

- faulting virtual address를 이용해 PTE와 disk location을 찾는다.
- free frame이 없으면 replacement page를 고른다.
- victim이 dirty이면 먼저 disk에 write한다.
- requested page를 physical memory로 읽고 page table을 update한다.
- process를 runnable 상태로 돌리고 faulting instruction부터 restart한다.

## Ch5 PDF 80쪽 / 내부 80쪽 - TLB와 Cache Interaction

- physical-tagged cache는 먼저 TLB translation으로 physical address를 얻은 뒤 cache tag를 비교해야 한다.
- 그림은 VA의 VPN을 TLB로 PPN으로 바꾸고 physical address의 index/tag를 cache에 사용하는 흐름이다.
- translation과 cache lookup을 serial하게 하면 hit latency가 길어진다.
- virtual index를 page offset bit에서 뽑으면 TLB와 cache data lookup을 parallel하게 할 수 있다.
- virtual tag는 서로 다른 VA가 같은 PA를 가리키는 aliasing 문제가 있어 protection/coherence가 복잡해진다.

## Ch5 PDF 81쪽 / 내부 81쪽 - Memory Protection

- 서로 다른 task가 virtual address space 일부를 공유할 수 있지만 잘못된 access는 막아야 한다.
- OS와 hardware가 supervisor/kernel mode와 user mode를 구분한다.
- privileged instruction, page table, interrupt control, I/O register는 supervisor mode만 접근한다.
- user program이 privileged operation을 요청하면 syscall 같은 exception을 통해 kernel로 들어간다.
- PTE permission bit로 read/write/execute와 process별 access를 통제할 수 있다.

## Ch5 PDF 82쪽 / 내부 82쪽 - Hierarchy 공통 Framework

- cache, virtual memory 등 모든 hierarchy level에는 같은 네 질문이 반복된다.
- block을 어디에 배치하는가.
- requested block이 있는지 어떻게 찾는가.
- miss에서 어느 block을 교체하는가.
- write를 upper/lower level에 어떻게 반영하는가.
- level별 penalty와 implementation cost에 따라 direct/set/full placement와 write policy가 달라진다.

## Ch5 PDF 83쪽 / 내부 83쪽 - Block Placement

- direct-mapped는 placement 선택이 하나다.
- n-way set-associative는 selected set 안에 n개 선택지가 있다.
- fully associative는 어느 entry든 사용할 수 있다.
- associativity가 높을수록 conflict miss가 줄지만 comparator, mux, replacement state와 hit time이 증가한다.
- virtual memory는 page fault penalty가 매우 커 full associativity를 선택한다.

## Ch5 PDF 84쪽 / 내부 84쪽 - Block 찾기

- direct-mapped는 index로 한 entry를 고르고 tag comparison 1번을 한다.
- n-way는 set index 후 n개 tag를 비교한다.
- fully associative hardware cache는 모든 entry tag를 비교한다.
- virtual memory는 page table이라는 full lookup table을 사용해 physical location을 찾으므로 hardware tag comparison이 0개로 표기된다.
- lookup cost와 miss-rate benefit의 trade-off가 placement choice를 결정한다.

## Ch5 PDF 85쪽 / 내부 85쪽 - Replacement 공통 원리

- miss에서 placement candidate 중 victim을 선택한다.
- LRU는 locality를 활용하지만 high associativity에서 exact hardware가 복잡하고 비싸다.
- random은 구현이 쉽고 높은 associativity에서 LRU에 가까운 결과를 낼 수 있다.
- virtual memory는 reference bit 같은 hardware support로 LRU를 근사한다.
- invalid entry가 있으면 replacement 전에 그것을 우선 사용한다.

## Ch5 PDF 86쪽 / 내부 86쪽 - Write Policy 공통 원리

- write-through는 upper와 lower level을 동시에 update해 replacement가 단순하지만 write buffer가 필요할 수 있다.
- write-back은 upper level만 update하고 block replacement 때 lower level을 update한다.
- write-back에는 dirty state와 복잡한 miss handling이 필요하다.
- disk write latency가 큰 virtual memory에서는 write-through가 불가능에 가까워 write-back만 사용한다.
- policy 선택은 consistency 단순성, bandwidth와 penalty 사이의 trade-off다.

## Ch5 PDF 87쪽 / 내부 87쪽 - 3C Miss

- compulsory/cold miss는 block의 첫 access라 cache 크기나 associativity와 무관하게 발생한다.
- capacity miss는 total cache가 working set보다 작아 교체한 block을 다시 필요로 할 때 발생한다.
- conflict/collision miss는 non-fully-associative mapping에서 같은 set을 경쟁해 발생한다.
- 같은 total capacity의 fully associative cache에서 발생하지 않을 miss가 conflict miss다.
- miss 원인을 알아야 적절한 design change를 선택할 수 있다.

## Ch5 PDF 88쪽 / 내부 88쪽 - Cache Design Trade-off

- cache size 증가: capacity miss 감소, 하지만 access time 증가 가능.
- associativity 증가: conflict miss 감소, 하지만 access time와 comparison cost 증가 가능.
- block size 증가: spatial locality로 compulsory miss 감소 가능.
- 너무 큰 block은 miss penalty와 pollution을 늘리고 block 수를 줄여 miss rate가 다시 증가할 수 있다.
- 한 parameter를 개선하면 다른 performance factor가 나빠질 수 있다.

## Ch5 PDF 89쪽 / 내부 89쪽 - Simple Cache Control 대상

- direct-mapped, write-back, write-allocate, blocking cache를 FSM으로 control한다.
- block size 4 words=16 bytes, data capacity 16 KiB, 1024 blocks다.
- 32-bit byte address는 tag 18 bits, index 10 bits, offset 4 bits로 나뉜다.
- block마다 valid와 dirty bit를 둔다.
- blocking cache이므로 miss가 끝날 때까지 CPU가 기다린다.

## Ch5 PDF 90쪽 / 내부 90쪽 - CPU/Cache/Memory Interface

- CPU-cache interface에는 Read/Write, Valid, Address 32 bits, Write Data 32 bits, Read Data 32 bits, Ready가 있다.
- cache-memory interface도 request의 Read/Write/Valid와 completion Ready를 사용한다.
- memory block은 4 words이므로 write/read data path가 128 bits다.
- memory access는 여러 cycle이 걸려 Ready가 올 때까지 controller state를 유지해야 한다.
- Valid/Ready handshake로 request와 completion timing을 분리한다.

## Ch5 PDF 91쪽 / 내부 91쪽 - Finite State Machine

- FSM은 control step을 여러 state로 나누고 clock edge마다 transition한다.
- current state는 state register에 저장된다.
- `next state = f(current state,current inputs)`다.
- datapath control output은 주로 current state의 함수다.
- combinational control logic가 cache inputs와 state를 보고 next state와 memory/cache control signal을 만든다.

## Ch5 PDF 92쪽 / 내부 92쪽 - Cache Controller FSM

- Idle에서 valid CPU request를 받으면 Compare Tag로 간다.
- Compare Tag에서 valid/tag hit면 read/write를 완료하고 write hit이면 dirty를 set한 뒤 Idle로 돌아간다.
- miss이며 old block이 clean이면 바로 Allocate로 간다.
- miss이며 old block이 dirty이면 Write-Back에서 old block을 memory에 쓰고 Ready 후 Allocate로 간다.
- Allocate는 new block을 memory에서 읽고 Ready 후 Compare Tag로 돌아가 original request를 다시 처리한다.
- clock을 짧게 하려면 큰 state를 더 작은 state로 나눌 수 있다.

## Ch5 PDF 93쪽 / 내부 93쪽 - Cache Coherence Problem

- 두 CPU core가 같은 physical address X를 공유하고 private cache를 가진 상황이다.
- A와 B가 X를 읽으면 둘 다 0을 cache한다.
- A가 X에 1을 write하고 write-through로 memory를 1로 바꿔도 B cache에는 오래된 0이 남는다.
- B가 cache hit로 X를 읽으면 최신 값이 아닌 0을 보게 된다.
- private cache replication이 shared-memory correctness를 깨뜨리는 coherence 문제다.

## Ch5 PDF 94쪽 / 내부 94쪽 - Coherence 정의

- 같은 processor P가 X를 write하고 intervening write 없이 다시 read하면 자신이 쓴 값을 반환해야 한다.
- P1이 X를 write한 뒤 충분히 시간이 지나 P2가 read하면 P1의 값을 반환해야 한다.
- P1과 P2가 같은 X를 write하면 모든 processor가 두 write를 같은 order로 봐 최종 X가 같아야 한다.
- 요약하면 read는 가장 최근의 해당 location write를 일관된 순서로 관찰해야 한다.
- coherence는 주로 **한 memory location**의 write/read order를 다룬다.

## Ch5 PDF 95쪽 / 내부 95쪽 - Coherence Protocol

- protocol은 multiprocessor cache가 coherence를 유지하기 위해 수행하는 operation 집합이다.
- migration은 data를 사용하는 processor의 local cache로 옮겨 shared-memory bandwidth를 줄인다.
- replication은 read-shared data를 여러 cache에 복제해 contention을 줄인다.
- snooping protocol은 각 cache가 bus의 read/write transaction을 감시한다.
- directory protocol은 memory/cache가 block의 sharing/owner state를 directory에 기록한다.

## Ch5 PDF 96쪽 / 내부 96쪽 - Invalidating Snooping

- cache가 block을 write하려면 exclusive access를 얻고 bus에 invalidate message를 broadcast한다.
- 다른 cache의 copy는 invalid가 된다.
- 이후 다른 CPU가 그 block을 read하면 cache miss가 발생한다.
- owner cache 또는 memory가 최신 value를 공급하고 필요한 경우 memory도 update된다.
- 예제에서 A가 X=1을 write하면 B의 X=0 copy가 invalid되고, B의 다음 read는 miss 후 1을 받는다.

## Ch5 PDF 97쪽 / 내부 97쪽 - Memory Consistency

- consistency는 다른 processor가 여러 memory operation을 언제 어떤 순서로 보는지 다룬다.
- “write가 보인다”는 것은 다른 processor의 read가 그 값을 반환한다는 뜻이다.
- 강의의 단순 model은 write가 모든 processor에 보인 뒤 complete되고 processor가 write와 다른 access를 reorder하지 않는다고 가정한다.
- P가 X를 write한 뒤 Y를 write했다면 다른 processor가 새 Y를 본 경우 새 X도 봐야 한다.
- 이 model에서는 read는 reorder될 수 있지만 write는 reorder하지 않는다고 설명한다.

## Ch5 PDF 98쪽 / 내부 98쪽 - 실제 On-Chip Cache 비교

- ARM Cortex-A53과 Intel Core i7 모두 split L1 instruction/data cache를 사용한다.
- A53 L1은 16~64 KiB each, I 2-way/D 4-way, Core i7 L1은 32 KiB each, I 4-way/D 8-way다.
- 두 processor 모두 64-byte block을 사용한다.
- A53 L2는 unified 128 KiB~2 MiB, 16-way, 약 12 cycles다.
- Core i7 L2는 core당 256 KiB, 8-way, 약 10 cycles이고 L3는 shared 8 MiB, 16-way, 약 35 cycles다.
- 실제 design도 hit time, capacity, associativity와 sharing을 level별로 다르게 선택한다.

## Ch5 PDF 99쪽 / 내부 99쪽 - 실제 2-Level TLB

- 두 processor의 virtual address는 48 bits이고 physical address는 A53 40 bits, Core i7 44 bits다.
- 둘 다 core마다 instruction TLB와 data TLB를 분리한다.
- A53 micro-TLB는 fully associative 10 entries이며 lower TLB는 64-entry 4-way다.
- Core i7 L1 I-TLB는 small page 128 entries, D-TLB는 64 entries이고 large page용 entry도 따로 있다.
- Core i7 L2 TLB는 4-way 512 entries다.
- 둘 다 TLB miss를 hardware가 처리한다.

## Ch5 PDF 100쪽 / 내부 100쪽 - Multiple Issue 지원

- multiple issue processor는 cycle당 여러 memory access를 요구할 수 있다.
- multi-banked cache는 access가 다른 bank로 가면 동시에 처리하고 같은 bank이면 conflict가 발생한다.
- Core i7 optimization에는 requested word first가 있다.
- non-blocking cache는 miss가 pending이어도 다른 hit를 처리하는 hit-under-miss를 지원한다.
- 여러 miss를 동시에 추적하는 miss-under-miss와 data prefetching도 throughput을 높인다.

## Ch5 PDF 101쪽 / 내부 101쪽 - DGEMM: Blocking과 SIMD 결합

- cache blocking과 subword/vector parallelism을 함께 사용해야 높은 matrix performance를 얻는다.
- 그래프에서 unoptimized GFLOPS는 matrix가 커질수록 1.7에서 0.8로 떨어진다.
- AVX+unroll은 작은 32²에서 14.6 GFLOPS지만 큰 960²에서는 5.1로 memory effect를 받는다.
- AVX+unroll+blocked는 32² 13.6, 160² 12.7, 480² 11.7, 960² 12.0으로 큰 matrix에서도 성능을 유지한다.
- 960²에서 blocked/unoptimized speedup은 `12.0/0.8=15배`다.

## Ch5 PDF 102쪽 / 내부 102쪽 - Pitfall: Byte/Word와 Locality

- byte address와 word address를 혼동하면 cache mapping이 달라진다.
- 32-byte direct-mapped cache, 4-byte block이면 byte address 36은 block 9, index 1이다.
- word address 36은 byte address 144, block 36, index 4다.
- code generation에서 memory system을 무시하면 row/column loop order 때문에 큰 stride가 생긴다.
- row-major array를 column 방향으로 순회하면 spatial locality가 나빠질 수 있다.

## Ch5 PDF 103쪽 / 내부 103쪽 - Pitfall: Core 수와 AMAT

- shared L2/L3에서 associativity가 core 수보다 너무 작으면 서로 다른 core의 block이 같은 set을 경쟁해 conflict miss가 늘 수 있다.
- core 수 증가에 따라 shared cache associativity도 고려해야 한다.
- out-of-order processor를 AMAT만으로 평가하면 independent work와 non-blocking access 효과를 무시한다.
- 실제 performance는 instruction/data flow를 포함한 simulation으로 평가해야 한다.

## Ch5 PDF 104쪽 / 내부 104쪽 - Pitfall: Segment와 Virtualization

- segment로 address range를 확장하면 segment가 충분히 크지 않을 수 있고 address arithmetic이 복잡해진다. Intel 80286이 예다.
- flat virtual address와 paging이 programming model을 단순화한다.
- virtualization을 고려하지 않은 ISA에서 non-privileged instruction이 hardware resource에 접근하면 VMM이 trap할 수 없다.
- 해결하려면 ISA extension을 추가하거나 guest OS가 problematic instruction을 사용하지 않도록 수정해야 한다.

## Ch5 PDF 105쪽 / 내부 105쪽 - Chapter 5 결론

- fast memory는 작고 large memory는 느리지만 caching이 빠르고 큰 memory처럼 보이는 illusion을 만든다.
- 핵심 근거는 program이 memory space의 작은 부분을 반복 사용하는 locality다.
- hierarchy는 `L1 <-> L2/L3 <-> DRAM <-> disk`로 이어진다.
- 각 level에서 placement, lookup, replacement, write policy를 설계한다.
- multiprocessor에서는 성능뿐 아니라 cache coherence와 memory consistency가 필수다.

## Ch6 PDF 1쪽 / 내부 1쪽 - Chapter 6: Parallel Computing and GPU Architecture for AI

이 장은 AI 계산량이 왜 병렬 컴퓨터를 필요로 하는지, CPU의 멀티코어·SIMD와 GPU의 CUDA 실행 모델이 어떻게 다른지를 설명한다. 앞부분에서는 퍼셉트론과 CNN을 통해 병렬화할 실제 계산을 살펴보고, 뒷부분에서는 CUDA의 thread·block·grid, warp, 메모리 계층, NVIDIA V100의 구조와 성능 계산을 다룬다.

## Ch6 PDF 2쪽 / 내부 2쪽 - Perceptron

퍼셉트론은 여러 입력에 각각 가중치를 곱한 뒤 모두 더하고 활성화 함수를 적용하는 가장 기본적인 인공 뉴런이다.

\[
y=f(w_1x_1+w_2x_2+\cdots+w_nx_n)
  =f\left(\sum_{i=1}^{n}w_ix_i\right)
\]

그림에서 \(x_i\)는 입력 특징, \(w_i\)는 학습된 중요도, 원 안의 합산기는 가중합, sign function은 최종 판정을 만드는 활성화 함수이다. 편향 \(b\)까지 포함하면 일반식은 \(y=f(\sum_iw_ix_i+b)\)가 된다. 입력이 \(n\)개라면 한 뉴런의 기본 연산량은 대략 \(n\)번의 곱셈과 \(n-1\)번의 덧셈이다.

## Ch6 PDF 3쪽 / 내부 3쪽 - Multi-Layer Perceptron

다층 퍼셉트론(MLP)은 입력층, 하나 이상의 은닉층, 출력층으로 구성된다. 한 층의 모든 뉴런은 이전 층의 출력을 입력으로 받아 가중합과 활성화 함수를 계산한다.

완전연결층의 입력 뉴런 수가 \(N_{\text{in}}\), 출력 뉴런 수가 \(N_{\text{out}}\)이면 가중치 수와 곱셈 수는 대략

\[
N_{\text{in}}N_{\text{out}}
\]

이다. 뉴런 수가 늘수록 전체 계산량도 그에 비례해 커지지만, 같은 층의 뉴런들은 서로 독립적으로 계산할 수 있어 병렬화하기 좋다.

## Ch6 PDF 4쪽 / 내부 4쪽 - Convolution

2차원 합성곱은 입력 영상 \(f\)의 주변 값과 커널 \(h\)의 가중치를 곱해 더하여 출력 \(g\)를 만드는 연산이다.

\[
g(m,n)=f(m,n)*h(m,n)
=\sum_k\sum_l f(k,l)h(n-k,m-l)
\]

슬라이드의 \(3\times3\) 커널은 각 출력 위치에서 주변 9개 입력을 읽고, 각 커널 계수와 곱한 뒤 합산한다. 수학적 convolution은 커널을 뒤집어 적용하지만 딥러닝 라이브러리에서는 대개 뒤집지 않는 cross-correlation을 convolution이라고 부른다. 출력 한 점당 \(3\times3\)이면 9회 곱셈과 8회 덧셈, FMA 기준으로 약 9개의 multiply-add가 필요하다.

## Ch6 PDF 5쪽 / 내부 5쪽 - 3×3 평균 필터의 임펄스 응답 ①

입력 영상의 한 점만 1이고 나머지가 0인 2차원 단위 임펄스에 \(3\times3\) 평균 필터를 적용하는 과정의 시작이다.

\[
g[n,m]=\frac{1}{9}
\sum_{k=n-1}^{n+1}\sum_{l=m-1}^{m+1}f[k,l]
\]

임펄스가 커널의 한 계수와 겹치면 출력의 해당 위치에 \(1/9\)가 나타난다. 선형 시불변 시스템에서 임펄스 입력에 대한 출력은 곧 필터의 모양, 즉 임펄스 응답이다.

## Ch6 PDF 6쪽 / 내부 6쪽 - 3×3 평균 필터의 임펄스 응답 ②

커널 창을 한 칸 이동하면 같은 입력 임펄스가 커널의 다른 위치와 겹친다. 따라서 출력의 이웃 위치에도 \(1/9\)가 기록된다. 이 과정은 한 입력 값이 여러 출력 위치에 영향을 준다는 합성곱의 공간적 확산을 보여준다.

## Ch6 PDF 7쪽 / 내부 7쪽 - 3×3 평균 필터의 임펄스 응답 ③

계속 창을 이동하면 출력 영역에 \(1/9\) 값이 하나씩 추가된다. 평균 필터의 각 계수가 동일하므로 임펄스가 커널 내부 어느 위치와 겹쳐도 기여도는 \(1/9\)이다.

## Ch6 PDF 8쪽 / 내부 8쪽 - 3×3 평균 필터의 임펄스 응답 ④

출력에 두 개 이상의 \(1/9\) 값이 나타나며 커널의 형태가 드러나기 시작한다. 실제 영상에서는 여러 비영점 입력의 영향이 중첩되므로 각 출력은 주변 픽셀들의 평균이 된다.

## Ch6 PDF 9쪽 / 내부 9쪽 - 3×3 평균 필터의 임펄스 응답 ⑤

출력의 한 행에 세 개의 \(1/9\)가 채워진 상태이다. 이는 \(3\times3\) 커널의 가로 폭이 세 칸이라는 뜻이다. 경계 밖의 값을 어떻게 처리하는지에 따라 valid, zero padding 등의 출력 크기와 경계 값이 달라진다.

## Ch6 PDF 10쪽 / 내부 10쪽 - 3×3 평균 필터의 임펄스 응답 완성

최종 출력에는 \(3\times3\) 영역 전체가 \(1/9\)로 채워진다.

\[
h=
\frac{1}{9}
\begin{bmatrix}
1&1&1\\
1&1&1\\
1&1&1
\end{bmatrix},
\qquad
\sum_{i,j}h[i,j]=1
\]

계수 합이 1이므로 일정한 밝기의 영상은 평균 필터를 거쳐도 전체 밝기가 유지된다. 대신 이웃 값들이 평균화되어 고주파 성분과 잡음이 줄고 영상은 흐려진다.

## Ch6 PDF 11쪽 / 내부 11쪽 - 영상용 신경망의 Convolutional Layer ①

CNN의 합성곱층은 작은 커널을 입력 영상 전체에 이동시키며 같은 연산을 반복한다. 한 커널은 가장자리, 방향, 질감처럼 특정 패턴에 반응하도록 학습된다. 완전연결층과 달리 모든 위치에서 같은 가중치를 재사용하므로 매개변수 수를 크게 줄일 수 있다.

## Ch6 PDF 12쪽 / 내부 12쪽 - Learned Weights와 Feature Map

커널의 숫자는 사람이 고정하는 평균 필터와 달리 학습을 통해 결정되는 learned weights이다. 커널과 입력의 지역 영역을 곱해 더한 결과를 각 위치에 기록하면 feature map이 된다.

입력 크기가 \(H\times W\), 커널이 \(K\times K\), stride가 \(S\), padding이 \(P\)일 때 한 방향의 출력 크기는

\[
H_{\text{out}}
=\left\lfloor\frac{H+2P-K}{S}\right\rfloor+1
\]

이며 폭도 같은 방식으로 계산한다.

## Ch6 PDF 13쪽 / 내부 13쪽 - Convolutional Layer 완성

커널을 모든 유효 위치에 적용하면 하나의 완성된 feature map이 생성된다. 여러 커널을 사용하면 커널마다 서로 다른 특징 맵이 만들어진다. 입력 채널 수가 \(C_{\text{in}}\), 출력 채널 수가 \(C_{\text{out}}\)이면 가중치 수는 보통

\[
K^2C_{\text{in}}C_{\text{out}}
\]

이고 편향까지 포함하면 \(C_{\text{out}}\)개가 추가된다. 출력 전체의 MAC 수는

\[
H_{\text{out}}W_{\text{out}}C_{\text{out}}K^2C_{\text{in}}
\]

이다.

## Ch6 PDF 14쪽 / 내부 14쪽 - LeNet-5

LeNet-5는 손글씨 숫자 인식용 초기 CNN이다. 32×32 입력에서 합성곱층 C1, 평균 풀링층 S2, 합성곱층 C3, 평균 풀링층 S4를 거친 뒤 C5와 F6, 10개 출력으로 연결된다. 그림의 대표 크기는 C1이 6개 28×28 맵, S2가 6개 14×14, C3가 16개 10×10, S4가 16개 5×5, 이후 120개와 84개 뉴런이다.

활성화 함수로 sigmoid/tanh를 쓰고 평균 풀링을 사용한다. MNIST 훈련 데이터 약 60,000개로 학습되었으며, 합성곱을 통한 지역 특징 추출과 점진적인 공간 크기 축소라는 현대 CNN의 기본 구조를 보여준다.

## Ch6 PDF 15쪽 / 내부 15쪽 - AlexNet

AlexNet은 LeNet과 유사한 계층 구조를 훨씬 큰 데이터와 모델로 확장했다. 평균 풀링 대신 max pooling, sigmoid/tanh 대신 ReLU를 사용하고, 과적합을 줄이기 위해 dropout을 적용했다.

7개의 주요 은닉층, 약 65만 개의 활성 유닛, 약 6천만 개 매개변수를 가지며 두 GPU에서 약 일주일 동안 학습되었다. 당시 GPU 구현은 CPU보다 약 50배 빠른 것으로 제시된다. AlexNet의 성공은 대규모 CNN 학습에서 GPU 병렬 계산이 결정적이라는 사실을 보여주었다.

## Ch6 PDF 16쪽 / 내부 16쪽 - Parallel Programming과 Amdahl의 법칙

전체 실행 시간 중 개선 가능한 부분만 빨라지므로 전체 속도 향상에는 한계가 있다. 원래 총 실행 시간이 100초일 때 새 실행 시간은

\[
T_{\text{new}}
=\frac{T_{\text{affected}}}{\text{improvement factor}}
+T_{\text{unaffected}}
\]

이다. Case 1은 개선 가능 80초·불가능 20초이고, Case 2는 개선 가능 20초·불가능 80초이다. 개선 불가능한 부분이 작을수록 병렬화의 효과가 크다.

병렬화 가능한 비율을 \(P\), 프로세서 수를 \(N\)이라 하면 정규화된 Amdahl 식은

\[
S(N)=\frac{1}{(1-P)+P/N}
\]

이다.

## Ch6 PDF 17쪽 / 내부 17쪽 - 프로세서 수에 따른 Amdahl 계산

Case 1의 새 실행 시간은 \(80/N+20\)초이다.

| 프로세서 \(N\) | 실행 시간 | 속도 향상 \(100/T\) |
|---:|---:|---:|
| 4 | 40초 | 2.5배 |
| 40 | 22초 | 약 4.545배 |
| 400 | 20.2초 | 약 4.950배 |

\(N\to\infty\)여도 20초가 남으므로 최대 속도 향상은 \(100/20=5\)배이다.

Case 2는 \(20/N+80\)초이다.

| 프로세서 \(N\) | 실행 시간 | 속도 향상 \(100/T\) |
|---:|---:|---:|
| 4 | 85초 | 약 1.176배 |
| 40 | 80.5초 | 약 1.242배 |
| 400 | 80.05초 | 약 1.249배 |

무한히 많은 프로세서를 사용해도 최대 \(100/80=1.25\)배뿐이다. 시험에서는 병렬 부분의 가속률과 전체 가속률을 혼동하지 않아야 한다.

## Ch6 PDF 18쪽 / 내부 18쪽 - Single-Core Convolution Code

단일 코어 코드는 출력의 \(y,x\) 위치를 순서대로 순회하고, 각 위치에서 \(3\times3\) 이웃을 곱해 더한 뒤 활성화 함수를 적용한다.

```c
for (y = 1; y < H - 1; y++)
  for (x = 1; x < W - 1; x++) {
    sum = 0;
    for (ky = -1; ky <= 1; ky++)
      for (kx = -1; kx <= 1; kx++)
        sum += input[y + ky][x + kx] * kernel[ky + 1][kx + 1];
    output[y][x] = activation(sum);
  }
```

가장자리 한 칸을 제외하므로 padding 없는 valid convolution이다. 출력 픽셀 사이에는 데이터 의존성이 없지만, 이 코드는 한 코어가 모든 위치를 직렬로 처리한다.

## Ch6 PDF 19쪽 / 내부 19쪽 - Multicore Convolution

멀티코어 버전은 바깥쪽 \(y\) 반복을 `parallel_for`로 나누어 여러 코어에 출력 행을 분배한다. 각 출력 위치가 서로 다른 메모리에 결과를 쓰고 입력은 읽기만 하므로 반복 간 의존성이 없다.

이론상 \(N\)개 코어에서 병렬 부분은 최대 \(N\)배 빨라질 수 있지만 실제로는 스레드 생성·스케줄링, 캐시 미스, 메모리 대역폭, 병렬화되지 않는 부분 때문에 Amdahl 한계보다 낮은 속도가 나온다. 작업량을 균등하게 나누는 load balancing도 중요하다.

## Ch6 PDF 20쪽 / 내부 20쪽 - SIMD Convolution

SIMD 버전은 \(x\)를 `SIMD_WIDTH`만큼 증가시키고 여러 인접 출력 픽셀을 하나의 벡터 명령으로 계산한다. `load_vector`가 여러 픽셀을 한꺼번에 읽고, 벡터 곱셈·덧셈으로 여러 `sum`을 동시에 갱신한 뒤 `store_vector`로 저장한다.

벡터 폭이 \(W\)이고 모든 lane이 유효하다면 산술 처리량은 스칼라 대비 이론상 \(W\)배가 된다. 다만 행 끝에서 픽셀 수가 벡터 폭의 배수가 아니면 tail 처리나 mask가 필요하고, 정렬·경계·메모리 접근 패턴도 성능에 영향을 준다.

## Ch6 PDF 21쪽 / 내부 21쪽 - AI 모델의 Parameters와 FLOPs

슬라이드는 2012년부터 2020년 사이 주요 AI 모델의 크기와 연산량이 폭발적으로 증가했음을 보여준다.

| 모델 | 연도 | Parameters | FLOPs | 파라미터 메모리 |
|---|---:|---:|---:|---:|
| LeNet-5 | 1998 | 60K | 341K | 0.24MB |
| AlexNet | 2012 | 61M | 724M | 244MB |
| VGG16 | 2014 | 138M | 15.5G | 552MB |
| ResNet-50 | 2015 | 25M | 4.1G | 100MB |
| BERT-base | 2018 | 110M | 22.5G | 440MB |
| GPT-2 | 2019 | 1.5B | 약 300G | 6GB |
| GPT-3 | 2020 | 175B | 약 350T | 700GB |

메모리 값은 대체로 FP32 매개변수당 4바이트를 사용한 값이다. 예를 들어 GPT-3은 \(175\times10^9\times4=700\)GB이다. Parameters는 저장 공간과 모델 크기, FLOPs는 한 번 실행할 때의 계산량을 뜻하므로 둘은 같은 지표가 아니다. 슬라이드는 이 기간 계산 요구량이 약 \(10^9\)배 증가했다고 강조한다.

## Ch6 PDF 22쪽 / 내부 22쪽 - CPU와 GPU의 추론 시간 비교

기준 장치는 FP32 약 1 TFLOPS의 Intel i9 CPU와 약 82 TFLOPS의 RTX 4090 GPU이다.

| 모델 | 연산량 | CPU 추론 | GPU 추론 | 제시된 속도 향상 |
|---|---:|---:|---:|---:|
| ResNet | 4.1G | 4ms | 0.5ms | 8배 |
| VGG | 15.5G | 15ms | 2ms | 약 7배 |
| BERT | 22.5G | 22ms | 3ms | 약 7배 |
| GPT-2 | 300G | 300ms | 20ms | 15배 |
| GPT-3 | 350T | 97시간 | 20초 | 17,500배 |

속도 향상은 \(T_{\text{CPU}}/T_{\text{GPU}}\)로 계산한다. 예를 들어 ResNet은 \(4/0.5=8\)배이다. 실제 시간은 peak TFLOPS만으로 정해지지 않고 메모리 전송, 병렬도, 연산 장치 활용률, 정밀도에 좌우된다. 특히 표의 GPT-3 수치는 단순히 350T를 peak 성능으로 나눈 값과 일치하지 않으므로, 표에 제시된 추정 조건을 그대로 사용해야 한다.

## Ch6 PDF 23쪽 / 내부 23쪽 - Shared-Memory Multiprocessor

공유 메모리 구조에서는 모든 프로세서가 하나의 물리 주소 공간을 사용한다. 프로그래머가 포인터와 공유 자료구조를 쉽게 사용할 수 있지만, 동시에 같은 데이터를 수정하면 race condition이 발생하므로 lock이나 atomic operation이 필요하다.

각 프로세서의 private cache가 interconnect를 통해 주 메모리와 I/O에 연결된다. 프로세서 수가 많아질수록 interconnect와 메모리 대역폭이 병목이 되고, 캐시 일관성 유지 비용도 증가한다.

## Ch6 PDF 24쪽 / 내부 24쪽 - 초기 GPU의 목적

초기 GPU는 화면의 수백만 픽셀을 병렬로 처리하기 위해 만들어졌다. vertex transformation, rasterization, texture mapping, pixel coloring이 고정된 순서의 fixed-function graphics pipeline으로 구현되었다. 대량의 정점과 픽셀에 같은 종류의 연산을 적용하므로 데이터 병렬 처리에 적합했지만, 사용자는 미리 정해진 그래픽 연산만 수행할 수 있었다.

## Ch6 PDF 25쪽 / 내부 25쪽 - Programmable Shader Pipeline

programmable shader가 등장하면서 각 vertex나 pixel에 작은 사용자 프로그램을 실행할 수 있게 되었다. 그래픽 효과의 자유도는 크게 늘었지만 여전히 그래픽 파이프라인에 묶여 있었다.

초기 shader는 일반 목적 프로그램처럼 쓰기 어려웠다. 임의 메모리 접근이 제한적이고 픽셀 간 통신도 어렵고, 입력과 출력을 그래픽 자료 형식에 맞춰야 했다.

## Ch6 PDF 26쪽 / 내부 26쪽 - GPGPU

GPGPU는 General-Purpose Computing on Graphics Processing Units의 약자이다. 연구자들은 GPU의 높은 병렬 처리량을 coupled map lattice simulation, sparse matrix solver, ray tracing 같은 비그래픽 계산에 사용하기 시작했다. 공통점은 많은 데이터 원소에 유사한 계산을 반복한다는 것이다.

## Ch6 PDF 27쪽 / 내부 27쪽 - CUDA의 등장

초기 GPGPU에서는 데이터를 texture로 포장하고 계산을 shader로 표현해야 했다. NVIDIA는 2007년에 CUDA를 도입하여 그래픽 파이프라인과 독립된 일반 목적 병렬 계산 모델을 제공했다.

CUDA 프로그램은 CPU에서 실행되는 host code와 GPU에서 실행되는 kernel로 나뉜다. 프로그래머는 C/C++과 유사한 언어로 수많은 thread가 수행할 작업을 직접 정의할 수 있다.

## Ch6 PDF 28쪽 / 내부 28쪽 - CUDA Thread Hierarchy

CUDA는 작업을 세 단계로 구성한다.

- **Thread:** 하나의 출력 원소처럼 작은 작업을 수행한다.
- **Block:** 여러 thread의 그룹이다. 같은 block의 thread는 shared memory를 공유하고 동기화할 수 있다.
- **Grid:** 한 번의 kernel launch로 실행되는 모든 block의 집합이다.

그림처럼 grid에는 여러 block이 있고, 각 block은 1차원·2차원·3차원 thread 배열을 가질 수 있다. 전체 좌표는 일반적으로 `blockIdx`, `blockDim`, `threadIdx`를 이용해 계산한다.

\[
\text{global index}
=\text{blockIdx}\times\text{blockDim}+\text{threadIdx}
\]

## Ch6 PDF 29쪽 / 내부 29쪽 - Deep Learning 연산의 CUDA 매핑

CNN 합성곱에서는 grid가 전체 출력 feature map 계산, block이 출력의 한 타일, thread가 한 개 또는 몇 개의 출력 원소를 담당하도록 매핑할 수 있다. 핵심 조건은 각 출력 원소를 다른 thread와 독립적으로 계산할 수 있어야 한다는 것이다.

2차원 출력이라면

\[
x=\text{blockIdx.x}\times\text{blockDim.x}+\text{threadIdx.x}
\]
\[
y=\text{blockIdx.y}\times\text{blockDim.y}+\text{threadIdx.y}
\]

로 담당 출력 좌표를 찾는다.

## Ch6 PDF 30쪽 / 내부 30쪽 - GPU Parallel Execution Model

큰 문제는 독립적인 subproblem으로 나뉘고, 각 subproblem은 더 작은 task로 분해되어 worker thread에 할당된다. GPU의 장점은 매우 많은 가벼운 thread를 만들어 데이터 병렬 작업을 동시에 진행하는 데 있다.

모든 문제를 무조건 잘 가속하는 것은 아니다. 충분한 독립 작업, 규칙적인 제어 흐름, 연속적인 메모리 접근이 있어야 GPU 자원을 효율적으로 사용할 수 있다.

## Ch6 PDF 31쪽 / 내부 31쪽 - Redundant Memory Access: Thread (0,0)

합성곱에서 출력 \((0,0)\)을 담당하는 thread는 자신의 커널 창에 해당하는 여러 입력 픽셀을 global memory에서 읽는다. \(3\times3\) 필터라면 출력 하나당 최대 9개 입력을 읽는다. 이 단계만 보면 각 thread가 필요한 값을 직접 읽는 단순한 구현이다.

## Ch6 PDF 32쪽 / 내부 32쪽 - Redundant Memory Access: Thread (0,1)

바로 옆 출력 \((0,1)\)을 담당하는 thread도 \(3\times3\) 입력 영역을 읽는다. 두 창이 한 칸만 차이 나므로 대부분의 입력 픽셀이 앞 thread가 읽은 값과 겹친다. 값을 재사용하지 않으면 같은 global memory 데이터를 여러 번 읽게 된다.

## Ch6 PDF 33쪽 / 내부 33쪽 - 이웃 Thread의 중복 접근

이웃한 출력 thread들의 입력 영역은 크게 겹친다. 이를 줄이기 위해 block이 필요한 입력 타일을 한 번 shared memory에 올린 뒤 block 안의 모든 thread가 재사용한다.

\(B\times B\) 출력 타일과 \(K\times K\) 커널을 생각하면 단순 구현의 입력 읽기 수는 대략

\[
B^2K^2
\]

이고, shared-memory 타일링에서는 halo까지 포함한

\[
(B+K-1)^2
\]

개만 읽으면 된다. \(B=16,\ K=3\)이면

\[
16^2\times3^2=2304,\qquad(16+3-1)^2=324
\]

이므로 이상적으로 global memory 읽기를 \(2304/324\approx7.11\)배 줄일 수 있다.

## Ch6 PDF 34쪽 / 내부 34쪽 - CUDA Memory Hierarchy

CUDA 메모리는 범위와 속도가 다르다.

- **Global memory:** 용량이 크고 모든 thread가 접근할 수 있지만 지연 시간이 길다.
- **Shared memory:** 한 block의 thread가 공유하며 작지만 빠르다. 타일링과 데이터 재사용에 사용한다.
- **Private/thread-local state:** 각 thread의 register와 local data로, 가장 빠르지만 수가 제한된다.

성능의 핵심은 global memory 접근을 coalescing하고, 반복 사용되는 값을 shared memory나 register에 유지하는 것이다. shared memory를 지나치게 많이 쓰면 한 SM에 동시에 올라갈 block 수가 줄어 occupancy가 낮아질 수 있다.

## Ch6 PDF 35쪽 / 내부 35쪽 - Host와 CUDA Device

CPU host는 주로 직렬 제어 코드를 실행하고, GPU device는 같은 kernel을 많은 thread가 수행하는 SPMD 방식으로 실행한다. host memory와 device global memory는 별도 주소 공간이므로 입력을 host에서 device로 복사하고, 계산 후 결과를 다시 가져와야 한다.

\[
T_{\text{total}}
=T_{\text{host}}
+T_{\text{H2D}}
+T_{\text{kernel}}
+T_{\text{D2H}}
\]

kernel 자체가 빨라도 전송 시간이 크면 전체 가속률이 낮아진다. 따라서 가능한 한 데이터를 GPU에 오래 유지하고 kernel launch 및 복사 횟수를 줄이는 것이 중요하다.

## Ch6 PDF 36쪽 / 내부 36쪽 - V100 SM Subcore

V100의 한 SM은 여러 subcore로 나뉜다. 그림의 한 subcore에는 다음 실행 장치가 포함된다.

- 16-wide FP32 SIMD 장치: clock당 16개의 FP32 multiply-add를 처리하며, 32-thread warp 명령은 두 clock에 걸쳐 실행된다.
- 16-wide integer 장치
- 8-wide FP64 장치: 32-thread warp는 네 clock에 걸쳐 처리된다.
- tensor unit: 행렬 곱셈 중심의 딥러닝 연산을 가속한다.
- load/store unit: 메모리 명령을 처리한다.

서로 다른 종류의 실행 장치가 있어 정수 주소 계산, 부동소수점 계산, 메모리 접근을 겹쳐 처리할 수 있다.

## Ch6 PDF 37쪽 / 내부 37쪽 - Warp와 SIMD 실행

warp는 32개의 CUDA thread로 구성되는 하드웨어 스케줄링 단위이다. 같은 warp의 thread는 같은 명령을 서로 다른 데이터에 수행한다. warp selector가 준비된 warp를 고르고 FP32 add, memory load, tensor 연산 등의 명령을 해당 실행 장치로 보낸다.

FP32 장치가 16-wide이므로 32-thread warp의 FP32 명령 하나는 두 번에 나뉘어 실행된다. 분기에서 thread마다 다른 경로를 선택하면 경로별로 순차 실행되어 SIMD lane 활용률이 낮아지는 branch divergence가 발생한다.

## Ch6 PDF 38쪽 / 내부 38쪽 - Thread별 Register 상태

소스 코드에서는 각 CUDA thread가 자신만의 스칼라 register \(R0, R1,\ldots\)를 가진 것처럼 보인다. warp가 하나의 명령을 실행해도 각 thread의 register 값은 서로 다르므로 서로 다른 데이터에 같은 연산을 적용할 수 있다.

register는 매우 빠르지만 총량이 제한된다. thread당 register 사용량이 많으면 한 SM에 동시에 유지할 수 있는 thread와 warp 수가 감소한다.

## Ch6 PDF 39쪽 / 내부 39쪽 - Warp 구성과 상주 수

한 block의 thread는 연속된 32개씩 warp로 묶인다. thread 0–31이 첫 warp, 32–63이 둘째 warp가 된다. 따라서 256-thread block은

\[
256/32=8\text{ warps}
\]

를 만든다. 32로 나누어떨어지지 않으면 마지막 warp의 일부 lane이 비어 효율이 낮아진다.

V100의 각 subcore는 최대 16개 warp를 유지하고 교대로 스케줄할 수 있다. 메모리 접근 등으로 한 warp가 기다리는 동안 다른 준비된 warp를 실행해 지연 시간을 숨긴다.

## Ch6 PDF 40쪽 / 내부 40쪽 - Warp Instruction Execution

각 warp는 `fp32 mul`, `int32 add`, `fp32 mul` 같은 자신의 명령 흐름과 program counter를 가진다. fetch/decode 단계가 warp 명령을 해석하여 적절한 실행 장치로 보낸다.

한 warp에는 32 thread가 있지만 FP32 ALU가 16개이므로 FP32 warp 명령 하나를 완료하는 데 두 clock이 필요하다. 많은 warp를 번갈아 실행하는 이유는 단일 warp의 명령 지연과 메모리 대기 시간을 가리기 위해서이다.

## Ch6 PDF 41쪽 / 내부 41쪽 - 하나의 V100 SM

V100의 한 SM에는 4개의 subcore와 warp selector가 있다. SM 전체에는 최대 64개 warp context가 상주할 수 있으므로 thread context 수는

\[
64\text{ warps}\times32\text{ threads/warp}
=2048\text{ threads}
\]

이다. 각 subcore에 64KB register file이 있어 SM 전체 register는 256KB이고, 이 자원은 상주 warp들이 나누어 사용한다. shared memory와 L1 cache의 합은 128KB이다.

실제 occupancy는 최대 64 warp와 비교한 활성 warp 비율이며, register 수, shared memory 사용량, block당 thread 수의 제한을 모두 받는다.

## Ch6 PDF 42쪽 / 내부 42쪽 - V100 전체 Chip

V100 한 칩에는 80개의 SM이 있다. 각 SM에 최대 64 warp가 상주하므로 전체 상주 warp context는

\[
80\times64=5120\text{ warps}
\]

이고 thread context는

\[
5120\times32=163{,}840\text{ threads}
\]

이다. 칩 전체가 공유하는 L2 cache는 6MB이며, 16GB HBM이 4096-bit 인터페이스와 약 900GB/s 대역폭으로 연결된다.

## Ch6 PDF 43쪽 / 내부 43쪽 - 163,840개의 동시 처리 문맥

슬라이드는 80개 SM에 총 163,840개의 thread context가 존재해 대규모 데이터를 병렬로 처리할 수 있음을 강조한다. 다만 이는 같은 순간에 163,840개의 FP32 연산기가 모두 존재한다는 뜻이 아니다.

실행 장치 수보다 훨씬 많은 thread/warp 상태를 보관하고, 실행 가능한 warp를 빠르게 교대하여 연산 장치를 계속 바쁘게 만드는 구조이다. L2 6MB, HBM 16GB, 900GB/s의 메모리 시스템은 이 많은 thread에 데이터를 공급한다.

## Ch6 PDF 44쪽 / 내부 44쪽 - V100 수치 종합

V100의 핵심 수치는 clock 1.245GHz, 80 SM, SM당 4 subcore, subcore당 16개 FP32 multiply-add lane이다. 따라서 FP32 lane 수는

\[
80\times4\times16=5120
\]

이다. FMA 한 번을 곱셈 1회와 덧셈 1회, 즉 2 FLOPs로 계산하면 peak FP32 성능은

\[
5120\times1.245\times10^9\times2
=12.7488\times10^{12}\text{ FLOPS}
\approx12.7\text{ TFLOPS}
\]

이다.

최대 상주 warp는 \(80\times64=5120\), 최대 상주 thread context는 \(5120\times32=163{,}840\)이다. 16GB를 900GB/s로 한 번 읽는 최소 이론 시간은

\[
\frac{16}{900}\text{ s}\approx0.01778\text{ s}=17.78\text{ ms}
\]

이다. 4096-bit 인터페이스에서 900GB/s를 내기 위해 필요한 pin당 유효 전송률은

\[
\frac{900\times10^9\times8}{4096}
\approx1.758\times10^9\text{ transfers/s}
=1.758\text{ GT/s}
\]

이다. 이 값들은 모두 peak 또는 이상적 조건이며 실제 성능은 명령 혼합, 메모리 접근, 분기, occupancy와 활용률에 따라 낮아진다.
