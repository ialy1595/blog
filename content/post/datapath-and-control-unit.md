---
title: "Datapath and Control Unit"
date: 2019-03-21T17:12:20+09:00
categories: ["code"]
subcategories: ["system architecture"]
tags: ["datapath", "control unit", "control word", "instruction", "FDE cycle"]
draft: false
nextp: ""
prevp: "Sequential Logic"
---

# Datapath

Datapath는 CPU에서 직접적으로 연산이 이루어지는 부분이다. 따라서 연산을 하거나 연산된 결과를 저장할 register file과 연산이 실행되는 ALU로 이루어져 있다. 그리고 memory와 데이터를 주고받아야하기 때문에 memory와도 연결되었다. 그리고 이 데이터들은 버스에 담겨서 각각 부분으로 들어간다.

![](/images/datapath_control_unit/cpu0.png#center75)

여기서 신기한 점은 위의 그림에서 **빨간색으로 표시한 AA, BA, DA, AS, BS, FS, DS, RW, MW 만 조절하면 우리가 가능한 모든 동작을 할 수 있다**는 것이다. 이러한 비트들을 **control bit**라고 하고, 이 control bit들을 모은 것을 **control word**라고 한다. 따라서 위의 datapath의 control word는 다음과 같이 구성된다.

![](/images/datapath_control_unit/controlword0.png#center100)

그리고 각각의 control bit의 동작은 다음과 같다.

 - AA(A Address) : register file에서 A<sub>data</sub>로 출력할 register의 주소
 - BA(B Address) : register file에서 B<sub>data</sub>로 출력할 register의 주소
 - DA(D Address) : register file에서 D<sub>data</sub>를 저장할 register의 주소
 - AS(A Select) : Bus A에 Address<sub>in</sub>과 A<sub>data</sub>중 무엇을 담을지 선택
 - BS(B Select) : Bus B에 Constant<sub>in</sub>과 B<sub>data</sub>중 무엇을 담을지 선택
 - FS(F Select) : ALU에서 어떤 연산을 할지 선택. 각 연산 아래 표를 따른다.

FS<sub>4</sub> | FS<sub>3</sub> | FS<sub>2</sub> | FS<sub>1</sub> | FS<sub>0</sub> || F
:-:|:-:|:-:|:-:|:-:|:-:|:-:
0 | 0 | 0 | 0 | 0 || A
0 | 0 | 0 | 0 | 1 || A + 1
0 | 0 | 0 | 1 | 0 || A + B
0 | 0 | 0 | 1 | 1 || A + B + 1
0 | 0 | 1 | 0 | 0 || A - B - 1
0 | 0 | 1 | 0 | 1 || A - B
0 | 0 | 1 | 1 | 0 || A - 1
0 | 0 | 1 | 1 | 1 || A
0 | 1 | 0 | 0 | x || A OR B
0 | 1 | 0 | 1 | x || A AND B
0 | 1 | 1 | 0 | x || A XOR B
0 | 1 | 1 | 1 | x || A'
1 | 0 | 0 | x | x || B
1 | 0 | 1 | 0 | x || logical shift right B
1 | 0 | 1 | 1 | x || arithmetic shift right B
1 | 1 | 0 | x | x || shift left B
1 | 1 | 1 | x | x || B

 - DS(D Select) : Bus D에 F(ALU 결과)와 Data<sub>out</sub>(memory에서 읽은 값)중 무엇을 담을지 선택
 - RW(Register Write) : D<sub>data</sub>를 register[DA]에 쓸지 선택
 - MW(Memory Write) : Data<sub>in</sub>을 memory[Address]에 쓸지 선택

이제 이를 이용해서 어떤 동작을 할 수 있고, 각 동작은 어떻게 수행되는지 살펴보자.

## Register Operation

Register에 할 수 있는 연산은 아래 두 종류가 있다.

 - A register의 값을 B register에 대입
 - register에 상수 값을 대입

 먼저 A register의 값을 B register에 대입하는 과정을 보자. 예를 들어 `$ R2 \leftarrow R5 $`인 연산을 보자. 이는 다음과 같은 과정으로 이루어진다.

  1. Register file에서 R5를 A<sub>data</sub>로 출력한다.
  2. ALU에서 F = A 연산으로 R2값을 그대로 뽑아낸다.
  3. 이 값을 Bus D에 실어서 R5에 대입한다.

이제 이 동작을 수행되도록 각 control bit를 조정해보자.

 - AA : R5를 A<sub>data</sub>로 출력해야 한다. 따라서 R5의 주소인 101이어야 한다.
 - BA : B<sub>data</sub>는 사용되지 않는다. 따라서 아무 값이나 되어도 상관없다.
 - DA : D<sub>data</sub>를 R2에 대입해야 한다. 따라서 R2의 주소인 010이어야 한다.
 - AS : Address<sub>in</sub>과 A<sub>data</sub>중 A<sub>data</sub>를 선택해야 한다. 따라서 1이어야 한다.
 - BS : Bus B의 값은 사용되지 않는다. 따라서 아무 값이나 되어도 상관없다.
 - FS : F = A 연산을 해야 한다. 따라서 00000이어야 한다.
 - DS : ALU에서 나온 F값을 선택해야 한다. 따라서 0이어야 한다.
 - RW : Register file에서 R2에 값을 쓰는 작업을 해야 한다. 따라서 1이어야 한다.
 - MW : Memory에 쓰는 작업을 하지 않는다. 따라서 0이어야 한다.

따라서 `$ R2 \leftarrow R5 $`를 수행하는 control word는 아래와 같다.

![](/images/datapath_control_unit/cwregister0.png#center100)

그리고 이 과정에서의 데이터 흐름은 다음과 같다.

![](/images/datapath_control_unit/dpregister0.png#center75)

Control word가 어떻게 될지는 나와 있는 결과를 보는 것 보다 자신이 직접 각 상황에서 주소나 select를 어떻게 해야 할지 생각해 보는 것이 도움이 많이 된다. 이번에는 register에 상수 값을 대입하는 과정을 살펴보자. register에 대입할 상수값은 Constant<sub>in</sub>에 들어있다. 예를 들어 `$ R3 \leftarrow 7 $`을 한다고 하자. 위에서처럼 각 control bit가 어떻게 되야할지 생각해보고, 이렇게 만든 control word가 아래와 같은지 확인해보자.

![](/images/datapath_control_unit/cwregister1.png#center100)

이 과정에서의 데이터 흐름은 다음과 같다.

![](/images/datapath_control_unit/dpregister1.png#center75)

## Arithmetic & Logic Operation

위에서 ALU는 단순히 A나 B값을 그대로 출력했다. 이번에는 ALU가 실제 연산을 하는 경우를 살펴보자. ALU에서 연산하기 위해서는 Bus A와 Bus B에 피연산자를 담은 다음 FS값으로 어떤 연산을 할지 정하면 된다. 예를 들어 `$ R1 \leftarrow R3 + R4 $`인 경우 이를 수행하는 control word는 다음과 같다.

![](/images/datapath_control_unit/cwalu0.png#center100)

피연산자중 하나를 상수 값으로 하는 것도 가능하다. 이때 상수 값은 Constant<sub>in</sub>에 들어있다. 예를 들어 아래 control word는 `$ R2 \leftarrow R7 ~ \& ~ 1 $`을 수행한다.

![](/images/datapath_control_unit/cwalu1.png#center100)

만약에 1을 더하거나 빼는 경우 이 연산이 이미 ALU에 들어있다. 따라서 이 경우에는 상수 값을 사용하지 않아도 수행할 수 있다. 예를 들어 아래 control word는 `$ R6 \leftarrow R0 + 1 $`을 수행한다.

![](/images/datapath_control_unit/cwalu2.png#center100)

xor 연산은 같은 수에 대해서 항상 0이다. 따라서 자기 자신을 xor하면 항상 0이 나온다. 이를 이용하면 어떤 register 값을 0으로 초기화 하는 작업을 0이라는 상수 값을 사용하지 않아도 수행할 수 있다. 예를 들어 아래 control word는 `$ R1 \leftarrow 0 = R0 \oplus R0 $`를 수행한다.

![](/images/datapath_control_unit/cwalu3.png#center100)

## Memory Operation

이번에는 memory에서 데이터를 읽거나 memory에 데이터를 쓰는 작업을 해보자. 메모리 관련 작업에서는 아래 사항만 고려하면 된다.

 - Memory에 접근할 주소는 Bus A에 담는다.
 - Memory에서 데이터를 읽을 경우 읽은 데이터를 Bud D에 담는다.
 - Memory에 데이터를 쓸 경우 쓸 데이터를 Bus B에 담는다.

먼저 데이터를 읽는 작업을 먼저 해보자. 예를 들어 아래 control word는 `$ R2 \leftarrow M[R5] $`를 수행한다.

![](/images/datapath_control_unit/cwmemory0.png#center100)

이번에는 데이터에 값을 쓰는 작업을 해보자. 예를 들어 아래 control word는 `$ M[R0] \leftarrow R7 $`를 수행한다.

![](/images/datapath_control_unit/cwmemory2.png#center100)

여기서 Memory에 쓸 값을 특정 상수 값으로 할 수 있다. 이 때 데이터는 Constant<sub>in</sub>에 들어있다. 예를 들어 아래 control word는 `$ M[3] \leftarrow 24 $`를 수행한다.

![](/images/datapath_control_unit/cwmemory3.png#center100)

지금까지 여러 명령어들을 수행할 수 있는 control word들을 살펴봤다. 이제 이 control word를 만드는 control unit 부분을 살펴보자.

# Control Unit

처음에 얘기했던 것처럼 우리가 코드를 작성하면 이는 컴파일러를 통해 기계어인 instruction으로 변하고, 이 instruction을 분석해서 control word를 생성한다.

![](/images/datapath_control_unit/flow0.png#center30)

## Instruction Set Architecture (ISA)

Instruction은 CPU에 들어가기 직전인 최종 단계의 기계어이다. 우리가 코드를 짤 때 일정한 문법이 있어서 그 형식 안에서만 가능하듯이, instruction도 CPU마다 가능한 instruction의 종류가 정해져 있다. 이렇게 가능한 instruction들을 모아둔 것을 instruction set이라고 하고, 그 구조를 **Instruction Set Architecture (ISA)**라고 한다. 즉, 각 CPU는 그 CPU의 instruction set에 있는 instruction만 실행할 수 있고, instruction set 안에 있는 모든 CPU에 의해 분석되어 control word로 변환될 수 있다.

Instruction의 구조는 ISA마다 다르지만, 공통적으로 **opcode(operation code)**라는 부분을 가지고 있다. Opcode는 각 instruction이 어떤 작업을 하는지에 대해 나타낸다. 따라서 opcode가 n-bit라면 이는 2<sup>n</sup>개의 작업을 나타낼 수 있다. Control unit이 instruction을 분석할 때 제일 먼저 이 opcode를 통해 이 instruction이 어떤 작업을 하는지 파악하고, 이에 따라 나머지 bit가 무엇을 나타내는지 분석한다.

이러한 instruction들은 memory에 다른 데이터들과 같이 저장된다. 그렇다고 완전히 구분없이 저장되는 것은 아니고, memory의 특정 위치에 저장된 다음 순차적으로 불러오면서 프로그램을 실행한다. 이러한 시스템을 **stored program**이라고 하고, 이 시스템을 가지는 구조를 **von Neumann architecture**라고 한다. 이렇게 memory에 저장된 instruction을 실행하기 위해 현재 실행할 instruction을 가리키는 주소가 필요한데, 이를 **program counter(PC)**라고 한다. PC는 instruction이 실행될 때 마다 자동으로 1씩 증가하면서 다음 instruction을 실행할 수 있게 한다. 

## FDE cycle

CPU가 instruction을 수행하는 과정을 살펴보자.

 1. Memory에 저장된 instruction을 읽는다.
 2. instruction을 분석해서 control signal을 뿌린다.
 3. 위에서 나온 control signal을 바탕으로 연산을 한다.

여기서 1번을 **Fetch**, 2번을 **Decode**, 3번을 **Excute**라고 하고, 이러한 실행 사이클을 **FDE cycle**이라고 한다. 이중 datapath가 담당하는 부분이 Excute이고, control unit에서는 instruction을 읽어오는 fetch와 이를 분석하는 decode을 담당한다. 이 과정에서 이뤄지는 control unit, datapath, memory간의 상호작용은 다음과 같다.

![](/images/datapath_control_unit/interaction0.png#center50)

## Fetch

우선 control unit까지 적용한 CPU의 회로도에서 instruction이 fetch되는 과정을 보면 다음과 같다.

![](/images/datapath_control_unit/fetch0.png#center75)

PC가 datapath의 Address<sub>in</sub>을 타고 memory로 들어가서 instruction을 불러온다. 이 instruction은 Bus D에 담겨서 control unit으로 들어간다. control unit는 이 instruction과 NZCV flag를 통해 control word, Address<sub>in</sub>, Constant<sub>in</sub>을 만들어낸다.

## Decode

Instruction의 종류는 수십에서 수백 개로 많지만 하는 역할은 다음과 같이 크게 3개로 분류할 수 있다.

 - Arithmetic & Logic Operation
 - Data transfer
 - Control flow

하나씩 살펴보면서 각 instruction이 어떤 control word를 만들어내는지 살펴보자.

### Arithmetic & Logic Operation

여기에 속하는 instruction은 말 그대로 논리나 산술 연산을 한다. 따라서 연산을 할 피연산자와 연산의 결과가 저장될 곳이 필요하다. 예를 들어 2개의 register(R<sub>n</sub>, R<sub>m</sub>)를 계산해서 register(R<sub>d</sub>)에 저장하는 연산의 instruction은 아래와 같다.

![](/images/datapath_control_unit/cu0.png#center100)

이제 decode 과정에서 opcode를 통해 이 instruction이 두 register를 더해서 그 결과를 register에 저장하고자 함을 알아낸다. 그리고 다음과 같이 control word를 만들어낸다.

 - AA : 피연산자중 하나인 R<sub>n</sub>이다.
 - BA : 피연산자중 하나인 R<sub>m</sub>이다.
 - DA : 연산 결과가 저장될 R<sub>d</sub>이다.
 - AS : A<sub>data</sub>를 연산해야 하므로 1이다.
 - BS : B<sub>data</sub>를 연산해야 하므로 0이다.
 - FS : opcode에 따라 연산에 해당되는 fs값을 택한다.
 - DS : ALU에서 나온 F값이어야 하므로 0이다.
 - RW : 연산 결과를 저장해야 하므로 1이다.
 - MW : Memory에 쓰는 작업을 하지 않으므로 0이다.

만약에 피연산자중 하나가 Constant<sub>in</sub>일 경우 아래와 같은 instruction을 가진다.

![](/images/datapath_control_unit/cu1.png#center100)

이 경우 instruction의 constant로 constant<sub>in</sub>을 만들고, BS를 1로 하면 된다.

### Data transfer

Data transfer란 register에 저장된 값을 memory에 저장(store)하거나 memory에 저장된 값을 register에 불러오는(load) 작업을 의미한다. 이 작업을 하는 instruction은 다음과 같다.

![](/images/datapath_control_unit/cu2.png#center100)

여기서 R<sub>n</sub>은 memory의 주소가 저장된 register의 주소, R<sub>d</sub>는 memory에 저장하거나 memory의 데이터를 저장할 register의 주소이다. 만약 store를 하는 `$ M[Rn] \leftarrow Rd $`의 경우 다음과 같이 control word를 만들어낸다.

 - AA : memory에 저장할 주소가 저장된 R<sub>n</sub>이다.
 - BA : memory에 저장할 데이터가 저장된 R<sub>d</sub>이다.
 - DA : register에 저장하지 않으므로 어떤 값이든 상관없다.
 - AS : A<sub>data</sub>를 memory에 Address로 넘겨야 하므로 1이다.
 - BS : B<sub>data</sub>를 memory에 Data<sub>in</sub>으로 넘겨야 하므로 0이다.
 - FS : ALU 연산을 하지 않으므로 어떤 값이든 상관없다.
 - DS : Bus D를 사용하지 않으므로 어떤 값이든 상관없다.
 - RW : register에 저장하지 않으므로 0이다.
 - MW : Memory에 저장하므로 1이다.

만약 load를 하는 `$ Rd \leftarrow M[Rn] $`의 경우 AA, AS, FS는 그대로이고 나머지는 다음과 같이 달라진다.

 - BA : Bus B를 사용하지 않으므로 어떤 값이든 상관 없다.
 - DA : memory에서 꺼낸 값을 저장할 R<sub>d</sub>이다.
 - BS : Bus B를 사용하지 않으므로 어떤 값이든 상관 없다.
 - DS : memory에서 꺼낸 값을 저장해야 하므로 1이다.
 - RW : register에 저장하므로 1이다.
 - MW : Memory에 저장하지 않으므로 0이다.

### Control flow

Control flow는 위에서 datapath를 할 때 다루지 않았던 부분이다. 위에서 설명했듯이 instruction은 PC가 1씩 증가하면서 저장된 순서대로 실행된다. 그러나 조건문에 의해서 다음에 실행해야 할 instruction이 바뀌거나 반복문에 의해 특정 instruction이 반복되는 것처럼, 저장된 순서대로 바로 다음 instruction이 아니라 다른 곳에 저장된 instruction을 실행해야할 경우가 생긴다. 이러한 경우 PC가 해당 instruction을 가리키도록 바꾸는 작업을 하고, 이러한 작업을 control flow라고 한다. Control flow의 instruction은 다음과 같다.

![](/images/datapath_control_unit/cu3.png#center100)

여기서 NZCV는 이전 instruction에서 ALU연산을 한 후 저장된 flag로, 각각 **N**egative, **Z**ero, **C**arry out, o**V**erflow를 뜻한다. if와 같이 조건문을 검사할 때 이 NZCV를 통해 그 조건이 true인지 false인지 확인해서 PC를 변경할지 말지 정하게 된다. 예를 들어 `$ R1 == 7 $`을 검사하고 싶을 경우, 이전 instruction에서 `$ R1 - 7 $`을 연산한다. 그 다음 Zero flag를 보면 `$ R1 $`이 7인지 알 수 있다.

이를 통해 PC가 바뀌어야한다고 정해지면 `$ PC \leftarrow PC + offset $`을 한다. 이를 수행하는 control word는 다음과 같이 생성된다.

 - AA : A<sub>data</sub>를 사용하지 않으므로 어떤 값이든 상관없다.
 - BA : B<sub>data</sub>를 사용하지 않으므로 어떤 값이든 상관없다.
 - DA : register에 저장하지 않으므로 어떤 값이든 상관없다.
 - AS : PC값인 Address<sub>in</sub>을 사용해야 하므로 0이다.
 - BS : Constant<sub>in</sub>을 사용해야 하므로 1이다.
 - FS : 더하는 연산을 해야 하므로 00010이다.
 - DS : ALU에서 나온 F값이어야 하므로 0이다.
 - RW : register에 저장하지 않으므로 0이다.
 - MW : Memory에 저장하지 않으므로 0이다.
