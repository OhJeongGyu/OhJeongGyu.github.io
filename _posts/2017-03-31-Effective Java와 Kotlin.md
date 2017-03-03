# Effective Java와 Kotlin

### 2017. 3. 1

---

Java의 유명 서적인 [Effective Java](https://www.pearsonhighered.com/program/Bloch-Effective-Java-2nd-Edition/PGM310651.html)
가 ```Jetbrain```의 **kotlin** 설계에 어떻게 영향을 주었는 지 정리하는 글을 번역&정리 하는 글임

다음 문서를 참고하여 작성하였음 [part1](https://medium.com/@lukleDev/how-effective-java-may-have-influenced-the-design-of-kotlin-part-1-45fd64c2f974#.uueg9fwrg) [part2](https://medium.com/@lukleDev/how-effective-java-may-have-influenced-the-design-of-kotlin-part-2-89844d62ddf3#.kjhlca7pt)

##  Builder Pattern

### Effective Java 2장 규칙2
> 객체를 생성하는 다양한 방법이 있으며, 그 중 객체 내에 필드가 선택적인 경우에 [Builder Pattern](https://ko.wikipedia.org/wiki/%EB%B9%8C%EB%8D%94_%ED%8C%A8%ED%84%B4)을 사용하라. 

### Kotlin 
코틀린에서는 Java에서의 빌더패턴을 사용할 필요가 없다. 클래스를 정의하면서 동시에 ```default``` 파라미터를 설정할 수 있다.

```
class User ( 
	private val name :String,
	private val age :Int = 0,
	private val address :String,
	private val phone :Int)
```
위와 같이 클래스를 정의하고,

```
val user = User("Jeonggyu", 
				28,
				phone = 86343623)

```
와 같이 ***선택적으로*** 필요한 필드에 필드 네임을 지정하여 값을 초기화할 수 있다. 

## Singletons 

### Effective Java 2장 규칙3
> 싱글톤 객체를 생성하는 방법은 두 가지다. ```static``` 필드로 자기 자신의 객체를 갖는 방법, ```public static``` 팩토리 메서드를 사용하는 방법.

첫 번째 방법은 다음과 같다.

```
public class Elvis {
	public static final Elvis INSTANCE = new Elvis():        
	private Elvis() { ...}
	publiC foo();
	public bar();
	...
}

```
위와 같은 방법에서는 Elvis.INSTANCE와 같은 방법으로 싱글톤 객체에 접근할 수 있다.

두 번째 방법은 

```
public class Elvis {
	private static final Elvis INSTANCE = new Elvis(); 
	private Elvis() { ...}
	public static Elvis getlnstance() { return INSTANCE; }
	public foo();
	public bar();

}
```
와 같다. 첫 번째 방법에서는 ```public```으로 선언된 객체에 직접 접근하는 것과 다르게 ```public static``` 메서드를 통해 싱글톤 객체를 얻을 수 있다.

### Kotlin 
코틀린에서는 Java에서의 싱글톤 객체를 만들 필요가 없다.

```
object Elvis {
	fun foo(){ }
	fun bar(){ }
}
```
```object``` 선언을 통해 싱글톤 객체를 손쉽게 만들 수 있다.

## equals(), hashCode()

### Effective Java 3장 규칙8, 9
> equals()를 정의할 때 일반 규약을 따라라. 또한 equals()를 정의할 땐 hashCode()도 함께 정의하라.


### Effective Java 4장 규칙15
> 변경 가능성을 최소화하라. 변경 가능 클래스로 만들 특별한 이유가 없다면 변경 불가능하도록 작성하라. 변경 불가능은 설계하기 쉽고, 구현하기도 쉽고, 사용하기도 쉽다. 또한 오류도 적고 안전하다.

### Java - 변경 불가능 클래스 
Java에서의 불가능 클래스는 지루하다. 불가능 클래스는 ```equals()```와 ```hashCode()```를 재정의 해야한다. 이러한 메서드를 재정의할 때 규칙 8과 9에서 설명하는 ```일반적인 규약```을 따라야한다. 이러한 규약은 **reflexivity, symmetry, transitivity, consistency and nun-nullity**이다. 규약들을 모두 지키기엔 자원이 너무 크다.

### Kotlin 
코틀린에서는 ```data``` 클래스를 정의하여 변경불가능 클래스를 만들 수 있다. 컴파일러는 자동으로 ```equals()```와 ```hashCode()```를 자동으로 생성한다.  클래스의 앞에 ```data```라는 키워드를 붙이면 끝이다. 


## 변수(Field) 대신 프로퍼티(Property)
### Effective Java 4장 규칙14
> ```public``` 클래스 안에는 ```public``` 객체를 두지 말고 접근자를 사용하라. 이 규칙을 지키지 않는다면 캡슐화와 유연성을 잃게 된다.

### Java의 Getter/Setter
이 규칙을 따른다면 ```private```필드와 ```public``` 접근자를 가질 것이다. 하지만, 이러한 기본 접근자는 클래스 내의 필드가 바뀐다면 모든 접근자 또한 바꿔야한다. 만약 해당 클래스가 공개 API라면 내부 필드를 변경하기는 더욱 힘들다.

### Kotlin
코틀린에서는 Java의 ```private``` 필드 접근자 ```getter/setter```를 자동으로 생성하는 프로퍼티를 사용한다. 

```
class Person {
	var name :string? = null
	var age :Int? = null
}

...

fun foo(){
	var person = Person()
	person.name = "Jeonggyu"
	...
}
...
```
위와 같이 Java의 ```public``` 객체에 접근하는 방법으로 접근할 수 있다. 코틀린의 프로퍼티를 통해 추가적인 커스텀 접근자를 만들 수 있다. 

## override의 선택적 annotation 대신 강제 키워드!
### Effective Java 6장 규칙36
> 상위 클래스의 메서드를 재정의 할 때는 Override 어노테이션을 일관되게 사용하라. 

### Java에서는 선택적인 어노테이션
Java에서 어노테이션은 실제로 오버라이딩을 하지 않는데 어노테이션을 붙였을 때 잘못된 어노테이션이라 표시를 해주는 등 필수적인 기능이지만 선택적으로 사용이 가능하다. 

### Kotlin에서는 강제 키워드
코틀린에서 override는 어노테이션이 아니다. 메서드 앞에 ```keyword```로 붙여줘야 한다. 

```
open class Parent {
    open fun foo() { }
}
class Child : Parent {
    override fun foo() { }
}
```

상속을 받는 클래스인 Child에서 Parent의 메서드인 ```foo()```를 재정의 하기 위하여 ```override``` 키워드를  붙여야한다. 


## Final classes by default

### Effective Java 4장 규칙17
> 모든 클래스는 상속을 받을 수 없어야 한다. 혹은 상속을 위해 신중히 설계되고, 문서화되야 한다. 

### Java에서의 클래스 
Java에서의 클래스는 기본적으로 상속받을 수 있는 클래스이다. 상속을 금지하기 위해선 ```final``` 키워드를 붙여줘야 한다. 

### Kotlin
코틀린에서의 클래스는 ```default```가 ```final``` 클래스이다. 상속을 위해선 ```open``` 키워드를 붙여야 한다. 

## No Checked Exceptions
### Effective Java 9장 규칙65
> 예외를 무시하지 마라. 

### Java에서의 예외
- [코틀린 문서](https://kotlinlang.org/docs/reference/exceptions.html#checked-exceptions)에서 말하는 Java 예외 처리의 문제점 
절대로 발생하지 않는 Checked Exception도 모두 처리해야 한다. 

### 코틀린에서는 Checked Exception이 없다! 
코틀린에서 발생할 수 있는 예외를 알리기 위해서 ```@Throws(<ExceptionName>::class)```태그를 이용하여 발생할 수 있는 예외를 알릴 수 있다. 


## Forced Null-Checks

### Effective Java 7장 규칙43
> Null 대신 빈 배열이나 컬렉션을 반환하라.

### Java 
Java에서는 어떤 메서드가 ```null```을 리턴 하는지 안하는 지 알 수가 없다. (자바독에서 설명을 해 준다면 운이 좋은 경우이다!) 

***개발자가 실수할 수 있는 두 가지***

- ```null``` 체크를 하지 않는 경우
- 절대로 ```null```을 리턴하지 않는 메서드에 필요하지 않은 ```null``` 체크를 하는 경우

### Kotlin
리턴 타입 ```List<Item>```은 절대로 ```null```을 리턴하지 않는다. 반면에 ```List<Item>?```은 ```null```을 리턴할 수 있다. 이렇게 강한 타입 시스템을 통해 개발자가 실수하는 것을 방지한다. 

## No Raw Type 
### Effective Java 5장 규칙23
> 새 코드에는 무인자 제네릭 자료형을 사용하지 마라.

## Easier way of defining variance
### Effective Java 5장 규칙28
> 한정적 와일드카드를 써서 API의 유연성을 높여라

### Java
Java에서의 한정적 와일드카드 ```<? extends E> ``` 와 ```<? super E>``` 같은 키워드들은 이해하기 힘들다. 

### Kotlin
```in```과 ```out``` 키워드를 사용한다. 





