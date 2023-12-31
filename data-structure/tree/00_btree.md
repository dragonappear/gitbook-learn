# B-tree

## 정의

![btree](https://github.com/dragonappear/learn/assets/89398909/ca080851-c783-4cd2-a17e-0c5216ad7455)

- `BST` 를 일반화시킨 자료구조
- `BST` 는 한 노드에 최대 2개의 자식노드를 가질 수 있지만, B-tree 는 한 노드에 여러개의 자식노드를 가질 수 있다.

<br>

## 주요 파라미터

- `M` = 각 노드의 최대 자녀 노드 수
- `M-1` = 각 노드의 최대 키 수
- `⌈M/2⌉`= 각 노드의 최소 자녀 노드 수 (root,leaf 노드 제외)
- `⌈M/2⌉-1` = 각 노드의 최소 key 수 (root 노드 제외)

<br>

## 특징

- `internal` 노드의 `key` 수가 `x`개이면 자녀 노드의 수는 언제나 `x+1`개이다.
- 노드가 최소 하나의 `key`는 가지기 때문에 몇 차 `btree` 인지 상관없이 `internal` 노드는 최소 두 개의 자녀는 가진다.
- 모든 `leaf` 노드들은 같은 레벨에 있다.
- 노드 내의 `key`들은 오름차순으로 저장된다.

<br>

## 삽입

- **삽입은 항상 leaf 노드에서 일어난다.**
- 노드가 넘치면(노드가 가질 수 있는 최대 key 수를 넘으면) 가운데(median) 키 기준으로 좌우 `key`들을 분할하고 가운데 `key`는 승진한다.

### 삽입 재조정 과정

- 가운데 `key`를 기준으로 좌우 `key`들을 분할한다.
- 승진한 `key`를 부모 노드에 삽입한다.
- 부모 노드도 넘치면 위 과정을 반복한다.

<br>

## 삭제

- **삭제는 항상 leaf 노드에서 일어난다.**
- 삭제 후 최소 key 수(루트 노드를 제외하고 `⌈M/2⌉-1`)를 만족하지 않는 노드가 생기면 재조정한다
- 인터널 노드에 있는 데이터를 삭제하려면 리프 노드에 있는 데이터와 위치를 바꾼후 삭제한다
    - 삭제할 데이터의 선임자(나보다 작은 데이터들 중 가장 큰 데이터)나 후임자(나보다 큰 데이터들 중 가장 작은 데이터)와 위치를 바꾼다.

### 삭제 재조정 과정

1. 키 수가 여유있는 형제의 지원을 받는다.
   - 지원 받은 형제의 키를 부모 노드로 이동시키고 그 자리에 있던 부모 노드의 키를 가져온다.


2. 형제의 지원이 불가능하면 부모의 지원을 받고 형제와 합친다.
   - 부모 노드의 키를 형제 노드로 이동시키고 합친다.
   

3. 부모가 지원한 후 부모에 문제가 있다면 상황에 맞게 대응한다.
   - 부모가 루트 노드가 아니라면 그 위치에서부터 다시 1번부터 재조정 시작
   - 부모가 루트 노드이고 비어있다면 부모 노드를 삭제한 후 이전에 합쳐진 노드가 루트 노드가 된다.

<br>

## B-tree 계열이 인덱스에 사용되는 이유

- btree, bbst 모두 조회, 삽입, 삭제 avg case, worst case 모두 `O(log n)` 이다.
- DB는 기본적으로 secondary storage에 데이터를 저장하는데, DB에서 데이터를 조회할 때 secondary storage 최대한 적게 접근하는 것이 성능 면에서 좋다. 
  - `secondary storage`은 데이터 처리 속도가 가장 느리다 (탐색 시간 + 회전 대기 시간)
  - `secondary storage`은 블락 단위(파일 시스템에서 데이터를 읽고 쓰는 논리적 단위)로 데이터를 읽고 쓴다.
- btree는 블락 단위로 데이터를 읽고 쓰기 때문에 bbst에 비해 `secondary storage`를 적게 접근한다
  - 블락 단위로 저장되기 때문에 높이가 작고, 더 적은 높이로 더 많은 데이터를 저장한다
  - 자녀 노드 수가 많기 때문에 데이터를 찾을 때 탐색 범위를 빠르게 좁힐 수 있다

<br>

## B+tree

### 특징

- B-tree의 변형으로, B-tree의 단점을 보완한 자료구조
- 모든 리프 노드들은 링크드 리스트 형태로 존재
  - full scan을 할 때 리프 노드들만 순차적으로 읽으면 되기 때문에 탐색에 유리함
- 실제 데이터는 리프 노드에만 존재
  - 내부 노드들은 키만 가지고 있고, 리프 노드로 연결해주는 라우팅 기능을 함
- 중복 키를 가짐
  - 내부 노드들이 데이터를 가지고 있지 않기 때문에 리프 노드들이 키와 데이터를 모두 가지고 있어야 함

<br>

### 장점

- 풀 스캔 시, B+tree는 리프 노드에 데이터가 모두 있어 한 번의 선형 탐색만 하면 됨
    - B-tree에 비해 빠름

<br>

### B-tree와 비교

|  | B-tree | B+tree |
|----------| --- | --- |
| 데이터 저장   | 모든 내부, 리프 노드들이 데이터를 가짐 | 리프노드만 데이터를 가짐 |
| 검색 속도    | 모든 노드 검색 | 리프노드에서 선형 탐색 |
| 키 중복     | 없음 | 있음 |
| 삭제       | 내부 노드의 삭제는 보잡하고 트리 변경히 많음 | 어떠한 노드든 리프에 있기 때문에 삭제가 쉬움 |
| 링크드 리스트  | 존재 x | 리프 노드는 링크드 리스트로 저장됨 |
| 높이       | 특정 갯수의 노드는 높이가 높음 | 같은 노드일 때 B-tree보다 높이가 낮음 |


<br>

## Reference

- https://www.youtube.com/watch?v=bqkcoSm_rCs
- https://www.youtube.com/watch?v=H_u28u0usjA
- https://www.youtube.com/watch?v=liPSnc6Wzfk