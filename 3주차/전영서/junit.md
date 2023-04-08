JUnit


# 단위 테스트 프레임워크 이해하기    
- 단위 테스트는 다른 모든 단위 테스트들과 독립적으로 실행되어야 한다.   
- 프레임워크는 테스트 각각의 오류를 식별하고 보고해야 한다.   
- 어떤 테스트를 실행할지 선택하기 쉬워야 한다.   

# JUnit의 설계 목표
- 유용한 테스트를 작성하는 데 보탬이 되어야 한다.   
- 시간이 지나도 가치가 변하지 않는 테스트를 작성하는 데 보탬이 되어야 한다.   
- 코드 재사용을 통해 테스트 작성 비용을 낮추는 데 보탬이 되어야 한다.   

💁 간단한 예시
```
public class CalculatorTest {   
    @Test   
    public void testAdd() {   
        Calculator calculator = new Calculator();   
        double result = calculator.add(10, 50);   
        assertEquals(60, result, 0);   
    }   
}
```
< 설명 >    
1. 반드시 public 이어야 한다.   
2. @Test 어노테이션을 사용하여 이 메서드가 단위 테스트 메서드임을 표시한다.   
3. Calculator 클래스의 인스턴스를 생성 -- 객체 형태   
4. assertEquals 메서드를 호출해 테스트 결과를 확인한다.   
5. 반환형은 void이어야 하고, 파라미터를 받아서는 안된다.   
   
# Junit assert 예시   
- assertArrayEquals("message", A,B)    
배열 A와 B가 일치함을 확인   
- assertEquals("message", A,B)   
객체 A와 B가 일치함을 확인. equals()   
- assertSame("message", A,B)   
객체 A와 B가 같은 객체임을 확인한다. ==   
- assertTrue("message",A)   
조건 A가 참인지 확인   
assertNotNull("message",A)   
객체 A가 null이 아님을 확인   
      
# JUNIT 자세히 살펴보기   
@Before @After 어노테이션이 부여된 메서드들은 매 @Test 메서드가 호출되기 바로 직전과 직후에 실행된다. @Test 메서드의 성공 실패 여부는 상관없다.    
때문에 필요한 도메인 객체를 미리 생성해두거나, 특정 상태로 미리 셋팅하기 위한 공통 코드를 뽑아두기에 최적의 장소이다!!   
   
# 가장 널리 쓰이는 Hamcrest 매처   
anything - 무엇이든 상관없이 모든 것을 가리킴. assert문의 가독성을 높이고 싶을 때 유용   
is - 문장 가독성 향상 목적으로만 사용됨   
allOf - 포함한 모든 매처가 매칭되는지 검사한다. (&&와 동일)   
anyOf - 포함한 매처 중 하나라도 (||와 동일)   
not - 포함한 매처들의 의미를 부정한다.(자바의 ! 연산자와 동일)   
instanceOf, isCompatibleType - 객체들의 호환 가능한 타입인지 확인   
sameInstance - 객체 신원을 확인한다.   
notNullValue, nullValue - 값이 null인지/혹은 아닌지 검사   
hasProperty - 자바빈이 특정 속성을 갖는지 검사   
hasEntry, hasKey, hasValue - 주어진 Map이 명시된 entry, key, value를 포함하는지 검사         
hasItem, hasItems - 주어진 컬렉션이 명시한 아이템, 혹은 아이템들을 포함하는지 검사   
closeTo, greaterThan, graterThanOrEqual, lessThan, lessThanOrEqual - 주어진 숫자가 또 다른 숫자에 근접한지, 더 큰지, 더 크거나 같은지 ...   
equalToIgnoringCase - 주어진 문자열이 다른 문자열과 일치하는지 검사(대소문자 무시)   
equalToIgnoringWhiteSpace - 위와 일치(공백문자 무시)   
containsString, endsWith, startWith - 주어진 문자열이 다른 문자열을 포함하는지, 그 문자열로 시작/끝나는지   

# JUNIT 모범 사례   
✨한 번에 하나의 객체만 테스트하라.   
✨테스트 메서드에 의미있는 이름을 부여하라.    
testXXXYYY -> XXX에는 테스트할 도메인 메서드의 이름이 들어가고, YYY에는 기본 테스트 메서드와 어떻게 다른지를 기술한다.   
✨위에서 message에는 테스트 실패 시 의미있는 설명을 제공할 수 있도록 한다.   
✨실패할 가능성이 있는 모든 것을 테스트하라.   
✨테스트 작성은 때론 더 좋은 코드 작성에 도움이 된다. TDD를 활용하라.   
✨예외 테스트도 읽기 쉽게 만들라.   
@Test(expected=RuntimeException.class) -> 이 테스트에서는 어떤 예외가 발생하는지 한 눈에 볼 수 있다.   
@Test(timeout=130) -> 타임아웃 테스트로 130초가 지나면 더 이상 객체를 생성하지 않고 끝.   
✨테스트를 건너뛸 때에는 반드시 그 이유를 명시하라.   
@Ignore(value="이유")   
✨같은 패키지, 다른 디렉토리   
테스트 클래스와 도메인 클래스는 같은 패키지를 공유하되, 물리적으로는 다른 디렉터리 구조를 사용하라.    
protected 메서드에 접근하려면 테스트와 도메인 클래스를 같은 클래스에 두어야 한다.   
그리고 파일 관리 간소화 측면이나 테스트와 도메인 클래스를 명확히 구분하기 위해서는 서로 독립된 디렉토리에 보관해야 한다.

# 소프트웨어 테스트의 4가지 종류   
1. 통합테스트   
여러 작업 단위가 연계된 워크플로우 테스트
2. 기능테스트   
공개된 API의 가장 바깥쪽에 있는 코드 검사
ex. 로그인을 하지 않은 채 보안 페이지에 접근시 redirect.
3. 스트레스 테스트와 부하 테스트   
타임아웃 주로 사용
4. 인수 테스트   
사용성이나 룩앤필 같은 주제까지 포함

# 싱글톤?
싱글톤은 클래스의 인스턴스가 오직 하나 뿐임을 보장하는 유용한 디자인 패턴이다. 싱글톤의 개념은 좀 더 많은 객체를 생성하도록 확장되기도 한다.
싱글톤을 구현할 떄는 흔히 private 생성자와 정적 변수를 사용한다.
## 가장 단순한 싱글톤 구현 예
```
public class Singleton {      
    public static final Singleton INSTANCE = new Singleton();   
    private Singleton() {}   
}   
```

```
public static Singleton {    
    public static Singleton INSTANCE;    
    private Singleton() {}    
    public static Singleton getInstance() {    
        if (INSTANCE == null) {    
            INSTANCE = new Singleton();    
        }    
        return INSTANCE    
    }    
} 
```

# Stub이란?
스텁은 실제 코드나 아직 준비되지 못한 코드의 행동으로 가장하는 메커니즘을 말한다. 스텁을 사용하면 시스템의 특정 부분이 미처 준비되지 못했더라도 다른 부분을 테스트할 수 있다.

스텁이란 호출자를 실제 구현물로부터 격리시킬 목적으로 런타임에 실제 코드 대신 삽입되는 코드 조각이다! 복잡한 행위를 단순한 것으로 교체하여 실 코드의 일부를 독립적으로 테스트하기 위한 의도로 사용된다.

다음과 같은 경우에 스텁 활용 가능
-시스템이 너무 복잡하고 깨지기 쉬워 수정이 불가할 때
=하부 시스템 간의 통합 테스트처럼 포괄적인 테스트를 수행할 떄

# Mockito 사용 이유??
상황 >
```
    @Autowired 
    public MemberService(MemberRepository memberRepository, LoginService loginService, JwtService jwtService) {
        this.memberRepository = memberRepository;
        this.loginService = loginService;
        this.jwtService = jwtService; 
    }
```        
memberService는 loginService와 jwtService의 주입을 받고 있다.
물론 하나하나 구현한 후 넣어줄 수 있지만 이는 매우 오래 걸리고 비효율적이다. 
이런 일을 대신 해주는 것이 mockito!

```
class MemberServiceTest {

    @Test
    void createMemberServiceService() {
        LoginService loginService = Mockito.mock(LoginService.class);
        JwtService jwtService = Mockito.mock(JwtService.class);

        MemberService memberService = new MemberService(loginService, jwtService);

        Assertions.assertNotNull(memberService);
    }
}
```

또는 그냥 생성할 때 @Mock을 @Autowired처럼 사용할 수 있다.

## when() , thenReturn()
~했을 때 ~가 리턴되어야 한다.
## thenThrow(), doThrow()
thenThrow()를 이용해서 예외를 터뜨린다.

✨내용 출처

실전! 스프링부트 상품-주문 API 개발로 알아보는 TDD   
https://velog.io/@zunzero/TDD


