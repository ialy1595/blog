---
title: "시간복잡도 (Time Complexity)"
date: 2019-04-16T21:28:24+09:00
categories: ["code"]
subcategories: ["algorithm"]
tags: ["time complexity", "Big-O natation"]
references : ["https://en.wikipedia.org/wiki/Big_O_notation"]
draft: false
nextp: "Brute Force"
prevp: ""
---

# 효율적인 코드란

우리는 프로그램을 만들기 위해 코드를 작성한다. 여기서 프로그램이란 어떤 input을 넣었을 때 원하는 output이 나오도록 하는 것이다. 하지만 같은 input을 넣었을 때 같은 output이 나왔다고 해서 다 같은 프로그램은 아니다. 분명 그중에는 더 효율적이라고 할 수 있는 프로그램이 있고, 이를 만든 더 효율적인 코드가 존재한다.

코드가 효율적인지 비효율적인지는 크게 아래 두개의 기준으로 판별할 수 있다.

 - 협업, 유지보수를 효율적으로 할 수 있는가
 - 자원(시간, 공간)을 효율적으로 사용하는가

물론 첫 번째 기준도 코드를 작성할 때 중요한 점이다. 하지만 이 글에서는 알고리즘에 대해 다루고 있으므로 알고리즘에서 주로 생각하는 두번째 효율, 자원을 효율적으로 사용하는가에 대해 알아보자.

프로그램이 실행하면서 사용하는 자원은 크게 시간, 공간으로 두 가지이다. 여기서 시간은 **이 프로그램이 원하는 output을 낼 때까지 얼마나 걸리는가**, 공간은 **이 프로그램이 원하는 output을 내는 동안 얼마나 많은 메모리를 차지하는가**이다. 둘 중에서 더 중요한 것을 꼽으라면 시간이다. 공간은 디스크나 램을 구매하는 등 돈으로 해결할 수 있지만 시간은 아무리 돈을 주고 애를 써봐도 하염없이 흘러가기 때문에 코드 구조를 통해서만 해결할 수 있기 때문이다.

# 시간복잡도

시간의 효울성을 분석하려면 프로그램의 수행 시간을 알아야 한다. 그러나 매번 실행할 때 마다 수행시간을 측정할수도 없는 노릇이고, 측정한다 해도 단순히 측정한 결과만으로는 input에 따라 수행시간에 어떻게 변화하는지 분석하기 쉽지 않다. 따라서 우리는 코드를 보고 수행시간을 분석할 수 있어야 한다.

우리가 코드를 작성한 뒤 실행하면 이 코드는 instruction으로 변환한 후 CPU에서 실행된다. 여기서 중요한 점은 instruction은 CPU clock이 rising edge일 때 실행되므로 **각 instruction의 수행 시간은 일정**하다고 볼 수 있다. 따라서 우리의 코드가 얼마나 많은 instruction을 실행하는지 분석하는 것이 얼마나 많은 시간이 걸리는지 분석하는 것과 같다고 볼 수 있다.

instruction 수를 분석하는 것은 어렵지 않다. 코드에서 연산을 하거나, 조건문을 걸거나, 값을 대입하는 것이 모두 instruction이 된다, 그렇다고 이런 연산 하나에 수만 개의 instruction이 실행되지는 않는다. 즉, 각각의 연산이 상수개 정도의 instruction을 실행한다고 가정하고 코드를 분석할 수 있다. 예를 들어 `$ n $`을 입력하면 `$ n \times n $`까지 구구단을 출력해주는 아래 코드에서 instruction을 얼마나 실행하는지 분석해보자.

```c
for(i = 1; i <= n; i++)
{
    for(j = 1; j <= n; j++)
    {
        printf("%d * %d = %d\n",i, j, i * j);
    }
}
```

 - 첫 번째 for문의 `i`를 보면 맨 처음에 1을 대입하는 과정에서 `$ C_1 $`개의 instruction을 실행한다. 이후 `$ n + 1 $`까지 1씩 `$ n $`번 증가하면서 `$ n $`보다 작거나 같은지 비교하므로 `$ C_2 n $`개의 instruction을 실행한다.
 - 두 번째 for문의 `j`의 경우에도 처음에는 1이 대입된 후 `$ n $`번 1씩 증가하면서 `$ n $`보다 작거나 같은지 비교하므로 `$ C_1 + C_2 n $`개의 instruction을 실행한다. 이만큼의 instruction을 `i`가 1일 때부터 `$ n $`일 때까지 `$ n $`번 실행하므로 총 `$ (C_1 + C_2 n)n $`개의 instruction을 실행한다. 
 - 출력부분을 보면 `i * j`연산을 한 뒤 출력을 하는 과정에서 `$ C_3 $`개의 instruction을 실행한다. 이 과정을 `i`가 1부터 `$ n $`까지, `j`가 1부터 `$ n $`까지 총 `$ n^2 $`번 실행한다. 따라서 `$ C_3 n^2 $`개의 instruction을 실행한다.

이를 정리하면 `$ C_1 + C_2 n + (C_1 + C_2 n)n + C_3 n^2 $`개의 instruction이 실행된다.

자, 이제 `$ C_1 + C_2 n + (C_1 + C_2 n)n + C_3 n^2 $`라는 식이 나왔다. 이렇게 output이 나오는데 까지 걸리는 시간을 input(위에서는 `$ n $`)에 따라 분석한 것을 시간복잡도라 한다. 하지만 저렇게 써놓으면 얼마나 효율적인지 한눈에 알아보기 힘들다. 따라서 일반적으로 시간복잡도를 표기할 때 **한눈에 알아보기 쉬운 Big-O notation을 사용한다.**

## Big-O notation

Big-O notation의 정의는 다음과 같다.

`$$ f(x) = O(g(x)) \quad when \quad \exists \ N, C \quad s.t. \quad n \geq N \ \Rightarrow \ f(n) \leq Cg(n) $$`

나는 개인적으로 아래처럼 생각하는 것이 편하다. 시간복잡도가 항상 양수이기 때문에 두 식은 동치이다.

`$$ f(x) = O(g(x)) \quad when \quad \exists \ C \quad s.t. \quad \lim_{n \to \infty} \frac{f(n)}{g(n)} \leq C $$`

즉, `$ \lim_{n \to \infty} \frac{f(n)}{g(n)} $`이 무한대로 발산하지만 않으면 된다.

여기서 주의할 점은 `$ f(x) = O(g(x)) $`라고 표기할 때 쓰는 등호는 우리가 일반적으로 쓰는 등호와 다르다는 것이다. 이에 대해 [위키피디아에는 다음과 같이 설명한다.](https://en.wikipedia.org/wiki/Big_O_notation#Equals_sign)

 > The statement "f(x) is O(g(x))" as defined above is usually written as f(x) = O(g(x)). Some consider this to be an abuse of notation, since the use of the equals sign could be misleading as it suggests a symmetry that this statement does not have. As de Bruijn says, O(x) = O(x2) is true but O(x2) = O(x) is not. Knuth describes such statements as "one-way equalities", since if the sides could be reversed, "we could deduce ridiculous things like n = n2 from the identities n = O(n2) and n2 = O(n2)."
For these reasons, it would be more precise to use set notation and write f(x) ∈ O(g(x)), thinking of O(g(x)) as the class of all functions h(x) such that |h(x)| ≤ C|g(x)| for some constant C. However, the use of the equals sign is customary. Knuth pointed out that "mathematicians customarily use the = sign as they use the word 'is' in English: Aristotle is a man, but a man isn't necessarily Aristotle."


우리가 *f(x)는 O(g(x))이다* 라는 표현에서 *는*(영어에서는 *is*)이라는 표현을 쓰면서 자연스럽게 등호를 사용하게 됐다. 하지만 삼단 논법이나 교환법칙이 성립하지 않는 등 실제 등호와 완전히 같은 역할을 하지 않는다.

 - `$ n = O(n^2) \quad and \quad n = O(n) \quad but \quad O(n^2) \neq O(n) $`
 - `$ O(n) = O(n^2) \quad but \quad O(n^2) \neq O(n) $`

이를 방지하기 위해 `$ f(x) \in O(g(x)) $`처럼 set notation을 사용하자는 사람들도 있다. 하지만 이 경우에는 익숙하지 않을 뿐더러 `$ (n+1)^2 = n^2 + O(n) $`과 같은 표현을 쓰기 어려워서 불편함이 있다. 따라서 그냥 조심히 등호를 사용하는 추세이다.

다시 위의 식을 보자. 위의 식에서 `$ \lim_{n \to \infty} \frac{C_1 + C_2 n + (C_1 + C_2 n)n + C_3 n^2}{n^2} = C_2 + C_3$` 이다. 따라서 위 코드의 시간복잡도는 `$ O(n^2) $`라고 할 수 있다. 시간복잡도를 나타낼 때 `$ O(n^2) $`처럼 상수는 모두 떼고 가장 빠르게 증가하는 항만 남겨둔다. 이를 이용하면 input이 증가함에 따라 수행시간이 얼마나 빠르게 증가할지 짐작할 수 있다.

## 구하는 방법

이제 Big-O notation을 사용하면 시간복잡도를 효과적으로 나타낼 수 있다. 하지만 아직 남은 걱정거리는 위에서처럼 일일이 instruction 수를 분석해야 하냐는 것이다. 이 또한 Big-O notation을 이용해서 해결할 수 있다. 예를 들어 코드에서 윗부분의 시간복잡도가 `$ O(n) $`이고, 아랫부분의 시간복잡도가 `$ O(n^2) $`이면 **이들를 합친 시간복잡도는 결국 큰 부분을 따르게 되므로** `$ O(n^2) $`이다. 따라서 Big-O notation으로 표시할 경우 모든 코드를 분석할 필요가 없다. 이를 활용하면 아래와 같이 분석할 수 있다.

### for문 분석

가장 쉬운 방법이다. 왜냐하면 for문은 아래에 있는 구문이 얼마나 반복할지 바로 알려주기 때문에다. 예를 들어 위의 구구단의 경우 첫 번째 for문에 의해 아래 코드가 `$ n $`번 실행되고, 그 코드의 for문에 의해 `printf`구문은 또 `$ n $`번 실행된다. 따라서 `printf`구문은 `$ n^2 $`번 실행하게 된다. 따라서 이 코드의 시간복잡도는 `$ O(n^2) $`이다. 이 과정에서 `i`나 `j`가 변하는 것은 어차피 `$ O(n^2) $` 이하이므로 신경 쓰지 않아도 된다.

### 가장 많이 실행되는 부분 분석

위에서 말했듯이 시간복잡도가 작은 부분은 결국 큰 부분에 먹히게 된다. 따라서 우리는 코드에서 가장 중요하고 많이 실행되는 부분이 어디인지 찾아내고, 그 부분이 몇 번 실행되는지 분석하면 된다.

### 점화식

시간복잡도를 input에 대한 점화식으로 나타낸 후 이 식을 푸는 방법으로 시간복잡도를 구할 수도 있다. 예를 들어 `$ n $`개짜리의 문제를 해결할 때 `$ O(n) $`의 연산을 한 다음 `$ n - 1 $`개짜리 동일한 문제를 해결하면 된다고 하자. 이 때 `$ n $`개에서의 시간복잡도를 `$ T(n) $`이라 하면

`$\begin{eqnarray}
T(n) &=& T(n-1) + C_1 n   \\
&=& T(n-2) + C_1 (n-1) + C_1 n   \\
&=& T(n-3) + C_1 (n-2) + C_1 (n-1) + C_1 n \\
&=& \cdots \\
&=& T(1) + C_1 [2 + 3 + \cdots + n] \\
&=& C_2 + C_1 [ \frac{n(n+1)}{2} - 1] \\
&=& O(n^2)
\end{eqnarray}$`

처럼 점화식을 통해 시간복잡도를 구할 수 있다.

## 주의점 : 최악의 상황을 가정

여기서 주의할 점은 **최악의 상황을 가정**해야 한다는 것이다. 예를 들어 1이 나올 때까지 `$ n $` 이하의 자연수를 복원추출 하는 프로그램이 있다고 하자.

 - 최고의 경우, 한번에 1이 나올 수도 있다. 이 경우 시간복잡도는 `$ O(1) $`이다.

 - 평균적인 경우, 실행되는 횟수의 기댓값은 `$ n $`이다. 이 경우 시간복잡도는 `$ O(n) $`이다.

 - 최악의 경우, 영원히 1이 안 나올 수도 있다. 이 경우 시간복잡도는 `$ O( \infty ) $`이다.

이 경우 위 프로그램의 시간복잡도는 `$ O( \infty ) $`이다. 평소에 아무리 잘한다 해도 중요할 때 삐끗하면 망하는 거고, 받은 것은 3초를 기억하지만 당한 것은 3대가 기억한다는 말이 있다. 허위광고를 하면 안 되듯이 시간복잡도는 아무리 신이 날 버리고 상황이 최악이라고 해도 이 시간 안에는 가능하다는 **보증**이어야 한다.

## 알고리즘 대회에서의 시간복잡도

우리가 위의 방법으로 시간복잡도를 구했다고 하자. 하지만 알고리즘 대회에서 보면 "`$ O(n) $`안에 수행하시오"가 아니라 "1초 안에 수행하도록 하시오"처럼 쓰여 있다. 따라서 시간복잡도를 보고 얼마나 시간이 걸릴지 어림짐작 할 수 있어야 한다.

완전히 정확한 수치는 아니지만 시간복잡도에서 최댓값을 계산한 결과 **1억 번에 1초**정도 걸린다고 생각하면 대강 맞다. 예를 들어 `$ n $`이 1000이하라면 `$ O(n^2) $`은 1초안에 실행이 가능하지만 `$ O(n^3) $`은 불가능하고 보면 되고, `$ n $`이 1000000이하라면 `$ O(n) $`이나 `$ O(n \log n) $`은 가능하지만 `$ O(n^2) $`은 불가능하다. 이를 이용하면 문제 조건을 보고 대략 어떤 시간복잡도의 알고리즘을 사용해야할지 유추할 수도 있다.