아주 문제가 되는 내 memberRepository를 jpa로 다시 짜려고 한다..!!   

먼저, 함수를 조금 나열해보면   

```
#본래 함수
public Long createMember(SignupReq signupReq) 
```
이 함수는 SignupReq를 받아 Member를 저장하는 함수이다!   
따라서 em.persist(member); 로 해결 가능!   

```
#바뀐 코드
public boolean checkEmail(String email)
```
이 함수는 email이 있는지 확인하는 코드로, jpaMemberRespository의 existsMemberByEmail(email) 로 대체한다.(jpa 메소드를 상속받은 repository)   

```
#본래 코드
public int modifyUserName(UpdateNickNameReq updateNickNameReq) {
        String modifyUserNameQuery = "update umc3.member set nick_name = ? where member_id = ? "; // 해당 userIdx를 만족하는 User를 해당 nickname으로 변경한다.
        Object[] modifyUserNameParams = new Object[]{updateNickNameReq.getNickName(), updateNickNameReq.getMemberId()}; // 주입될 값들(nickname, userIdx) 순

        return this.jdbcTemplate.update(modifyUserNameQuery, modifyUserNameParams); // 대응시켜 매핑시켜 쿼리 요청(생성했으면 1, 실패했으면 0)
    }
```
update 함수를 createQuery로 만든 후 setParameter로 더욱 간결하게 만들 수 있다.   
```
#바뀐 코드
return em.createQuery("update Member m set m.nickName = :nickName where m.id = :memberId")
                .setParameter("nickName", updateNickNameReq.getNickName())
                .setParameter("memberId", updateNickNameReq.getMemberId())
                .executeUpdate();
```

### 조회 후 update 하는 쿼리   
```
#기존
    public int modifyIntroduction(UpdateIntroReq updateIntroReq) {
        String modifyUserNameQuery = "update umc3.member set introduction = ? where member_id = ? "; // 해당 userIdx를 만족하는 User를 해당 nickname으로 변경한다.
        Object[] modifyUserNameParams = new Object[]{updateIntroReq.getIntroduction(), updateIntroReq.getMemberId()}; // 주입될 값들(nickname, userIdx) 순

        return this.jdbcTemplate.update(modifyUserNameQuery, modifyUserNameParams); // 대응시켜 매핑시켜 쿼리 요청(생성했으면 1, 실패했으면 0)
    }
```

em.find 후, 해당 member의 상태를 set해준다. --아래 코드   
```
#바뀐 코드
    public int modifyIntroduction(UpdateIntroReq updateIntroReq) {
        Member user = em.find(Member.class, updateIntroReq.getMemberId());
        user.setIntroduction(updateIntroReq.getIntroduction());

        return 1; // 변경 성공 시 1 반환
    }
```
메소드를 호출하면, JPA는 해당 엔티티의 상태를 '변경됨'으로 표시하고, 추후에 트랜잭션이 커밋되는 시점에서 변경된 내용을 데이터베이스에 반영한다. 아직 **변경감지중**!!   
반면에, ~~병합(Merge)~~은 영속성 컨텍스트에 **존재하지 않는 엔티티**를 데이터베이스에서 가져오는 경우에 사용된다.