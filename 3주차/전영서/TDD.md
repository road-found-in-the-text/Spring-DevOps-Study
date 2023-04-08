# 기본 양식
```
import org.junit.Test;
import static org.junit.Assert.*;

public class MyTest {
    @Test
    public void testSomething() {
        // given (무엇인가가 주어졌을 때)
        // when (어떤 동작을 수행했을 때)
        // then (예상 결과가 나와야 함)
        // assert (실제 결과가 예상 결과와 같은지 검증)
    }
}
```

일단 TDD를 위해선 Repository -> Service -> Controller 순으로 구현해 나가야 한다.
Controller는 Service를, Service는 Repository를 의존하기 때문에, 의존 관계에서 가장 하위층부터 구현한다.


💖 @BeforeAll @AfterAll 사용 시   
1. 리턴타입이 있으면 안된다.   
2. private 키워드는 안된다. (default 키워드는 가능)   

결론은 위 어노테이션 사용시 웬만하면 static void를 사용하는걸 기본으로 생각하자.   

💖 @DisplayName   
테스트 클래스나 테스트 메소드에 이름을 붙일 때 사용한다.   

💖 @Nested
test 클래스안에 Nested 테스트 클래스를 작성할 때 사용되며, static이 아닌 중첩클래스, 즉 Inner 클래스여야만 한다.   

💖 @ExtendWith   
extension을 등록한다. 이 어노테이션은 상속이 된다. 확장팩   

💖 @ParameterizedTest
테스트를 반복할 때마다 다른 값들을 가지고 테스트 할 수 있다.   
```
class StudyTest {
	@DisplayName("test1")
    @ParameterizedTest
    @ValueSource(strings = {"날씨가", "많이", "더워지고", "있네요."})
    void parameterizedTest(String message){
        System.out.println(message);
    }
} 
```

💖
💖
💖

💖
💖
💖

