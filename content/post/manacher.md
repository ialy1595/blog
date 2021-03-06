---
title: "Manacher 알고리즘"
date: 2020-11-10T18:55:30+09:00
categories: ["code"]
subcategories: ["algorithm"]
tags: ["manacher", "palindrome"]
references: []
nextp: ""
prevp: "문자열 매칭 알고리즘 (KMP, Z-Array)"
---

# 개요

이 알고리즘은 회문(팰린드롬, palindrome)에 관해서 빠른 시간 안에 강력한 솔루션을 제공한다. 여기서 회문이란 `$ abcba $`처럼 뒤집어서 읽어도 똑같은 문자열을 뜻한다. 여기에 Manacher 알고리즘을 통해 문자열의 모든 위치에 대해서 그 위치를 중심으로 하는 최대 회문의 한쪽 길이를 `$ O(n) $`안에 구할 수 있다. 예를 들어 `$ banana $`에 이 알고리즘을 적용하면 결과는 `$ [0, 0, 1, 2, 1, 0] $`이 나온다.

위 결과만 보면 이 알고리즘에는 두 가지 문제점이 있는데, 첫 번째는 어떤 위치를 중심으로만 고려하므로 길이가 홀수인 회문만 구할 수 있다는 점이고, 두 번째는 여기서 구한 결과가 회문의 한쪽 길이만 구하기 때문에 결과 숫자가 직관적이지 않을 수 있다는 점이다. 그러나 하나의 트릭으로 이 두 문제점을 모두 해결할 수 있다. 바로 각 문자 앞뒤에 특수 문자를 끼워넣는 것이다. 예를 들어 원래 문자열이 `$ bananaa $`였다면 `$ @b@a@n@a@n@a@a@ $`에 위 알고리즘을 적용하면 된다. 그러면 결과가 `$ [0, 1, 0, 1, 0, 3, 0, 5, 0, 3, 0, 1, 2, 1, 0] $`으로, 끼워둔 특수 문자에서는 짝수 회문의 길이를, 원래 문자에서는 홀수 회문의 길이를 구할 수 있다.

이 알고리즘의 기본적인 매커니즘은 이전에 다뤘던 [Z-Array 알고리즘](https://ialy1595.github.io/post/string-match/#z-array)하고 굉장히 비슷하다. Z-Array 알고리즘에서는 이전에 비교했던 값 중 가장 오른쪽에 위치한 부분 문자열의 좌우 인덱스 `$ l $`과 `$ r $`값을 이용하면서 이전에 비교했던 정보를 적극적으로 활용했다. 이 알고리즘에서도 비슷하게 이전에 구했던 회문 중 가장 오른쪽으로 긴 회문의 중심 인덱스 `$ p $`와 오른쪽 끝 인덱스인 `$ r $`값을 통해 이전에 구한 데이터를 활용하여 연산을 최적화한다.

# 구현

우선 처음 시작할 때나 현재 구하려는 위치가 `$ r $`보다 오른쪽에 있는 경우에는 이전에 구한 정보를 활용할 수 없으므로 그 위치에서 가장 긴 회문을 찾아준다.

```
int p, r = -1;
int len = strlen(S);

for(i = 0; i < len; i++) {
    if(i > r) {
        p = r = i;
        while(r < len && r <= 2 * p && S[r] == S[2 * p - r]) r++;
        res[i] = r - p;
        r--;
    }
    else {
        ...
    }
}
```

이제 `$ i $`가 `$ r $` 왼쪽에 있어서 이전에 구한 정보를 활용할 수 있는 경우를 살펴보자. `$ r $`이 `$ p $`에 대칭되는 점을 `$ l $`, `$ i $`가 `$ p $`에 대칭되는 점을 `$ j $`이라 해보자. 현재 상황을 그림으로 나타내면 아래와 같다.

![](/images/manacher_algorithm/manacher0.png#center100)

여기서 `$ j $`의 값은 당연히 `$ i $`보다 작으므로 우리는 `$ res[j] $`은 이미 구해져 있다. 이제 이 값에 따라서 `$ res[i] $`의 값을 구하면 된다.

먼저 `$ res[j] $`가 `$ r - i $`보다 작은 경우를 살펴보자. 이 경우 `$ res[j] $`로 부터 뻗어나간 최대 길이의 팰린드롬이 `$ l $`을 벗어나지 못하므로, 이 상황을 그림으로 나타내면 아래와 같다.

![](/images/manacher_algorithm/manacher1.png#center100)

이 때 `$ l $`에서 `$ r $`까지 글자가 `$ p $`를 중심으로 대칭이므로, `$ i $`에서의 상황도 `$ j $`에서의 상활과 같다. 따라서 `$ res[i] $`의 값도 당연히 `$ res[j] $`와 같다.

```
for(i = 0; i < len; i++) {
    if(i > r) {
        p = r = i;
        while(r < len && r <= 2 * p && S[r] == S[2 * p - r]) r++;
        r--;
        res[i] = r - p;
    }
    else{
        j = 2 * p - i;
        if(res[j] < r - i) {
            res[i] = res[j];
        }
        else {
            ...
        }
    }
}
```

그 다음은 `$ res[j] $`가 `$ r - i$`보다 큰 경우를 보자. 이 경우에는 `$ j $`로 부터 뻗어나간 최대 길이의 팰린드롬이 `$ l $`을 완전히 벗어난다.

![](/images/manacher_algorithm/manacher2.png#center100)

여기서 `$ S[l - 1] $`을 중점으로 생각해보자. 이 글자와 이 글자가 `$ j $`에 대칭된 글자는 `$ j $`를 중심으로 한 팰린드롬 안에 속해있으므로 같다. 그리고 그 대칭된 글자를 `$ p $`에 대칭한 글자도 `$ p $`를 중심으로 한 팰린드롬 안에 속해 있으므로 같다. 그리고 이 글자를 `$ i $`에 대칭시키면 `$ S[r + 1] $`에 매칭된다.

그러나 `$ S[r + 1] $`은 `$ S[l - 1] $`과 다르다. 만약 둘이 같다면 `$ p $`를 중심으로 구한 최대 길이 팰린드롬의 끝이 `$ r $`이라는 정의에 부합하기 때문이다. 따라서 `$ S[r + 1] $`과 이 글자가 `$ i $`에 대칭된 글자(그림에서 보라색 관계)는 서로 다를 수 밖에 없다. 그리고 그 이전까지는 `$ j $`를 중심으로 한 팰린드롬과 상황이 같으므로 모두 대칭이다. 따라서 `$ res[i] $`의 값은 정확히 `$ r - i $`이 된다.

```
for(i = 0; i < len; i++) {
    if(i > r) {
        p = r = i;
        while(r < len && r <= 2 * p && S[r] == S[2 * p - r]) r++;
        r--;
        res[i] = r - p;
    }
    else{
        j = 2 * p - i;
        if(res[j] < r - i) {
            res[i] = res[j];
        }
        else if(res[j] > r - i) {
            res[i] = r - i;
        }
        else {
            ...
        }
    }
}
```

마지막으로 `$ res[j] $`가 정확히 `$ r - i $`여서 `$ j $`에서 뻗어나간 팰린드롬이 정확히 `$ l $`에 걸치는 경우를 살펴보자.

![](/images/manacher_algorithm/manacher3.png#center100)

우선 `$ j $`에서의 상황과 `$ i $`에서의 상황이 `$ p $`에 대칭이므로 `$ r $`까지는 모두 대칭된다는 것은 보장할 수 있다. 그러나 그 이후 상황은 알 수 없다. 같다고 확신할 수도 없고, 다르다고 확신할 수도 없이 말 그대로 알 수 없다. 그림에서 보면, 지금 상황은 마치 `$ x \ne y $`, `$ y \ne z $`인 상황에서 `$ x $`와 `$ z $`가 같을지 아냐는 질문과 같기 때문이다. 따라서 이 경우에서 `$ r $` 이후의 상황은 직접 비교해볼 수 밖에 없다. 그리고 이 경우 새로 얻어진 팰린드롬의 오른쪽 끝 값은 항상 `$ r $` 이상이므로 `$ p $`와 `$ r $`을 갱신해주면 된다.

```
for(i = 0; i < len; i++) {
    if(i > r) {
        p = r = i;
        while(r < len && r <= 2 * p && S[r] == S[2 * p - r]) r++;
        r--;
        res[i] = r - p;
    }
    else{
        j = 2 * p - i;
        if(res[j] < r - i) {
            res[i] = res[j];
        }
        else if(res[j] > r - i) {
            res[i] = r - i;
        }
        else {
            p = i;
            while(r < len && r <= 2 * p && S[r] == S[2 * p - r]) r++;
            r--;
            res[i] = r - p;
        }
    }
}
```