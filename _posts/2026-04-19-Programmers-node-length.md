---
title: "[프로그래머스] 방문 길이"
date: 2026-04-19 00:00:00 +0900
categories: [Algorithm, 프로그래머스]
tags: [Level2, 문자열, Java, HashSet]
---

## 문제 요약

게임 캐릭터가 명령어(`U`, `D`, `L`, `R`)에 따라 이동할 때, **처음 걸어본 길의 수**를 return하는 문제.
좌표 범위는 -5 ~ 5이며, 범위를 벗어나는 이동은 무시한다.

```
"ULURRDLLU" → 7
"LULLLLLLU" → 7
```

---

## 접근 방법

처음에 고민한 것: **어떻게 "처음 지나간 길"을 판별하지?**

- 좌표(점)를 저장하면 되지 않을까? → 점이 아니라 **간선(길)** 을 저장해야 한다
- `(0,0)→(0,1)` 과 `(0,1)→(0,0)` 은 **같은 길** → 방향 무관하게 같은 형태로 저장해야 한다
- 이를 위해 **정규화** 가 필요하다 → 항상 작은 좌표가 앞에 오도록

---

## 풀이 1 — 배열로 직접 구현 (import 없이)

### 풀이 과정

1. 이동 전 좌표 `(prevX, prevY)` 와 이동 후 좌표 `(nx, ny)` 를 함께 저장
2. 정규화: x좌표 기준으로, x가 같으면 y 기준으로 작은 쪽을 앞에 저장
3. 범위 초과 이동은 스킵 (`continue`)
4. 전체 간선 수에서 중복 간선 수를 빼서 정답 계산

### 정규화가 필요한 이유

```
(0,0)→(0,1) 저장: {0, 0, 0, 1}
(0,1)→(0,0) 저장: {0, 1, 0, 0}  ← 같은 길인데 다르게 저장됨!

정규화 후:
(0,0)→(0,1): {0, 0, 0, 1}
(0,1)→(0,0): {0, 0, 0, 1}  ← 동일!
```

### 흐름 예시 (`"UUR"`)

```
이동1: (0,0)→(0,1)  edges[0] = {0, 0, 0, 1}
이동2: (0,1)→(0,2)  edges[1] = {0, 1, 0, 2}
이동3: (0,2)→(1,2)  edges[2] = {0, 2, 1, 2}
중복 없음 → answer = 3
```

### 최종 코드

```java
class Solution {
    public int solution(String dirs) {
        int[] arr = {0, 0};
        int[][] edges = new int[dirs.length()][4];
        int edgeCount = 0;

        for (char c : dirs.toCharArray()) {
            int prevX = arr[0], prevY = arr[1];
            int nx = arr[0], ny = arr[1];

            if (c == 'U') ny++;
            else if (c == 'D') ny--;
            else if (c == 'R') nx++;
            else if (c == 'L') nx--;

            // 범위 초과 스킵
            if (nx < -5 || nx > 5 || ny < -5 || ny > 5) continue;
            arr[0] = nx; arr[1] = ny;

            // 정규화: x 우선, x 같으면 y 기준으로 작은 좌표를 앞에
            if (prevX < nx || (prevX == nx && prevY < ny))
                edges[edgeCount++] = new int[]{prevX, prevY, nx, ny};
            else
                edges[edgeCount++] = new int[]{nx, ny, prevX, prevY};
        }

        // 전체 - 중복
        int answer = edgeCount;
        for (int i = 0; i < edgeCount; i++) {
            for (int j = i + 1; j < edgeCount; j++) {
                if (edges[i][0] == edges[j][0] && edges[i][1] == edges[j][1]
                 && edges[i][2] == edges[j][2] && edges[i][3] == edges[j][3]) {
                    answer--;
                    break;  // ← 같은 i가 여러 번 차감되지 않도록
                }
            }
        }

        return answer;
    }
}
```

### 주의할 점

- `edgeCount`를 인덱스로 쓰는 이유: 범위 초과로 `continue`되면 `i`와 실제 저장 개수가 달라지기 때문
- 중복 체크 시 `break` 필수: 같은 길이 3번 등장하면 `answer--`가 2번만 돼야 함
- 배열 참조 복사 주의: `edges[i] = arr` 이 아니라 값을 직접 넣어야 함

---

## 풀이 2 — HashSet 이용 (권장)

### 풀이 과정

1. 이동한 간선을 **문자열** 로 변환해서 HashSet에 저장
2. HashSet은 중복을 자동으로 제거해주므로 별도 중복 체크 불필요
3. 정규화는 `Math.min` / `Math.max` 로 한 줄에 처리

### 흐름 예시 (`"UUR"`)

```
이동1: (0,0)→(0,1)  → "0,0,0,1" 저장
이동2: (0,1)→(0,2)  → "0,1,0,2" 저장
이동3: (0,2)→(1,2)  → "0,2,1,2" 저장
visited.size() = 3 → answer = 3
```

역방향 정규화:
```
(0,1)→(0,0) → Math.min(0,0)+","+Math.min(1,0)+","+Math.max(0,0)+","+Math.max(1,0)
             → "0,0,0,1"  ← (0,0)→(0,1) 과 동일!
```

### 최종 코드

```java
import java.util.HashSet;

class Solution {
    public int solution(String dirs) {
        HashSet<String> visited = new HashSet<>();
        int x = 0, y = 0;

        for (char c : dirs.toCharArray()) {
            int nx = x, ny = y;
            if (c == 'U') ny++;
            else if (c == 'D') ny--;
            else if (c == 'R') nx++;
            else if (c == 'L') nx--;

            // 범위 초과 스킵
            if (nx < -5 || nx > 5 || ny < -5 || ny > 5) continue;

            // 정규화해서 문자열로 저장
            visited.add(Math.min(x,nx)+","+Math.min(y,ny)+","+Math.max(x,nx)+","+Math.max(y,ny));
            x = nx; y = ny;
        }

        return visited.size();
    }
}
```

---

## 두 풀이 비교

| | 풀이 1 (배열) | 풀이 2 (HashSet) |
|---|---|---|
| import | 불필요 | `java.util.HashSet` 필요 |
| 정규화 | 조건문으로 직접 | `Math.min/max` 한 줄 |
| 중복 제거 | 이중 for문 직접 구현 | Set이 자동 처리 |
| 코드 길이 | 길다 | 짧다 |
| 시간복잡도 | O(n²) | O(n) |

---

## 채점 결과

✅ 정확성 통과

---

## 배운 것 / 느낀 점

### 1. 점이 아니라 간선을 저장해야 한다

처음엔 방문한 좌표(점)를 저장하면 된다고 생각했다.
그런데 `(0,0)→(0,1)→(0,0)` 처럼 왔다 갔다 하면 점은 중복이지만 길은 1개다.
**"처음 걸어본 길"** 이므로 반드시 간선 단위로 저장해야 한다.

### 2. 정규화 — 같은 길을 항상 같은 형태로

`(0,0)→(0,1)` 과 `(0,1)→(0,0)` 은 방향만 다른 같은 길이다.
항상 작은 좌표가 앞에 오도록 정규화해야 중복 감지가 가능하다.

배열 방식: x 우선, x 같으면 y 기준
```java
if (prevX < nx || (prevX == nx && prevY < ny))
```

HashSet 방식: `Math.min/max` 활용
```java
Math.min(x,nx)+","+Math.min(y,ny)+","+Math.max(x,nx)+","+Math.max(y,ny)
```

### 3. HashSet — 중복 자동 제거 컬렉션

| 메소드 | 설명 |
|---|---|
| `set.add(value)` | 추가 (중복이면 무시) |
| `set.size()` | 중복 제거된 원소 수 |
| `set.contains(value)` | 포함 여부 확인 |

---

## 다음에 비슷한 문제 만나면?

- **"처음 방문"** 카운트 → 간선(이동 전/후 좌표 쌍) 단위로 저장
- 방향 무관하게 같은 경로 → **정규화** 필수
- import 가능하면 → **HashSet** 이 훨씬 간결
- import 불가능하면 → 4개값 배열 + 이중 for문 중복 체크
