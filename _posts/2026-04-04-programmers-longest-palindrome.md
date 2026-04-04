---
title: "[프로그래머스] 가장 긴 팰린드롬"
date: 2026-04-04
categories: [Algorithm, 프로그래머스]
tags: [Level3, 문자열, DP]
---

## 문제 요약
문자열이 주어질 때, 부분문자열 중 가장 긴 팰린드롬의 길이를 구하는 문제.  
(팰린드롬: 앞뒤를 뒤집어도 동일한 문자열)

## 접근 방법
- 문자열을 한 글자씩 비교해야 하므로 `toCharArray()`로 char 배열에 저장
- 중심 인덱스 i를 기준으로 `arr[i-j] == arr[i+j]`인 경우 팰린드롬이 확장된다는 규칙 발견
- 홀수 길이와 짝수 길이를 각각 처리해야 함을 인식
- 최댓값 갱신 로직을 매 i 루프마다 수행

## 풀이 과정
1. `char[] arr`, `int len`, `int maxLen` 선언
2. 홀수 길이 팰린드롬: 중심 i 기준으로 `arr[i-j] == arr[i+j]` 확인
3. 배열 범위 초과 오류 발생 → for문 조건에 범위 체크 추가로 해결
4. 테스트케이스 일부 미통과 → 짝수 길이 고려 안 했음을 발견
5. 짝수 길이 팰린드롬: `arr[i-j] == arr[i+1+j]` 규칙 적용
6. `len` 초기화 위치 문제로 오답 발생 → i 루프 시작 시 초기화하도록 로직 재정비
7. maxLen 갱신을 홀수/짝수 각각의 for루프 직후에 수행하도록 수정

## 코드
```java
class Solution {
    public int solution(String s) {
        int answer = 0;
        
        char[] arr = s.toCharArray();
        int len = 0;
        int maxLen = 0;
        
        for (int i = 0; i < arr.length; i++) {
            
            // 홀수 길이 팰린드롬
            len = 1;
            for (int j = 1; i-j >= 0 && i+j < arr.length; j++) {
                if (arr[i-j] == arr[i+j]) len += 2;
                else break;
            }
            if (len > maxLen) maxLen = len;
            
            // 짝수 길이 팰린드롬
            len = 0;
            for (int j = 0; i-j >= 0 && i+j < arr.length-1; j++) {
                if (arr[i-j] == arr[i+1+j]) len += 2;
                else break;
            }
            if (len > maxLen) maxLen = len;
        }
        
        answer = maxLen;
        return answer;
    }
}
```

## 채점 결과
- 정확성: 72.1
- 효율성: 27.9

## 느낀 점 / 배운 것
- 로직을 먼저 설계하고 코드를 짜야 한다는 것을 다시 한번 느꼈다. for루프와 if문 중 무엇을 쓸지 고민하다 시간을 많이 소모했다.
- 다양한 테스트케이스(홀수/짝수 길이)를 처음부터 고려하는 습관이 필요하다.
- maxLen 갱신 로직은 이전에도 쓴 패턴인데 바로 떠올리지 못했다. 자주 쓰는 패턴은 따로 정리해두면 좋을 것 같다.

---

### 문제점 분석
- 정확성: 어떤 엣지케이스를 놓쳤는지
- 효율성: O(n²)이라 큰 입력에서 느림

## 2차 시도 (엣지케이스 수정)
### 수정한 부분
...
### 채점 결과
...

## 최적화 (Manacher 알고리즘)
### 개념 정리
...
### 코드
...
### 채점 결과
...

## 최종 정리
- 배운 것
- 다음에 비슷한 문제 만나면?
