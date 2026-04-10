# Week 1 - 은지

> 주제: 자료구조 / 알고리즘 기초

---

## 1️⃣ 시간 복잡도 / 공간 복잡도

O(100,000,000) 이하면 1초 이내 처리 가능한 기준선으로 활용합니다.

## 시간 복잡도

특정 크기의 입력(n)을 기준으로 실행하는 연산의 횟수입니다.

> **시간 복잡도를 구하는 요령**
> - 하나의 루프를 사용하여 단일 요소 집합을 반복하는 경우: O(n)
> - 컬렉션의 절반 이상을 반복하는 경우: O(n / 2) → O(n)
> - 두 개의 다른 루프를 사용하여 두 개의 개별 컬렉션을 반복하는 경우: O(n + m) → O(n)
> - 두 개의 중첩 루프를 사용하여 단일 컬렉션을 반복하는 경우: O(n²)
> - 두 개의 중첩 루프를 사용하여 두 개의 다른 컬렉션을 반복하는 경우: O(n * m) → O(n²)
> - 컬렉션 정렬을 사용하는 경우: O(n * log(n))

<details>
<summary><b>시간 복잡도 예시 코드</b></summary>

<br/>

### `O(1)` — 배열의 특정 인덱스 접근

```swift
func firstElement(of arr: [Int]) -> Int? {
    if arr.isEmpty { return nil }
    return arr[0]
}
```

설명: 입력 크기 `n`이 커져도 `arr[0]`에 접근하는 횟수는 일정합니다.

---

### `O(log n)` — 이진 탐색

```swift
func binarySearch(_ arr: [Int], target: Int) -> Int? {
    var left = 0
    var right = arr.count - 1

    while left <= right {
        let mid = (left + right) / 2

        if arr[mid] == target {
            return mid
        } else if arr[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }

    return nil
}
```

설명: 한 번 비교할 때마다 탐색 범위를 절반으로 줄이므로 `O(log n)`입니다.

---

### `O(n)` — 배열 전체 합 구하기

```swift
func sum(of arr: [Int]) -> Int {
    var total = 0
    for num in arr {
        total += num
    }
    return total
}
```

설명: 배열 원소를 처음부터 끝까지 한 번씩 모두 확인하므로 `O(n)`입니다.

---

### `O(n log n)` — 병합 정렬

```swift
func mergeSort(_ arr: [Int]) -> [Int] {
    if arr.count <= 1 { return arr }

    let mid = arr.count / 2
    let left = mergeSort(Array(arr[..<mid]))
    let right = mergeSort(Array(arr[mid...]))

    return merge(left, right)
}

func merge(_ left: [Int], _ right: [Int]) -> [Int] {
    var result: [Int] = []
    var i = 0
    var j = 0

    while i < left.count && j < right.count {
        if left[i] < right[j] {
            result.append(left[i])
            i += 1
        } else {
            result.append(right[j])
            j += 1
        }
    }

    while i < left.count { result.append(left[i]); i += 1 }
    while j < right.count { result.append(right[j]); j += 1 }

    return result
}
```

설명: 배열을 반으로 나누는 과정이 `log n`, 각 단계에서 원소를 합치는 과정이 `n`이라 전체 `O(n log n)`입니다.

---

### `O(n²)` — 이중 반복문

```swift
func printAllPairs(_ arr: [Int]) {
    for i in 0..<arr.count {
        for j in 0..<arr.count {
            print("(\(arr[i]), \(arr[j]))")
        }
    }
}
```

설명: 바깥 반복문 `n` × 안쪽 반복문 `n` = `O(n²)`입니다.

---

### `O(2^n)` — 피보나치 단순 재귀

```swift
func fibonacci(_ n: Int) -> Int {
    if n <= 1 { return n }
    return fibonacci(n - 1) + fibonacci(n - 2)
}
```

설명: 재귀 호출이 계속 두 갈래로 퍼져 호출 수가 급격히 늘어납니다. 대표적인 지수 시간 예시입니다.

</details>

---

## 공간 복잡도

알고리즘에서 사용하는 메모리의 양을 나타냅니다.

**공간 복잡도에 영향을 미치는 요소**
- 변수 (Variables)
- 자료구조 (Data structure)
- 함수 호출 (Function call)
- 할당 (Allocation)

<details>
<summary><b>공간 복잡도 예시 코드</b></summary>

<br/>

### 변수 — O(1)

```swift
func sum(_ n: Int) -> Int {
    var result = 0
    for i in 1...n {
        result += i
    }
    return result
}
```

`result`, `i` 같은 고정 개수 변수만 사용. 입력 크기 `n`이 커져도 변수 개수는 늘어나지 않음.

---

### 자료구조 — O(n)

```swift
func makeArray(_ n: Int) -> [Int] {
    var arr: [Int] = []
    for i in 0..<n {
        arr.append(i)
    }
    return arr
}
```

배열 `arr`에 `n`개의 원소 저장 → 입력 크기만큼 메모리 증가.

---

### 함수 호출 (재귀 스택) — O(n)

```swift
func factorial(_ n: Int) -> Int {
    if n == 1 { return 1 }
    return n * factorial(n - 1)
}
```

함수가 호출될 때마다 스택 프레임이 쌓임. 최대 `n`번 호출됨.

---

### 할당 (추가 메모리 생성) — O(n)

```swift
func copyArray(_ arr: [Int]) -> [Int] {
    let newArr = arr   // 새로운 배열 복사
    return newArr
}
```

입력 배열과 같은 크기의 새로운 메모리 생성 → 메모리 2배 사용 가능.

</details>

---

## 2️⃣ Array vs Linked List

### Array

Swift의 Array는 **구조체(struct)로 구현된 값 타입(Value Type)** 입니다.
변수에 할당할 때 기본적으로 값이 복사되는 특징을 가집니다.

```swift
var a = [1, 2, 3]
var b = a
b.append(4)

print(a) // [1, 2, 3]
print(b) // [1, 2, 3, 4]
```

`a`와 `b`는 독립적입니다. `b`를 수정해도 `a`에는 영향을 주지 않습니다.

Swift는 성능 최적화를 위해 내부적으로 **Copy-on-Write** 전략을 사용합니다.

#### Copy-on-Write

- 읽기만 하면 공유: `var b = a`
- 값을 수정하면 복사: `b.append(4)`

불필요한 메모리 사용을 줄이면서도 값 타입의 안전성을 유지할 수 있습니다.

Array는 메모리에 **연속적으로 저장**되는 구조라 인덱스를 통한 접근이 매우 빠릅니다.

```swift
array[3]  // O(1)
```

반면, 배열 앞부분에 삽입/삭제할 경우 기존 요소들을 모두 이동시켜야 하므로 `O(n)`이 발생합니다.

**성능 요약**

| 연산 | 시간복잡도 | 특징 |
|------|-----------|------|
| `array[i]` | O(1) | 매우 빠름 |
| `append` | 평균 O(1) | capacity 덕분 |
| `insert(at: 0)` | O(n) | 전체 이동 |
| `remove(at: 0)` | O(n) | 전체 이동 |
| `removeLast()` | O(1) | 빠름 |
| `contains` | O(n) | 선형 탐색 |
| `min/max` | O(n) | 전체 순회 |

**Swift의 Array가 빠른 이유**

1. **연속 메모리** — CPU 캐시 효율이 좋아 실제 실행 속도가 빠름
2. **Copy-on-Write** — 불필요한 복사 방지
3. **Capacity 전략** — `append` 시 capacity를 초과하면 더 큰 메모리 영역을 할당하고 복사. 배열 크기를 두 배씩 늘려 재할당 빈도를 줄이는 전략

---

### Linked List

포인터 기반의 연결 구조로, 각 노드가 **값(value)** 과 **다음 노드의 주소(next)** 를 가지고 있는 형태입니다.

데이터가 메모리에 연속적으로 저장되지 않고, 각 노드가 흩어져 존재하며 포인터로 연결됩니다.

- **삽입/삭제**: O(1) — 포인터만 변경하면 됨
- **탐색**: O(n) — 원하는 위치를 찾기 위해 처음부터 순차 탐색

Linked List는 포인터를 통해 노드를 따라가야 하므로 메모리 접근이 분산되고 **CPU 캐시 효율이 떨어집니다.**
또한 Swift 환경에서는 **ARC에 따른 참조 관리 비용**까지 추가되기 때문에, 이론상 삽입/삭제에 유리함에도 실제 성능에서는 Array보다 느린 경우가 많습니다.

> **결론**: Swift에서는 대부분 Array가 더 빠릅니다.
> - CPU 캐시 최적화 (연속 메모리)
> - ARC + 포인터 비용 부재

---

## 3️⃣ Stack / Queue

### Stack

- **LIFO (Last In First Out)**

| 연산 | 시간복잡도 |
|------|-----------|
| push | O(1) |
| pop | O(1) |
| peek | O(1) |

모든 연산이 스택의 top에서만 이루어지기 때문에, 데이터 크기와 관계없이 항상 일정한 시간 안에 수행됩니다.

**깊이 우선 탐색(DFS)** 에서 자주 사용합니다.
- DFS는 가장 최근에 방문한 노드를 기준으로 계속 깊이 들어가는 방식

---

### Queue

- **FIFO (First In First Out)**

| 연산 | 시간복잡도 |
|------|-----------|
| enqueue | O(1) |
| dequeue | 평균 O(1) |

데이터는 뒤(rear)에서 추가되고, 앞(front)에서 제거됩니다.

단, `removeFirst()`를 사용할 경우 내부적으로 모든 요소를 앞으로 한 칸씩 이동시키기 때문에 `O(n)`의 비용이 발생합니다.

**너비 우선 탐색(BFS)** 에서 사용합니다.
- BFS는 먼저 들어온 노드부터 순차적으로 탐색
