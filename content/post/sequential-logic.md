---
title: "Sequential Logic"
date: 2019-02-03T19:53:09+09:00
categories: ["code"]
subcategories: ["system architecture"]
tags: ["Sequential Logic", "Register", "Latch", "memory", "clock", "Flip-flop", "RAM"]
nextp: "Datapath"
prevp: "Combinational Logic"
---

sequential logic이란 같은 input이 들어왔다고 해도 logic의 state나 memory에 따라 결과가 달라지는 logic을 의미한다. 이러한 sequential logic이 존재할 수 있는 중요한 요소중 하나는 gate가 이상적이지 않아서 delay가 존재한다는 것이다.

## Clock

sequential logic들은 회로도를 보면 이상적으로 작동한다면 말이 안되는 회로들이 많다. 어쩌면 그렇기 때문에 delay라는 요소가 들어감으로써 combinational logic과 달리 같은 input에도 다른 output을 낼 수 있는 것일 수도 있다. 이전 챕터에서 마지막에 다뤘던 clock 회로를 다시 봐보자.

![](/images/sequential_logic/oscilator0.png#center50)

여기서 만약 A가 1이라면,

`B = (A NAND D) = (1 NAND D) = D' = C = B'`

따라서 `B = B'`라는 말도 안되는 결과가 나오게 된다. 그러나 이 회로에 delay라는 요소가 추가됨으로써 아래처럼 아주 신기한 작동을 하게 된다.

![](/images/sequential_logic/oscilator1.png#center75)

이처럼 0과 1이 일정하게 반복하는 회로를 clock라고 한다. 참고로 cpu 성능을 결정하는 요소중 하나인 clock speed의 clock이 이 clock이다. 뒤에서 보면 알게 되겠지만 0과 1이 빨리 반복 될 수록 데이터가 빨리 처리되기 때문에 clock speed가 빠를수록 cpu 성능이 좋아진다고 보면 된다.

![](/images/sequential_logic/clock0.png#center50)

clock의 결과부분만 그리면 이처럼 되는데, 0에서 1로 올라오는 부분을 rising edge, 1에서 0을 떨어지는 부분을 falling edge라고 한다. 또한 rising edge에서 다음 rising edge(혹을 falling edge에서 다음 falling edge)까지를 한 cycle이라 한다.

## Register

이제 본격적으로 sequential logic을 만들어보자. 먼저 특정한 값을 저장하고 출력할 수 있는 회로인 Latch와 Flip-flop을 만들어 볼 것이다. 이후 이를 점점 발전시켜 가장 기본적인 저장 기능을 하는 회로인 register를 만들어보자.

### SR Latch

제일 먼저 만들어 볼 것은 SR Latch로, 저장된 값을 1 또는 0으로 Set/Reset할 수 있고 이 값을 보존한 채로 계속 출력할 수도 있는 회로이다. 자세한 회로도와 진리표는 다음과 같다.


![](/images/sequential_logic/srlatch0.png#center50)

S | R | Q
:-:|:-:|:-:
 0 |0 | Q<sub>prev</sub>
 0 | 1 | 0
 1 | 0 | 1
 1 | 1 | undefined

Q에 대한 식을 생각해보면, `Q = (R NOR (S NOR Q_prev)) = (R' AND (S OR Q_prev))`이다.

따라서 S가 0, R이 0이면 `Q = Q_prev`가 되므로 보존이 된다. SR이 01일 때와 10일 때 Q가 0 ,1이 됨도 위의 진리식에서 쉽게 알 수 있다. 아래 그림을 통해 더 상세한 Q의 변화 과정을 살펴보자.

![](/images/sequential_logic/srlatch1.png#center75)

Set bit가 1이 되면 1과 NOR을 하면 무조건 0이므로 Q'이 먼저 0이 된다. 이후 0과 0을 NOR을 하면 1이 되므로 Q가 1이 된다. 마찬가지로 Reset bit가 1이 되면 Q가 먼저 0이 되고, 이후 이 Q와 S가 NOR을 해서 Q'이 1이 된다.

S와 R이 모두 1인 경우에는 Q와 Q' 모두 0이 된다. 그러나 이 경우에는 Q와 Q', 즉 두 output이 서로 반대라는 의도에 어긋난다. 이러한 경우 두 output이 서로 반대라고 가정한 상태에서 SR Latch를 이용한 회로를 구성할 때 원하지 않은 결과가 나올 수 있다. 따라서 undefined가 나오지 않도록 input에 각별히 주의해야한다. 그러나 언제나 인간은 실수를 하기 마련이다. 따라서 아얘 회로적으로 이러한 경우가 절대 나오지 않도록 개선한 것이 D Latch이다.

### D Latch

SR Latch를 잘 이해했다면 D Latch는 크게 어려울 것이 없다. 왜냐하면 SR Latch에서 input 부분만 바꿔서 11이 input으로 들어가지 못하게 한 것이 D Latch이기 때문이다. D Latch의 회로도와 진리표는 다음과 같다.

![](/images/sequential_logic/dlatch0.png#center50)

C | D | Q
:-:|:-:|:-:
 0 | x | Q<sub>prev</sub>
 1 | 0 | 0
 1 | 1 | 1

 C가 0이라면 모두 AND게이트에 연결되어있으므로 SR에 00이 들어간다. 따라서 Q값이 그대로 보존된다. 만약에 C가 1이라면 S에 D, R에 D'이 들어가게 된다. 따라서 D가 0이면 Reset, D가 1이면 Set이 되고, 11이 들어가는 경우는 절대 일어나지 않는다.

 D Latch는 SR Latch보다 작동이 좀 더 직관적이라는 장점도 있다. 우선 값이 보존/바뀜이 C값에만 의존하게 된다. 따라서 값을 보존하고 싶으면 C를 0으로, 바꾸고 싶으면 C를 1로 하면 된다. 또한 바뀌는 값도 D와 완벽하게 같다. 즉, D Latch에 대한 진리표를 아래처럼도 나타낼 수 있다.

 C | Q
:-:|:-:
 0 | Q<sub>prev</sub>
 1 | D

D Latch에서 값이 변화하는 과정을 보면 다음과 같다.

![](/images/sequential_logic/dlatch1.png#center75)

C가 1일 경우, D의 값에 따라 Q도 계속 바뀐다. 따라서 Q값을 바꾸고 난 후 한동은 그 값으로 유지하고 싶다면 값을 바꿀 때만 C를 잠깐 1로 올렸다가 내리거나, D값을 계속 일정한 값으로 유지해야 한다. 이러한 불편함을 해소하기 위해 어느 순간에 Q값을 D값으로 바꾸고, 추가적인 조작 없이도 일정 시간동안 그 값을 유지할 수 있게 고안한 회로가 D Flip-flop이다.

### D Flip-flop

![](/images/sequential_logic/dflipflop0.png#center75)

D Flip-Flop은 위의 그림처럼 D-Latch 두개와 처음에 다룬 clock으로 구성된다. 기호로 그릴 땐 오른쪽 그림처럼 clock 부분에 삼각형 표시를 해둔다. clock의 변화에 따른 D Flip-flop의 동작을 보자.

 1. clock에서 0일 때 master에는 1이 들어가고 slave에는 0이 들어간다. 이때 master에서 input을 가지고 있는다.
 1. rising edge가 되면 master에는 0이 들어오면서 그 값을 유지하게 되면서 다시 falling edge가 될 때까지 값이 바뀌지 않는다. 그리고 slave가 그 input을 받아서 출력하게 된다. 
 1. falling edge가 되면 slave에는 0이 들어가므로 이전의 input을 그대로 출력하게 된다. 
 1. 반복

이를 종합하여 결론적인 output인 slave의 작동만 보자면, rising edge때의 입력을 한 clock cycle동안 출력하게 된다. 따라서 아래와 같은 동작을 보여주게 된다.

![](/images/sequential_logic/dflipflop1.png#center50)

### Register

이제 거의 최종단계에 다 왔다. 이번에 다루는 register가 흔히 말하는 cpu에 들어가는 그 register이다. 우리가 사용하는 register는 값을 받아서 저장하고, 원할 때 그 값을 출력할 수 있어야 한다. 그렇다면 모든 rising edge에 값이 바뀌는 것이 아니라 내가 원할 때 값을 바꿀 수 있어야 한다. 이러한 작동을 하도록 하기 위해 MUX를 사용하여 다음과 같이 구현할 수 있다.

![](/images/sequential_logic/register0.png#center50)

위의 D Flip-flop만 이해했다면 register의 동작을 이해하기엔 크게 어렵지 않다. D Flip-flop에서 개선해야할 점은 바꾸지 않아야 할 때 값이 바뀌는 것을 방지하는 것이다. 따라서 load가 0이면 원래 가지고 있던 값을 다시 D flip-flop에 input으로 넣어주면서 그 값을 유지하게 한다. 만약에 새로 값을 load 해야 할 상황이라면 load에 1을 넣어 MUX가 새로운 input을 D flip-flop에 넣어주게 한다. 그러면 D flip-flop때와 같이 다음 rising edge때 값이 갱신 될 것이다. 따라서 다음과 같이 정리할 수 있다.

- load가 0이라면 저장된 값이 변하지 않고 계속 같은 값을 출력한다.
- load가 1이라면 다음 rising edge 때 그 시점의 input으로 저장된 값이 갱신된다.

이 동작을 그래프로 보면 다음과 같다.

![](/images/sequential_logic/register1.png#center50)

또한 이 register를 n개를 겹쳐서 사용하면 n-bit data를 저장할 수 있는 n-bit register를 구현한 수 있다. 이제 이 register를 메모리 관련 회로의 가장 기본 단위처럼 삼아 메모리를 구현해보자.

## RAM

RAM이란 Random Access Memory의 약자이다. 여기서 random access라는 의미는 의도치 않은 random한 곳으로 접근이 된다는 것이 아니라 어떤 random한 곳이라도 바로 접근을 하여 사용할 수 있다는 뜻이다. 따라서 다음과 같은 특징을 가진다.

- 단어의 크기가 w-bit라면 w개의 bit가 모두 동시에 저장되고, 출력된다.
- m-bit의 adress를 사용해서 어떤 단어를 고를지 선택한다. 따라서 총 2^m개의 단어를 저장할 수 있고, 각 단어는 고유의 주소값을 가진다.

이 RAM을 만들기 위해 언제나처럼 가장 작은 Memory cell 부터 구현해서 점점 확장해보자.

### Memory cell

우선 register를 약간 바꿔서 1-bit짜리 1개의 data를 처리하는 memory cell을 만들어보자. memory cell은 3개의 input인 DI, select, write와 1개의 output인 DO가 있다.

select는 이 cell이 선택되었는지를 뜻하는 bit이다. 따라서 select가 1일 경우에면 이 cell에 data가 쓰이거나 출력될 수 있고, 0이면 아무런 작동도 하지 않는다. 여기서 아무런 작동을 하지 않는다는 의미는 DO가 0이란 뜻이 아니라 Hi-Z라는 의미이다.

write는 이 cell에 저장된 값을 DI로 갱신할지 정하는 bit이다. 따라서 write가 1이면 cell에 저장된 값이 DI로 갱신되고, 0일 경우 저장된 값이 변하지 않은 채로 그대로 출력한다. 단, 이러한 작동을 하기 위해선 우선 select가 1이어야 한다.

따라서 Data가 read, write가 되는 경우를 다음과 같이 정리할 수 있다.

 * **read** : select가 1이고 write가 0일 경우 cell에 저장된 값이 DO에 출력된다.
 * **write** : select가 1이고 write가 1일 경우 cell에 저장된 값이 DI로 바뀐다. 이 때 값이 바뀌는 시기는 clock의 다음 rising edge이다.

값이 바뀌는 경우는 select와 write가 모두 1일 경우이다. 따라서 register의 load는 select와 write를 AND해서 연결하면 된다. 또한 select가 1이고 write가 0일 경우에만 cell에 저장된 값이 출력되어야 하고, 나머지 경우엔 Hi-Z여야 한다. 이를 위해선 write에 NOT을 한 것과 select를 AND해서 tristate-buffer에 연결하면 된다. 따라서 다음과 같이 구현할 수 있다.

![](/images/sequential_logic/memorycell0.png#center50)

### 4 by 4 RAM

이제 이 memory cell을 조합해서 4-bit짜리 단어를 4개 저장할 수 있는 RAM을 만들어보자. 이 RAM에는 4개의 input인 addr, DI, write, CS와 1개의 output인 DO로 구성되어 있다.

 - addr은 4개의 단어중 어떤 단어를 사용할지 선택하는 주소(address)이다. 여기에서는 단어가 총 4개이므로 2-bit adress를 사용한다.
 - CS는 chip select로, 이 4 by 4 RAM 자체를 동작 시킬지 말지 정하는 bit이다.
 - write는 memory cell에서의 wirte와 동일하다. write가 1이라면 addr에 의해 선택된 단어가 DI로 갱신된다.

따라서 이 RAM의 read, write 동작은 다음과 같이 정리할 수 있다.

 * **read** : CS가 1이고 write가 0일 경우 addr에 의해 선택된 단어가 DO로 출력된다.
 * **write** : CS가 1이고 write가 1일 경우 addr에 의해 선택된 단어가 DI로 갱신된다. 이 때 값이 바뀌는 시기는 clock의 다음 rising edge이다.

먼저 단어를 선택하는 것 부터 구현해보자. 우리는 2-bit의 addr을 받아서 4개의 단어중 하나만 골라야 한다. 단어를 선택하는 방법은 선택된 단어의 select만 1이 들어가게 하고, 나머지 단어에는 0이 들어가게 하면 된다. 따라서 2-bit의 addr을 읽어서 4개중 해당된 한 bit에만 1을 주고 나머지 bit에는 0을 줘야하므로, decoder를 이용하면 구현할 수 있다. 

이후 과정은 register를 cell로 변환시킨 것 처럼 적용하면 된다. 즉, write와 CS를 AND해서 cell의 write에 연결하고, write에 NOT을 한 것과 CS를 AND해서 tristate-buffer에 연결하면 된다. 따라서 다음과 같이 구현할 수 있다.

![](/images/sequential_logic/4by4ram0.png#center75)

### RAM 확장

Combinational circuit에서 DEMUX 등을 다룰 때 작은 DEMUX를 합쳐서 더 큰 DEMUX를 만들 수 있었다. 여기서도 같은 방법으로 작은 RAM을 조합해서 더 큰 RAM을 만들 수 있다. RAM을 확장하는 방법은 다음과 같은 두가지 방법이 있다.

#### More word

우선 저장하는 단어 수를 늘리는 방법에 대해 생각해보자. 저장할 수 있는 단어의 양이 2배로 늘어날 때마다 address는 1-bit 증가한다. 그리고 이 bit는 decoder로 변환되어 memory cell에 전달된다. 따라서 decoder를 확장했던 것과 같은 방식을 사용하여 아래와 같이 확장할 수 있다.

![](/images/sequential_logic/moreword0.png#center75)

#### Bigger word

단어의 길이를 늘리는 방법은 훨씬 쉽다. 우선 같은 주소에 같은 동작을 헤야하니 addr, write, CS는 공유를 하면 된다. 그리고 data는 나눠서 들어간다음 합쳐서 받으면 된다. 따라서 다음과 같이 구현할 수 있다.

![](/images/sequential_logic/biggerword0.png#center75)

## Register file

이제 위의 RAM을 조금 개조한 Register file을 보자. Register file은 다음과 같은 동작을 할 수 있다.

 * 총 8개의 32-bit register를 가진다. 따라서 address는 3-bit, data는 32-bit이다.
 * 동시에 2개의 data(A, B)를 읽을 수 있다. 따라서 2개의 주소값 A<sub>addr</sub>, B<sub>addr</sub>와 2개의 output A<sub>data</sub>, B<sub>data</sub>를 가지고 있다.
 * 위의 작용과 동시에 하나의 data를 쓸 수 있다. 따라서 또 하나의 주소값 D<sub>addr</sub>와 write bit, 그리고 input으로 D<sub>data</sub>를 가진다.

따라서 다음과 같이 생겼다.

![](/images/sequential_logic/registerfile0.png#center50)

이제 이 register file과 memory, 그리고 sequential circuit을 다룰 때 만들었던 ALU를 합치면 다음에 다룰 간단한 CPU를 구현할 수 있다. 