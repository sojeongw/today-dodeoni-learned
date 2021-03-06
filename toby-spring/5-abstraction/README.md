# 서비스 추상화

기술이 달라도 공통된 접근 방식으로 접근하기 위해 추상화하는 방법을 살펴보자.

## 사용자 레벨 관리 기능 추가

기초적인 CRUD만 가능한 `UserDao`에 다음과 같은 비즈니스 로직을 추가해보자.

- 사용자 레벨은 `BASIC`, `SILVER`, `GOLD` 세 가지 중 하나다.
- 처음 가입하면 `BASIC`이 되고 이후 활동에 따라 한 단계씩 업그레이드 된다.
- 50회 이상 로그인을 하면 `BASIC`에서 `SILVER`가 된다.
- `SILVER`에서 30번 이상 추천을 받으면 `GOLD` 레벨이 된다.
- 사용자 레벨 변경 작업은 일정한 주기를 가지고 일괄적으로 진행된다.
- 변경 작업 전에는 조건을 충족해도 레벨의 변경이 일어나지 않는다.

```java
public class User {
    private static final int BASIC = 1;
    private static final int SILVER = 2;
    private static final int GOLD = 3;

    int level;
    
    public void setLevel(int level) {
        this.level = level;
    }
	
    ...
}
```

각 레벨을 문자 그대로 저장하는 것 대신 코드화 해서 넣으면 DB 용량을 줄일 수 있다. 하지만 int 타입의 엉뚱한 값이 들어가면 컴파일러가 체크하지 못하고 버그가 발생한다.

```java
user.setLevel(other.getSum());
```

예를 들어, 엉뚱한 계산의 결과로 1, 2, 3이 들어갈 수 있다.

```java
user.seteLevel(1000);
```

혹은 범위를 벗어나는 값을 넣을 위험도 있다.

그래서 숫자 타입을 직접 사용하는 것 보다 enum을 이용하는 게 안전하고 편리하다.

```java
public enum Level {
    // enum 오브젝트 정의
    BASIC(1), SILVER(2), GOLD(3);
    
    private final int value;

    // DB에 저장하기 위한 생성자
    Level(int value) {
        this.value = value;
    }

    // 값을 가져오는 메서드
    public int intValue() {
        return value;
    }

    // 값으로부터 Level 타입 오브젝트를 가져오도록 만든 메서
    public static Level valueOf(int value) {
        switch(value) {
            case 1: return BASIC;
            case 2: return SILVER;
            case 3: return GOLD;
            default: throw new AssertionError("Unknown value: " + value);
        }
    }
}
```

`Level` enum은 내부에는 DB에 저장할 int 타입의 값을 갖고 있지만, 겉으로는 Level 타입의 오브젝트이므로 안전하게 사용할 수 있다.

```java
public class User {
    ...
    // 위에서 만든 Level 타입 변수 추가
    Level level;
    // 로그인 횟수
    int login;
    // 추천수
    int recommend;

    public Level getLevel() {
        return level;
    }
	
    public void setLevel(Level level) {
        this.level = level;
    }

    ...
}
```

비즈니스 로직에서 필요한 필드를 `User`에 추가했으니 이제 `UserDaoJdbc`와 `UserDaoTest`에도 추가해야 한다.

{% tabs %}
{% tab title="After" %}
```java
public class UserDaoTest {
    @Before
    public void setUp() {
        // 뒤에 3개가 새로 추가된 level, login, recommend 필드 값이다.
        this.user1 = new User("gyumee", "박성철", "springno1", Level.BASIC, 1, 0);
		this.user2 = new User("leegw700", "이길원", "springno2", Level.SILVER, 55, 10);
		this.user3 = new User("bumjin", "박범진", "springno3", Level.GOLD, 100, 40);
    }

    ...

	@Test 
	public void andAndGet() {		
		dao.deleteAll();
		assertThat(dao.getCount(), is(0));

		dao.add(user1);
		dao.add(user2);
		assertThat(dao.getCount(), is(2));
		
		User userget1 = dao.get(user1.getId());
        // checkSameUser 메서드를 재사용해서 로직을 일정하게 유지한다.
		checkSameUser(userget1, user1);
		
		User userget2 = dao.get(user2.getId());
        // checkSameUser 메서드를 재사용해서 로직을 일정하게 유지한다.
		checkSameUser(userget2, user2);
	}

    private void checkSameUser(User user1, User user2) {
		assertThat(user1.getId(), is(user2.getId()));
		assertThat(user1.getName(), is(user2.getName()));
		assertThat(user1.getPassword(), is(user2.getPassword()));
        // 추가된 필드를 비교하는 코드를 추가한다.
		assertThat(user1.getLevel(), is(user2.getLevel()));
		assertThat(user1.getLogin(), is(user2.getLogin()));
		assertThat(user1.getRecommend(), is(user2.getRecommend()));
	}

}
```
{% endtab %}

{% tab title="Before" %}
```java
public class UserDaoTest {
    private User user1;
	private User user2;
	private User user3;
	
	@Before
	public void setUp() {
		this.user1 = new User("gyumee", "박성철", "springno1");
		this.user2 = new User("leegw700", "이길원", "springno2");
		this.user3 = new User("bumjin", "박범진", "springno3");
	}
    
    ...

	@Test 
	public void andAndGet() {		
		dao.deleteAll();
		assertThat(dao.getCount(), is(0));

		dao.add(user1);
		dao.add(user2);
		assertThat(dao.getCount(), is(2));
		
		User userget1 = dao.get(user1.getId());
		assertThat(userget1.getName(), is(user1.getName()));
		assertThat(userget1.getPassword(), is(user1.getPassword()));
		
		User userget2 = dao.get(user2.getId());
		assertThat(userget2.getName(), is(user2.getName()));
		assertThat(userget2.getPassword(), is(user2.getPassword()));
	}
    
    private void checkSameUser(User user1, User user2) {
        assertThat(user1.getId(), is(user2.getId()));
        assertThat(user1.getName(), is(user2.getName()));
        assertThat(user1.getPassword(), is(user2.getPassword()));
    }
}
```
{% endtab %}
{% endtabs %}

테스트 픽스처로 만든 `user1`, `user2`, `user3`에 새로 추가된 필드 값을 넣는다. `checkSameUser` 메서드에도 새 필드를 비교하는 코드를 추가한다.

## 사용자 수정 기능 추가

기본키인 id를 제외한 나머지 필드는 수정될 수 있다. 만들어야 할 코드의 기능을 생각해보며 테스트를 먼저 작성한다.

```java
public class UserDaoTest {
    @Test
    public void update() {
        dao.deleteAll();
        dao.add(user1);
        
        // 픽스처 정보를 변경한다.
        user1.setName("오민규");
        user1.setPassword("springno6");
        user1.setLevel(Level.GOLD);
        user1.setLogin(1000);
        user1.setRecommend(999);
        dao.update(user1);

        // 수정 사항이 반영됐는지 확인한다.
        User user1update = dao.get(user1.getId);
        checkSameUser(user1, user1update);
    }
}
```

실제 반영해야 할 `UserDao` 인터페이스와 구현 클래스인 `UserDaoJdbc`에 `update()`를 추가해보자.

```java
public interface UserDao {
    ...
    public void update(User user1);
}
```

```java
public class UserDaoJdbc {
    public void update(User user) {
        this.jdbcTemplate.update(
        "update users set name = ?, password = ?, level = ?, login = ?, " +
        "recommend = ? where id = ? ", user.getName(), user.getPassword(), 
		user.getLevel().intValue(), user.getLogin(), user.getRecommend(),
		user.getId()); 
    }
}
```

그런데 보통 이런 SQL 문장은 실수가 많이 일어난다. 특히 UPDATE 문은 WHERE 절을 빼먹어도 경고 없이 정상 동작하는 것처럼 보인다. 이때 해결할 수 있는 방법은 두 가지다.

### `jdbcTemplate.update()`의 리턴 값을 확인

SQL을 실행했을 때 영향받은 로우의 개수를 돌려준다. 메소드의 리턴 타입을 int로 바꾸고 리턴하게 한 뒤, 값이 맞는지 확인한다.

### 테스트 코드 보완

원하는 사용자 외의 정보는 변경되지 않도록 테스트 코드를 수정한다. 

{% tabs %}
{% tab title="After" %}
```java
public class UserDaoTest {
    @Test
    public void update() {
        dao.deleteAll();
        // 수정할 사용자
        dao.add(user1);
        // 수정하지 않을 사용자
        dao.add(user2);
        
        user1.setName("오민규");
        user1.setPassword("springno6");
        user1.setLevel(Level.GOLD);
        user1.setLogin(1000);
        user1.setRecommend(999);

        dao.update(user1);

        // 수정 사항이 반영됐는지 확인한다.
        User user1update = dao.get(user1.getId);
        checkSameUser(user1, user1update);
        // 변경되지 않았음을 확인한다.
        User user2same = dao.get(user2.getId());
        checkSameUser(user2, user2same);
    }
}
```
{% endtab %}

{% tab title="Before" %}
```java
public class UserDaoTest {
    @Test
    public void update() {
        dao.deleteAll();
        dao.add(user1);
        
        user1.setName("오민규");
        user1.setPassword("springno6");
        user1.setLevel(Level.GOLD);
        user1.setLogin(1000);
        user1.setRecommend(999);
        dao.update(user1);

        User user1update = dao.get(user1.getId);
        checkSameUser(user1, user1update);
    }
}
```
{% endtab %}
{% endtabs %}

원래의 `update()`에서 WHERE를 빼먹었다면 이 테스트는 실패할 것이다.

## 레벨 업그레이드

`UserDaoJdbc`는 데이터를 어떻게 가져올지 다루는 곳이므로 비즈니스 로직을 담을 `UserService` 클래스에 넣는다.

`UserService`는 `UserDao` 인터페이스 타입으로 `userDao` 빈을 DI 받아 사용한다. `UserDao`의 구현 클래스가 바뀌어도 영향을 받지 않아야 하기 때문이다. 

즉, 지금 쓰고 있는 JDBC에서 다른 방식으로 데이터 액세스 로직이 바뀌더라도 비즈니스 로직 코드를 수정하는 일이 있어서는 안된다.

또한, DI를 `UserService`에 적용하려면 그 자신도 스프링 빈으로 당연히 등록되어야 한다. `UserService` 테스트를 위한 `UserServiceTest`도 만든다.

![](../../.gitbook/assets/toby/screenshot%202020-03-01%20오후%204.35.32.png)

지금까지 설명한 `UserService`의 클래스 레벨 의존 관계는 위와 같다.

```java
public class UserService {
    // DI로 가져올 UserDao 오브젝트
    UserDao userDao;

    // DI용 수정자 메서드
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
}
```

```java
@RunWith(SpringJunit4ClassRunner.class)
@ContextConfiguration(locations="/test-applicationContext.xml")
public class UserServiceTest {
    // 테스트 대상인 UserService 빈을 제공받을 수 있도록 
    // @Autowired가 붙은 인스턴스 변수를 선언한다.
    @Autowired
    UserService userService;
}
```

이제 로직을 추가할 기본 준비가 끝났다.

### upgradeLevels() 로직 추가

```java
public class UserService {
    UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void upgradeLevels() {
        List<User> users = userDao.getAll();

        for(User user : users) {
            // 레벨의 변화를 확인하는 flag
            Boolean changed = null;

            // BASIC 레벨 업그레이드
            if(user.getLevel() == Level.BASIC && user.getLogin() >= 50) {
                user.setLevel(Level.SILVER);
                changed = true;
            }

            // SILVER 레벨 업그레이드
            else if(user.getLevel() == Level.SILVER && user.getRecommend() >= 30) {
                user.setLevel(Level.GOLD);
                changed = true;
            }

            // GOLD 레벨은 업그레이드 없음
            else if(user.getLevel() == Level.GOLD) { changed = false; }

            // 일치하는 조건 없으면 변경 없음
            else { changed = false; }

            // 변경이 있는 경우 update()
            if(changed) { userDao.update(user); }
        }
    }
}
```

아래는 위의 코드에 대한 테스트 코드다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/test-applicationContext.xml")
public class UserServiceTest {
	@Autowired 	UserService userService;	
	@Autowired UserDao userDao;
	
    // 테스트 픽스쳐
	List<User> users;	
	
	@Before
	public void setUp() {
        // asList()를 이용해 배열을 가변 인자인 리스트로 넣어주면 편리하다.
		users = Arrays.asList(
            new User("bumjin", "박범진", "p1", Level.BASIC, 49, 0),
            new User("joytouch", "강명성", "p2", Level.BASIC, 50, 0),
            new User("erwins", "신승한", "p3", Level.SILVER, 60, 29),
            new User("madnite1", "이상호", "p4", Level.SILVER, 60, 30),
            new User("green", "오민규", "p5", Level.GOLD, 100, 100)
            );
	}

	@Test
	public void upgradeLevels() {
		userDao.deleteAll();
		for(User user : users) userDao.add(user);
		
		userService.upgradeLevels();
		
        // 사용자별로 업그레이드 후의 예쌍 레벨을 검증한다.
		checkLevelUpgraded(users.get(0), false);
		checkLevelUpgraded(users.get(1), true);
		checkLevelUpgraded(users.get(2), false);
		checkLevelUpgraded(users.get(3), true);
		checkLevelUpgraded(users.get(4), false);
	}

    // DB에서 사용자 정보를 가져와 레벨을 확인하는 코드가 중복되므로 헬퍼 메서드로 분리한다.
	private void checkLevelUpgraded(User user, Level expectedLevel) {
		User userUpdate = userDao.get(user.getId());
		assertThat(userUpdate.getLevel(), is(expectedLevel));
	}
}
```

### 기본 레벨 설정 하기

처음 가입하는 사용자는 기본적으로 BASIC 레벨이어야 한다. 이 기능은 어디에 넣어야 할까?

- UserDaoJdbc
    - DB 정보를 넣고 읽는 부분에만 관심을 가지므로 바람직하지 않다.
- User
    - 처음 가입할 때만 의미 있는 정보인데 단지 이 로직 때문에 클래스에서 직접 초기화 하는 것은 문제가 있다.
- UserService
    - add() 메서드를 만들어 사용자가 등록될 때 적용할 만한 비즈니스 로직을 넣기 적합하다.
    
먼저 테스트 코드를 작성해보자.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/test-applicationContext.xml")
public class UserServiceTest {
    ...
    @Test
    public void add() {
        userDao.deleteAll();

        // GOLD 레벨 사용자
        User userWithLevel = users.get(4);
        // 레벨이 비어있는 사용자
        User userWithoutLevel = users.get(0);
        userWithoutLevel.setLevel(null);

        userService.add(userWithLevel);
        userService.add(userWithoutLevel);

        User userWithLevelRead = userDao.get(userWithLevel.getId());
        User userWithoutLevelRead = userDao.get(userWithoutLevel.getId());

        // GOLD인 사용자가 그대로 GOLD로 유지되었는지 확인한다.
        assertThat(userWithLevelRead.getLevel(), is(userWithLevel.getLevel()));
        // 레벨이 없던 사용자가 BASIC으로 기본 설정되어 나오는지 확인한다.
        assertThat(userWithoutLevelRead.getLevel(), is(Level.BASIC));
    }
}
```

테스트를 준비했으니 테스트가 성공하도록 코드를 만든다.

```java
public class UserService {
    ...
    public void add(User user) {
        if(user.getLevel() == null) user.setLevel(Level.BASIC);
        userDao.add(user);
    }
}
```

테스트는 성공했지만 코드가 조금 복잡하다. 다음 장에서 리팩토링을 해보자.