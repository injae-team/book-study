# 생성자 대신 정적 팩터리 메서드를 고려하라

public 생성자 대신 정적 팩토리 메서드로 클래스의 인스턴스를 얻을 수 있음

ex)
```java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE
}
```

## 정적 팩토리 메서드의 장점 5가지

1. **이름을 가질 수 있음**
   <br>생성자 `BigInteger(int, int, Random)` 보다 `BigInteger.probablePrime` 가 의미를 더 잘 설명함<br>
2. **호출 될 때마다 인스턴스를 새로 생성하지 않아도 됨**
   <br>인스턴스를 미리 만들어놓거나 캐싱하여 인스턴스를 재사용할 수 있음, 이러한 인스턴스 통제를 통해 싱글턴이나 인스턴스 불가로 만들 수 있음
3. **반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있음**
   <br>반환할 객체의 클래스를 자유롭게 선택 할 수있는데 이는 유연성을 제공함, 이는 인터페이스 기반 프레임 워크의 핵심 기술이 됨 ex) `Collections` 하나로 45개 구현체를 제공함
4. **입력 변수에 따라 매번 다른 클래스의 객체를 반환할 수 있음**
   <br>`EnumSet` 클래스는 원소가 64개 이하이면 `RegularEnumSet`을, 65개 이상이면 `JumboEnumSet`을 반환함.
5. **정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됨.**
   <br>서비스 제공자 프레임워크를 만다는 근간이 됨 ex) JDBC 유연성 관련 내용인거 같은데 모르겠음

## 정적 팩토리 메서드의 단점 2가지

1. **상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없음**
   <br>컬렉션 프레임워크의 구현 클래스들은 상속할수 없음, 상속보다 컴포지션을 사용하도록 유도하고 불변타입으로 만들려면 이 제약을 지켜야 함
2. **정적 팩터리 메서드는 프로그래머가 찾기 어려움**
   <br>생성자처럼 API 설명에 드러나지 않아 사용자가 정적 팩토리 메서드 방식 클래스를 인스턴스화 할 방법을 알아내야 함, API 문서를 잘 작성하고 메서드 이름을 규약에 따라 잘 짓는 식으로 문제를 완화해야 함
   
### 정적 팩터리 메서드 명명 규칙

- **from**: 하나의 매개변수를 받아 인스턴스를 반환하는 형 변환 메서드.
  <br>`Date d = Date.from(instant);`
- **of**: 여러 매개변수를 받아 적합한 인스턴스를 반환하는 집계 메서드.
  <br>`Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);`
- **valueOf**: from과 of의 더 자세한 버전.
  <br>`BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);`
- **instance/getInstance**: 인스턴스를 반환한다(생성 or 재사용 여부 불명).
  <br>`StackWalker luke = StackWalker.getInstance(options);`
- **create/newInstance**: 인스턴스를 매 번 새로 생성하여 반환한다.
  <br>`Object newArray = Array.newInstance(classObject, arrayLen);`
- **get*Type***: getInstance와 같으나, *Type*을 반환한다.
  <br>`FileStore fs = Files.getFileStore(path);`
- **new*Type***: newInstance와 같으나, *Type*을 반환한다.
  <br>`BufferedReader br = Files.newBufferedReader(path);`
- **type**: get*Type*과 new*Type*의 간결한 버전.
  <br>`List<Complaint> litany = Collections.list(legacyLitany);`

> 정적 팩터리 메서드 / public 생성자는 각자 쓰임새가 있으니 장단점을 이해하고 사용하는 것이 좋음.
> 정적 팩터리를 사용하는게 유리한 경우가 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.


# 생성자에 매개변수가 많다면 빌더를 고려하라
