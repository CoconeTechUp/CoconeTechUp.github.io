---
layout: post
title: "JAVA Stream API"
comments: true
excerpt: "JAVA Stream API에 대해 알아볼까요?"
date: 2023-09-13
tags: [Java, Stream API, for-loop]
thumbnail: https://www.north-47.com/wp-content/uploads/2019/06/java8streams.png
---

안녕하세요!🙇‍♂️

Cocone M SYF 컴퍼니 스컹크1팀 서버 개발자입니다.

서버 개발을 진행하면서 적극적으로 사용했던 Java의 Stream API에 대해 정리해 보려고 합니다.

## 소개

Java Stream API는 Java 8에서 소개된 데이터 컬렉션 처리를 위한 강력한 도구입니다. 이후로 많은 개발자들이 사용하고 있습니다. Stream은 몇 줄의 코드로 데이터 컬렉션에 대한 복잡한 작업을 수행할 수 있습니다. 또한 Stream을 사용하기 위해서는 Java 1.8 이상 버전을 사용해야 합니다.

## Stream 사용 이점

### 1. 함수형 프로그래밍 스타일

Java에서 Stream을 사용하는 큰 이점 중 하나는 함수형 프로그래밍 스타일을 제공한다는 것입니다. Stream을 사용하면 원본 데이터 소스를 수정하지 않고 데이터 컬렉션에서 작업을 수행할 수 있습니다. 이를 통해 모듈화 및 재사용할 수 있는 코드를 작성할 수 있습니다. 명령형 대신 선언적 프로그래밍 스타일을 사용하므로 더 간결하고 읽기 쉬운 코드를 작성할 수 있습니다.

### 2. 효율성

Stream은 대규모 데이터 집합에 대한 작업을 더 효율적으로 수행할 수 있도록 해줍니다. 데이터 컬렉션을 여러 번 반복하는 대신 Stream을 사용하여 한 번에 작업을 수행할 수 있습니다. 이는 특히 대규모 데이터 집합에서 작업할 때 큰 성능 향상을 가져올 수 있습니다. 또한 Stream은 병렬 처리를 가능하게 하므로 멀티 코어 프로세서를 활용하여 성능을 더욱 향상하게 시킬 수 있습니다.

### 3. 간결성

Stream을 사용하면 기존의 반복문을 사용하여 수행해야 했던 많은 작업을 한 줄의 코드로 수행할 수 있습니다. 이는 코드를 더 읽기 쉽고 유지보수하기 쉽게 만들어 줍니다. Stream에서는 메소드 체이닝, 람다 표현식 및 함수형 인터페이스를 사용하여 표현적이고 간결하며 우아한 코드를 작성할 수 있습니다.

## Stream 사용 방법

Java에서 Stream을 사용하려면 먼저 데이터 컬렉션에서 Stream 객체를 생성해야 합니다. Stream 객체를 생성하면 필터링, 매핑 및 축소와 같은 다양한 작업을 수행할 수 있습니다.

### Stream 생성

Java에서는 몇 가지 방법으로 Stream을 생성할 수 있습니다. 컬렉션, 배열 또는 `Stream.of()` 메소드를 사용하여 Stream을 생성할 수 있습니다.

예를 들어, 정수 배열이 있다면 다음과 같은 방법으로 Stream을 생성할 수 있습니다.

```java
int[] numbers = {1, 2, 3, 4, 5};
IntStream stream = Arrays.stream(numbers);

```

### 필터링

필터링은 주어진 조건에 따라 Stream에서 요소를 선택하는 과정입니다. `filter()` 메소드는 주어진 조건에 따라 Stream의 요소를 필터링하는 데 사용됩니다.

예를 들어, 문자열 리스트가 있고 문자 "a"로 시작하는 모든 요소를 찾으려면 다음과 같이 `filter()` 메소드를 사용할 수 있습니다.

```java
List<String> fruits = Arrays.asList("apple", "banana", "apricot", "orange");
List<String> filteredFruits = fruits.stream()
                                    .filter(fruit -> fruit.startsWith("a"))
                                    .collect(Collectors.toList());
```

### 매핑

매핑은 Stream의 요소를 하나의 형식에서 다른 형식으로 변환하는 과정입니다. `map()` 메소드는 주어진 함수에 따라 Stream의 요소를 변환하는 데 사용됩니다.

예를 들어, 정수 리스트가 있고 각 요소의 제곱을 찾으려면 다음과 같이 `map()` 메소드를 사용할 수 있습니다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> squares = numbers.stream()
                                .map(number -> number * number)
                                .collect(Collectors.toList());
```

### 축소

축소는 Stream의 요소를 하나의 결과로 집계하는 과정입니다. `reduce()` 메소드는 주어진 함수에 따라 Stream의 요소를 집계하는 데 사용됩니다.

예를 들어, 정수 리스트가 있고 모든 요소의 합을 찾으려면 다음과 같이 `reduce()` 메소드를 사용할 수 있습니다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
                .reduce(0, (a, b) -> a + b);

```

## Stream 고급 기능

### FlatMap

`flatMap()` 메소드는 Stream의 Stream 요소를 하나의 Stream으로 평탄화하는 데 사용됩니다. 이는 중첩된 데이터 구조가 있을 때 내부 요소에 대한 작업을 수행할 때 유용합니다.

예를 들어, 리스트의 리스트가 있고 모든 리스트의 모든 요소의 합을 찾으려면 다음과 같이 `flatMap()` 메소드를 사용할 수 있습니다.

```java
List<List<Integer>> listOfLists = Arrays.asList(Arrays.asList(1, 2), Arrays.asList(3, 4), Arrays.asList(5, 6));
int sum = listOfLists.stream().flatMap(list -> list.stream()).reduce(0, (a, b) -> a + b);

```

### GroupingBy

`groupingBy()` 메소드는 주어진 기준에 따라 Stream의 요소를 그룹화하는 데 사용됩니다. 이는 특정 속성이나 속성에 따라 요소를 그룹화하려는 경우 유용합니다.

예를 들어, 사람 리스트가 있고 각 사람의 나이로 그룹화하려면 다음과 같이 `groupingBy()` 메소드를 사용할 수 있습니다.

```java
List<Person> people = Arrays.asList(new Person("Alice", 25), new Person("Bob", 30), new Person("Charlie", 25));
Map<Integer, List<Person>> groupedByAge = people.stream().collect(Collectors.groupingBy(Person::getAge));

```

### 병렬 처리

Stream은 병렬 처리를 가능하게 함으로써 멀티 코어 프로세서를 활용하여 작업을 더 빠르게 수행할 수 있습니다. 병렬 처리를 활성화하려면 `stream()` 메소드 대신 `parallelStream()` 메소드를 사용할 수 있습니다.

예를 들어, 정수 리스트가 있고 모든 짝수의 합을 찾으려면 다음과 같이 병렬 처리를 사용할 수 있습니다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
int sum = numbers.parallelStream().filter(n -> n % 2 == 0).mapToInt(Integer::intValue).sum();

```

## for-loop와 비교

일반적으로 Stream은 전통적인 for-loop에 비해 성능이 떨어지는 것으로 알려져 있습니다. 그래서 간단한 예제로 성능을 비교하도록 하겠습니다.

비교 환경은 아래와 같습니다.

```
CPU: Apple M1 Pro
java version: JDK 11.0.19
benchmark tool: jmh
반복횟수: 10번
```

### Primitive type 비교

```java
private int[] array = new int[1000000];

for (int i = 0; i < 1000000; i++) {
    array[i] = i;
}

// for-loop
int value = Integer.MIN_VALUE;
for (int i : array) {
    if (i > value) {
        value = i;
    }
}

// stream
Arrays.stream(array).reduce(Integer.MIN_VALUE, Math::max);
```

1,000,000개의 primitive type으로 1씩 증가하는 배열을 만들고 해당 배열의 최대값을 구하는 로직을 구하고 비교 결과 아래와 같이 나왔습니다.

```
Benchmark                         Mode  Cnt    Score    Error  Units
BenchmarkTest.forLoop             avgt   10    0.178 ±  0.001  ms/op
BenchmarkTest.stream              avgt   10    0.624 ±  0.001  ms/op
```

약 3.5배 정도 for-loop문이 성능이 나은 것을 나왔습니다.

### Reference Type 비교

```java
private List<Integer> list = new ArrayList<>();

for (int i = 0; i < 1000000; i++) {
    list.add(i);
}

// for-loop
int value = Integer.MIN_VALUE;
for (Integer integer : list) {
    if (integer > value) {
        value = integer;
    }
}

// stream
list.stream().reduce(Integer.MIN_VALUE, Math::max);
```

1,000,000개의 reference type으로 1씩 증가하는 배열을 만들고 해당 배열의 최대값을 구하는 로직을 구하고 비교 결과 아래와 같이 나왔습니다.

```
Benchmark                         Mode  Cnt    Score    Error  Units
BenchmarkTest.forLoop             avgt   10    1.177 ±  0.096  ms/op
BenchmarkTest.stream              avgt   10    3.247 ±  0.234  ms/op
```

약 2.7배 정도 for-loop문이 성능이 나은 것을 나왔습니다.

### 높은 계산 비용이 드는 로직 비교

```java
private int[] array = new int[1000000];
private List<Integer> list = new ArrayList<>();

for (int i = 0; i < 1000000; i++) {
    array[i] = i;
    list.add(i);
}


// array for-loop
double value = Double.MIN_VALUE;
for (int i : array) {
    double d = Sine.slowSin(i);
    if (d > value) {
        value = d;
    }
}

// array stream
Arrays.stream(array).mapToDouble(Sine::slowSin).reduce(Integer.MIN_VALUE, Math::max);

// list for-loop
double value = Double.MIN_VALUE;
for (Integer integer : list) {
    double d = Sine.slowSin(integer);
    if (d > value) {
        value = d;
    }
}

// list stream
list.stream().mapToDouble(Sine::slowSin).reduce(Integer.MIN_VALUE, Math::max);
```

slowSin()은 파라미터로 넘겨지는 메서드에 대해서 사인함수 값을 계산하고 이에 대한 테일러 급수를 계산하는 메소드로써 Apache의 commons-math3의 라이브러리를 사용했습니다.

결과는 아래와 같습니다.

```
Benchmark                         Mode  Cnt    Score    Error  Units
BenchmarkTest.arrayForLoop        avgt   10  228.070 ±  0.629  ms/op
BenchmarkTest.arrayStream         avgt   10  230.442 ±  1.526  ms/op
BenchmarkTest.listForLoop         avgt   10  230.662 ±  2.558  ms/op
BenchmarkTest.listStream          avgt   10  226.761 ±  2.158  ms/op
```

계산비용이 많이 발생하는 메소드를 사용할 시 오차범위에 거의 같은 성능을 나오는 것으로 결과가 나왔습니다.

### 성능차이

for-loop의 경우 오버헤드가 없는 단순한 인덱스 기반 메모리 접근이기 때문에 속도가 빠릅니다.
또한, stream은 2015년 이후에 도입된 반면 for-loop는 도입된지 40년이 넘는 시간이 지났기 때문에 컴파일러는 for-loop에 더 최적화가 잘 되어있을 수 밖에 없습니다.

### 어떤 것을 선택해야 할까요?

간단한 작업: 간단한 데이터 처리나 직관적인 코드가 필요한 경우에는 전통적인 for-loop를 선택할 수 있습니다.

복잡한 작업: 데이터 처리 과정이 복잡하고 중간 처리 단계가 많은 경우, 그리고 병렬 처리 또는 최적화가 필요한 경우에는 stream이 더 적합할 수 있습니다. stream은 간결하게 복잡한 작업을 처리하고 유지보수성을 높여줍니다.

## 결론

Java Stream API는 데이터 처리를 위한 강력한 도구로, 선언적 프로그래밍과 내부 반복을 통해 코드의 가독성과 유지보수성을 향상시키며, 병렬 처리와 최적화를 지원하여 성능을 향상시킵니다. 이를 적극적으로 활용하여 데이터 처리 작업을 더욱 효율적으로 수행할 수 있으니 극단적으로 성능을 요구하는 특이 상황이 아니라면 stream 사용을 적극적으로 사용하는 것도 좋다고 생각합니다.
