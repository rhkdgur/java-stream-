# java-stream

## java Stream 개념과 함수 정리

<Stream 이란><br/>
: 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소

1) 연속된 요소 <br>
: Collection 과 마찬가지로, Stream은 특성 요소 형식으로 이루어진 연속된 값 집합 인터페이스를 제공 <br>
: Collection의 경우 시간과 공간의 복잡성과 관련된 요소의 저장 및 접근 연산이 주를 이루고, Stream의 경우에는 filter,sorted,map과 같은 데이터를 처리하게 위해 계산식이 주로 이룸 <br>

2) 소스
: Stream은 Collection,배열 ,I/O 자원등의 데이터 제공 소스로 부터 테이터를 소비합니다.<br>

3)데이터 처리 연산
: Stream은 filter,map,reduce,find,amtch,sort 등의 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 데이터베이스와 비슷한 연산을 지원<br>

>1.Stream 과 Collection의 차이
>>데이터 계산 시점
>>>Collection의 경우 현재 자료구조(LinkedList,ArrayList)가 포함하는 모든 데이터를 메모리에 저장하는 자료구조. 즉, Collection에 포함될 각 요소들은 포함 되기 전에 계산이 완료되어야함
>>>반면에 Stream의 경우에는 요청할 때만 요소를 계산하는 고정된 자료구조

>2.외부반복과 내부반복
```c
  List<String> names = new ArrayList<>();
  for(Dish dish : menu){
    names.add(dish.getName());
  }
```
: 외부반복의 예제이다.<br>
-> Collection 인터페이스를 사용하기 위해서는 사용자가 직접 for-each를 통해 반복문을 만들어 연산처리를 작성해야한다.<br>
반면,<br>
-> Stream의 경우에는 반복 연산을 알아서 처리하고, 결과 Stream 값을 어딘가에 저장해주는 내부반복을 사용한다.<br>
> 내부반복의 이점
>> 반복문을 사용할 필요가 없다.
>>> 내부에서 자동으로 반복 연산을 처리해주기 때문에 사용자가 직접 처리할 필요가 없음
>> 병렬성 처리의 이점
>>> 외부 반복을 사용하는 경우 , 병렬처리를 위해서는 스레드간의 공유자원에 대해 동기화(한 스레드만 공유자원에 접근하는 코드를 실행하도록)를 처리해주어야 하지만, 내부반복을 사용한다면 
이를 관리할 필요가 없다.

<stream 사용 예제><br>
```C
  	List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(1);
		list.add(2);
		list.add(4);
		list.add(4);
		list.add(3);
    
    결과:
   [1, 1, 2, 4, 4, 3]
```
1.filter 필터링<br>
:filter 연산은 프리디케이트(boolean을 반환하는 함수)를 인수로 받아 true를 반환하는 모든 요소를 포함하는 스트림을 반환 한다.<br>
```C
    List<Integer> result = list.stream().filter(i -> i > 3).collect(Collectors.toList());
    
    결과:
   [4, 4]
```
:filter(조건문) -> 각 요소 i가 3보다 큰 요소를 찾아 반환하는 예제이다.<br>

2.distinct<br>
```C
   List<Integer> result2 = list.stream().distinct().filter(x -> x > 3).collect(Collectors.toList());
   
   결과 : 
   [4]
```
:distinct의 경우 stream안의 중복된 요소를 제거하는 연산이다. 중복된 요소에 대해 고유하게 만들어준다. 이와 같은 역할을 하는 함수가 toSet()이다.<br>
번외) toSet() 처리 코드<br>
```C
  Set<Integer> result3 =  list.stream().filter(x -> x > 3).collect(Collectors.toSet());
  결과 : 
  [4]
```
3.map(매핑)<br>
```C
   List<String> result4 = list.stream().map(x -> Integer.toString(x*2)).collect(Collectors.toList());
   // 
   결과:
   [2, 2, 4, 8, 8, 6]
```
: map 연산은 Stream을 새로운 Stream으로 변환하는 연산을 합니다.(즉, 요소(item)을 변경하여 새로운 컨텐츠를 생성하는 기능)<br>
위 예제에서 Integer 형태 List를 String형 List로 변환 과 함께, 곱하기 2를 통해 값에 변화를 주었다.<br>

<검색, 매칭>
4.anyMatch<br>
```C
   	boolean b = list.stream().anyMatch(x -> x > 4);		
		boolean a = list.stream().anyMatch(x -> x <= 4);

    결과:
    false
    true
```
: anyMatch는 인수로 전달받은 boolean형태가 true를 반환하는 요소가 하나라도 있을 경우 true 값을 반환<br>

5.noneMatch<br>
```C
  		boolean b = list.stream().noneMatch(x -> x > 4);	
		  boolean a = list.stream().noneMatch(x -> x <= 4);
    결과:
    true
    false
```
:noneMatch는 인수로 전달받은 boolean형태가 true를 반환하는 요소가 존재하지 않을 경우 true 값을 반환한다.<br>
:allMatch는 noneMatch와 반대라고 보면 된다.<br>

6.findFirst<br>
```C
    int i = list.stream().findFirst().get();
    
    결과 : 1
```
:findFirst의 경우 스트림에서 첫번째 요소의 값을 찾아 반환한다.<br>
:이 연산의 경우 optional을 반환한다. Stream은 여러가지 연산을 거칠 수 있으며 연산의 결과로 Stream에 한개의 요소도 포함이 되어 있지 않을 수 있다.<br>
:따라서 Optional을 통해 null관련 에러를 피해준다.<br>

<리듀싱><br>
7.reduce
```C
    int sum = list.stream().reduce(0, (x, y)->{ System.out.println("x : "+x+" y : "+ y); return x+y;});
    
    결과:
    x : 0 y : 1
    x : 1 y : 1
    x : 2 y : 2
    x : 4 y : 4
    x : 8 y : 4
    x : 12 y : 3
    x+y = 15
```
:리듀싱은 모든 스트림의 요소를 처리하여 하나의 값으로 도출한다. 즉, 스트림의 요소가 하나의 값으로 줄어들 때까지 람다는 각 요소를 반복하여 연산 및 조합 진행한다.<br>

>반복연산 대신 reduce를 사용하는 이유
>>반복 연산을 통해 위와 같은 작업을 처리한 경우, 병렬적으로 처리하기 위해선느 동기화 작업이 필요. 동기화를 하더라도 결국 병렬화로 얻는 이득이 스레드간의 소모적 경쟁때문에
>>상쇄되어버린다. 반면 reduce를 사용하면 병렬작업을 편리하게 이용 할 수 있다.
>reduce를 병렬작업을 처리할 경우 제한사항을 만족해야함
>>1)람다에서 사용되는 변수값이 외부의 코드를 통해 바뀌면 안됨
>>2)결과 값이 연산 순서에 영향을 받으면 안됨
