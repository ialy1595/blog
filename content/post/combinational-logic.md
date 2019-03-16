---
title: "Combinational Logic"
date: 2018-09-26T18:27:42+09:00
categories: ["code"]
subcategories: ["system architecture"]
tags: ["Combinational logic", "Boolean Expression", "Algebraic Structure", "Canonical Forms", "Karnaugh map", "Arithmetic Logic Unit", "Delay"]
nextp: "Sequential Logic"
prevp: "Trangister and Basic Logic Gate"
---

Combinational Logic에서는 이전에 basic logic gate를 다루면서 만들었던 재료인 NOT, AND, OR, NAND, NOR을 가지고 요리를 해볼 것이다. 하지만 그 전에 Combinational Logic과 Sequential Logic의 차이 대해 먼저 알아보자.

Combinational logic이란 언제나 어디서나 눈이 오던 바람이 불던 남녀노소 같은 input을 넣으면 같은 output이 나오는 것을 뜻한다. 예를 들면 ADDER를 생각해보자. 내가 ADDER에 `2`와 `3`을 집어넣으면 오늘 넣든 내일 넣든 항상 `5`를 반환할 것이다.

이와 대비되는 sequential logic은 내가 같은 input을 넣어도 상황에 따라 output이 바뀔 수 있는 것을 뜻한다. 예를 들어 내가

> 너의 책장의 왼쪽에서 세번째 책이 뭐야?

라고 물어본다고 해보자. 나는 분명 `너의 책장`과 `왼쪽에서 세번째` 라는 같은 input을 주지만, 오늘 물어본 결과와 내년에 물어본 결과가 다를 수 있다. 이처럼 logic의 내부 state나 memory에 의해 같은 input이어도 output이 다를 수 있는 경우를 sequential logic이라고 한다.

둘 중에 딱 봐도 combinational logic이 더 쉬워보인다. 그러니 combinational logic부터 알아보자.

# Boolean Expression

우리가 만들어 볼 것은 0과 1들을 받아서 0과 1들을 반환하는 boolean function이다. 하지만 우리가 이 boolean function을 표현할 때 언제까지나 공간도 많이 잡아먹고 별 효과도 없는 진리표만 주구장창 그릴 것인가? 우리는 더 효과적으로 boolean function을 표현하는 방법을 배울 필요가 있다. 따라서 효과적으로 boolean function을 표현하는 두가지 방법을 배워보자.

## Algebraic Structure

우리가 Boolean fnction을 표현할 때 `(A AND B) OR C` 이런식으로 표현하는데, 이를 잘 보면 문자, 기호(AND, OR, ...), 괄호로 이루어져 있다. 그렇다면 우리가 수학에서 식을 쓰듯이 이것도 식처럼 쓸 수 있지 않을까? 해서 나온 것이 boolean algebra이다. boolean algebra는 다음과 같은 규칙을 가진다.

1. 모든 변수의 값은 `1` 또는 `0`이다. 이때 `1`은 V+, `0`은 접지상태 처럼 생각하면 된다.
2. `+`는 OR, `*`는 AND이다. 이 때 AND는 곱하기를 생략하는 것 처럼 알파벳을 붙여쓰는 것으로 생략할 수 있다.
3. `'`(unary operation, 뒤에 붙임)은 NOT이다.

예를 들어 `(A OR (NOT B)) AND C` 같은 식은 `(A + B')C`로 간단하게 표현할 수 있다.

또한 이러한 표현도 algebra에 속하기 때문에 수학에서 하는 것처럼 공리와 성질을 가진다.

1. Identity: `X + 0 = X`, `X * 1 = X`
1. Null: `X + 1 = 1`, `X * 0 = 0`
1. Idempotency: `X + X = X`, `X * X = X`
1. Involution: `(X')' = X`
1. Complementarity: `X + X' = 1`, `X * X' = 0`
1. Commutativity: `X + Y = Y + X`, `X * Y = Y * X`
1. Associativity: `(X + Y) + Z = X + (Y + Z)`, `(X * Y) * Z = X * (Y * Z)`
1. Distributivity: `X * (Y + Z) = (X * Y) + (X * Z)`, `X + (Y * Z) = (X + Y) * (Y * Z)`
1. Uniting: `X * Y + X * Y' = X`, `(X + Y) * (X + Y') = X`
1. Absorption: `X + X * Y = X`, `X * (X + Y) = X`, `(X + Y') * Y = X * Y`, `X * Y' + Y = X + Y`
1. Factoring: `(X + Y) * (X' + Z) = X * Z + X' + Y`, `X * Y + X' * Z = (X + Z) * (X' + Y)`
1. Concensus: `X * Y + Y * Z + X' * Z = X * Y + X' * Z`, `(X + Y) * (Y + Z) * (X' + Z) = (X + Y) * (X' + Z)`

이를 잘 이용하면 나중에 긴 boolean expression을 짧게 줄일 수 있다. 굳이 막 외울 필요까진 없다.

## Canonical Forms

이제 이를 이용하여 truth table로 표현된 boolean function을 boolean expression으로 바꿔보자.

A | B | C | OUT
:-:|:-:|:-:|:-:
0 | 0 | 0 | 0
0 | 0 | 1 | 1
0 | 1 | 0 | 1
0 | 1 | 1 | 0
1 | 0 | 0 | 0
1 | 0 | 1 | 0
1 | 1 | 0 | 1
1 | 1 | 1 | 0

만약 AND처럼 특별한 의미가 있고 이미 우리가 잘 아는 함수라면 괜찮겠지만, 위에 처럼 아무런 특징 없는 처음 보는 표만 보고 당장 이와 같은 결과가 나오는 boolean expression을 쓰라고 하면 막막할 것이다. 이를 해결하기 위해 우리는 canonical form이라는 것을 쓸 것이다. Canonical form은 Sop와 PoS로 두가지 종류가 있다.

### Sum-of-Products(SoP)

SoP는 말 그대로 곱들의 합으로 표현하는 것이다. 여기서 우리는 저 truth table의 1에 주목한다.

첫번째 1은 A가 0, B가 0, C가 1일 때 결과가 1이 나온다. 이 경우에만 1이 나오고 그 외에는 모두 0이 나오는 product 형태는 `A'B'C`이다. 같은 방법으로 두번째 1의 product 꼴은 `A'BC'`이고, 세번째는 `ABC'`이다. 이 세 product 꼴이 SoP에서 굉장히 중요한 역할을 한다. 결과가 1이어야 하는 A, B, C값이라면 이 세 식중 적어도 하나는 1이 나온다. 또한 저 세 경우가 아니라면 이 세 식은 모두 0이 된다. 따라서 이 세 식을 모두 OR을 하게 되면 truth table과 같은 결과가 나오게 된다. 즉, `A'B'C + A'BC' + ABC'`이 저 truth table과 같은 결과를 주는 boolean expression이 된다. 이처럼 1이 되는 결과를 product 형태로 나타낸 후 이를 모두 sum하기 때문에 Sop Canonical Form이라고 부른다. 이를 더 쉽게 표현하기 위해 다음과 같은 기호와 식을 사용한다.

A | B | C | expression | minterms
:-:|:-:|:-:|:-:|:-:
0 | 0 | 0 | A'B'C' | m<sub>0</sub>
0 | 0 | 1 | A'B'C | m<sub>1</sub>
0 | 1 | 0 | A'BC' | m<sub>2</sub>
0 | 1 | 1 | A'BC | m<sub>3</sub>
1 | 0 | 0 | AB'C' | m<sub>4</sub>
1 | 0 | 1 | AB'C | m<sub>5</sub>
1 | 1 | 0 | ABC' | m<sub>6</sub>
1 | 1 | 1 | ABC | m<sub>7</sub>

이 표현식을 사용하면 위 함수는 m<sub>1</sub>, m<sub>2</sub>, m<sub>6</sub>이 1이다. 따라서 다음과 같이 표현할 수 있다.

`$\begin{eqnarray} 
F(A,B,C) &=& \sum m(1,2,6)   \\
&=& m1+m2+m6   \\
&=& A'B'C+A'BC'+ABC'
\end{eqnarray}$`

### Products-of-Sum(PoS)

Pos는 Sop와 반대로 합들의 곱으로 나타내는 것이다. SoP의 경우 결과중 1에 주목했던 것 처럼, SoP의 경우 0에 집중한다.

맨 처음 0의 경우 A, B, C 값이 모두 0일 경우 0이다. 이 경우에만 0이 나오고 그 외에는 모두 1이 나오는 sum 형태는 `A + B + C`이다. 위에서 했던 것 처럼 이런 방식으로 다른 0들도 sum 꼴로 나타낸다. 이후 이 항을 다 product 하면 SoP 형이 완성된다. 즉, 위의 truth table과 동치인 SoP는 `(A + B + C)(A + B' + C')(A' + B + C)(A' + B + C')(A' + B' + C')`이다.

이 또한 기호와 식을 사용하면 다음과 같이 더욱 간단하게 나타낼 수 있다.

A | B | C | expression | minterms
:-:|:-:|:-:|:-:|:-:
0 | 0 | 0 | A + B + C | M<sub>0</sub>
0 | 0 | 1 | A + B + C' | M<sub>1</sub>
0 | 1 | 0 | A + B' + C | M<sub>2</sub>
0 | 1 | 1 | A + B' + C' | M<sub>3</sub>
1 | 0 | 0 | A' + B + C | M<sub>4</sub>
1 | 0 | 1 | A' + B + C' | M<sub>5</sub>
1 | 1 | 0 | A' + B' + C | M<sub>6</sub>
1 | 1 | 1 | A' + B' + C' | M<sub>7</sub>

`$\begin{eqnarray} 
F(A,B,C) &=& \prod M(0,3,4,5,7)   \\
&=& M0 \cdot M3 \cdot M4 \cdot M5 \cdot M7   \\
&=& (A+B+C)(A+B'+C')(A'+B+C)(A'+B+C')(A'+B'+C')
\end{eqnarray}$`

### Two-level implementation

이렇게 SoP나 PoS로 나타난 함수들은 모두 Two-level implementation을 할 수 있다. Two-level implement를 하기 위해서는 우선 de Morgan's theorem을 알아야 한다.

[de Morgan's theorem](https://en.wikipedia.org/wiki/De_Morgan%27s_laws)이란 다음 두 법칙을 뜻한다.

 * `$ (A_1 + A_2 + \ldots + A_n)' = A_1 ' \cdot A_2 ' \cdot \ldots \cdot A_n ' $`
 * `$ (A_1 \cdot A_2 \cdot \ldots \cdot A_n)' = A_1 ' + A_2 ' + \ldots + A_n ' $`

 즉, 전체에 NOT을 한 것은 각각에 NOT을 한 다음 `*`은 `+`로, `+`는 `*`로 바꾼 것과 결과가 같다는 것이 드 모르간의 법칙이다. 이를 이용하면 canonical form을 효과적으로 구현할 수 있다. 예시로 SoP 형태로 만든 것을 구현해보자.

 우선 위 함수의 SoP 형태를 실제 회로로 다음과 같이 구현할 수 있다.

![](/images/combinational_logic/sop0.png#center50)

 위에 공리에서의 Involution 처럼 NOT 연산을 두번 하면 그대로이다. 따라서 다음과 같이 표현할 수 있다. 원이 NOT을 표현하는 기호이다.

![](/images/combinational_logic/sop1.png#center50)

 이제 드 모르간의 법칙을 사용해보자. 드 모르간의 법칙에 따르면 `D' + E' + F' = (DEF)'` 이므로 다음과 같이 나타낼 수 있다.

![](/images/combinational_logic/sop2.png#center50)

 따라서 모든 게이트를 NAND만 사용하여 SoP 형태를 구현할 수 있다. PoS 꼴로 나타낸 것도 위에 처럼 OR연산을 한 뒤 이들을 AND 연산하는 것으로 구현한 후 NOT을 두개씩 붙이면 모든 게이트를 NOR만 사용하여 구현할 수 있다. 우리는 지난시간에 NAND와 NOR이 트랜지스터 수가 적은 아주 효율적인 게이트임을 배웠다. 따라서 canonical form으로 만든 후 회로로 구현을 하면 효율적인 구현을 할 수 있다.

 하지만 그렇다고 canonical form이 만능은 아니다 예를 들어 다음 함수를 보자.

 A | B | C | D | OUT
 :-:|:-:|:-:|:-:|:-:
 0 | 0 | 0 | 0 | 0
 0 | 0 | 0 | 1 | 1
 0 | 0 | 1 | 0 | 0
 0 | 0 | 1 | 1 | 1
 0 | 1 | 0 | 0 | 0
 0 | 1 | 0 | 1 | 1
 0 | 1 | 1 | 0 | 0
 0 | 1 | 1 | 1 | 1
 1 | 0 | 0 | 0 | 0
 1 | 0 | 0 | 1 | 1
 1 | 0 | 1 | 0 | 0
 1 | 0 | 1 | 1 | 1
 1 | 1 | 0 | 0 | 0
 1 | 1 | 0 | 1 | 1
 1 | 1 | 1 | 0 | 1
 1 | 1 | 1 | 1 | 1

이 함수는 `ABC + D` 라는 함수로, 회로 구현에도 AND와 OR 하나씩만 있으면 된다. 그러나 이 함수를 canonical form으로 표현하면

`A'B'C'D + A'B'CD + A'BC'D + A'BCD' + AB'C'D + AB'CD + ABC'D + ABCD' + ABCD`

라는 굉장히 긴 식이 되고, 이를 구현하려면 10개의 NAND 게이트가 필요하다. 따라서 우리는 식을 효과적으로 간소화할 수 있는 새로운 방법을 찾을 필요가 있다.

## Karnaugh map

카르노맵의 기본 기념은 위의 공리중 Uniting 중 `X * Y + X * Y' = X` 에서 시작되었다. 우선 카르노맵을 그리는 방법을 먼저 배워보자.

![](/images/combinational_logic/karnaugh0.png#center50)

Input이 네개일 경우 위와 같이 4 by 4 표로 그리면 된다. 이 표의 둘레에 두꺼운 선으로 표시하여 알파벳을 써분 부분이 있는데, 이는 그 부분이 해당 알파벳이 1인 칸임을 뜻하는 것이다. 예를 들어 A의 경우 오른쪽 두 열이 A가 1인 경우를 나타내는 칸이고, B의 경우 가운데 두 열이 B가 1이 된다. 이처럼 나타내면 4 by 4 칸 안에 ABCD가 0000인 경우부터 1111인 경우까지 모두 나타낼 수 있다. 이해를 돕기 위해 각 칸에 해당되는 ABCD 값을 회색으로 써놓았다.

이렇게 표를 그린 후, 이제 각 칸에 해당되는 ABCD 값에 맞는 함수값을 쓰면 된다. 예를 들어 다음 함수를 보자.

A | B | C | D | OUT
:-:|:-:|:-:|:-:|:-:
0 | 0 | 0 | 0 | 1
0 | 0 | 0 | 1 | 0
0 | 0 | 1 | 0 | 1
0 | 0 | 1 | 1 | 1
0 | 1 | 0 | 0 | 0
0 | 1 | 0 | 1 | 1
0 | 1 | 1 | 0 | 1
0 | 1 | 1 | 1 | 1
1 | 0 | 0 | 0 | 1
1 | 0 | 0 | 1 | 0
1 | 0 | 1 | 0 | 0
1 | 0 | 1 | 1 | 1
1 | 1 | 0 | 0 | 0
1 | 1 | 0 | 1 | 0
1 | 1 | 1 | 0 | 0
1 | 1 | 1 | 1 | 1

이 함수를 카르노맵으로 나타내면 다음과 같다.

![](/images/combinational_logic/karnaugh1.png#center50)

몇개만 예를 들어 확인해보자. ABCD가 0000일 경우 결과값이 1이다. 따라서 제일 왼쪽 위의 칸에 해당하는 값은 1이다. ABCD가 0001일 경우 결과값이 0이다. 따라서 그 아래 칸에 해당하는 값은 0이다. 이처럼 각 칸의 ABCD에 해당하는 결과값을 써두면 된다. undefined일 경우 x로 표현한다.

이렇게 카르노맵을 그리고 나면 이제 본격적인 작업이 시작된다. 우선 우리는 1에 주목한다. 그 다음 각 1들만 덮는 가장 큰 직사각형을 그리는데, 이 직사각형의 가로와 세로는 항상 2<sup>n</sup> 꼴이어야 한다. 즉, 2 by 4나 4 by 1은 되지만 2 by 3같은 것은 안된다. 또한 이 카르노맵은 좌우가 서로 연결되어있고, 위아래가 서로 연결되어 있다고 생각해야한다.

제일 왼쪽 위의 1부터 보면 다음과 같은 두 직사각형을 그릴 수 있다.

![](/images/combinational_logic/karnaugh2.png#center50)

이 직사각형을 보면 위아래나 좌우가 연결되어있다는 것이 무슨 의미인지 짐작이 갈 것이다.

오른쪽 위의 1은 이미 초록색 직사각형에 포함되어있고, 이를 벗어나는 직사각형을 그릴 수 없으므로 넘어간다. 이처럼 모든 1에 대해서 이를 포함하는 가장 큰 직사각형들을 그리면 다음과 같다.

![](/images/combinational_logic/karnaugh3.png#center50)

여기서 주황색 직사각형은 이미 다른 직사각형에 모두 포함되므로 제거해도 된다. 따라서 최종적인 그림은 다음과 같다.

![](/images/combinational_logic/karnaugh4.png#center50)

이제 각 직사각형에 해당하는 식을 찾으면 된다.

 * 초록색 직사각형의 경우 A의 값은 섞여있지만, B, C, D는 항상 0이다. 따라서 이 직사각형에 해당하는 식은 `B'C'D'`이다.
 * 파란색 직사각형의 경우 C의 값은 섞여있지만, A의 값은 항상 0이고 B, D의 값은 항상 1이다. 따라서 이에 해당하는 식은 `A'BD`이다.
 * 노란색 직사각형의 경우 A, B의 값은 섞여있지만 C, D의 값은 항상 1이다. 따라서 이에 해당하는 식은 `CD`이다.
 * 보라색 직사각형의 경우 B, D의 값은 섞여있지만 A값은 항상 0이고 C의 값은 항상 1이다. 따라서 이에 해당하는 식은 `A'C` 이다.

따라서 이를 총 정리하면 `B'C'D' + A'BD + CD + A'C`가 된다.

이게 성립하는 이유는 처음에 말했듯이 Uniting 공리로 설명할 수 있다. 예를 들어 보라색 사각형을 보자. 보라색 사각형의 1들을 SoP로 나타내면

`A'B'CD + A'BCD + A'B'CD' + A'BCD'`

이다. 이를 정리하면

`A'B'CD + A'BCD + A'B'CD' + A'BCD' = A'CD(B' + B) + A'CD'(B' + B) 
= A'CD + A'CD' = A'C(D + D') = A'C`

가 된다. 이처럼 직사각형의 가로와 세로가 2<sup>n</sup> 꼴이라면 SoP 꼴을 Uniting 공리를 사용하여 짧게 줄일 수 있다.

사실 이거 한번 읽어보는 것만으로는 바로 깨닫기 어렵다. 랜덤으로 함수를 만들어본 다음에 연습해 보는 것을 추천한다.

# Commonly used gates

지금까지 랜덤한 boolean function을 정리하고 구현하는 것을 해봤다. 이제 일반적으로 많이 쓰이는 게이트들을 다뤄보자.

## Multi-bit notation

복잡한 게이트를 구현하려 하다보면 여러 비트를 겹쳐서 써야할 때가 있다. 이 떄 다음과 같이 빗금을 친 다음 겹쳐있는 수 만큼 표시를 한다.

![](/images/combinational_logic/multibit0.png#center50)

여기서 n이 아니라 [31:0]처럼 표기하는 경우도 있는데 이는 31번째 부터 0번째까지 사용한다는 것이고, 이 때 앞의 숫자인 31이 [최상위 비트(Most significant bit, MSB)](https://ko.wikipedia.org/wiki/%EC%B5%9C%EC%83%81%EC%9C%84_%EB%B9%84%ED%8A%B8)가 된다.

## Demultiplexer(DEMUX)

일명 DEMUX라고 불리는 Demultiplexer는 다른 이름으로 decoder라고도 불린다. 이 회로는 n개의 input을 받아서 2<sup>n</sup>개의 output을 낸다. 이 갯수에 따라 2-to-4, 3-to-8, 4-to-16 등으로 불린다.

DEMUX의 n개의 인풋은 이진수처럼 생각할 수 있다. 즉, 0 ~ (2<sup>n-1</sup>) 까지 표현할 수 있는데, 이에 해당되는 output만 1이고 나머지는 모두 0을 출력한다. 예를 들어 2-to-4 DEMUX를 표로 나타내면 다음과 같다.

enable | a<sub>0</sub> | a<sub>1</sub> || d<sub>0</sub> | d<sub>1</sub> | d<sub>2</sub> | d<sub>3</sub>
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
1 | 0 | 0 || 1 | 0 | 0 | 0
1 | 0 | 1 || 0 | 1 | 0 | 0
1 | 1 | 0 || 0 | 0 | 1 | 0
1 | 1 | 1 || 0 | 0 | 0 | 1
0 | x | x || 0 | 0 | 0 | 0

이제 이 DEMUX를 구현해보자. 우선 가장 간단한 1-to-2 DEMUX부터 만들어보자. 1-to-2 DEMUX의 진리표는 다음과 같다.

enable | a<sub>0</sub> || d<sub>0</sub> | d<sub>1</sub>
:-:|:-:|:-:|:-:|:-:
1 | 0 || 1 | 0 | 0 | 0
1 | 1 || 0 | 1 | 0 | 0
0 | x || 0 | 0 | 0 | 0

이는 다음과 같이 구현할 수 있다. en은 enable을 뜻한다.

![](/images/combinational_logic/demux0.png#center50)

방금 만든 1-to-2 DEMUX를 사용하면 더 큰 2-to-4 DEMUX를 만들 수 있다. 2-to-4 DEMUX 진리표의 결과부분에서 enable이 1인 부분을 가로세로로 반으로 나눠서 4등분을 해보자. 그중 왼쪽 위의 모양을 보면 1-to-2 DEMUX의 결과부분과 같다. 또한 오른쪽 아래 부분도 1-to-2 DEMUX의 결과 부분과 같다. 또한 나머지 부분은 모두 0이다. 따라서 다음과 같이 경우를 생각할 수 있다.

 * a<sub>0</sub>가 0인 경우(절반 중 윗부분)
   * d<sub>0</sub>와 d<sub>1</sub>(절반 중 왼쪽부분) : 1-to-2 DEMUX 
   * d<sub>2</sub>와 d<sub>3</sub>(절반 중 오른쪽부분) : 0
 * a<sub>0</sub>가 1인 경우(절반 중 아랫부분)
   * d<sub>0</sub>와 d<sub>1</sub>(절반 중 왼쪽부분) : 0이고, 
   * d<sub>2</sub>와 d<sub>3</sub>(절반 중 오른쪽부분) : 1-to-2 DEMUX

여기서 0인 부분은 작은 DEMUX의 enable에 0을 넣어주면 된다. 따라서 다음과 같이 구현할 수 있다.

![](/images/combinational_logic/demux1.png#center50)

이와 같은 방법으로 2-to-4 DEMUX 두개를 조합하면 3-to-8을 만들 수 있고, n-to-2<sup>n</sup> DEMUX 두개로 (n+1)-to-2<sup>n+1</sup> DEMUX를 만들 수 있다.

## Multiplexer(MUX)

일명 MUX라고 불리는 Multiplexer는 다른 이름으로 selector라고 불리기도 한다. 말 그대로 여러 input 중 어떤 것을 내보낼지 선택하는 기능을 한다. 이 input의 갯수에 따라 n-to-1 MUX라고 부른다. 예를 들어 2-to-1 MUX는 다음과 같이 표현한다. MUX의 기호는 사다리꼴로 그리고, 선택 당할 input들을 긴 변에, select하는 인자를 빗변에 그린다.

s || f
:-:|:-:|:-:
0 || x
1 || y

![](/images/combinational_logic/mux0.png#center75)

더 큰 MUX 또한 작은 MUX를 이용해서 만들 수 있다. 우선 4-to-1 MUX의 진리표를 먼저 보자.

s<sub>1</sub> | s<sub>0</sub> || f
:-:|:-:|:-:|:-:
0 | 0 || x
0 | 1 || y
1 | 0 || z
1 | 1 || w

만약에 여기서 s<sub>1</sub>이 0이로 고정되었다고 생각해보자. 그렇다면 진리표는 다음과 같이 바뀐다.

s<sub>0</sub> || f
:-:|:-:|:-:
0 || x
1 || y

즉, 2-to-1 MUX와 같이 동작한다. 만약에 s<sub>0</sub>가 0으로 고정되었다고 생각해보자.

s<sub>1</sub> || f
:-:|:-:|:-:
0 || x
1 || z

이 또한 2-to-1 MUX와 같이 동작한다. 즉, 모든 select bit는 2-to-1 MUX의 select bit처럼 동작한다. 따라서 다음과 같이 구현할 수 있다.

![](/images/combinational_logic/mux1.png#center75)

MUX를 구현하는 방법 중에는 DEMUX를 이용하는 방법도 있다. 처음에 2-to-1 MUX를 구현한 것을 다시 살펴보자.

![](/images/combinational_logic/mux2.png#center50)

여기서 빨간 네모친 부분을 살펴보자. 이 부분만 생각하면 s라는 하나의 input을 받아서 NOT s와 s를 출력하는 회로로 생각할 수 있다. 이는 1-to-2 DEMUX와 같은 동작을 한다. 여기서 힌트를 받아서 생각해보자.

 1. DEMUX는 선택된 단 하나의 bit만 1을 내보내고, 나머지는 0을 내보낸다.
 2. 이를 MUX의 각 input에 AND하면 선택된 bit만 그대로 나오고 나머지는 모두 0이 된다. 
 3. 이 결과를 모두 OR하면 선택된 비트가 그대로 나온다.

따라서 이러한 방법으로 DEMUX를 이용해서 MUX를 구현할 수 있다. 예를 들어 4-to-1 MUX는 다음과 같이 구현할 수 있다.

![](/images/combinational_logic/mux3.png#center50)

## Shifter

이제 좀더 실용적으로 쓰일만한 연산기를 만들어보자. 우선 제일 간단한 shifter부터 만들어보자. Shifter는 말 그대로 shift연산을 해주는 연산기이다. 왼쪽이나 오른쪽으로 shift를 하게 되면 반대편의 빈 자리는 Lin, Rin이 차지가게 된다. 즉, 입력이 B이고 출력이 D라고 할 때 아래 표와 같은 역할을 한다.

s<sub>1</sub> | s<sub>0</sub> || D<sub>3</sub> | D<sub>2</sub> | D<sub>1</sub> | D<sub>0</sub> | act
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
0 | 0 || B<sub>3</sub> | B<sub>2</sub> | B<sub>1</sub> | B<sub>0</sub> | Idle
0 | 1 || R<sub>in</sub> | B<sub>3</sub> | B<sub>2</sub> | B<sub>1</sub> | Shift right
1 | 0 || B<sub>2</sub> | B<sub>1</sub> | B<sub>0</sub> | L<sub>in</sub> | Shift left
1 | 1 || B<sub>3</sub> | B<sub>2</sub> | B<sub>1</sub> | B<sub>0</sub> | Idle

이는 MUX를 사용하여 어떤 것을 출력할지 정하는 방식으로 구현하면 간단하게 구현할 수 있다.

![](/images/combinational_logic/shifter0.png#center75)

## Adder

### 2'complement (2의 보수)

Adder를 만들어보기 전에 우선 2진법으로 수를 나타내는 방법부터 알아보자. 자주 쓰이는 방법으로는 unsingned와 2's complement 두가지 방법이 있다.

unsigned는 0001은 1이고 1101은 13인것 처럼 우리가 아는 이진수 그대로 쓰면 된다. 이 방법에는 큰 문제가 있는데, 음수를 표현하지 못한다는 것이다.

일반적으로 우리가 쓰는 아라비아 숫자의 경우 앞에 -라는 unary operation을 사용하여 음수 여부를 나타낸다. 그러나 이진수 체계에서는 0과 1만을 사용하여 음수와 양수를 모두 표현해야 한다. 이를 효과적으로 처리하기 위해 나온 개념이 2's complement, 한글로 하면 2의 보수이다.

보수란 말 그대로 보충을 해주는 수이다. 즉, 2의 보수란 n-bit 체계에서 보충을 해서 2^n이 되어 overflow 되는 bit를 제외하면 0이 되도록 해주는 수이다. 4-bit로 예를 들면, 0010의 경우 1110이 보수가 된다. 물론 1110 의 보수는 0010이 된다. 이 때 맨 앞의 bit가 0인 부분이 양수가 되고, 1인 부분을 그 양수의 음수표현으로 사용한다. 즉, 0010은 2이므로 1110은 -2가 된다. 이를 정리하면 아래와 같다.

binary | 0000 | 0001 | 0010 | 0011 | 0100 | 0101 | 0110 | 0111 | 1000 | 1001 | 1010 | 1011 | 1100 | 1101 | 1110 | 1111
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
unsigned | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15
2's complement | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | -8 | -7 | -6 | -5 | -4 | -3 | -2 | -1

이처럼 2의 보수를 사용하면 n-bit일 경우 -2<sup>n-1</sup> 부터 2<sup>n-1</sup> - 1 까지 나타낼 수 있다. 여기서 우리가 덧셈이나 뻴셈을 하다 보면 저 범위를 넘어갈 경우가 있고, 그럴 경우 우리가 의도한 수가 안나올 수도 있다. 예를 들어 `5 + 6`을 하려고 할 경우 `0101 + 0110 = 1011 = -5`로 의도하지 않은 수가 나오게 된다. 이러한 경우를 overflow라고 하고, 이러한 상황이 나오지 않도록 주의해야한다.

2의 보수를 사용하는 이유는 덧셈을 평소 더하듯이 자연스럽게 더해도 되기 때문이다. 보수인 수끼리 서로 더할 경우 overflow 되는 bit를 제외하면 0000으로 0이 된다. 또한 -2 + 5의 경우 -2 + 5 = -2 + (2 + 3) = (-2 + 2) + 3으로 쪼개어보면 역시 자연스럽게 계산됨을 알 수 있다. 이처럼 우리가 원래 알던 방식대로 더해도 자연스럽게 성립이 되는 2의 보수라는 방식을 통해 음수를 표현한다.

2의 보수를 구하는 방법은 간단하다. 10000을 9999 + 1로 나타낼 수 있듯이 2<sup>n</sup>을 만드려면 11...11를 만드는 수에서 1을 더하면 된다. 또한 11...11을 만드려면 원래 수에서 1인부분은 0으로, 0인부분은 1로 바꾼 수를 더하면 되고, NOT 연산이 이와 같은 역할을 한다. 따라서 `A`의 보수는 MOT을 한 다음 1을 더하면 되므로 `A' + 1`이 된다.

### Half Adder & Full Adder

우선 덧셈이라는 연산을 진리표로 나타내면 다음과 같다.

x | y || sum | carry
:-:|:-:|:-:|:-:|:-:
0 | 0 || 0 | 0 |
0 | 1 || 1 | 0 |
1 | 0 || 1 | 0 |
1 | 1 || 0 | 1 |

여기서 carry는 받아올림으로 생각하면 된다. 각 output을 식으로 나타내면

`sum = x ^ y`

`carry = x * y`

여기서 `^`는 XOR을 나타내는 기호이다. 따라서 이를 로직 회로로 구현하면 다음과 같다.

![](/images/combinational_logic/halfadder0.png#center50)

이를 half adder, 한글는 반가산기라고 부른다. 얘를 반쪽이라고 부르는 carry in, 즉 받아올림이 없기 때문이다. 우리는 단순히 한자리만 더하는 것이 아니라 여러 자리를 더하는 것을 만들어야 한다. 따라서 완벽한 전가산기(full adder)을 만드려고 하면 이전 자리에서 올라온 carry in을 고려해야 한다. 이를 고려한 전가산기의 진리표는 다음과 같다.

Cin | x | y || sum | Cout
:-:|:-:|:-:|:-:|:-:|:-:
0 | 0 | 0 || 0 | 0 |
0 | 0 | 1 || 1 | 0 |
0 | 1 | 0 || 1 | 0 |
0 | 1 | 1 || 0 | 1 |
1 | 0 | 0 || 1 | 0 |
1 | 0 | 1 || 0 | 1 |
1 | 1 | 0 || 0 | 1 |
1 | 1 | 1 || 1 | 1 |

또한 이를 식으로 나타내면 다음과 같다.

`sum = x ^ y ^ Cin`

`Cout = x * y + Cin * (x ^ y)`

사실 잘 생각하면 결국 결과는 `x + y + Cin`이고,  `x + y + Cin = (x + y) + Cin`이므로 전가산기는 반가산기 두개로 구현할 수 있다.

![](/images/combinational_logic/fulladder0.png#center75)

### Ripple Carry Adder

이제 carry in과 carry out을 모두 처리할 수 있으니 n-bit adder을 만들 수 있다. 아래와 같이 이전 bit의 carry out을 다음 bit의 carry in으로 연결하는 방식으로 구현한 것을 ripple carry adder라고 한다.

![](/images/combinational_logic/ripplecarryadder0.png#center75)

## ALU (Arithmetic Loguc Unit)

헷갈림을 방지하기 위해 여기서의 `+`는 logical AND가 아니라 arithmetic ADD를 뜻한다.

### Arithmetic Unit

지금부터는 점점 우리가 만든 것들을 합쳐서 큰 unit을 만들 것이다. 우선 만들어볼 것은 Arithemetic unit으로, 아래의 진리표 처럼 작동하며 덧셈과 뺄셈 기능을 수행한다.

s<sub>1</sub> | s<sub>0</sub> || Y | F when C<sub>in</sub> = 0 | F when C<sub>in</sub> = 1
:-:|:-:|:-:|:-:|:-:|:-:
0 | 0 || 00...0 | A | A + 1
0 | 1 || B | A + B | A + B + 1
1 | 0 || B' | A + B' = A - B - 1 | A + B' + 1 = A - B
1 | 1 || 11...1 | A - 1 | A

![](/images/combinational_logic/arithmeticunit0.png#center75)

### Logic Unit

Logic unit은 말 그대로 logic 연산을 해주는 unit으로, 어떤 연산을 할지를 MUX를 사용하여 결정한다.

s<sub>1</sub> | s<sub>0</sub> || F
:-:|:-:|:-:|:-:
0 | 0 || A OR B
0 | 1 || A AND B
1 | 0 || A XOR B
1 | 1 || A'

![](/images/combinational_logic/logicunit0.png#center50)

### Arithmetic Logic Unit

이제 위에서 만든 두 arithmetic unit과 logic unit을 합치면 arithmetic logic unit을 만들 수 있다.

s<sub>2</sub> | s<sub>1</sub> | s<sub>0</sub> | C<sub>in</sub> || F
:-:|:-:|:-:|:-:|:-:|:-:
0 | 0 | 0 | 0 || A
0 | 0 | 0 | 1 || A + 1
0 | 0 | 1 | 0 || A + B
0 | 0 | 1 | 1 || A + B + 1
0 | 1 | 0 | 0 || A - B - 1
0 | 1 | 0 | 1 || A - B
0 | 1 | 1 | 0 || A - 1
0 | 1 | 1 | 1 || A
1 | 0 | 0 | x || A OR B
1 | 0 | 1 | x || A AND B
1 | 1 | 0 | x || A XOR B
1 | 1 | 1 | x || A'

![](/images/combinational_logic/alu0.png#center75)

그러나 일반적으로 CPU 등에 들어가는 ALU는 이 ALU에 Shifter까지 붙인 것을 뜻한다. 이는 아래와 같이 작동하고, 바지를 뒤집은 듯한 기호로 나타낸다. Output중 Negative, Zero, Carry, oVerflow를 NZCV flag라고 부른다.

Fs<sub>4</sub> | Fs<sub>3</sub> | Fs<sub>2</sub> | Fs<sub>1</sub> | Fs<sub>0</sub> || F
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

![](/images/combinational_logic/alu1.png#center100)

# Delay

이론처럼 게이트에서 전기신호가 바로바로 슉슉 바뀌면 이상적이겠지만, 실제로는 신호 변환에 딜레이가 존재한다. 이러한 딜레이로 인하여 내가 하고자 하는 task의 수행 시간이 엄청 길어질 수도 있고, 잘못 처리하면 glitch 등이 발생할 수도 있다. 예를 들어 아래 상황을 보자.

![](/images/combinational_logic/delay0.png#center50)

이 상황은 A와 A'을 AND하고 있으므로 결과인 F는 항상 0이어야 한다. 그러나 실제로 작동하는 waveform을 보면 아래와 같다.

![](/images/combinational_logic/delay1.png#center50)

NOT 게이트의 딜레이 때문에 A가 1이 되자마자 D가 바로 0이 되지 못했기 때문에 그동안 F에서 1이 출력되게 된다. 이처럼 회로를 설계할 때 딜레이를 잘 고려하지 않으면 의도하지 않은 작동으로 인해 제대로 task가 수행되지 않을 수도 있다.

## Carry-Lookahead Adder (CLA)

우리가 위에서 만들었던 ripple carry adder의 모양을 다시 살펴보자.

![](/images/combinational_logic/ripplecarryadder0.png#center75)

이는 이전 bit의 carry out을 다시 carry in으로 받는 방식으로 계산한다. 따라서 이전 bit에서 계산이 완료되어야 다음 bit의 계산 결과가 제대로 나오게 된다. Full adder의 딜레이를 t라고 하면 첫번째 bit의 계산 결과가 나오는데 t가 걸린다. 두번째 bit의 계산 결과가 나오려면 2t가 걸리고, 결국 모든 bit가 계산이 되려면 nt만큼의 시간이 걸린다. 이처럼 계산 시간이 n에 비례해서 선형적으로 증가하므로, 만약에 n이 엄청 크다면 그에 따른 계산 시간도 엄청 오래걸리게 된다. 이러한 점을 방지하기 위해 나온 것이 carry-lookahead adder이다.

이름에서 유추해볼 수 있듯이 우리는 carry부분에 주목한다. 계산시간을 줄이기 위해서는 몇 번째 bit냐에 상관 없이 상수시간 안에 carry in을 구해야 한다. 이러한 작업을 하기 위해 우리는 몇 가지 변수를 정의한다.

 * C<sub>i</sub>는 i번째 bit가 받는 carry in을 뜻한다.
 * G<sub>i</sub>는 i번째 비트의 `x * y` 이다.
 * P<sub>i</sub>는 i번째 비트의 `x ^ y` 이다.

 우리는 위에서 했듯이 `Cout = x * y + Cin * (x ^ y)`임을 이미 알고있다. 따라서 다음과 같이 적을 수 있다.

`$ C_{i+1} = G_{i} + P_{i} \cdot C_{i} $`

예를 들어 4-bit 상황에선 다음과 같이 쓸 수 있다.

`$
C_{1} = G_{0} + P_{0} \cdot C_{0} \\
C_{2} = G_{1} + P_{1} \cdot C_{1} \\
C_{3} = G_{2} + P_{2} \cdot C_{2} \\
C_{4} = G_{3} + P_{3} \cdot C_{3}
$`

여기서 C<sub>1</sub>, C<sub>2</sub>, C<sub>3</sub> 등을 위에서 구한 식을 대입할 수 있다. 예를 들어 C<sub>2</sub>에 대해 아래처럼 정리할 수 있다.

`$\begin{eqnarray}
C_{2} &=& G_{1} + P_{1} \cdot C_{1}   \\
&=& G_{1} + P_{1} \cdot (G_{0} + P_{0} \cdot C_{0})   \\
&=& G_{1} + G_{0} \cdot P_{1} + C_{0} \cdot P_{0} \cdot P_{1}
\end{eqnarray}$`

이와 같이 모든 값을 정리하면 다음과 같다.

`$
C_{1} = G_{0} + P_{0} \cdot C_{0} \\
C_{2} = G_{1} + G_{0} \cdot P_{1} + C_{0} \cdot P_{0} \cdot P_{1} \\
C_{3} = G_{2} + G_{1} \cdot P_{2} + G_{0} \cdot P_{1} \cdot P_{2} + C_{0} \cdot P_{0} \cdot P_{1} \cdot P_{2} \\
C_{4} = G_{3} + G_{2} \cdot P_{3} + G_{1} \cdot P_{2} \cdot P_{3} + G_{0} \cdot P_{1} \cdot P_{2} \cdot P_{3} + C_{0} \cdot P_{0} \cdot P_{1} \cdot P_{2} \cdot P_{3}
$`

이제 계산시간을 살펴보자. 우선 P, G를 계산하는데 1 게이트 딜레이가 걸린다. 이후 각 값들을 AND한 다음 OR해야 하므로 여기서 2 게이트 딜레이가 걸린다. 이렇게 나온 C 값으로 full adder에서 계산을 하는데 2 게이트 딜레이가 걸린다. 따라서 n에 상관 없이 총 5 게이트 딜레이면 우리는 올바른 결과를 얻을 수 있다.

## Oscilator

이번에는 딜레이를 이용하는 경우를 살펴보자.

![](/images/combinational_logic/oscilator0.png#center50)

만약에 딜레이가 없다면 이는 굉장히 괴이한 회로가 된다. A 값이 1일 경우를 생각해보자. 만약에 D값이 1이라면 B값은 0이고, D값이 0이라면 B값은 1이어야 한다. 따라서 B랑 D는 절대로 같은 값이 될 수 없는데 B에 NOT을 두번 하면 D이니 두 값은 같아야 하는 모순이 발생한다. 이러한 모순 덕분에 딜레이가 작용하면 신기한 작용을 하는 회로가 된다.

![](/images/combinational_logic/oscilator1.png#center50)

이처럼 1과 0이 공존해야 하는 모순이 딜레이를 만나서 1과 0이 반복하게 되는 작업을 한다. 이처럼 1과 0이 반복되는 장치를 clock이라고 하고, 다음에 다룰 sequential logic에서 중요한 역할을 하게 된다.