---
title: "Trangister and Basic Logic Gate"
date: 2018-07-31T02:13:42+09:00
categories: ["code"]
subcategories: ["system architecture"]
tags: ["trangister", "basic logic gate", "functional completeness"]
nextp: "Combinational Logic"
prevp: ""
---

컴퓨터공학부에 입학하고 처음으로 들은 전공 관련 수업이 컴퓨터 개론 및 실습이었는데, 코딩을 배울 것이라고 생각했던 것과 달리 갑자기 반도체와 트랜지스터가 나와서 멘붕했던 내용이다. 그 당시에는 전기과도 아닌데 이런 것을 왜하나 생각을 했지만 배우고 나서 뒤의 흐름을 익히니 기초부터 하나씩 쌓아올려 나가려는 큰 그림이었음을 깨달았다.

# Transister

![](/images/trangister_and_basic_logic_gate/trangister0.png#center30)

트랜지스터는 위의 그림처럼 생긴 다리 세 개 달린 부품이다. 물론 생긴 게 중요한건 아니지만 이를 통해 입/출력이 3개라는 것은 알 수 있다. 트랜지스터도 종류가 여러 개가 있는데 그중 여기서 다룰 것은 MOS(Metal-Oxide-Silicon)트랜지스터이다.

MOS트랜지스터의 3개의 입/출력은 각각 G(gate), S(source), D(drain) 이라고 한다. MOS트랜지스터는 p-type과 n-type이 있는데, 이 둘은 역할이 정 반대이다. 우선 p-type부터 보자.

![](/images/trangister_and_basic_logic_gate/ptype0.png#center30)

p-type의 기호는 위와 같이 표기한다. 여기서 G값에 따라 S와 D가 연결되거나 끊어지는데, G가 0일 때 S와 D가 연결되고, 1이면 끊어진다. 즉, 아래와 같이 동작한다고 볼 수 있다.

![](/images/trangister_and_basic_logic_gate/ptype1.png#center30)

n-type은 p-type과 완전히 반대로 작용한다. 우선 n-type의 기호부터 보면 다음과 같다.

![](/images/trangister_and_basic_logic_gate/ntype0.png#center30)

위에서 말했듯이 n-type은 p-type과 반대로 작용하기 때문에 G가 0일 때 끊긴 회로가 되고 G가 1일 때 S와 D가 연결된다. 즉, 아래와 같이 동작한다.

![](/images/trangister_and_basic_logic_gate/ntype1.png#center30)

이 두개를 외울 때 쉽게 외우는 방법이 있다. p는 n과 달리 글자에 구멍이 있으니, p-type 게이트를 그릴 때도 동그라미를 그려주고, 0일때 둘이 연결된다. n-type은 p-type의 반대라고 생각하면 된다.

쨌든 둘은 G의 값에 따라 S와 D 사이를 연결하거나 끊는 스위치 같은 역할을 한다. 그러면

> 둘 중 하나만 쓰면 되는데 왜 두개나 존재해서 우리를 헷갈리게 만드냐!

라고 생각할 수 있다. 그 이유는 p와 n이 "잘" 전달하는 신호가 다르기 때문이다. 결론부터 말하자면 p는 1을 잘 전달하고, n은 0을 잘 전달한다. 이는 아래에서 더 자세히 다뤄보자.

# Basic Logic Gate

## NOT

우선 가장 간단한 1-input인 NOT부터 만들어보면 다음과 같이 구현할 수 있다.

![](/images/trangister_and_basic_logic_gate/not0.png#center30)

IN이 0일 경우 n-type인 접지 부분은 끊어지고, p-type인 V+부분은 연결된다. 즉, OUT은 V+와 연결되므로 1이 된다. IN이 1일 경우 반대로 접지부분이 연결되고 V+부분이 끊어지면서 OUT이 0이 된다. 즉, IN이 0이면 OUT이 1이고 IN이 1이면 OUT이 0이 되므로 NOT게이트와 같은 동작을 하게 된다.

## NAND

다음으로 조금 어려운 NAND 게이트를 만들어보자.

X | Y | OUT
:-:|:-:|:-:
0 | 0 | 1
0 | 1 | 1
1 | 0 | 1
1 | 1 | 0

 NAND는 NOT AND의 약자로 위의 표처럼 AND에 NOT을 한 것처럼 동작한다. 즉, X나 Y 둘중 하나라도 0이면 V+와 연결되도록 하고, 둘 다 1일 경우 접지부분이랑 연결되도록 하면 된다. 여기서 "둘 중 하나만"이라는 부분은 병렬로, "둘 다"라는 부분은 직렬로 회로를 구성함으로써 아래와 같이 NAND를 구현할 수 있다.

![](/images/trangister_and_basic_logic_gate/nand0.png#center30)

X, Y 둘 중 하나라도 0이면 p-type 부분에서 위나 아래 둘 중에서 적어도 하나는 V+와 연결된다. 또한 둘 중 하나라도 1이면 n-type 부분에서 적어도 하나는 끊어지므로 접지와는 끊어진다. 따라서 OUT은 1이다. X와 Y 둘 다 1이라면 p-type부분은 둘 다 끊어지고, n-type부분은 둘다 연결되므로 OUT은 접지와 연결되어 NAND게이트와 같은 동작을 하게 된다.

## AND

그러면 이제 NAND보다 친숙한 AND를 만들어보자. 회로를 어떻게 구성할지 고민할 필요 없다. NAND는 NOT AND이므로, 여기에 다시 NOT을 하면 NOT NOT AND로, AND가 된다. 따라서 아래와 같이 위에서 만든 두개를 합치면 AND게이트를 만들 수 있다.

![](/images/trangister_and_basic_logic_gate/and0.png#center50)

이때, 어쩌면 한 가지 의문점이 생길 수 있다.

> 위에처럼 AND게이트를 만들려면 트랜지스터가 6개가 드는데, 아래처럼 4개만 쓰고 만들 수 있지 않나요?

![](/images/trangister_and_basic_logic_gate/and1.png#center30)

그 해답은 위에서 잠깐 말한 p-type과 n-type 두개가 필요한 이유로 설명할 수 있다. 위에서 말했듯이 p-type은 1을 잘 전달하고, n-type은 0을 잘 전달하기 때문에 p-type은 V+를 전달할 때 사용하고, n-type은 접지부분을 연결할 때 사용한다. 따라서 위에처럼 p-type을 접지와 연결할 때 사용하고 n-type을 V+를 전달할 때 쓰는 식의 구현은 좋지 않으므로, 트랜지스터가 조금 더 들더라도 6개를 사용하는 방식으로 구현을 한다.

## NOR과 OR

NAND를 만들었을 때처럼 직렬과 병렬의 특성을 사용하면 아래와 같이 NOR을 구현할 수 있다.

![](/images/trangister_and_basic_logic_gate/nor0.png#center30)

그리고 이 NOR도 NOT과 결합해서 OR을 만들 수 있다.

![](/images/trangister_and_basic_logic_gate/or0.png#center50)

## Tristate Buffer

Tristate Buffer는 마치 스위치처럼 E가 1이면 D를 그대로 Out에 출력하고, 0이면 회로가 끊긴 효과를 낸다. 여기서 회로가 끊긴 것을 표현하기 위해 Hi-Z라는 새로운 상태가 나온다. Hi-Z란 High-impedance를 뜻하는 것으로, 0과는 달리 회로가 아예 끊어진, 통하지 않는 것을 뜻한다. 따라서 tristate-buffer의 동작은 아래 그림 같이 나타낼 수 있다.

![](/images/trangister_and_basic_logic_gate/tristatebuffer0.png#center50)

또한 이를 표로 표현하면 다음과 같다.

E | D | OUT
:-:|:-:|:-:
0 | x | Hi-Z
1 | 0 | 0
1 | 1 | 1

이를 구현하기 위해 우선 E부터 고려해보자. 만약에 E가 0이라면 V+랑 접지와 모두 끊어져야 한다. 따라서 V+에 연결되는 p-type에 1이 들어가야 하므로 NOT E가 들어가야 하고, 접지와 연결되는 n-type에는 0이 들어가야 하므로 그대로 E를 넣으면 된다. 이렇게 구성할 경우 E가 1이면 p-type, n-type모두 끊어지지 않고 연결시키므로 이 회로를 D가 작용하는 부분과 직렬로 연결해서 구성하면 된다.

D부분의 경우, D가 1일 때 V+ 부분은 연결시키고 접지 부분은 끊어져야한다. 따라서 p-type에는 0이 들어가야 연결되므로 NOT D가 들어가야 한다. 또한 n-type에도 0이 들어가야 끊어지므로 NOT D가 들어가야 한다. 이 경우 D가 0이면 자동으로 p-type은 끊어지고 n-type은 연결되면서 접지와 연결되게 된다. 이를 종합하면 다음과 같이 구성할 수 있다.

![](/images/trangister_and_basic_logic_gate/tristatebuffer1.png#center50)

# 왜 NAND와 NOR?

이렇게 우리는 트랜지스터부터 시작해서 NOT, NAND, AND, NOR, OR을 구현하는데 성공했다. 그런데 사실 NOT, AND, OR은 익숙하지만, NAND와 NOR은 약간 어색한 존재일 것이다. 그리고 왜 굳이 사용하는지 의문이 들 수 있다. 그러나 논리게이트면 에서는 NAND와 NOR은 오히려 AND와 OR보다 의미 있다고 할 수 있다. 그 이유는 아래와 같다.

## 트랜지스터 갯수

위에서 보았듯이 p-type과 n-type의 특성 때문에 AND와 OR은 트랜지스터가 6개가 사용되지만 NAND와 NOR은 4개로 구현할 수 있다. 개수로는 단순히 2개 차이지만 비율로 생각하면 1.5배이다. 즉, NAND나 NOR로 구현할 수 있는 것을 AND와 OR을 사용하면 부피나 비용 면에서 1.5배나 들게 된다.

## 함수적 완결성(Functional Completeness)

함수적 완결성은 우리가 생각할 수 있는 모든 논리함수를 구현 가능함을 뜻한다. 예를 들어 {AND, OR, NOT}은 함수적 완결성을 가진다. 즉, AND, OR, NOT을 사용하면 우리가 원하는 모든 논리함수를 구현할 수 있다. 이는 진리표를 이용하면 쉽게 증명할 수 있다.

X | Y | Z | OUT
:-:|:-:|:-:|:-:
0 | 0 | 0 | 0
0 | 0 | 1 | 1
0 | 1 | 0 | 1
0 | 1 | 1 | 0
1 | 0 | 0 | 0
1 | 0 | 1 | 0
1 | 1 | 0 | 1
1 | 1 | 1 | 0

예를 들어 위와 같은 함수가 있다고 하자(별 생각 안하고 이 글을 쓰면서 방금 막 만들었다.)

우리가 주목할 부분은 결과가 1이 되는 부분이다. 우선 `$ X,Y,Z $`가 `$ 0,0,1 $`일 때 OUT이 1이 된다. 이 때 0인 `$ X $`와 `$ Y $`에만 NOT을 붙인 후 다 AND를 한 `$(not ~ X) ~ and ~ (not ~ Y) ~ and ~ Z$` 라는 식을 생각해보자. 편의상 `$ not ~ X $`을 `$ X' $`, `$ and $`는 `$\cdot$`, `$ or $`는 `$ + $`로 표현하면 `$X' \cdot Y' \cdot Z$`이다. 이 식은 `$ X, Y, Z $`가 `$ 0,0,1 $`일 때만 1이고, 다른 경우에는 모두 0이다.

그렇다면 모든 1의 결과에 대해 이처럼 같은 식을 만들 수 있다.

`$ 0,0,1 \rightarrow X' \cdot Y' \cdot Z $`

`$ 0,1,0 \rightarrow X' \cdot Y \cdot Z' $`

`$ 1,1,0 \rightarrow X \cdot Y \cdot Z'$`

이후 이 식을 모드 OR하면 각 식은 자신을 만들어낸 `$ X,Y,Z $`값에 대해서만 1이므로 진리표와 같은 역할을 하게 된다. 즉, 아래 식은 위의 진리표와 동치이다.

`$$ X' \cdot Y' \cdot Z + X' \cdot Y \cdot Z' + X \cdot Y \cdot Z' $$`

이처럼 우리는 AND, OR, NOT만 있으면 모든 논리함수를 구현할 수 있다. 근데 NAND, NOR얘기하다가 갑자기 이 얘기는 왜하느냐? 그 이유는 NAND나 NOR은 자기 자신 하나로만으로도 함수적 완결성을 가지기 때문이다! 즉, 우리는 NAND 하나만 가지고도 모든 함수를 구현할 수 있다.

이는 NAND로 NOT, AND, OR을 구현함으로써 증명할 수 있다. 만약에 NAND로 NOT, AND, OR을 만들 수 있다면 {AND, OR, NOT}은 함수적 완결성을 가지므로 NAND도 함수적 완결성을 가지기 때문이다.

우선 NAND로 NOT을 만드는 건 쉽다. `$ X \cdot X $` 는 `$ X $` 이고, NAND는 NOT AND이므로 `$ X ~ nand ~ X $` 는 NOT X이다. [게이트 기호](https://ko.wikipedia.org/wiki/%EB%85%BC%EB%A6%AC_%ED%9A%8C%EB%A1%9C)를 이용해서 그리면 아래와 같다.

![](/images/trangister_and_basic_logic_gate/nand2not0.png#center30)

NOT을 구현하는데 성공했으면 AND는 매우 쉽다. NAND에 NOT을 하면 AND이기 때문이다. 따라서 아래와 같이 NAND와 NOT을 결합하면 AND를 구현할 수 있다.

![](/images/trangister_and_basic_logic_gate/nand2and0.png#center30)

OR은 구현하려면 머리를 좀 써야한다. 여기서 [드 모르간의 법칙](https://ko.wikipedia.org/wiki/%EB%93%9C_%EB%AA%A8%EB%A5%B4%EA%B0%84%EC%9D%98_%EB%B2%95%EC%B9%99)이 사용된다. 드 모르간의 법칙에 따르면 `$ (X \cdot Y)' $` 와 `$ X' + Y' $`는 동치이다. 여기서 `$ X $`와 `$ Y $`를 `$ X' $`와 `$ Y' $`로 바꾸면 다음과 같이 정리된다.

`$$ (X' \cdot Y')' = (X')' + (Y')' = X + Y $$`

그리고 우리는 NOT과 NAND를 구현할 수 있으니 `$ X' ~ nand ~ Y' = (X' \cdot Y')' $`을 구현할 수 있다. 이를 구현하면 아래와 같다.

![](/images/trangister_and_basic_logic_gate/nand2or0.png#center30)

NOR도 위와 같은 방식을 사용하면 AND, OR, NOT을 만들 수 있다. 따라서 NAND나 NOR은 자기 자신 혼자서도 함수적 완결성을 가진다.

이러한 점은 공장의 입장에서 굉장한 중요성을 가진다. 하나의 종류만 찍어내는 것은 당연히 여러 종류의 부품을 찍어내는 것보다 엄청난 효율을 가지게 된다. 즉, 공장에서 NAND게이트만 엄청 찍어낸다 하더라도 우리는 모든 종류의 논리함수를 구현할 수 있다. 따라서 NAND와 NOR은 조금 어색하지만 굉장히 중요한 논리 게이트이다.