---
title: "[Java] 타입 → 배열 변환 정리"
date: 2026-04-08 00:00:00 +0900
categories: [java, 개념정리]
tags: [java, array, string, 코테]
---
## 1. String → 배열

### `char[]`로 변환
```java
String s = "hello";
char[] arr = s.toCharArray();
// ['h', 'e', 'l', 'l', 'o']
```

### `String[]`로 변환 (split)
```java
// 구분자 기준 분리
String s = "a, b, c";
String[] arr = s.split(",");  // ["a", "b", "c"]

// 한 글자씩 분리
String s2 = "hello";
String[] arr2 = s2.split("");  // ["h", "e", "l", "l", "o"]
```

---

## 2. char → 배열
 
단독 `char` 하나를 배열로 만드는 경우는 드물지만:
```java
char c = 'a';
char[] arr = new char[]{c};
 
// String 경유 방법
char[] arr2 = String.valueOf(c).toCharArray();
```
 
---
 
## 3. int(숫자) → 배열
 
코테에서 **자릿수 분리**할 때 제일 많이 씀.
 
### String 경유 → `String[]`
```java
int n = 1234;
String[] arr = String.valueOf(n).split("");
// ["1", "2", "3", "4"]
```
 
### `int[]`로 받기
```java
int n = 1234;
String[] strArr = String.valueOf(n).split("");
int[] digits = new int[strArr.length];
for (int i = 0; i < strArr.length; i++) {
    digits[i] = Integer.parseInt(strArr[i]);
}
// [1, 2, 3, 4]
```

 
### 스트림으로 한 번에 (Java 8+)
```java
int n = 1234;
int[] digits = String.valueOf(n)
                 .chars()           // IntStream (ASCII 코드값)
                 .map(c -> c - '0') // 숫자로 변환
                 .toArray();
// [1, 2, 3, 4]
```
 
> `.chars()`는 ASCII 코드를 반환하므로 `- '0'` 처리 필수!
 
---
 
## 역변환 (배열 → 원래 타입)
 
자주 쓰는 역방향도 정리.
 
```java
// char[] → String
char[] arr = {'h', 'e', 'l', 'l', 'o'};
String s = new String(arr);       // "hello"
String s2 = String.valueOf(arr);  // 동일
 
// String[] → String (이어붙이기)
String[] arr2 = {"a", "b", "c"};
String s3 = String.join("", arr2); // "abc"
```
 
---


## 정리 표
 
| 변환 | 방법 | 결과 타입 |
|------|------|-----------|
| `String` → `char[]` | `s.toCharArray()` | `char[]` |
| `String` → `String[]` | `s.split("")` | `String[]` |
| `int` → `String[]` | `String.valueOf(n).split("")` | `String[]` |
| `int` → `int[]` | `.chars().map(c->c-'0').toArray()` | `int[]` |
| `char[]` → `String` | `new String(arr)` | `String` |
| `String[]` → `String` | `String.join("", arr)` | `String` |
