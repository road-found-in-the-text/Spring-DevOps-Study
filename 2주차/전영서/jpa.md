# JPA의 필요성
객체와 관계형 데이터베이스의 차이?
객체에는 상속/캡슐화 등의 기능이 있다. 
Object를 저장하는 방법에 다양한 방법이 있다. 그 중 관계형 데이터베이스! RDB?
객체  ->  SQL 변환  ->  RDB 이렇게 SQL로 mapper하는 것이 우리의 역할이다!
sql을 삽입/조회/join .. 이 중간과정을 다 해야 한다.

java collection 에 저장한다고 가정해보자. 
list.add(album);
Album album = list.get(albumId);
그리고, 부모 타입으로 조회 후 다형성 활용도 가능하다.
Item item = list.get(albumId); 

연관관계가 있는 상태라면?
Team과 Member이 연관관계가 있는 상태라면, 자바 컬렉션으로 관리하면 그냥 해당 Team에 참조된 Member가 그대로 나오게 된다.

객체 그래프 탐색이 가능하면 좋다. 
SQL은 그래프 탐색에 불리하다. Member -- Order 
                             |--Team --Delivery 이렇게 되어있을 때 Order는 조회가 되는데 Delivery 조회는 안될 수 있다.
entity에 대한 신뢰 문제가 생긴다.
계층형 architecture? 다음 계층을 믿고 쓸 수 있어야 하는데, DAO를 까봐야 안다. 참조를 일일히 따라가기가 쉽지 않음
객체 vs 자바 컬렉션 **계층 분할** **객체 비교**

**객체를 자바 컬렉션에 저장 하듯이 DB에 저장할 수는 없을까??**


# JPA - ORM
애플리케이션과 JDBC 사이에서 동작한다.
JPA 동작 - 저장 및 조회 : JPA야 회원 객체 저장해줘!

표준 명세
- JPA는 인터페이스의 모음이다.

# JPA 장점
- SQL 중심적인 개발에서 객체 중심으로 개발
- 생산성
- 유지보수
- 패러다임의 불일치 해결
- 성능
- 데이터 접근 추상화와 벤더 독립성
- 표준

# 방법
저장 : jpa.persist(member)
조회 : Member member = jpa.find(memberId)
수정 : member.setName("변경할 이름")
삭제 : jpa.remove(member)

# 유지보수
기존: 필드 변경 시 모든 SQL 수정
JPA: 필드만 추가하면 됨

# 패러다임의 불일치 해결
상속 관계 일 때 이미 join해줘서 반환해준다.
연관 관계 일 때, 참조 굳이 막 안해줘도 다 해줌

# 신뢰할 수 있는 엔티티, 계층

# 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장해줌

# JPA의 성능 최적화 기능
1. 1차 캐시와 동일성 보장
같은 트랜잭션 안에서는 같은 엔티티를 반환한다. 약간의 조회 성능을 향상시킴
2. 트랜잭션을 지원하는 쓰기 지연
INSERT
트랜잭션을 커밋할 때까지 INSERT SQL을 모음. 트랜잭션이 중요하기 때문에 그 전까지는 굳이 데이터베이스에 보내지 않는 것임!
3. 지연 로딩과 즉시로딩
지연로딩 - 객체가 실제 사용될 때 로딩되도록 하는 것! 
굳이 연관된 거(Member와 Team)가 같이 쓰이지도 않는데 가져올 필요가 없다.

# JPA 시작
EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

EntityManager em = emf.createEntityManager();

em.close();

emf.close();

# 기타 정리
JPA에서는 트랜잭션이 아주 중요하다. 즉 JPA의 모든 데이터 변경은 트랜잭션 안에서 실행되어야 한다. EntityTransaction tx = em.getTransaction() 이걸 이해!

transaction.begin();

entityManager.persist(hotel);  //DB에 hotel이라는 객체 저장
> 상태변경이므로 트랜잭션 안에서 실행되어야 한다!
transaction.commit();



entity manager factory는 애플리케이션에서 하나만 생성하여 전체에서 공유하지만, entity manager는 쓰레드 간에 공유해서는 안 된다. 사용하고 버려야 한다.
Controller 파일이 다르다면, 새로운 Entity Manager를 생성하는 것이 좋을 것 같다.

# 영속성 컨텍스트 ==1차 캐시
엔티티를 영구 저장하는 환경
논리적인 개념이다. 눈에 보이지 않음

# 엔티티의 생명주기 persist
비영속 - 새로운 상태
영속 - 관리되는 상태
준영속 - 분리된 상태
em.detach(member);
삭제

그래서 영속성 컨텍스트 안에서 값을 찾는다면
처음에는 1차 캐시에서 찾고, 없으면 DB에서 찾아서 1차 캐시에 저장 후 가져오는 그런 형식임

영속 컨텍스트는 영속 엔티티의 동일성 또한 보장해준다.
==비교를 했을 때 1차 캐시에서 똑같은 것을 가져오기 떄문에 동일성이 보장됨

# 쓰기 지연
transaction.commit()을 하기 전까지는 DB에 저장하지 않는다.

# JPA 로직 그림
<img src="http://www.s-dhttps://velog.velcdn.com/cloudflare/seungho1216/06fb89f1-62dd-4447-81f0-66d469e0c5b2/%EC%98%81%EC%86%8D%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8%EC%A0%84%EC%B2%B4.png" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="JPA 로직 그림"></img>

# 수정 시 변경 감지
entity 값을 바꾸려면 뭔가 set 후에 entity.persist();를 해야할 것 같지만 아님.
비밀은 영속성 컨텍스트 안에 있다. 커밋하는 시점에 무슨 일이 벌어지는가?
flush()가 호출됨 -> entity와 snapshot이 생기게 됨 -> JPA가 이걸 다 바뀌는지 계속 지켜보고 있다가 오? 하면서 자동으로 변경

<img src="https://user-images.githubusercontent.com/87989933/197335056-12530693-d980-4ce0-8c88-e3ca0d3129fa.png" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="JPA 로직 그림"></img>

# 플러시
영속성 컨텍스트의 변경 내용을 DB에 반영

언제 플러시가 발생하는가?
> 변경감지 </br>
> 수정된 엔티티 쓰기 지연 SQL 저장소에 등록</br>
> 쓰기 지연 SQL 저장소의 쿼리를 DB에 전송 (등록, 수정, 삭제 쿼리)

어떻게 플러시?
> em.flush() - 강제 저장 (DB에 insert문 바로 적용됨)</br>
> 트랜잭션 커밋 - 플러시 자동 호출 </br>
> JPQL 쿼리 실행 - 플러시 자동 호출 </br> --em.persist 안해도 그냥 날라가는 구나 이해

# 플러시 특징
> 영속성 컨텍스트를 비우지 않음 </br>
> 영속성 컨텍스트의 변경 내용을 DB에 동기화 </br>
> 트랜잭션이라는 작업 단위가 중요 -> 커밋 직전에만 동기화하면 됨 </br>

# 영속 vs 준영속
### 영속 상태
생성한 객체를 em.persist()를 통해 영속성 컨텍스트에 저장하거나,

혹은 em.find()를 통해 DB에서 엔티티를 조회하면 영속성 컨텍스트에 저장되어 관리되어지므로 이때 영속 상태가 된다.

### 준영속 상태
em.detach(member); 이렇게 하면 영속상태에서 빠짐

준영속 상태로 만드는 방법?
> em.detach(member); 특정 entity를 영속상태에서 뺌 </br>
> em.clear(); 영속성 컨텍스트를 완전히 초기화
> em.close(); 영속성 컨텍스트를 종료

# hibernate.auto 설정 속성
create - 기존 테이블 삭제 후 다시 생성
create-drop - create와 같으나 종료 시점에 테이블 DROP
update - 변경분만 반영(운영DB에는 사용하면 안 됨)
validate - 엔티티와 테이블이 정상 매핑되었는지만 확인
none - 사용하지 않음

주로 개발 초기 단계는 create나 update,
테스트 서버는 update 또는 validate
스테이징과 운영 서버는 validate 또는 none

# Mapping Annotation
> @Column : 컬럼 매핑</br>
> @Temporal : 날짜 타입 매핑</br>
> @Enumerated : enum 타입 매핑   --원래는 ORDINAL(0,1,2 ...)이 기본인데 이렇게 하면 숫자로 되어 있어서 값이 바뀔 때 문제가 생김   
> @Lob : BLOB, CLOB 매핑 </br>
> @Transient : 특정 필드를 컬럼에 매핑하고 싶지 않을 때 - 생략하고 싶을 때</br>
> @Lob : 엄청 큰 (예를 들어 description) 문자열/숫자열을 넣을 때 

만약 어떤 컬럼 조건을 내가 세세하게 주고 싶다면? </br>
columnDefinition = "default 'EMPTY'" 뭐 이런 식으로 만들 수 있다!

# 기본키 매핑
@Id   
@GeneratedValue

권장하는 식별자 전략   
Long형 + 대체키 + 키 생성전략 사용   
GenerationType.SEQUENCE 면 전략이 1부터 시작해서 1만큼 증가되는 형식   
allocationSize = 50 이면 한 번 호출될 때마다 db id 50개가 한 번에 생성됨. 다 쓰면 51~100까지 생성

# 연관 관계의 매핑 
연관 관계의 주인(Owner)

Member와 Team   
Member 입장에서는 Team이 ManyToOne이다.   
즉,    
@ManyToOne @JoinColumn(name="Team_id")
Team team;

ManyToOne에 fetch라는 게 있는데 지연로딩 .. 나중에 더 공부하자!

# 객체 참조와 테이블의 양방향 매핑 ★★★

<img src="https://blog.kakaocdn.net/dn/bbfwZN/btqFNgJPObb/k4SbI2jWxrK66N8h3Wzu51/img.png" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="JPA 로직 그림"></img>

그러면 위에서 만약 Team입장이라면,   
Team Entity 안에서는

@OneToMany(mappedBy = "team") //나는 팀으로 매핑되어있는 애야   
private List<Member> members = new ArrayList<>();

**객체와 테이블이 관계를 맺는 차이**
객체에서는 단방향이 두 개 있는 방식이고(참조가 두 개),   
테이블에서는 양쪽이 다 있는 방식이다. 

그러면 만약 Member에 있는 Team 이름을 바꾸고 싶다면 어떡하지?   
안되겠다. 룰이 생김. --> 연관 관계의 주인!!

연관관계의 주인만이 외래 키를 관리한다. *등록 수정 가능하다*
**외래키가 있는 곳을 주인으로 정하라.** 일 대 다 중에 '다' 쪽 !!!

팀에다가 멤버를 넣는 게 아니라   
멤버에다가 팀을 넣는 형식이다!!   

양방향 매핑 시 연관관계의 주인에 값을 입력해야 한다.   
(순수한 객체 관계를 고려하면 항상 **양쪽 다** 값을 입력해야 한다.)

### 그럼 어떻게??
연관관계 편의 메소드를 생성하자.
Member Entity Class 안에 이런 함수를 생성한다.
public void setTeam(Team team) {  -> 되도록이면 changeTeam으로 이름 바꾼다.   
    this.team = team;   
    team.getMembers().add(this);   
}   
  
이러면 원래 코드는   
Team team = new Team();   
team.setName("TeamA");   
em.persist(team);   

Member member = new Member();   
member.setUsername("member1");   
member.setTeam(team);     //team setting 시점에 member에도 넣어준다면 !!   
em.persist(member);

**team.getMembers().add(member);  --> 이 코드 지워도 됨**

## 주의
- Entity는 Controller에서 웬만하면 DTO로 변환해서 반환하자!    
(양방향 매핑 시에 무한 루프를 예방할 수 있음)

- 설계할 때는 일단 단방향만 표시해두고, 개발 시 양방향이 필요하다면 그 때 구현하자!

# EntityManager와 영속 컨텍스트(=캐시)
- 트랜잭션 커밋 시점에 save()로 추가한 영속 객체를 DB에 반영한다.
- 또는 flush()를 사용한다면 이 시점에도 DB에 억지로 반영할 수 있다.

# 영속 객체의 라이프 사이클
<img src="https://ifh.cc/g/3AnOyQ.png" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="영속 객체의 라이프 사이클"></img>


# 연관 매핑
## 1:1 단방향 매핑
member - Card

어떻게?

public class MembershipCard {
    @OneToOne
    @JoinColumn(name="user")
    private User owner ; //식별자를 column에 저장하는 형식이다. 
}

--> 위 상황에서, owner에 null 주는 것은 괜찮다. 하지만 반대로, owner가 영속상태에 있지 않은 상태에서 트랜잭션 커밋을 한다면 exception 발생한다.

# 즉시로딩과 지연로딩
즉시로딩 : 연관된 객체를 함께 불러온다. (fetch = FetchType.EAGER)
지연로딩 : 연관된 객체가 필요할 때만 불러온다. (fetch = FetchType.LAZY)
          연관된 객체가 필요로 하는 기능이 적을 때 사용한다.
          예를 들어, 카드를 사용 불가 상태로 바꾸는 기능이 있다고 하자. 이 기능을 쓸 때에는 굳이 owner를 같이 불러 가져올 필요가 없다.

# 1:1 양방향
+mappedBy="owner"을 추가하는 것 뿐!
User Entity 속에 Card라는 클래스를 넣고 위에 mappedBy를 추가하자!
'참조키'를 통해 이루어진다고 이해한다.

주로 위에서 말했던 User Entity에 setter를 사용한다.
public void setCard(Card card){
    this card = card;
}

1:1 연관을 끊는 법: 양쪽 연관에 null을 할당한다.

## 프록시 객체 ---- 지연 로딩
지연로딩 조회라고 해도 SQL문을(find, 즉 select) 두 번 호출하는 방식이 아니다.
<img src="https://ifh.cc/g/6HFnX4.jpg" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="프록시 객체와 지연로딩"></img>

일단 한 테이블만 조회하고(Card) Owner 필드에 User 프록시를 주입해 둔 후 Card 객체를 먼저 리턴한다. 이후 카드의 Owner 이름(참조키)을 이용해 User 프록시에서 프록시가 한 번 실제 entity를 로딩하면 이후 접근에서는 이미 로딩한 User entity를 사용한다.

## N:1 단방향
Review : Hotel

public class Review {
    @ManyToOne 
    @JoinCOlumn(name="hotel_id")
    private Hotel hotel;
}

만약 한 호텔의 리뷰들을 보고 싶다면?
EntityManager em = EMF.createEntityManager();
try {
    Hotel hotel = em.find(Hotel.class, "H100-01");
    TypedQuery<Review> query = em.createQuery(
        "select r from Review r where r.hotel=:hotel" +
        "order by r.id desc", Review.class
    )
    query.setParameter("hotel",hotel);
}

# 값의 collection 매핑
@Embeddable - 밸류 타입을 매핑한 것을 기억한다.
List -> ArrayList
Set -> HashSet
Map -> HashMap
엔티티 로딩 시 다음 클래스를 이용해 각 콜렉션 타입의 인스턴스를 생성한다.

1:N collection 매핑
Team Player
팀에 Player 추가 가능 / Player가 속한 팀을 바꿀 수 있다.

### Team에는
private Set <Player> players = new HashSet<>();

### Player 에는
player가 속한 팀을 바꿀 수 있다.
private Team team; 이 들어가 있음

# 영속성 전이
엔티티의 영속성 상태 변화를 연관된 엔티티에도 함께 적용한다.
Card 속에

@OneToOne(cascade = CascadeType.PERSIST)  //함께 저장할 것임
@JoinColumn(name="user_email")
private User owner;

------

# JPA 내장함수 조금씩 이해하기

public interface JpaMemberRepository extends JpaRepository<Member,Long> {
        int modifyMemberName(Long memberId, String nickName);
}

modifyMemberName(Long memberId, String nickname) 메서드는 JPA의 내장 함수인 @Modifying 어노테이션과 @Query 어노테이션을 함께 사용하여 구현된 쿼리이다.
Member 엔티티에서 memberId를 가진 데이터의 nickName 값을 nickname으로 변경하는 쿼리로, 아래와 같다!

```
UPDATE member SET nick_name = :nickName WHERE member_id = :memberId
```
이 로직은 updateMemberName으로 메소드 이름을 변경해도 상관없다.   
JPA는 메소드명을 보고 실행해야 하는 쿼리를 자동으로 생성하기 때문에, 메소드 이름 자체가 중요한 것은 아니고,    
메소드의 **파라미터 타입과 리턴 타입, 그리고 메소드가 수행해야 하는 로직**이 중요하다.   
따라서, 메소드 이름을 적절하게 변경해도, 메소드 파라미터와 로직을 변경하지 않는 한, 여전히 동일한 기능을 수행할 것입니다.