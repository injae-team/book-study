# 생성자 대신 정적 팩터리 메서드를 고려하라

클래스의 인스턴스를 반환하는 기법으로 정적 팩터리 메서드라는게 있다. 다음은 그런 기법이 적용 된 Boolean의 valueOf 메서드이다.

```java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE
}
```



## 정적 팩터리 메서드의 장점

정적 팩터리 메서드를 구현함으로써 다음 <ins>5가지의 장점</ins>을 얻을 수 있다.

1. **이름을 가질 수 있다.**
   생성자와는 다르게 반환하는 객체의 특성을 메서드 이름에 나타낼 수 있다. 소수를 반환하는 메서드인 `BigInteger.probablePrime`이 그 예시다.
2. **호출 될 때마다 인스턴스를 새로 생성하지 않아도 된다.**
   필요에 따라 인스턴스를 미리 만들어놓거나 캐싱하여 인스턴스를 재사용할 수 있다. 생성 비용이 큰 객체를 자주 요청해야 되는 상황에 사용하면 성능을 개선할 수 있다.
   또한 인스턴스의 <ins>생명주기를 통제</ins>할 수 있다. 인스턴스를 싱글턴으로 만들수도, 인스턴스화가 불가하도록 만들수도 있다.
3. **반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.**
   메서드를 통해 자식 타입의 객체를 반환할 수 있다. 이는 인터페이스를 반환 타입으로 사용하는 인터페이스 기반 프레임워크의 핵심이다. 예를 들어 `Collections`이라는 단 하나의 클래스에서 메서드 호출에 따라 수십가지 종류의 구현체를 얻을 수 있다.
4. **입력 변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.**
   예를 들어 `EnumSet`은 원소가 64개 이하이면 `RegularEnumSet`을, 65개 이상이면 `JumboEnumSet`을 반환한다. 클라이언트 입장에서는 팩터리가 반환하는 인스턴스가 어떤 클래스인지 알 필요도 없다.
5. **정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.**
   @TODO@잘 이해 안되므로 추후 찾아볼것



## 정적 팩터리 메서드의 단점

대신 아래 <ins>2가지 단점</ins>이 발생한다.

1. **상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.**
   컬렉션 프레임워크에서는 `Collections`를 통해서만 구현 불가나 동기화 기능 등을 덧붙인 유틸리티 구현체를 얻을 수 있다. 이런 구현체들을 상속할 수 없으며, 이는 상속보다 컴포지션을 사용하도록 유도한다.
2. **정적 팩터리 메서드는 프로그래머가 찾기 어렵다.**
   생성자처럼 API 설명에 드러나지 않으니 사용자가 클래스를 인스턴스화 할 방법을 알아내야 한다. 현재 이러한 단점을 해결하기 위해 메서드 명 정의 시 명명 규칙을 사용한다.



### 정적 팩터리 메서드 명명 규칙

- **from**: 하나의 매개변수를 받아 인스턴스를 반환하는 형 변환 메서드
`Date d = Date.from(instant);`
- **of**: 여러 매개변수를 받아 적합한 인스턴스를 반환하는 집계 메서드
`Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);`
- **valueOf**: from과 of의 더 자세한 버전
`BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);` 
- **instance/getInstance**: 인스턴스를 반환한다(생성 or 재사용 여부 불명).
`StackWalker luke = StackWalker.getInstance(options);`
- **create/newInstance**: 인스턴스를 매 번 새로 생성하여 반환한다.
`Object newArray = Array.newInstance(classObject, arrayLen);`
- **get*Type***: getInstance와 같으나, *Type*을 반환한다.
`FileStore fs = Files.getFileStore(path);`
- **new*Type***: newInstance와 같으나, *Type*을 반환한다.
`BufferedReader br = Files.newBufferedReader(path);`
- **type**: get*Type*과 new*Type*의 간결한 버전.
`List<Complaint> litany = Collections.list(legacyLitany);`



> 정적 팩터리 메서드와 public 생성자는 각자 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다. 그렇다고 하더라도 정적 팩터리를 사용하는게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.