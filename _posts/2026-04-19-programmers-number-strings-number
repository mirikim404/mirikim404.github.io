---
title: "[프로그래머스] 숫자 문자열과 영단어"
date: 2026-04-19 00:00:00 +0900
categories: [Algorithm, 프로그래머스]
tags: [Level1, 문자열, Java]
---

## 문제 요약

숫자의 일부 자릿수가 영단어로 바뀌어졌거나, 혹은 바뀌지 않고 그대로인 문자열 `s`가 주어질 때, 원래 숫자를 return하는 문제.

```
"one4seveneight" → 1478
"23four5six7"    → 234567
"1zerotwozero3"  → 10203
```

---

## 접근 방법

처음에 고민한 것: **어떤 타입으로 비교하지?**

- `int` 상태로 비교? → 영단어를 숫자로 어떻게 변환하지?
- `char`로 하나씩 보자 → 여기까진 생각했는데...
- **여러 char를 어떻게 합쳐서 단어로 만들지?** → 여기서 막혔다

해결책은 생각보다 단순했다. `temp = ""` 빈 문자열을 선언하고 `+= c`로 하나씩 붙이면 되는 거였다.

---

## 풀이 과정

1. `s`를 `toCharArray()`로 char 배열로 변환
2. 각 char를 순회하면서:
   - **숫자면** → `result`에 바로 추가, `temp` 초기화
   - **문자면** → `temp`에 누적, 영단어와 매칭되면 숫자로 변환
3. 최종 `result` 문자열을 `Integer.parseInt()`로 int 변환

### 흐름 예시 (`"one4"`)

```
'o' → temp = "o"
'n' → temp = "on"
'e' → temp = "one" → words[1]과 매칭! result = "1", temp = ""
'4' → 숫자! result = "14", temp = ""
```

---

## 최종 코드

```java
class Solution {
    public int solution(String s) {
        int answer = 0;
        String[] words = {"zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine"};
        String temp = "";
        String result = "";

        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                result += c;
                temp = "";         // ← 숫자 나왔을 때도 temp 초기화 필수!
            } else {
                temp += c;
                for (int i = 0; i < words.length; i++) {
                    if (temp.equals(words[i])) {
                        result += i;
                        temp = "";
                        break;
                    }
                }
            }
        }

        answer = Integer.parseInt(result);
        return answer;
    }
}
```

---

## 채점 결과

✅ 정확성 통과

---

## 배운 것 / 느낀 점

### 1. `temp` 초기화는 숫자가 나왔을 때도 해줘야 한다

처음엔 `else` 블록에서만 `temp = ""`를 초기화하면 된다고 생각했다.  
그런데 `"one4seven"` 같은 입력에서 `'4'`를 만나면 `temp`에 이전 잔재가 남아있을 수 있다.  
숫자가 나왔을 때도 반드시 `temp = ""`를 해줘야 한다.

### 2. `Character.isDigit()` — `Character` 클래스

`Character`는 `java.lang` 패키지라 **import 없이** 바로 사용 가능하다.  
`Int.isDigit()` 같은 건 존재하지 않는다. `isDigit`은 **문자(char)** 가 숫자인지 판별하는 거라 `Character` 클래스에 속하는 게 맞다.

같이 알아두면 좋은 `Character` 메소드들:

| 메소드 | 설명 |
|---|---|
| `Character.isDigit(c)` | 숫자인지 (`'0'`~`'9'`) |
| `Character.isLetter(c)` | 문자인지 |
| `Character.isUpperCase(c)` | 대문자인지 |
| `Character.isLowerCase(c)` | 소문자인지 |

### 3. `String` → `int` 변환: `Integer.parseInt()`

최종적으로 `result`가 `"1478"` 같은 문자열로 완성되는데, 반환 타입이 `int`라 변환이 필요하다.  
순간 헷갈릴 수 있으니 기억해두자.

```java
int answer = Integer.parseInt(result);  // "1478" → 1478
```

반대로 `int → String`은 `String.valueOf(n)` 또는 `"" + n`.

---

## 다음에 비슷한 문제 만나면?

- 문자열을 **char 단위로 순회**하면서 조건 분기할 때 → `toCharArray()` + `Character.isDigit()`
- 여러 char를 **누적해서 단어로 만들 때** → 빈 String `temp`에 `+= c`
- 매칭 후엔 반드시 **`temp` 초기화**
- 최종 문자열을 숫자로 바꿀 땐 → `Integer.parseInt()`
