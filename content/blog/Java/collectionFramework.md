---
title: "[Java] Collection Framework"
date: 2022-01-08 16:21:13
category: 'Java'
draft: false
---

컬렉션 프레임 워크는 데이터 군을 저장하는 클래스를 표준화한 프레임워크로, 데이터를 저장하는 자료구조와 알고리즘을 클래스로 구현해놓은것입니다.
제네릭과 다형성을 이용한 객체지향 설계로 표준화 되어있어 사용법을 익히기도 편리하며, 재사용성이 높은 코드를 작성할 수 있다는 장점이 있습니다.

![image](https://user-images.githubusercontent.com/28651727/148642487-97e031a3-ed68-459b-921b-fef31b771cb0.png)

### 컬렉션 프레임워크 인터페이스
컬렉션 프레임 워크에는 대표적으로 다음의 3가지 인터페이스가 존재합니다.

- List
- Set
- Map

`List`와 `Set`을 구현한 컬렉션 클래스들은 공통점이 많아 공통된 부분을 `Collection`이라는 새로운 인터페이스로 정의하여 놓았습니다. 반면 `Map`을 구현한 클래스들은 전혀 다른 형태로 컬렉션을 다루기 때문에 `Collection`을 상속하지 않습니다.

| Interface | 특징                                       | 구현 클래스                             |
| ---------- | ------------------------------------------ | --------------------------------------- |
| List       | 순서유지 O, 중복 허용                      | ArrayList, LinkedList, Stack, Vector    |
| Set        | 순서유지 X, 중복 불가                      | HashSet, TreeSet                        |
| Map        | key-value데이터 집합, 키 중복 X, 값 중복 O | HashMap, TreeMap, HashTable, Properties |

컬렉션 프레임 워크의 구현클래스들은 인터페이스의 이름을 포함하는 명명법을 따르지만 Vector, Stack, HashTable, Properties와 같은 클래스들은 컬렉션 프레임워크 이전부터 존재하였던 것이기 때문에 컬렉션 프레임워크의 명명법을 따르지 않습니다.

자바 컬렉션 프레임워크를 사용할 때 List, Set, Map과 같은 인터페이스가 아닌 ArrayList, Vector, HashMap, HashTable과 같은 구현체로 선언할 때가 있는데 이는 객체의 결합도를 매우 강하게 유지하여 비효율적이며, 추상화에 의존해야한다는 SOLID의 DIP원칙을 위반합니다.

```java
import java.util.*;

class Main {
    public static void main(String[] args) {
        ArrayList<Integer> list1 = new ArrayList<>();   // Bad usecase
        List<Integer> list2 = new ArrayList<>();        // Good usecase
    }
}
```

`Vector`, `HashTable`은 Backward Compatibility를 위해 존재하는것으로 가능하면 사용하지 않는것을 권장합니다.

## Collection

`Collection`은 `List`와 `Set`의 조상 인터페이스로 컬렉션 클래스에 저장된 데이터를 조회하고 추가 삭제하는등의 기본적인 메서드들을 정의하고 있습니다.

| Type & Method                            | Description                                                 |
| ---------------------------------------- | ----------------------------------------------------------- |
| boolean add(E e)                         | 객체 e를 컬렉션에 추가한다.                                 |
| boolean addAll(Collection<? extends E> c | 객체 c의 모든 요소를 컬렉션에 추가한다.                     |
| void clear()                             | 컬렉션의 모든 요소를 삭제한다.                              |
| boolean contains(Object o)               | 컬렉션이 객체 o를 포함하고 있는지 확인합니다.               |
| boolean containsAll(Collection<?> c)     | 컬렉션 c의 모든 요소를 컬렉션이 포함하고있는지 확인합니다.  |
| boolean isEmpty()                        | 컬렉션이 비어있는지 확인합니다.                             |
| int size()                               | 컬렉션의 크기를 반환합니다.                                 |
| boolean remove(Object o)                 | 객체 o를 컬렉션에서 찾고, 있다면 삭제합니다.                |
| boolean removeAll(Collection<?> c)       | 컬렉션 c에 존재하는 요소를 컬렉션에서 찾아 모두 삭제합니다. |
| Object[] toArray()                       | 컬렉션을 배열로 변환합니다.                                 |
| int hashCode()                           | 컬렉션의 해시값을 반환합니다                                |
| equals(Object o)                         | 컬렉션 내부의 값을 비교한다.                                |
| Iterator\<E\> iterator()                 | 컬렉션의 이터레이터를 반환합니다.                           |

`contains`의 경우 구현에 따라 시간복잡도가 달라집니다. `ArrayList`의 경우 O(n)시간에 확인하며, `HashMap`의 경우 O(log n)의 시간에 확인합니다.

`equals`의 경우 내부의 값을 비교하는데, 값의 순서가 달라도 `false`를 반환합니다. 따라서 값의 포함 여부를 확인하고 싶으면 `contains`를 사용하면 됩니다.

```java
import java.util.*;

class Main {
    public static void main(String[] args) {
        List<Integer> list1 = new ArrayList<>();
        List<Integer> list2 = new ArrayList<>();
        List<Integer> list3 = new ArrayList<>();

        for(int i = 0; i < 10; i++) {
            list1.add(i);
            list2.add(i);
        }

        for(int i = 0; i < 8; i++) {
            list3.add(i);
        }

        list3.add(9);
        list3.add(8);

        System.out.println(list1.equals(list2));
        System.out.println(list1.equals(list3));
    }
}
```

## List Interface

`List`인터페이스는 중복을 허용하고 저장순서가 유지되는 자료구조를 가집니다. `Collection`인터페이스를 상속합니다.

순서가 유지되는 만큼 인덱스로 데이터에 접근할 수 있는 메서드들이 존재합니다.

| Type & Method                                 | Description                                       |
| --------------------------------------------- | ------------------------------------------------- |
| void add(int index, E element)                | index위치에 요소를 삽입합니다.                    |
| void addAll(int index,Collection c)           | index위치에 컬렉션c의 데이터들을 모두 삽입합니다. |
| int indexOf(Object o)                         | 객체 o의 index를 반환합니다.                      |
| boolean remove(int index)                     | index위치의 요소를 삭제합니다.                    |
| E set(int index, E element)                   | index 위치의 요소를 element로 대체하고 반환합니다 |
| void sort(Comparator c)                       | 비교자 c를 통해 컬렉션을 정렬합니다               |
| List\<E\> subList(int fromIndex, int toIndex) | fromIndex부터 toIndex에 있는 요소를 반환합니다    |

![image](https://user-images.githubusercontent.com/28651727/148644066-394cb4ef-bb7c-49af-8846-821eba448255.png)

다음의 구현 클래스를 가집니다.

- ArrayList
- LinkedList
- Vector
- Stack

### ArrayList vs LinkedList

`ArrayList`의 경우 저장공간이 부족하면, 현재사이즈 * 2만큼의 새로운 저장공간을 확보한 후 데이터를 추가합니다. 반면, `LinkedList`의 경우 저장공간을 따로 확보하는것이 아니라 새로운 노드를 생성하여 연결하는 방식입니다. 따라서 충분한 공간을 확보해 준다면 순차적인 추가/삭제는 `ArrayList`가 더 빠릅니다.

반면 중간의 데이터를 추가/삭제하는 경우 `ArrayList`는 모든 요소를 재배치해야 하기 때문에 `LinkedList`에 비해 속도가 느립니다.

`ArrayList`의 경우 사용하지 않는 공간도 할당 될 수 있기 때문에 메모리 사용이 비효율적이라는 단점이 있으며, `LinkedList`의 경우 각 요소들이 연결되어있는 형태이기 때문에 데이터가 많을수록 읽어오는 시간이 길어진다는 단점이 있습니다.

### Stack

![image](https://user-images.githubusercontent.com/28651727/148645619-68e6a897-58f3-440d-8f73-e8afc03b04dc.png)

`Stack`클래스는 `Vector`클래스를 상속하며, Last In First Out의 자료구조입니다. 

| Type & Method        | Description                                     |
| -------------------- | ----------------------------------------------- |
| boolean empty()      | 스택이 비어있는지 확인합니다                    |
| E peek()             | Top의 값을 반환합니다                           |
| E pop()              | Top의 값을 pop합니다                            |
| E push(E item)       | Top에 데이터 요소를 추가합니다                  |
| int search(Object o) | 가장 아래에서부터 객체의 위치를 찾아 반환합니다 |

```java
import java.util.Stack;

class Main {
    public static void main(String[] args) {
        Stack<String> animals= new Stack<>();

        animals.push("Dog");
        animals.push("Horse");
        animals.push("Cat");
        System.out.println("Stack: " + animals);

        // Search an element
        int position = animals.search("Horse");
        System.out.println("Position of Horse: " + position);
    }
}
```

```
Stack: [Dog, Horse, Cat]
Position of Horse: 2
```

## Queue Interface

`Queue`인터페이스는 `Collection`인터페이스를 상속하며 First In First Out의 자료구조입니다.

| Type & Method           | Description                                                                    |
| ----------------------- | ------------------------------------------------------------------------------ |
| boolean add(E e)        | e 요소를 tail에 추가합니다                                                     |
| Object peek()           | head의 값을 반환합니다                                                         |
| Object poll()           | head의 값을 반환하고 삭제합니다                                                |
| boolean offer(Object o) | 객체o를 저장하며, 성공여부를 반환합니다                                        |
| Object element()        | peek과 같은 역할이나, 큐가 비어있을 경우 NoSuchElementException을 발생시킵니다 |
| Object remove()         | poll과 같은 역할이나, 큐가 비어있을 경우 NoSuchElementException을 발생시킵니다 |

구현체는 다음과 같습니다.
- `ArrayDequeue`
- `LinkedList`
- `PriorityQueue`
  - 저장한 순서에 관계없이 우선순위가 높은 것부터 꺼내는 형태로, 힙 자료구조 형태로 저장합니다. 

이외에도 `Queue`인터페이스를 상속하는 다음과 같은 인터페이스들이 있습니다
- Deque
  - 기존의 큐와 달리 앞뒤로 삽입 삭제를 할 수 있습니다.
- BlockingQueue
- BlockingDeque

## Set Interface

`List`인터페이스와 동일하게 `Set`인터페이스도 `Collection`인터페이스를 상속합니다. 하지만 중복 요소를 허용하지 않으며, 저장순서 또한 유지되지 않습니다.

- HashSet
  - `HashSet`은 Set인터페이스를 구현한 가장 대표적인 컬렉션입니다. `add`나 `addAll`메서드를 통해 새로운 요소를 추가하며, 중복된 요소를 허용하지 않기 위해 `equals`를 호출하여 중복된 요소가 컬렉션 내에 존재하는지 확인합니다. 
  - `HashSet`은 저장된 순서를 유지하지 않기 때문에, 저장한 순서를 유지하고자 한다면, `LinkedHashSet`을 사용하여 저장순서를 유지할 수 있습니다. 

- TreeSet
  - 이진 탐색 트리의 일종인 레드-블랙트리로 구현된 컬렉션 클래스 입니다. 

## Map Interface

`Map`은 key-value의 형태로 요소를 저장하는 인터페이스입니다. 중복된 key를 허용하지 않고, 각 키는 하나의 값에 대응됩니다.

| Type & Method                       | Description                                           |
| ----------------------------------- | ----------------------------------------------------- |
| void clear()                        | 모든 mapping을 삭제합니다                             |
| boolean containsKey(Object key)     | 해당하는 key가 존재하는지 확인합니다                  |
| boolean containsValue(Object value) | 해당하는 value가 존재하는지 확인합니다                |
| Set<Map.Entry<K,V>> enrtySet        | 저장된 key-value를 key-value형태의 Set으로 반환합니다 |
| Set<K> keySet()                     | 저장된 key들의 set을 반환합니다.                      |
| V put(K key, V value)               | key-value를 추가합니다                                |
| void putAll(Map<K,V> m)             | map의 모든 key-value를 추가합니다                     |
| V	remove(Object key)                | 키값에 대응되는 key-value가 존재할 경우 삭제합니다    |
| V get(Object key)                   | 키값에 대응되는 값을 반환합니다                       |
| int size()                          | key-value쌍의 개수를 반환합니다.                      |
| Collection<V>	values()              | map의 모든 value를 반환합니다.                        |

`Map`은 다음의 5개 구현 클래스를 가집니다

- HashMap
  - 해싱을 사용하여 많은 양의 데이터를 검색하는데 좋은 성능을 보입니다.
- EnumMap
- LinkedHashMap
- WeakHashMap
- TreeMap
  - 이진 탐색트리의 형태로 탐색과 정렬에 적합한 클래스입니다.

`Map`은 다음과 같이 3개의 확장 인터페이스를 가집니다.

- SortedMap
- NavigableMap
- ConcurrentMap


## Reference

- Java의 정석
- https://docs.oracle.com/javase/7/docs/api/
- https://www.programiz.com/java-programming
- https://www.scaler.com/topics/java/collections-in-java/
