---
title: "[Java] 정렬 알고리즘 총정리 (import 없이 구현)"
date: 2026-04-19 00:00:00 +0900
categories: [java, 개념정리]
tags: [Java, 정렬, 버블정렬, 선택정렬, 삽입정렬, 퀵정렬, 병합정렬]
---

## 왜 직접 구현하는가?

`Arrays.sort()`를 쓰면 한 줄로 끝나지만, 코테에서 import를 못 쓰는 환경이거나 정렬 로직을 커스터마이징해야 할 때는 직접 구현할 수 있어야 한다.

---

## 비교 요약

| 정렬 | 시간복잡도 (평균) | 시간복잡도 (최악) | 공간복잡도 | 안정 정렬 |
|---|---|---|---|---|
| 버블 | O(n²) | O(n²) | O(1) | ✅ |
| 선택 | O(n²) | O(n²) | O(1) | ❌ |
| 삽입 | O(n²) | O(n²) | O(1) | ✅ |
| 퀵 | O(n log n) | O(n²) | O(log n) | ❌ |
| 병합 | O(n log n) | O(n log n) | O(n) | ✅ |

---

## 버블 정렬 (Bubble Sort)

### 개념

인접한 두 원소를 비교해서 순서가 틀리면 swap. 매 단계마다 가장 큰 값이 뒤쪽으로 "버블링"되어 확정된다.

```
[3, 1, 4, 2]
 i=0: 3↔1, 3↔4(패스), 4↔2 → [1, 3, 2, 4] ← 4 확정
 i=1: 1↔3(패스), 3↔2 → [1, 2, 3, 4] ← 3 확정
 i=2: 1↔2(패스) → [1, 2, 3, 4] ← 완료
```

### 코드

```java
for (int i = 0; i < arr.length - 1; i++) {
    for (int j = 0; j < arr.length - 1 - i; j++) {  // ← -i: 뒤쪽 확정된 부분 제외
        if (arr[j] > arr[j + 1]) {
            int temp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = temp;
        }
    }
}
```

### 포인트

- j루프가 `length - 1 - i`인 이유: 매 i단계마다 뒤에서부터 최댓값이 확정되므로 이미 정렬된 부분을 다시 볼 필요 없음
- swap 횟수가 많아서 실제로는 느리지만 구현이 직관적

---

## 선택 정렬 (Selection Sort)

### 개념

매 단계마다 남은 원소 중 **최솟값을 찾아** 맨 앞으로 가져온다. 앞에서부터 하나씩 확정.

```
[3, 1, 4, 2]
 i=0: 최솟값=1(idx=1) → 0번과 swap → [1, 3, 4, 2]
 i=1: 최솟값=2(idx=3) → 1번과 swap → [1, 2, 4, 3]
 i=2: 최솟값=3(idx=3) → 2번과 swap → [1, 2, 3, 4]
```

### 코드

```java
for (int i = 0; i < arr.length - 1; i++) {  // ← -1: 마지막은 자동 확정
    int minIdx = i;
    for (int j = i + 1; j < arr.length; j++) {
        if (arr[j] < arr[minIdx]) minIdx = j;
    }
    int temp = arr[minIdx];
    arr[minIdx] = arr[i];
    arr[i] = temp;
}
```

### 포인트

- i루프가 `length - 1`인 이유: 마지막 원소는 비교할 대상이 없어 자동 확정
- 버블과 달리 swap이 i당 최대 1번 → swap 횟수 최소화
- 하지만 비교 횟수는 똑같이 O(n²)

---

## 삽입 정렬 (Insertion Sort)

### 개념

이미 정렬된 앞부분에 현재 원소를 **올바른 위치에 삽입**하는 방식. 카드 패를 정렬할 때 쓰는 방식과 같다.

```
[3, 1, 4, 2]
 i=1: key=1, 3>1이므로 3을 오른쪽으로 밀고 1 삽입 → [1, 3, 4, 2]
 i=2: key=4, 3<4이므로 그대로 → [1, 3, 4, 2]
 i=3: key=2, 4>2, 3>2이므로 밀고 2 삽입 → [1, 2, 3, 4]
```

### 코드

```java
for (int i = 1; i < arr.length; i++) {
    int key = arr[i];
    int j = i - 1;
    while (j >= 0 && arr[j] > key) {
        arr[j + 1] = arr[j];  // ← swap 아니고 오른쪽으로 밀기
        j--;
    }
    arr[j + 1] = key;  // ← 빈 자리에 key 삽입
}
```

### 포인트

- 거의 정렬된 배열에서는 O(n)에 가까워 실제로 빠름
- swap이 아니라 **밀기(shift)** 를 쓴다는 것이 특징
- `j + 1`에 삽입하는 이유: while이 끝난 시점의 j는 삽입 위치보다 1 왼쪽

---

## 퀵 정렬 (Quick Sort)

### 개념

**pivot**을 기준으로 작은 것은 왼쪽, 큰 것은 오른쪽으로 분할 후 재귀적으로 정렬.

```
[3, 1, 4, 2] pivot=2
 → [1, 2, 3, 4] (2 기준으로 좌우 분할 후 재귀)
```

### 코드

```java
void quickSort(int[] arr, int left, int right) {
    if (left >= right) return;
    
    int pivot = arr[right];  // 맨 오른쪽을 pivot으로
    int i = left - 1;
    
    for (int j = left; j < right; j++) {
        if (arr[j] <= pivot) {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    // pivot을 올바른 위치로
    int temp = arr[i + 1];
    arr[i + 1] = arr[right];
    arr[right] = temp;
    
    int pi = i + 1;
    quickSort(arr, left, pi - 1);
    quickSort(arr, pi + 1, right);
}

// 호출: quickSort(arr, 0, arr.length - 1);
```

### 포인트

- 평균 O(n log n)이지만 pivot이 최솟값/최댓값일 경우 최악 O(n²)
- 이미 정렬된 배열에서 맨 앞/뒤를 pivot으로 쓰면 최악이 됨 → 중간값 pivot 전략으로 개선 가능
- 재귀 깊이만큼 스택 사용 (공간복잡도 O(log n))

---

## 병합 정렬 (Merge Sort)

### 개념

배열을 반으로 계속 나눈 뒤, **정렬하면서 합치는** 방식. 분할정복의 대표 예시.

```
[3, 1, 4, 2]
 → [3, 1] / [4, 2]
 → [3] [1] / [4] [2]
 → [1, 3] / [2, 4]
 → [1, 2, 3, 4]
```

### 코드

```java
void mergeSort(int[] arr, int left, int right) {
    if (left >= right) return;
    
    int mid = (left + right) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
}

void merge(int[] arr, int left, int mid, int right) {
    int[] temp = new int[right - left + 1];
    int i = left, j = mid + 1, k = 0;
    
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) temp[k++] = arr[i++];
        else temp[k++] = arr[j++];
    }
    while (i <= mid) temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];
    
    for (int l = 0; l < temp.length; l++)
        arr[left + l] = temp[l];
}

// 호출: mergeSort(arr, 0, arr.length - 1);
```

### 포인트

- 항상 O(n log n) 보장 (퀵정렬과 달리 최악이 없음)
- 합치는 과정에 임시 배열이 필요해서 공간복잡도 O(n)
- 안정 정렬이라 동일한 값의 순서가 유지됨

---

## 다음에 정렬 구현할 때는?

- 코테 기본 → `Arrays.sort()` (import java.util.Arrays)
- import 못 쓰는 환경, 간단한 구현 → **버블 or 삽입** (직관적)
- 성능이 중요할 때 → **퀵 or 병합**
- 안정 정렬 필요할 때 → **병합**
- 거의 정렬된 배열 → **삽입** (실제 O(n)에 가까움)
