# Data Structures in TypeScript

기본적인 몇가지 자료구조를 타입스크립트를 사용한 간단한 구현과 함께 알아봅니다.

이 글은 깊은 이해보다 기본적인 메커니즘을 이해하는 목적으로 작성되었기 때문에 전문적인 지식선에서 보기엔 잘못된 설명이 있을 수 있습니다. 또한 구현된 코드 역시 이해를 위한 단순한 구현 코드입니다.

글에 잘못된 부분이 있다면 이슈나 풀 리퀘스트 부탁드립니다. 🙇‍♂️

## Reference

이 문서는 [신찬수 교수님](https://www.youtube.com/@ChanSuShin)의 유튜브 강의와 추가로 공부한 내용을 함께 정리한 것입니다.

## List

- [배열(Array)](#배열array)
- [연결 리스트(Linked List)](#연결-리스트linked-list)
  - [단방향 연결 리스트(Singly Linked List)](#단방향-연결-리스트singly-linked-list)
  - [양방향 연결 리스트(Doubly Linked List)](#양방향-연결-리스트doubly-linked-list)
  - [원형 양방향 연결 리스트(Circular Doubly Linked List)](#원형-양방향-연결-리스트circular-doubly-linked-list)
- [스택(Stack)](#스택stack)
- [큐(Queue)](#큐queue)
- [해시 테이블(Hash Table)](#해시-테이블hash-table)
- [트리(Tree)](#트리tree)
  - [이진 트리(Binary Tree)](#이진-트리binary-tree)
  - [힙(Heap)](#힙heap)
  - [이진 탐색 트리(Binary Search Tree)](#이진-탐색-트리binary-search-tree)
  - [균형 이진 탐색 트리(Balaced Binary Search Tree)](#균형-이진-탐색-트리balanced-binary-search-tree)
  - [AVL 트리(Adelson Velsky and Landis Tree)](#avl-트리adelson-velsky-and-landis-tree)
- [유니온 파인드(Union-Find)](#유니온-파인드union-find)
- [그래프(Graph)](#그래프graph)

## 배열(Array)

index와 값의 형태로 구성된 자료구조로 index 값과 `[]`연산자를 사용해서 $O(1)$의 시간 복잡도로 값을 읽을 수 있습니다.

타입스크립트에서 배열은 기본적으로 내장되어 있으며, `Dynamic Array(동적 배열)`로 이름 그대로 값을 추가하거나 제거할 때에 사용하는 용량을 동적으로 조절해 줍니다.

배열의 마지막 요소에 값을 추가하거나(push)`*` 삭제(pop), 또는 특정 index의 값을 변경할때는 $O(1)$의 시간 복잡도를 가지며, 배열의 첫번째 요소를 제거(shift)하거나 특정 index의 요소를 제거, 또는 추가(splice)에는 $O(n)$의 시간 복잡도를 가집니다.

#### Note.

> `*`  
> 동적 배열은 내부적으로 현재 배열의 용량과 현재 배열의 개수를 가지고 있습니다.  
> 예를 들어, `A`라는 배열의 마지막에 값을 추가할 경우(push) 현재 배열의 용량에 여유가 있다면 바로 추가가 되지만, 용량이 부족하다면 기존의 배열보다 높은 용량의 임의의 배열 `B`을 생성하고 기존의 배열을 순회하며 복제합니다. 그리고 최종적으로 기존의 배열 `A`를 `B`로 재할당합니다. 이 과정에서의 push는 $O(1)$이 아닌 $O(n)$의 시간 복잡도를 가지게 됩니다.  
> 하지만 위 과정은 배열의 용량이 부족할때만 일어나는 것으로 많은 반복을 하였을 때의 평균 속도가 $O(1)$ 시간에 근접하여, 일반적으로 배열의 마지막 값의 추가(push)는 $O(1)$의 시간 복잡도를 가진다고 이야기합니다.

### 구현

```ts
class ExArray<T> {
  private items: T[]

  constructor() {
    this.items = []
  }

  push(item: T): void {
    this.item.push(item)
  }

  get(index: number): T | undefined {
    return this.items[index]
  }

  remove(item: T): boolean {
    const index = this.itmes.indexOf(item)
    if (index === -1) return false
    this.items.splice(index, 1)
    return true
  }

  size(): number {
    return this.items.length
  }
}
```

일반적으로 내장되어 있는 배열 객체를 사용하지만, 위와 같이 몇가지 배열의 동작을 정의하여 사용자 정의 배열을 구현할 수도 있습니다.

[⬆목차](#list)

## 연결 리스트(Linked list)

키(key)와 링크(Link)를 가진 노드(Node)들의 연결된 리스트를 연결 리스트라고 합니다.

연결 리스트의 첫번째 노드를 `헤드 노드(Head Node)`라고 부르며 특정 값을 읽기 위해서는 헤드 노드를 시작으로 링크를 따라 이동하며 탐색해야 하기 때문에 $O(n)$의 시간 복잡도를 가집니다.

배열에서는 특정 값에 바로 접근할 수 있는 index로 탐색에 있어서 속도의 이점이 있지만, 배열의 중간에 요소를 추가하거나 삭제할 때, 변경된 이후의 모든 요소들의 index 값을 변경해야 하기 때문에 $O(n)$의 시간 복잡도를 가지지만, 연결 리스트는 변경된 노드와 그 이전의 노드의 정보만 알고 있다면`*` 두 노드의 링크만 변경하면 되기 때문에 $O(1)$의 시간 복잡도를 가지고 변경할 수 있습니다.

#### Note.

> `*`  
> 간단하게 배열과 연결 리스트의 특징을 이야기하기 위한 내용으로, 이후 이야기할 `단방향 연결 리스트`의 경우에는 특정 노드에서 이전의 노드를 알 수 없기 때문에, 이전 노드를 알기 위해서 `헤드 노드`에서 부터 탐색해야 하기 때문에 배열과 같은 $O(n)$의 시간이 소요됩니다. 그리고 `양방향 연결 리스트`의 경우에는 이전 노드의 정보도 함께 가지고 있기 때문에 $O(1)$의 시간으로 리스트 중간의 값을 추가하거나 제거할 수 있습니다.

[⬆목차](#list)

## 단방향 연결 리스트(Singly linked list)

노드가 한쪽 방향으로만 링크를 가지고 있는 연결 리스트를 단방향 연결 리스트라고 합니다.

### 구현

```ts
class ExNode<T> {
  value: T
  next: Node<T> | null = null

  constructor(value: T) {
    this.value = value
  }
}
```

```ts
class ExSinglyLinkedList<T> {
  private head: ExNode<T> | null = null
  private size: number = 0

  insert(index: number, value: T): void {
    const newExNode = new ExNode(value)
    if (index === 0) {
      newExNode.next = this.head
      this.head = newExNode
    } else {
      let current = this.head
      let previous: ExNode<T> | null = null
      let i = 0

      while (i < index) {
        previous = current
        current = current!.next
        i += 1
      }

      newExNode.next = current
      previous!.next = newExNode
    }
    this.size += 1
  }

  remove(index: number): T | null {
    if (index < 0 || index >= this.size) {
      throw new Error("Index out of bounds")
    }

    let current = this.head
    if (index === 0) {
      this.head = current!.next
    } else {
      let previous: ExNode<T> | null = null
      let i = 0

      while (i < index) {
        previous = current
        current = current!.next
        i += 1
      }

      previous.next = current!.next
    }

    this.size -= 1
    return current ? current.value : null
  }

  getSize(): number {
    return this.size
  }
}
```

위 예시(`ExSinglyLinkedList`)의 단방향 연결 리스트는 `헤드 노드`를 가지고 있기 때문에 첫번째에 노드를 추가 또는 제거에는 $O(1)$의 시간 복잡도를 가지며 그밖에 연결 리스트 중간에 값을 추가하거나 또는 제거할 때에는 `while` 문을 통해 해당 노드까지 이동해야 하기 때문에 $O(n)$의 시간 복잡도를 가지게 됩니다.

### Generator

```ts
class ExSinglyLinkedList<T> {

  ...

  *[Symbol.iterator](): Generator<T> {
    let current = this.head;
    while (current) {
      yield current.value;
      current = current.next;
    }
  }
}
```

위와 같이 제너레이터 메서드를 구현하여 연결 리스트를 `for...of` 문을 통해 순회할 수 있습니다.

### 동작

```ts
const linkedList = new ExSinglyLinkedList<string>()

linkedList.insert(0, "A")
linkedList.insert(1, "B")

for (let node of linkedList) {
  console.log(node)
}
// "A", "B"

linkedList.insert(2, "C")
linkedList.remove(1)

for (let node of linkedList) {
  console.log(node)
}
// "A", "C"
```

[⬆목차](#list)

## 양방향 연결 리스트(Doubly linked list)

단방향 연결 리스트가 노드에 `next` 속성을 통해서 노드를 구성하는 것처럼, 양방향 연결 리스트는 `next`와 함께 `prev` 속성도 함께 구성한 자료구조입니다. 각 노드마다 가지고 있는 속성이 추가되는 것이기 때문에 메모리 사용은 늘지만 그만큼 변경에 용이합니다.

### 구현

```ts
class ExNode<T> {
  value: T
  next: ExNode<T> | null = null
  prev: ExNode<T> | null = null

  constructor(value: T) {
    this.value = value
  }
}
```

```ts
class ExDoublyLinkedList<T> {
  private head: ExNode<T> | null
  private size: number = 0

  search(value: T): ExNode<T> | null {
    let v = this.head
    while (v !== null) {
      if (v.value === value) {
        return v
      }
      v = v.next
    }
    return null
  }

  pushFront(node: ExNode<T>): void {
    if (this.head) {
      if (this.head.next) {
        this.head.next.prev = node
      }
      node.next = this.head.next
      node.prev = this.head
      this.head.next = node
    } else {
      this.head = node
    }
    this.size += 1
  }

  removeNode(node: ExNode<T>): void {
    node.prev.next = node.next
    node.next.prev = node.prev
    this.size -= 1
  }

  getSize(): number {
    return this.size
  }
}
```

양방향 연결 리스트에서도 단방향 연결 리스트처럼 `value`의 값을 가지고 노드를 삭제하는 메서드를 구현하여 사용할 수 있지만, 양방향 연결 리스트는 메서드의 파라미터를 리스트의 `node` 값으로 사용하면 `node`의 `next`, `prev` 속성을 활용하여 앞, 뒤의 노드의 링크만 수정하면 되기 때문에 $O(1)$의 시간으로 리스트에서 노드를 제거할 수 있습니다.

위 예시에는 구현하지 않았지만, 양방향 연결 리스트는 `next`와 `prev`속성이 있기 때문에 꼭 헤드 노드 다음이 아니더라도 파라미터로 노드가 주어진다면 $O(1)$의 시간으로 노드를 추가할 수 있습니다.

### 동작

```ts
const doublyLinkedList = new ExDoublyLinkedList<string>()

const headNode = new ExNode("Head")
const bNode = new ExNode("B")
const aNode = new ExNode("A")

doublyLinkedList.pushFront(headNode)
doublyLinkedList.pushFront(bNode)
doublyLinkedList.pushFront(aNode)

console.log(doublyLinkedList.getSize())
// 3

doublyLinkedList.removeNode(aNode)

console.log(doublyLinkedList.getSize())
// 2
console.log(doublyLinkedList.search("A"))
// null
console.log(doublyLinkedList.search("B"))
// ExNode
```

[⬆목차](#list)

## 원형 양방향 연결 리스트(Circular doubly linked list)

이어서, 양방향 연결 리스트에서 첫번째(head) 노드의 prev가 마지막(tail) 노드를 가리키고 tail 노드의 next가 head 노드를 가리키게 하여 원형을 이루는 형태를 원형 양방향 연결 리스트라고 합니다. 양방향 연결 리스트이기 때문에 초기 데이터의 next와 prev는 자신을 가리키도록 하였습니다.

원형 양방향 연결 리스트는 원의 형태를 가지기 때문에, 그 시작점을 구별하기 위하여 head 노드 빈값(`null`)을 가지는 더미(dummy) 노드를 만들어서 사용하기도 합니다.

### 구현

```ts
class ExNode<T> {
  value: T
  next: ExNode<T> = this
  prev: ExNode<T> = this

  constructor(value: T) {
    this.value = value
  }
}
```

```ts
class ExDoublyLinkedList<T> {
  private head: ExNode<T> | null
  private size: number = 0

  search(value: T): ExNode<T> | null {
    let v = this.head
    do {
      if (v.value === value) {
        return v
      }
      v = v.next
    } while (v !== this.head)
    return null
  }

  pushFront(node: ExNode<T>): void {
    if (this.head) {
      this.head.next.prev = node
      node.next = this.head.next
      node.prev = this.head
      this.head.next = node
    } else {
      this.head = node
    }
    this.size += 1
  }

  pushBack(node: ExNode<T>): void {
    if (this.head) {
      this.head.prev.next = node
      node.prev = this.head.prev
      node.next = this.head
      this.head.prev = node
    } else {
      this.head = node
    }
    this.size += 1
  }

  removeNode(node: ExNode<T>): void {
    node.prev.next = node.next
    node.next.prev = node.prev
    this.size -= 1
  }

  getSize(): number {
    return this.size
  }

  print(): void {
    if (this.size === 0) {
      console.log("List is empty")
      return
    }

    let current = this.head
    const result = []
    do {
      result.push(current!.value)
      current = current!.next
    } while (current !== this.head)

    console.log(result.join(" <-> "))
  }
}
```

원형 양방향 연결 리스트의 Node(`ExNode`) 클래스의 경우 기본적으로 `next`와 `prev`가 자신을 가리키도록 설정하였기 때문에 양방향 연결 리스트와 달리 `pushFront` 메서드의 `node`의 `next` 속성의 존재 여부를 확인하지 않아도 되며, 원형 양방향 연결 리스트의 경우 마지막 노드와 `head` 노드가 이어지기 때문에, 마지막 노드의 속성(`tail`)을 정의하지 않고도 마지막에 `node`를 추가할 수 있는 메서드(`pushBack`)를 $O(1)$ 시간에 동작하도록 구현할 수 있습니다.

그리고 탐색 메서드(`search`)에서 반복문의 종료 조건의 차이가 있습니다.  
이 `next` 속성의 값이 없을 때가 아닌, `head`일 때로의 차이가 있습니다.

### 동작

```ts
const doublyLinkedList = new ExDoublyLinkedList<string>()

const headNode = new ExNode("Head")
const bNode = new ExNode("B")
const aNode = new ExNode("A")
const cNode = new ExNode("C")

doublyLinkedList.pushFront(headNode)
doublyLinkedList.pushFront(bNode)
doublyLinkedList.pushFront(aNode)
doublyLinkedList.pushBack(cNode)

doublyLinkedList.print()
// Head <-> A <-> B <-> C
```

[⬆목차](#list)

## 스택(Stack)

먼저 들어온 값이 마지막에 나간다는(First In, Last Out) 규칙을 가진 자료구조입니다. 마지막에 값을 추가하는 메서드(push)와 마지막 값을 꺼내오는 메서드(pop)를 가집니다.  
스택은 내부적으로 배열이나 연결리스트를 사용합니다. 아래의 예시는 배열을 사용하였습니다.

### 구현

```ts
class ExStack<T> {
  private items: T[] = []

  push(item: T): void {
    this.items.push(item)
  }

  pop(): T | undefined {
    return this.items.pop()
  }

  peek(): T | undefined {
    return this.items[this.items.length - 1]
  }

  size(): number {
    return this.items.length
  }
}
```

스택은 값을 추가하거나 꺼낼 때 모두 배열의 마지막 요소의 추가, 제거이기 때문에 $O(1)$의 시간 복잡도를 가집니다.  
배열이 아닌 연결 리스트에서 스택을 구현한다면 `head` 노드에 요소를 추가하고 꺼낼 때 역시 `head` 노드를 제거하며 가져오도록 구현하면 동일하게 $O(1)$의 시간에 수행할 수 있습니다.

### 동작

```ts
const stack = new ExStack<string>()

stack.push("A")
stack.push("B")
stack.push("C")

console.log(stack.size())
// 3

stack.pop()
console.log(stack)
// ExStack { items: [A, B] }
```

[⬆목차](#list)

## 큐(Queue)

먼저 들어온 값이 먼저 나간다는(First In, First Out) 규칙을 가진 자료구조 입니다. 마지막에 값을 추가하는 메서드(enqueue)와 첫번째 값을 꺼내오는 메서드(dequeue)를 가집니다.  
큐 역시 내부적으로 배열이나 연결리스트를 사용합니다. 아래의 예시는 배열을 사용하였습니다.

### 구현

```ts
class ExQueue<T> {
  private items: T[] = []
  private count: number = 0
  private head: number = 0

  enqueue(item: T): void {
    this.items.push(item)
    this.count += 1
  }

  dequeue(): T | undefined {
    if (this.count === 0) {
      return undefined
    }

    const item = this.items[this.head]
    this.head += 1
    this.count -= 1

    // 메모리 관리를 위해 조건에 따라 배열을 축소합니다.
    if (this.head > this.items.length / 2) {
      this.items = this.items.slice(this.head)
      this.head = 0
    }

    return item
  }

  peek(): T | undefined {
    return this.isEmpty() ? undefined : this.items[this.head]
  }

  size(): number {
    return this.count
  }
}
```

dequeue를 정의 할때 배열의 `shift` 메서드를 사용한 방법을 생각할 수도 있지만, 그렇게 되면 타입스크립트의 동적 배열의 특성상 매번 내부적으로 $O(n)$ 시간으로 `index` 이동이 수행되기 때문에 `head` 라는 속성으로 첫번째 `index` 값을 정의하고 해당 값을 변경하여 값을 가져올 수 있도록 합니다.

단, `head` 속성을 사용하여 구현 시, 값이 추가되고 읽혀질 수록 사용되는 불필요한 메모리가 계속 증가하기 때문에 메모리를 조절할 수 있는 로직이 추가로 필요합니다.

연결 리스트를 사용하여 구현한다면, 연결 리스트에 `head`와 `tail` 속성을 정의해서 `tail` 노드 다음으로 노드를 `enqueue`하고 `dequeue`는 `head`의 노드를 제거하며 가져오도록 구현할 수 있습니다.

[⬆목차](#list)

## 해시 테이블(Hash Table)

key와 value 형태의 데이터 구조로, 크게 데이터가 보관되는 `table(리스트)`, key에 따른 테이블의 위치를 지정하는 `hash function(해시 함수)` 그리고 동일한 위치 지정으로 충돌이 발생했을때의 `collision resolution method(충돌 해결 방법)`. 이렇게 세가지로 되어있습니다.

아주 간단한 예로, `key`가 정수 일때, key의 나머지 값을 index 값을 지정하는 `hash function(해시 함수)`인 `division method hash function(나눗샘 해시 함수)`를 사용하고 `collision resolution method(충돌 해결 방법)`으로 `linear probing(선형 탐침법)`을 사용하는 테이블의 크기가 m인 해시 테이블이 있을때,

```ts
keys = [10, 11, 22]
```

위와 같이 `key` 값들이 주워졌다면

```ts
// hash function(division method hash function)
// (key % m) = hash index
10 % 3 = 1
11 % 3 = 2
22 % 3 = 1
```

`10`은 해시 테이블의 1번 index에 위치하며, `11`은 2번 index에, `22`는 1번에 위치해야 하지만 현재 1번 index에는 값이 있으므로 `linear probing(선형 탐침법)`에 따라 1을 더해서 2번으로 내려가지만(chaining)`*`, 2번 역시 값이 있으므로 다시 그 다음인, 처음으로 돌아가 0번의 인덱스에 위치하게 됩니다.

#### Note.

> `*`
> 간단하게 위 설명에서는 충돌에 대해서 `linear probing(선형 탐침법)`으로 단순하게 해시한 index에 1을 더했지만, 실제로는 해시한 index 값에 1을 더한 값에 대한 테이블 크기의 나머지 값을 사용합니다.  
> 1(해시한 index) + 1 % 3 = 2  
> 2(해시한 index) + 1 % 3 = 0

```ts
// hash table
hashTable = [22, 10, 11]
```

값을 탐색할때에도 역시 위와 같은 방법으로 탐색합니다.

주의할 점은 해시 테이블의 값을 지우는(`remove`) 메서드를 구현할 때에는 해당 인덱스를 비우는 것으로 끝나지 않고, 그 다음의 인덱스의 값이 선형 탐침법에 의하여 아래로 내려온 테이블인지 확인하여 맞다면 지워진 인덱스에 값을 넣습니다. 위 과정을 다음 인덱스가 비어있을때까지 수행합니다.

### 구현

```ts
class ExHashTable<T> {
  private buckets: Array<{ key: number; value: T } | null>
  private size: number

  constructor(size: number) {
    this.buckets = new Array(size).fill(null)
    this.size = size
  }

  private hash(key: number): number {
    return key % this.size
  }

  set(key: number, value: T): void {
    let index = this.hash(key)
    const originalIndex = index
    let i = 1

    // 충돌 해결 방법(Collision resolution method)
    while (this.buckets[index] !== null && this.buckets[index]!.key !== key) {
      index = (originalIndex + i) % this.size
      i += 1
      if (index === originalIndex) {
        throw new Error("Hash table is full")
      }
    }

    this.buckets[index] = { key, value }
  }

  get(key: number): T | undefined {
    let index = this.hash(key)
    const originalIndex = index
    let i = 1

    while (this.buckets[index] !== null) {
      if (this.buckets[index]!.key === key) {
        return this.buckets[index]!.value
      }
      index = (originalIndex + i) % this.size
      i += 1
      if (index === originalIndex) {
        return undefined
      }
    }
    return undefined
  }

  delete(key: number): boolean {
    let index = this.hash(key)
    const originalIndex = index
    let i = 1

    while (this.buckets[index] !== null) {
      if (this.buckets[index]!.key === key) {
        this.buckets[index] = null
        this.rearrange(index)
        return true
      }
      index = (originalIndex + i) % this.size
      i += 1
      if (index === originalIndex) {
        return false
      }
    }
    return false
  }

  private rearrange(startIndex: number): void {
    let currentIndex = (startIndex + 1) % this.size
    while (this.buckets[currentIndex] !== null) {
      const { key, value } = this.buckets[currentIndex]!
      this.buckets[currentIndex] = null
      this.set(key, value)
      currentIndex = (currentIndex + 1) % this.size
    }
  }
}
```

### 동작

```ts
const hashTable = new ExHashTable<string>(3)

hashTable.set(1, "1")
hashTable.set(4, "2")
hashTable.set(7, "3")

hashTable.print()
// [{"key":7,"value":"3"},{"key":1,"value":"1"},{"key":4,"value":"2"}]

hashTable.delete(1)

hashTable.print()
// [null,{"key":4,"value":"2"},{"key":7,"value":"3"}]
```

위 키값 1, 4, 7은 모두 나머지 연산시 1이 되기 때문에 `linear probing(선형 탐침법)`으로 처음 선언된 `"1"`은 1번 index에, `"2"`는 2번 index에, `"3"`은 0번 인덱스에 위치하게 되며, 여기에 `"1"`을 다시 삭제 할 경우 이후의 index 값을 재배열하여 위와 같이 하나씩 앞으로 당겨저 `"2"`는 1번 index로 `"3"`은 2번 index로 변경됩니다.

### 성능

해시 테이블의 성능은 어떻게 해시 함수를 정의하고 어떻게 충돌에 대한 처리를 정의하는지, 그리고 어느 정도의 로드 펙터를 유지하고 있는지에 따라 차이가 있습니다.

일반적으로 충돌에 대해 오픈 어드레스 방식('선형 탐침법'이 이에 해당)의 경우 로드 팩터를 0.5~0.7로 유지하거나, 체이닝 방식으로 구현할 경우 0.75를 유지한다면 $O(1)$의 성능을 가집니다.

체이닝 방식의 경우 내용으로 작성하진 않았지만, 간략히, 해시 테이블의 값을 연결 리스트로 구성하는 이중 자료구조 형태로 앞서 이야기한 '선형 탐침법'처럼 다른 index로 값을 넘기는 것이 아닌 같은 index안의 연결 리스트로 구성한 방식입니다. 위 방식 역시 최악의 경우 모든 데이터 n이 하나의 index에 모이게 되면 $O(n)$의 시간 복잡도를 가지기 때문에 체이닝 방식을 사용할 경우 해시 함수의 구성이 중요합니다. 해시 함수로 C-유니버설 해시 함수`*`를 사용할 경우 $O(1)$의 성능을 가집니다.

#### Note.

> `*`  
> 충돌의 빈도가 `1/m(테이블의 크기)`인 해시 함수를 '유니버설 해시 함수' 라고 부르며, 그보다 조금 완화된 `C(상수)/m`인 해시 함수를 'C-유니버설 해시 함수' 라고 합니다.

### 정리

해시 테이블에 대해 간략히 알아보았지만, 타입스크립트에서는 ES6에서 `Map` 객체가 도입되어 `Map` 객체를 활용하여 간편하게 해시 테이블의 자료구조를 사용할 수 있습니다

[⬆목차](#list)

## 트리(Tree)

간단하게는 연결 리스트를 세로로 세운 모습을 생각할 수 있으며, 최초 시작 노드를 `root 노드(또는 head 노드)`라고 하며, 가장 마지막의 자식이 없는 요소를 `leaf 노드`라고 합니다.  
트리 구조는 우리 주위에서 흔히 많이 사용되지만, 컴퓨터 공학에서는 그 중에서도 이진 트리(Binary tree)를 많이 사용됩니다.

[⬆목차](#list)

## 이진 트리(binary tree)

이진 트리는 자식 요소를 하나 또는 두개까지 갖는 트리 형태의 자료구조 입니다. 이진 트리에는 여러 표현법이 있지만 대표적으로 리스트로 표현하는 방법과 클래스로 표현하는 방법이 있습니다.

아래의 이진 트리를 예시로 사용합니다.

```
          A
     ┌────┴────┐
     B         C
     └───┐ ┌───┴───┐
         D E       F
```

### 리스트 표현

리스트로 표현하는 방법은 다시 두가지로 나뉘는데, 첫번째는 이진 노드가 꽉 차있을 때를 기준으로 순서대로 리스트에 담는 방법입니다. 빈요소는 `null`을 넣고, 빈 요소로 끝난다면 마지막의 빈요소 들은 담지 않습니다.

```
[A, B, C, null, D, E, F]
```

이와 같은 표현의 장점은 현재 노드의 왼쪽 자식의 index를 현재 노드의 $index * 2 + 1$, 그리고 오른쪽 자식의 index를 $index * 2 + 2$로 $O(1)$ 시간 안에 구할 수 있습니다. 역으로 $Math.floor((index - 1)/2)$로 부모의 index도 $O(1)$ 시간안에 구할 수 있습니다.

다음으로, 또 다른 방법은 리스트를 겹처서 표현하는 방법입니다.

```
// [현재요소, [현재 요소의 왼쪽 요소], [현재 요소의 오른쪽 요소]]
[A, [B, [], [D, [], []]], [C, [E, [], []], [F, [], []]]]
```

### 클래스 표현

```ts
class ExNode<T> {
  private key: T
  private parent: ExNode<T> | null
  private left: ExNode<T> | null
  private right: ExNode<T> | null

  constructor(key) {
    this.key = key
  }
}
```

대략, 위와 같은 노드들을 연결해서 이진 트리를 구현할 수 있습니다.

### 순회

이진 트리를 순회하는 방법으로 `전위 순회(preorder)`, `중위 순회(inorder)`, `후위 순회(postorder)` 방법이 있습니다.

```
          A
     ┌────┴────┐
     B         C
     └───┐ ┌───┴───┐
         D E       F
```

위 이진 트리를 예로 들면,

`preorder`는 현재의 노드, 왼쪽 자식 노드, 오른쪽 자식 노드 순서로 순회하는 방법입니다.

```
A, B, D, C, E, F
```

`inorder`는 왼쪽 자식 노드, 현재의 노드, 오른쪽 자식 노드 순서로 순회하는 방법입니다.

```
B, D, A, E, C, F
```

`postorder`는 왼쪽 자식 노드, 오른쪽 자식 노드, 현재의 노드를 순회하는 방법입니다.

```
D, B, E, F, C, A
```

[⬆목차](#list)

## 힙(Heap)

힙 자료구조는 완전 이진 트리(complete binary tree)`*`의 형태로 부모의 노드가 자식의 노드보다 항상 크거나 같은 값을 가지는 최대 힙(Max Heap)과 부모 노드가 자식의 노드보다 항상 작거나 같은 값을 가지는 최소 힙(Min Heap)이 있습니다.

#### Note.

> `*`  
> 앞서 이야기한 리스트 표현법 첫번째를 예로 들면, 중간에 null이 없이 꽉찬 형태의 이진 트리를 '완전 이진 트리'라고 합니다.  
> 완전 이진 트리의 조건은 마지막 레벨을 제외하고 모두 채워져 있어야 하며, 노드는 왼쪽에서 오른쪽으로 채워져야 합니다.

### 최소 힙(Min Heap)

```ts
class ExMinHeap {
  private heap: number[]

  constructor() {
    this.heap = []
  }

  insert(value: number): void {
    this.heap.push(value)
    this.bubbleUp()
  }

  extractMin(): number | null {
    if (this.heap.length === 0) {
      return null
    }
    if (this.heap.length === 1) {
      return this.heap.pop() || null
    }
    const min = this.heap[0]
    this.heap[0] = this.heap.pop()!
    this.bubbleDown()
    return min
  }

  private bubbleUp(): void {
    let index = this.heap.length - 1
    while (index > 0) {
      const parentIndex = Math.floor((index - 1) / 2)
      if (this.heap[index] >= this.heap[parentIndex]) {
        break
      }
      ;[this.heap[index], this.heap[parentIndex]] = [
        this.heap[parentIndex],
        this.heap[index],
      ]
      index = parentIndex
    }
  }

  private bubbleDown(): void {
    let index = 0
    const length = this.heap.length
    const element = this.heap[0]

    while (true) {
      let leftChildIndex = 2 * index + 1
      let rightChildIndex = 2 * index + 2
      let leftChild: number, rightChild: number
      let swap = null

      if (leftChildIndex < length) {
        leftChild = this.heap[leftChildIndex]
        if (leftChild < element) {
          swap = leftChildIndex
        }
      }

      if (rightChildIndex < length) {
        rightChild = this.heap[rightChildIndex]
        if (
          (swap === null && rightChild < element) ||
          (swap !== null && rightChild < leftChild!)
        ) {
          swap = rightChildIndex
        }
      }

      if (swap === null) {
        break
      }

      ;[this.heap[index], this.heap[swap]] = [this.heap[swap], this.heap[index]]
      index = swap
    }
  }

  printHeap(): void {
    console.log(this.heap)
  }
}
```

위와 같이, 값을 추가하는 `insert` 메서드와 가장 작은 값을 제거하며 가져오는 `extractMin` 메서드를 가지고 있습니다.  
값을 추가할 때, 트리의 마지막에 노드를 추가하고 추가한 노드의 부모를 따라가며 노드의 위치를 지정하는 `bubbleUp` 메서드와 가장 작은 값(head 노드 또는 root 노드)을 제거하고 가져올 때, 비어있는 head 노드 자리에 마지막 노드를 위치시키고 head 노드부터 자식 노드를 순회하며 자신보다 작은 값의 자식 노드를 만나면 스왑하는 `bubbleDown` 메서드를 가지고 있습니다.

위 예시는 '최소 힙'만을 구현 했지만, 비교 연산자만 반대로 하면 '최대 힙(Max Heap)'이 됩니다.

구현한 힙의 `insert`와 `extractMin` 메서드는 기본적으로 $O(1)$의 상수 시간의 연산과 각각 `bubbleUp`과 `bubbleDown` 메서드를 사용하며, 이 두 메서드는 힙의 높이만큼 부모 또는 자식 노드와 비교 및 교환을 수행하기 때문에 $O(\log n)$의 시간 복잡도를 가집니다.

### 동작

```ts
const minHeap = new ExMinHeap()
minHeap.insert(10)
minHeap.insert(5)
minHeap.insert(3)
minHeap.insert(2)
minHeap.insert(7)

minHeap.printHeap()
// [2, 3, 5, 10, 7]

console.log(minHeap.extractMin()) // 2
minHeap.printHeap()
// [3, 7, 5, 10]

console.log(minHeap.extractMin()) // 3
minHeap.printHeap()
// [5, 10, 7]
```

[⬆목차](#list)

## 이진 탐색 트리(Binary Search Tree)

모든 각 노드는 자신의 왼쪽 노드의 key 값보다 작아야 하고, 오른쪽 노드의 key 값보다 커야하는 규칙을 가진 이진 트리 자료구조 입니다. (동일한 키값에 대해서는 따로 정의가 필요한데, 이 문서에서는 동일한 키값의 노드가 없다고 가정합니다.)

### 구현

```ts
class ExNode<T> {
  value: T
  left: ExNode<T> | null = null
  right: ExNode<T> | null = null

  constructor(value: T) {
    this.value = value
  }
}
```

```ts
class ExBinarySearchTree<T> {
  root: ExNode<T> | null = null

  insert(value: T): void {
    const newNode = new ExNode(value)

    if (this.root === null) {
      this.root = newNode
    } else {
      this.insertNode(this.root, newNode)
    }
  }

  private insertNode(node: ExNode<T>, newNode: ExNode<T>): void {
    if (newNode.value < node.value) {
      if (node.left === null) {
        node.left = newNode
      } else {
        this.insertNode(node.left, newNode)
      }
    } else {
      if (node.right === null) {
        node.right = newNode
      } else {
        this.insertNode(node.right, newNode)
      }
    }
  }

  search(value: T): ExNode<T> | null {
    return this.searchNode(this.root, value)
  }

  private searchNode(node: ExNode<T> | null, value: T): ExNode<T> | null {
    if (node === null) {
      return null
    }

    if (value < node.value) {
      return this.searchNode(node.left, value)
    } else if (value > node.value) {
      return this.searchNode(node.right, value)
    } else {
      return node
    }
  }

  remove(value: T): void {
    this.root = this.removeNode(this.root, value)
  }

  private removeNode(node: ExNode<T> | null, value: T): ExNode<T> | null {
    if (node === null) {
      return null
    }

    if (value < node.value) {
      node.left = this.removeNode(node.left, value)
      return node
    } else if (value > node.value) {
      node.right = this.removeNode(node.right, value)
      return node
    } else {
      if (node.left === null && node.right === null) {
        return null
      }
      if (node.left === null) {
        return node.right
      }
      if (node.right === null) {
        return node.left
      }

      const minNode = this.findMinNode(node.right)
      node.value = minNode!.value
      node.right = this.removeNode(node.right, minNode!.value)
      return node
    }
  }

  private findMinNode(node: ExNode<T>): ExNode<T> | null {
    while (node && node.left !== null) {
      node = node.left
    }
    return node
  }

  preorderTraversal(
    node: ExNode<T> | null,
    callback: (value: T) => void
  ): void {
    if (node !== null) {
      callback(node.value)
      this.preorderTraversal(node.left, callback)
      this.preorderTraversal(node.right, callback)
    }
  }
}
```

추가(`insert`)의 경우 이진 탐색 트리의 조건 대로 추가할 노드와 현재의 노드의 크기를 비교하며 좌우 자식으로 이동하며 반복하고 빈 노드를 발견하면 위치 시킵니다. 탐색(`search`) 역시 같은 로직을 수행합니다.

### 삭제

삭제(`remove`)에는 'deleteByMerging' 과 'deleteByCopying' 방법이 있습니다.  
(위 예시에서는 deleteByCopying' 방법을 사용하였습니다.)

### deleteByCopying

`deleteByCopying` 방법은 또 두가지 방법으로 나뉘는데, 삭제할 노드의 왼쪽의 가장 오른쪽 자식 노드(삭제할 왼쪽 노드 중 가장 큰 값)를 삭제할 노드에 위치시키거나 삭제할 노드의 오른쪽의 가장 왼쪽 자식 노드(삭제할 오른쪽 노드중 가장 작은 값)를 삭제할 노드에 위치시키는 방법 입니다.

(위 예시에서는 삭제할 노드의 오른쪽 노드의 가장 왼쪽 자식 노드를 이동하였습니다.)

```
          3
     ┌────┴────┐
     1         6
     └───┐ ┌───┴───┐
         2 4       8
           └──┐ ┌──┴──┐
              5 7     9
```

동일하게 위와 같은 이진 탐색 트리가 있고 6 노드를 제거한다면, 삭제할 6 노드 자리에 오른쪽 자식 노드인 8번 노드의 자식중 가장 작은 노드를 찾아서 6 노드의 위치로 이동합니다.

```
          3
     ┌────┴────┐
     1         7
     └───┐ ┌───┴───┐
         2 4       8
           └──┐    └──┐
              5       9
```

### deleteByMerging

`deleteByMerging` 방법은 삭제할 노드의 오른쪽, 왼쪽 모든 자식 노드가 있을때 왼쪽 노드를 삭제한 자리에 이동시키고 삭제한 노드의 오른쪽 노드를 이동한 노드의 가장 오른쪽에 위치시키는 방법입니다.

```
          3
     ┌────┴────┐
     1         6
     └───┐ ┌───┴───┐
         2 4       8
           └──┐ ┌──┴──┐
              5 7     9
```

위와 같은 이진 탐색 트리가 있고, 만약 6 노드를 지운다고 가정한다면, 6 노드 자리에, 6 노드의 왼쪽 자식인 4 노드가 이동하고 4 노드의 가장 오른쪽 자식 노드에 6 노드의 오른쪽 자식 이었던 8 노드가 이동하는 방법입니다.

```
          3
     ┌────┴────┐
     1         4          8
     └───┐     └───┐   ┌──┴──┐
         2         5   7     9
```

```
          3
     ┌────┴────┐
     1         4
     └───┐     └───┐
         2         5
                   └───┐
                       8
                    ┌──┴──┐
                    7     9
```

이렇게 노드를 삭제한 후에도 '탐색 이진 트리'가 유지되는 것을 확인 할 수 있습니다.

### 시간 복잡도

삽입, 검색, 삭제의 모든 메서드가 $O(h)$ 의 시간 복잡도를 갖습니다.($h$ = 트리의 높이)  
최악의 경우 $O(n)$이지만, 평균적으로 $O(\log n)$의 시간 복잡도를 갖습니다.

### 동작

```ts
const bst = new ExBinarySearchTree<number>()
bst.insert(10)
bst.insert(5)
bst.insert(15)
bst.insert(3)
bst.insert(7)
bst.insert(12)
bst.insert(18)

bst.preorderTraversal(bst.root, (value) => console.log(value))
// 10 5 3 7 15 12 18

console.log("Search 7:", bst.search(7))
// ExNode { value: 7, left: null, right: null }
console.log("Search 20:", bst.search(20))
// null

bst.remove(10)
bst.preorderTraversal(bst.root, (value) => console.log(value))
// 12 5 3 7 15 18
```

[⬆목차](#list)

## 균형 이진 탐색 트리(Balanced Binary Search Tree)

'균형 이진 탐색 트리'는 앞서 이야기한 '이진 탐색 트리'의 경우 시간 복잡도가 트리의 높이에 해당하는데, 이는 평균적으로는 $O(\log n)$의 시간을 갖지만 최악의 경우 $O(n)$의 시간 복잡도를 가지기 때문에, 이 트리의 높이를 가능한 한 최소로 유지하여 시간 복잡도를 $O(\log n)$을 유지하도록 하는 자료구조 입니다.

'균형 이진 탐색 트리' 에서는 트리의 높이를 최소한으로 유지하기 위해 회전(rotation)을 사용합니다.

### 오른쪽 회전

```
                       7
                  ┌────┴────┐
                  5         8
              ┌───┴───┐     └───┐
              3       6         9
          ┌───┴───┐
          2       4
      ┌───┘
      1
```

위 이진 트리를 예로 들어보면, 현재 트리는 `5` 노드를 기준으로 왼쪽 자식 트리의 높이가 3이고 오른쪽 자식 트리의 높이는 1로 두 높이의 차이는 2입니다.
이때, `5` 노드를 기준으로 오른쪽 회전을 통해 두 높이 차이를 줄일 수 있습니다.

```
                       7
                  ┌────┴────┐
                  3         8
              ┌───┴───┐     └───┐
              2       5         9
          ┌───┘   ┌───┴───┐
          1       4       6
```

`5` 노드를 기준으로 오른쪽으로 회전을 한다면, `5` 노드는 오른쪽 아래 자식 노드 자리로 이동하고 왼쪽 노드였던 `3` 노드가 기존의 `5`번 위치인 위로 올라옵니다. 그리고 `3` 노드가 올라오면서 자리를 잃은 `4` 노드는 오른쪽 노드인 `5`의 왼쪽 노드 위치로 이동합니다.

이렇게 하면, 이진 탐색 트리의 규칙은 지키면서 노드의 왼쪽과 오른쪽의 높이 차이가 2에서 0으로 줄어든 것을 확인 할 수 있습니다.

`왼쪽 회전`은 위와 같은 로직에 방향만 반대로 동작합니다.

[⬆목차](#list)

## AVL 트리(Adelson-Velsky and Landis Tree)

AVL 트리는 앞서 이야기 했던 것처럼 모든 노드에서 각 노드의 왼쪽 노트 트리와 오른쪽 노드 트리의 높이 차이가 최대 1인 트리입니다.  
각 노드의 높이 차이가 중요한 구조이기 때문에 각 노드에 `height` 속성이 추가됩니다.

### 구현

```ts
class ExNode<T> {
  value: T
  left: ExNode<T> | null = null
  right: ExNode<T> | null = null
  height: number = 1

  constructor(value: T) {
    this.value = value
  }
}
```

```ts
class ExAVLTree<T> {
  root: ExNode<T> | null = null

  insert(value: T): void {
    this.root = this.insertNode(this.root, value)
  }

  private insertNode(node: ExNode<T> | null, value: T): ExNode<T> {
    if (node === null) {
      return new ExNode(value)
    }

    if (value < node.value) {
      node.left = this.insertNode(node.left, value)
    } else if (value > node.value) {
      node.right = this.insertNode(node.right, value)
    } else {
      return node
    }

    node.height =
      1 + Math.max(this.getHeight(node.left), this.getHeight(node.right))

    const balance = this.getBalance(node)

    if (balance > 1 && value < node.left!.value) {
      return this.rotateRight(node)
    }

    if (balance > 1 && value > node.left!.value) {
      node.left = this.rotateLeft(node.left!)
      return this.rotateRight(node)
    }

    if (balance < -1 && value > node.right!.value) {
      return this.rotateLeft(node)
    }

    if (balance < -1 && value < node.right!.value) {
      node.right = this.rotateRight(node.right!)
      return this.rotateLeft(node)
    }

    return node
  }

  private rotateLeft(z: ExNode<T>): ExNode<T> {
    const y = z.right!
    const T2 = y.left

    y.left = z
    z.right = T2

    z.height = Math.max(this.getHeight(z.left), this.getHeight(z.right)) + 1
    y.height = Math.max(this.getHeight(y.left), this.getHeight(y.right)) + 1

    return y
  }

  private rotateRight(z: ExNode<T>): ExNode<T> {
    const y = z.left!
    const T3 = y.right

    y.right = z
    z.left = T3

    z.height = Math.max(this.getHeight(z.left), this.getHeight(z.right)) + 1
    y.height = Math.max(this.getHeight(y.left), this.getHeight(y.right)) + 1

    return y
  }

  private getHeight(node: ExNode<T> | null): number {
    return node ? node.height : 0
  }

  private getBalance(node: ExNode<T> | null): number {
    return node ? this.getHeight(node.left) - this.getHeight(node.right) : 0
  }

  preorderTraversal(
    node: ExNode<T> | null,
    callback: (value: T) => void
  ): void {
    if (node !== null) {
      callback(node.value)
      this.preorderTraversal(node.left, callback)
      this.preorderTraversal(node.right, callback)
    }
  }
}
```

### insert(한번 회전)

```
                       7
                  ┌────┴────┐
                  5         8
              ┌───┴───┐     └───┐
              3       6         9
          ┌───┴───┐
          2       4
```

위와 같은 AVL 트리가 있고 `1` 노드를 추가한다고 하면, root 노드를 시작으로 우선 `7` 노드를 기준으로 `1` 노드는 가장 왼쪽 아래에 노드에 추가가 되는데요.

```
                       7
                  ┌────┴────┐
                  5         8
              ┌───┴───┐     └───┐
              3       6         9
          ┌───┴───┐
          2       4
      ┌───┘
      1
```

여기서 보면, 추가된 `1` 노드의 부모인 `2` 노드와 그 부모인 `3` 노드는 그 노드를 기준으로 왼쪽, 오른쪽 자식 트리의 높이 차이가 1 이기 때문에 회전을 하지 않습니다. 하지만 그 위의 부모 `5` 노드의 경우 왼쪽과 오른쪽의 트리의 높이 차이가 2가 되기 때문에 여기서 회전을 실행하며, 왼쪽 노드의 높이가 크기 때문에 오른쪽으로 회전을 합니다.

조금 더 자세히 이야기하면, 여기서 `5` 노드의 왼쪽, 오른쪽 트리의 높이의 차이가 2가 되어 회전을 할때, `5` 노드의 왼쪽 트리의 높이값이 오른쪽 트리의 높이값보다 크고, 새로 추가한 노드도 `5`의 왼쪽 자식 노드(`3`)의 왼쪽에 추가 되었을때(새로 추가한 노드가 `3`보다 작을때) 간략히 이야기하면, 왼쪽 높이값이 더 크고 같은 왼쪽 자식의 왼쪽에 노드가 추가되었을때는 오른쪽으로 회전합니다.

만약에 반대로 위 AVL 트리에서 `1` 노드가 추가되지 않고 `10` 노드가 추가되었다면, `8` 노드를 기준으로 봤을때 오른쪽 트리의 높이값이 왼쪽 트리의 높이값이 1 보다 크고 새로 추가된 노드가 `10`으로 `9`보다 크기 때문에 역시, 오른쪽 자식 노드의 오른쪽에 추가가 되어 이때는 `8` 노드를 기준으로 왼쪽으로 회전합니다.

```
                       7
                  ┌────┴────┐
                  3         8
              ┌───┴───┐     └───┐
              2       5         9
          ┌───┘   ┌───┴───┐
          1       4       6
```

오른쪽 회전을 하면, 위와 같이 높이 차이가 다시 줄어 들었고 이어서 부모 `7` 노드는 왼쪽, 오른쪽 자식 트리의 높이 차이가 1 이기 때문에 회전을 하지 않고 로직이 종료됩니다. 이렇게 최종적으로 모든 노드의 오른쪽, 왼쪽 트리의 높이 차이가 1 이하인 AVL 트리를 만족합니다.

### insert(두번 회전)

```
                       7
                  ┌────┴────┐
                  2         8
              ┌───┴───┐     └───┐
              1       5         9
          ┌───┘   ┌───┴───┐
          0       4       6
```

이번에는 위 AVL 트리에서 `3` 노드가 추가 되었을때,

```
                       7
                  ┌────┴────┐
                  2         8
              ┌───┴───┐     └───┐
              1       5         9
          ┌───┘   ┌───┴───┐
          0       4       6
              ┌───┘
              3
```

`4` 노드, `5` 노드,`2` 노드는 왼쪽, 오른쪽 노드의 높이값 차이가 1이기 때문에 회전이 일어나지 않지만, `7` 노드 기준에서는 왼쪽 트리의 높이가 2 크게 됩니다.
높이 차이가 2가 되어 회전을 해야하는데, 아까와 다르게 이번엔 `7` 노드 기준으로 왼쪽 트리가 더 크지만 추가되는 `3` 노드가 왼쪽 자식 노드인 `2` 보다 커서 오른쪽 트리에 추가가 됩니다. 이렇게 트리와 높이 차이가 큰 방향과 해당 방향의 자식 노드에서 추가되는 노드의 방향이 다른 경우 2회의 회전이 필요합니다.

```
                       2
                  ┌────┴────┐
                  1         7
              ┌───┘     ┌───┴───┐
              0         5       8
                    ┌───┴───┐   └───┐
                    4       6       9
                ┌───┘
                3
```

만약 전과 같이 오른쪽 회전을 한번 진행한다면, 위와 같은 트리가 만들어집니다. 위 트리는 `2` 노드를 기준으로 왼쪽 트리와 오른쪽 트리의 높이 차이가 2가 되어 AVL 트리의 기준을 만족하지 못합니다.

```
                       7
                  ┌────┴────┐
                  2         8
              ┌───┴───┐     └───┐
              1       5         9
          ┌───┘   ┌───┴───┐
          0       4       6
              ┌───┘
              3
```

다시 돌아가서, 이렇게 `3` 노드가 추가된 경우 `7` 노드의 높이가 큰 자식 `2` 노드를 기준으로 왼쪽으로 회전을 한번 진행 한 후에, 다시 `7` 노드를 기준으로 오른쪽 회전을 진행해주어야 합니다. 우선, `2` 노드를 기준으로 왼쪽으로 회전을 한번 진행하면 아래와 같습니다.

```
                       7
                  ┌────┴────┐
                  5         8
              ┌───┴───┐     └───┐
              1       6         9
          ┌───┴───┐
          0       4
              ┌───┘
              3

```

다음으로 `7` 노드를 기준으로 다시 오른쪽 회전을 진행하면 아래와 같습니다.

```
                       5
                  ┌────┴────┐
                  1         7
              ┌───┴───┐ ┌───┴───┐
              0       4 6       8
                  ┌───┘         └───┐
                  3                 9
```

위와 같이 2번의 회전을 진행하면 다시 AVL 트리가 되는 것을 확인할 수 있습니다.

### 차이

한번 회전해야 할 때와 두번 회전할 때의 차이를 트리에서 확인해보면,

```
                       7
                  ┌────┴────┐
                 (5)        8
              ┌───┴───┐     └───┐
             (3)       6         9
          ┌───┴───┐
         (2)       4
      ┌───┘
      1
```

위와 같이 높이 차이 2가 발생한 노드 `5`와 `5`를 기준으로 높은 트리의 자식 `3`, 그리고 `3`을 기준으로 새로 추가된 노드(`1`)가 위치할 트리의 노드 `2`가 일자로 위치하면 한번의 회전을 진행합니다.

```
                      (7)
                  ┌────┴────┐
                 (2)        8
              ┌───┴───┐     └───┐
              1      (5)        9
          ┌───┘   ┌───┴───┐
          0       4       6
              ┌───┘
              3
```

반면, 위와 같이 높이 차이 2가 발생한 노드 `7`과 `7`을 기준으로 높은 트리의 자식 `2`, 그리고 `2`를 기준으로 새로 추가된 노드(`3`)이 위치할 트리의 노드 `5`가 일자가 아닌 꺾인 그림이면, 두번의 회전을 진행합니다.

### remove

```ts
class ExAVLTree<T> {

  ...

  remove(value: T): void {
    this.root = this.removeNode(this.root, value)
  }

  private removeNode(node: ExNode<T> | null, value: T): ExNode<T> | null {
    if (node === null) {
      return null
    }

    if (value < node.value) {
      node.left = this.removeNode(node.left, value)
    } else if (value > node.value) {
      node.right = this.removeNode(node.right, value)
    } else {
      if (node.left === null && node.right === null) {
        return null
      }

      if (node.left === null) {
        return node.right
      }
      if (node.right === null) {
        return node.left
      }

      const minNode = this.findMinNode(node.right)
      node.value = minNode!.value
      node.right = this.removeNode(node.right, minNode!.value)
    }

    node.height =
      1 + Math.max(this.getHeight(node.left), this.getHeight(node.right))

    const balance = this.getBalance(node)

    if (balance > 1 && this.getBalance(node.left!) >= 0) {
      return this.rotateRight(node)
    }

    if (balance > 1 && this.getBalance(node.left!) < 0) {
      node.left = this.rotateLeft(node.left!)
      return this.rotateRight(node)
    }

    if (balance < -1 && this.getBalance(node.right!) <= 0) {
      return this.rotateLeft(node)
    }

    if (balance < -1 && this.getBalance(node.right!) > 0) {
      node.right = this.rotateRight(node.right!)
      return this.rotateLeft(node)
    }

    return node
  }

  private findMinNode(node: ExNode<T>): ExNode<T> | null {
    while (node.left !== null) {
      node = node.left
    }
    return node
  }
}
```

노드를 제거하는 메서드는 앞서 이야기한 `이진 탐색 트리`의 삭제 메서드와 같이 `deleteByCopying`나 `deleteByMerging` 방법을 사용하여 제거하고 AVL 트리의 요건을 만족시키기 위해 부모를 타고 올라가며 트리의 좌우 높이를 비교한 후에 회전을 진행합니다.

하지만 `insert`에서의 회전과 약간의 차이가 있는데, `insert`에서는 회전이 발생한다면(그건 새로운 노드가 추가되어서) `insert` 된 방향의 노드가 2의 높이 차이가 발생해서 회전이 일어나는 것처럼, `remove`에서 회전이 발생하는 상황은 `remove` 반대 방향의 노드가 2의 높이 차이가 발생한다는 점입니다.

```
                       7
                  ┌────┴────┐
                  5         8
              ┌───┴───┐     └───┐
              3       6         9
          ┌───┴───┐
          2       4
```

만약 위와 같은 AVL 트리가 있고 `9` 노드를 제거한 다고 하면, `9` 노드는 자식이 없기 때문에 바로 제거가 됩니다. 그리고 부모로 타고 올라가며 왼쪽과 오른쪽의 트리의 높이를 확인하는데, `7` 노드에서 왼쪽 노드와 오른쪽 노드의 높이 차이가 2가 발생하게 됩니다.

```
                      (7)
                  ┌────┴────┐
                 (5)        8
              ┌───┴───┐
             (3)      6
          ┌───┴───┐
          2       4
```

위와 같이 높이 차이가 발생한 `7` 노드를 기준으로 트리의 높이가 높은 `5` 그리고 다시 트리의 높이가 높은 `3` 이렇게 세 노드가 일직선으로 있으니 `insert` 때와 동일하게 `7` 노드를 기준으로 오른쪽 회전을 진행합니다.

```
                       5
                  ┌────┴────┐
                  3         7
              ┌───┴───┐ ┌───┴───┐
              2       4 6       8
```

그러면 위와 같이 `9` 노드가 제거된 후에도 모든 노드의 높이 차이가 1 이하인 AVL 트리를 만족하는 것을 확인할 수 있습니다.

### 동작

```ts
const avl = new ExAVLTree<number>()
avl.insert(10)
avl.insert(20)
avl.insert(30)
avl.insert(40)
avl.insert(50)
avl.insert(25)

avl.preorderTraversal(avl.root, (value) => console.log(value))
// 30 20 10 25 40 50

avl.remove(40)
avl.preorderTraversal(avl.root, (value) => console.log(value))
// 30 20 10 25 50

avl.remove(30)
avl.preorderTraversal(avl.root, (value) => console.log(value))
// 20 10 50 25
```

[⬆목차](#list)

## 유니온 파인드(Union-Find)

Disjoin-set(서로소 집합)이라고도 부르는 자료 구로로 이름 그대로 Union - 서로 다른 두 개의 집합을 하나의 집합으로 합치거나, Find - 해당 노드가 어떤 집합에 속해있는지 확인할 수 있는 자료구조입니다.

#### Note.

> 내부적으로 배열이나 연결 리스트 또는 트리 등 다양하게 구현할 수 있지만 이 문서에서는 트리를 사용하여 구현합니다.

간략하게 아래와 같이 두 개의 집합(트리) A, B가 있다고 가정했을 때,  
(여기서 아래의 트리의 각각의 노드들은 부모의 노드를 알고 있고, 루트의 노드가 해당 트리의 대표 노드라고 가정합니다.)

```
             A                       B
            (5)                      6
        ┌────┴────┐             ┌────┼────┐
       (8)        7             9    1    3
    ┌───┴───┐
    2      (4)
```

Find에 대해 알아보면, `4` 를 Find 했을 때 `4`의 부모인 `8` 그리고 `8`의 부모인 `5` 그리고 `5` 노드는 부모 노드가 없으므로 대표 노드임으로 `5`를 응답해 줍니다.

다음으로 `A`와 `B`를 Union한다면, 위와 같이 Find 할때 걸리는 시간이 트리의 높이에 비례하기 때문에 Union할때는 높이가 작은 트리를 높은 트리에 합칩니다.

```
                     A
                     5
        ┌────────────┼────────────┐
        8            7            6
    ┌───┴───┐                ┌────┼────┐
    2       4                9    1    3
```

위와 같이 `B`의 부모를 `A`의 부모 노드인 `5`로 지정하면서 높이 값을 유지하며 합쳐줍니다.  
만약에 두 개의 집합을 합치는데 둘의 높이가 같다면, 합친 후 트리의 높이가 한 단계 높아집니다.

### 구현

```ts
class ExNode {
  parent: ExNode
  value: number
  rank: number

  constructor(value: number) {
    this.parent = this
    this.value = value
    this.rank = 0
  }
}

class ExUnionFind {
  nodes: Map<number, ExNode> = new Map()

  addNode(value: number) {
    this.nodes.set(value, new ExNode(value))
  }

  find(value: number) {
    const node = this.nodes.get(value)
    if (!node) return
    if (node.parent !== node) {
      node.parent = this.find(node.parent.value)
    }
    return node.parent
  }

  union(a: number, b: number) {
    const rootA = this.find(a)
    const rootB = this.find(b)
    if (rootA === rootB) return

    if (rootA.rank > rootB.rank) {
      rootB.parent = rootA
    } else if (rootB.rank > rootA.rank) {
      rootA.parent = rootB
    } else {
      rootB.parent = rootA
      rootA.rank += 1
    }
  }
}
```

위 내부의 트리에선 노드의 부모의 경로 보다는 루트 노드가 중요하기 때문에 find 메서드 안에 루트 노드를 찾으면 해당 루트 노드를 부모 노드로 설정해줘서 전체적으로 트리의 높이를 낮게 만들어 성능을 향상 시킬 수 있습니다.

## 동작

```ts
const unionFind = new ExUnionFind()

unionFind.addNode(1)
unionFind.addNode(2)
unionFind.addNode(3)
unionFind.addNode(4)

unionFind.union(1, 2)
unionFind.union(2, 3)

console.log(unionFind.find(1)) // 1 - rank 1
console.log(unionFind.find(2)) // 1 - rank 1
console.log(unionFind.find(3)) // 1 - rank 1
console.log(unionFind.find(4)) // 4 - rank 0
```

[⬆목차](#list)

## 그래프(Graph)

그래프는 정점(vertex)과 간선(edge)로 구성되어 있습니다.

```
        7
        │
        3 ─ 5 ─┐
        │      2 ─ 6
        4 ─ 1 ─┘
```

위와 같이 간단하게 그림으로 표현하면 `1`, `2`, `3`, `4`, `5`, `6`, `7` 라는 정점이 있고 `(7, 3)`, `(3, 5)`, `(5, 2)`, ... 등의 간선이 있습니다.

### 분지수(Degree)

그래프에서 정점에 인접한 정점의 개수를 분지수(Degree)라고 합니다. 위 예시 그래프에서 `3` 의 분지수는 `7`, `5`, `4` 로 3입니다.

### 경로(Path)

정점에서 정점으로의 경로(Path)를 구할때는 같은 정점을 두번 거치지 않습니다. 예를 들면 `7`과 `1`의 경로는 `7 - 3 - 5 - 2 - 1`, `7 - 3 - 4 - 1`로 2가지 경로가 있습니다.  
(`7 - 3 - 5 - 3 - 4 - 1`과 같이 같은 정점(`3`)을 두번 거칠 수 없습니다.)

### 사이클(Cycle)

위에 경로에서 보았던 것처럼 두 정점의 경로가 1개 이상인 경우 위 그래프는 사이클이 존재한다고 합니다. 반대로 사이클이 존재하지 않는 그래프는 모든 정점 간의 경로가 하나라는 말로 그는 트리 구조와 같습니다.

### 유형

- 무방향 그래프(Undirected Graph): 간선에 방향이 없는 그래프
- 방향 그래프(Directed Graph): 간선에 방향이 있는 그래프
- 가중치 그래프(Weighted Graph): 간선에 가중치(Weight)가 부여된 그래프

### 표현법(인접 행렬)

```
        7
        │
        3 ─ 5 ─┐
        │      2 ─ 6
        4 ─ 1 ─┘
```

위 그래프를 인접 행렬로 표현하면 아래와 같습니다.

```
    │ 0 1 0 1 0 0 0 │
    │ 1 0 0 0 1 1 0 │
    │ 0 0 0 1 1 0 1 │
G = │ 1 0 1 0 0 0 0 │
    │ 0 1 1 0 0 0 0 │
    │ 0 1 0 0 0 0 0 │
    │ 0 0 1 0 0 0 0 │
```

행과 열로 각각 정점의 값을 대입해서 두 정점에 엣지가(u, v) 존재하는지 확인하여 존재하면 `1` 존재하지 않는다면 `0` 을 입력합니다. 같은 정점 (1, 1), (2, 2), (3, 3), ... 은 통일성있게 정의해 줍니다. 위 예는 모두 `0`로 통일 하였습니다.

위 그래프는 `무방향 그래프`로 같은 정점을 가리키는 대각선을 기준으로 대칭을 이루지만, `단방향 그래프`라면 대칭이 아니게 되며, 모든 값이 `1` 또는 `0`으로 구성되어 있지만 `가중치 그래프` 라면 `1`이 아닌 가중치(Weight) 값을 사용합니다.

정점의 인접 여부를 확인함에 있어 $O(1)$의 시간만에 알 수 있어서 좋지만 대신 $O(n^2)$의 많은 메모리를 사용하는 단점이 있습니다.

### 표현법(인접 리스트)

```
        7
        │
        3 ─ 5 ─┐
        │      2 ─ 6
        4 ─ 1 ─┘
```

위 그래프를 인접 리스트로 표현하면 아래와 같습니다.

```
    [1] - (2) - (4)
    [2] - (5) - (1)
    [3] - (7) - (4) - (5)
G = [4] - (3) - (1)
    [5] - (3) - (2)
    [6] - (2)
    [7] - (3)
```

위와 같이 각 정점마다 연결 리스트를 가지고 있는데, 자신의 점점과 이어저있는 모든 인접한 정점을 연결 리스트로 표현합니다.

정점의 인접 여부를 확인함에 있어 $O(n)$의 시간이 소요되지만 메모리는 $O(n + m)$만큼을 사용합니다.  
(n = 정점의 수, m = 해당 정점의 간선의 수)

### 그래프의 순회

`이진 트리`의 탐색 방법처럼 그래프의 탐색 방법에는 `preorder`, `postorder` 방법이 있습니다.  
(`inorder`의 경우 이진 트리와 달리 하위 노드의 개수가 다양하여 잘 사용되지 않습니다.)

### 깊이 우선 탐색(DFS: Depth Firest Search)

```
        7
        │
        3 ─ 5 ─┐
        │      2 ─ 6
        4 ─ 1 ─┘
```

위와 같은 그래프가 있다고 가정하고, 작은 숫자를 먼저 탐색한다고 했을때, 정점 `7`을 시작으로 모든 정점을 DFS로 탐색한다면, 정점 `7`을 인접한 정점 `3`을 탐색하고 다음 인접한 `4`와 `5`중에 작은 숫자인 `4`를 탐색하고 이어서 `1`, 이어서 `2` 를 탐색하고 `5`와 `6`중 작은 숫자인 `5`를 탐색합니다.

`5`까지 탐색한 후 이제 인접한 `3`은 이미 탐색한 정점이고 더 이상 탐색할 정점이 없기 때문에 뒤로(`2`) 이동합니다. 다시 `2`에서 가지 않은 `6` 정점으로 탐색한 후 이제 `6` 은 더 이상 탐색할 정점이 없기 때문에 뒤로(`2`) 이동하고, 이후 `2`, `1`, `4`, `3`, `7` 까지 탐색하지 않은 노드가 없기 때문에 계속 뒤로 이동하고 마지막 `7`에서, 탐색을 시작한 정점에 다시 도착하였고 더 이상 탐색할 정점이 없을때, 탐색이 종료됩니다.

결과적으로 `7 - 3 - 4 - 1 - 2 - 5 - 6` 순으로 탐색이 됩니다.

### 너비 우선 탐색(BFS: Breadth First Search)

```
        7
        │
        3 ─ 5 ─┐
        │      2 ─ 6
        4 ─ 1 ─┘
```

쉬운 이해를 위해서, 위 그래프를 조금 다르게 그려보면 아래와 같이 그릴 수 있습니다.

```
                       7
                       │
                       3
                  ┌────┴────┐
                  4         5
                  │         │
                  1 ─────── 2
                            │
                            6
```

동일하게 정점 `7`으로 탐색을 시작한다고 했을때 `7`을 기준으로 레벨0, `3`은 레벨1, `4` 와 `5`는 레벨2, `1`과 `2`는 레벨3, `6`은 레벨4 이렇게 나누었을때, 각 레벨 안에서 먼저 탐색을 하는 방법을 너비 우선 탐색이라고 합니다.

위 예로 들면, `7`을 먼저 탐색하고 다음 레벨인 레벨1의 `3`을 탐색하고 더이상 레벨1의 정점이 없으므로 레벨2로 넘어가서 `4`와 `5`를 탐색하고, 다시 다음 레벨로 넘어가서 `1`과 `2`를 탐색합니다. 그리고 마지막으로 다음 레벨로 이동하여 `6`을 탐색하고 더 이상 같은 레벨의 정점도 다음 레벨의 정점도 없기 때문에 탐색이 종료됩니다.

결과적으로 `7 - 3 - 4 - 5 - 1 - 2 - 6` 순으로 탐색이 됩니다.

### 구현

예시 코드는 양방향 그래프입니다.

```ts
class ExGraph {
  private adjacencyList: Map<string, string[]>

  constructor() {
    this.adjacencyList = new Map()
  }

  addVertex(vertex: string): void {
    if (!this.adjacencyList.has(vertex)) {
      this.adjacencyList.set(vertex, [])
    }
  }

  addEdge(vertex1: string, vertex2: string): void {
    this.addVertex(vertex1)
    this.addVertex(vertex2)
    this.adjacencyList.get(vertex1)!.push(vertex2)
    this.adjacencyList.get(vertex2)!.push(vertex1)
  }

  dfs(start: string): void {
    const visited = new Set<string>()
    const stack: string[] = [start]

    while (stack.length > 0) {
      const vertex = stack.pop()!
      if (!visited.has(vertex)) {
        visited.add(vertex)
        console.log(vertex)
        for (const neighbor of this.adjacencyList.get(vertex)!) {
          if (!visited.has(neighbor)) {
            stack.push(neighbor)
          }
        }
      }
    }
  }

  bfs(start: string): void {
    const visited = new Set<string>()
    const queue: string[] = [start]

    while (queue.length > 0) {
      const vertex = queue.shift()!
      if (!visited.has(vertex)) {
        visited.add(vertex)
        console.log(vertex)
        for (const neighbor of this.adjacencyList.get(vertex)!) {
          if (!visited.has(neighbor)) {
            queue.push(neighbor)
          }
        }
      }
    }
  }

  printGraph(): void {
    for (let [vertex, edges] of this.adjacencyList.entries()) {
      console.log(`${vertex} -> ${edges.join(", ")}`)
    }
  }
}
```

### 동작

```ts
const graph = new ExGraph()
graph.addEdge("A", "B")
graph.addEdge("A", "C")
graph.addEdge("B", "D")
graph.addEdge("C", "E")
graph.addEdge("D", "E")
graph.addEdge("D", "F")
graph.addEdge("E", "F")

graph.printGraph()
// A -> B, C
// B -> A, D
// C -> A, E
// D -> B, E, F
// E -> C, D, F
// F -> D, E

graph.bfs("A")
// A B C D E F

graph.dfs("A")
// A C E F D B
```
