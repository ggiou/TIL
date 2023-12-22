#  ❤️ [2023-12-22]
## 1. @Builder indexOut Error
### (1) 내가 겪은 에러
<details>
  <summary> 해당 에러 설명 (java-lang-indexoutofboundsexception-index-7-size7)</summary>
  <div markdown="1">
    <pre>
[에러 발생 상황]
: 프로젝트내에서 해당 DTO가 기존에는 우리 쪽 DB에서 데이터를 받아와 생성해 사용했는데,
특정 이유때문에, 다른 쪽에서 보내주는 데이터를 가공해 해당 DTO를 새로 생성해 특정 이유에 사용해야하는 상황이 왔다.
<br>
[에러 발생 부분]
(이 DTO의 이름을 편의상 TimeDto 라 지칭) 이때 데이터를 받아서 가공 할때, 받아온 데이터에서
TimeDto의 모든 변수의 값을 넣어 생성해야 했기에, @Setter나 생성자로 생성하는 것보다, 
@builder을 이용해 생성하는 것이 코드가 간결할 거 같아 builder을 이용해 변환해주는 함수를 만들었다.
하지만 그 후 소스를 실행해보니, 해당 데이터를 받아 변환하는 부분이 아닌, 기존의 TimeDto를 생성해 사용하는 다른 부분에서
(우리쪽 db mybatis를 통해 생성..) java-lang-indexoutofboundsexception-index-7-size7 에러가 발생했다.
<br>
[에러 문제 파악]
해당 에러에 대해 처음엔 애초에 리스트도 아닌데 인덱스가 벗어난지 이해가 안되 이것 저것 구글링하며 노력했다.
하지만 에러가 발생한 부분은, 정작 수정 된 부분이 아니고, 해당 함수를 넣어 수정하기 전에는 잘만 실행 되던 거였다.
이 부분에 대해 생각하고, 달라진 부분에 대해 생각하다가, 에러의 내용을 읽어보니 myBatis에서 해당 TimeDto를 반환할때, 
myBatis의 해당 select 쿼리는 가져오는 변수가 7개 이고, 실제 TimeDto는 변수가 8개가 있다는 차이점을 알아냈다.
이를 바탕으로 @Builder에도 설마 생성자 형태가 고정되어 있나 살펴 보았고, 
(만약 고정되어 있다면, 다른 해당 부분을 반환 OR 생성하는 부분에서 다른 형태로 (전체, 부분, final 만 등...)생성되지 않아 오류가 생길 거라 판단)
역시나, @Builder의 경우 모든멤버 변수를 가지는 생성자만 생겨, 오류가 발생한 부분의 쿼리는, 7개만 받아오고
실제 dto는 8개를 받아야하니, indexOut 에러가 떨어지며, 모든 멤버 변수를 가지며 해당 dto가 생성된게 아니라 생긴 문제였다.
</pre>
</div>
</details> 
<details>
<summary> 에러 요약 </summary>
<div markdown="1">
<pre>
  @builder 사용시 모든 멤버변수를 가지는 생성자가 필요해, 모든 변수가 아닌애들이 불러져서 생성되는데서 index out이 생길수 있음
  = builder은 모든멤버 변수를 가지는 생성자만 생기니.. index가 맞지 않다 왜냐면 
  ❗❗ 해당DTO 에선 총 8개의 변수가 선언되어있는데, select~ 가 실행 될때는 db에서 7개의 정보만 가져오니.. 
</pre>
</div>
</details> 

### (2) 해결 방법
#### 에러 -> Cause: java.lang.IndexOutOfBoundsException:
```` JAVA
// 에러 수정 전
import lombok.Builder;
import lombok.Data; 

@Builder
@Data

// 에러 수정 후
import lombok.Builder;
import lombok.Data; 
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@Builder
@Data
@NoArgsConstructor
@AllArgsConstructor
````
@Build 를 사용하려면 해당 dto를 생성하는 부분이 무저건 전체 변수를 받는 생성자만 사용하는 것이 아니라면<br>
@NoArgsConstructor (파라미터가 없는 디폴트 생성자를 생성)<br>
@AllArgsConstructor (모든 필드 값을 파라미터로 받는 생성자를 생성) 를 같이 사용해주어야 에러가 발생하지 않는다.<br>

즉 빌더는 모든 멤버변수를 가지는 생성자가 필요하기 때문에 lombok을 사용할 경우 @AllArgsConstructor를 사용하거나 생성자 코드를 작성해 주는게 필요하다.<br><br>

🔆 Lombok 공식 사이트의 @Builder에 관한 설명을 보면
```` JS
Finally, applying @Builder to a class is as if you added @AllArgsConstructor(access = AccessLevel.PACKAGE) to the class
and applied the @Builder annotation to this all-args-constructor.
This only works if you haven't written any explicit constructors yourself.
If you do have an explicit constructor, put the @Builder annotation on the constructor instead of on the class.
````
다른 생성자를 명시하지 않고 @Builder만 클래스에 적용하면 @AllArgsConstructor(access = AccessLevel.PACKAGE)가 자동 적용된다.

### (3) @Builder 개념
#### 1) Builder 패턴 이란?
: 디자인 패턴 중 하나로, 생성과 표현의 분리이다. 쉽게 말해 생성자에서 인자가 많을때 고려해볼 수 있는 패턴이다.<br>
이는 점증적 생성자 패턴과 + 자바 빈즈 패턴의 단점을 모두 보완해서 나타난 것으로, <br>
정보들은 자바빈즈패턴처럼 받되, 데이터 일관성을 위해 정보들을 다 받은 후에 객체를 생성한다.<br>

#### 2) Builder 패턴의 장점
<details>
<summary> Builder 패턴의 장점 </summary>
<div markdown="1">
<pre>
▶ 불필요한 생성자의 제거
▶ 데이터의 순서에 상관없이 객체생성 가능
▶ 명시적 선언으로 이해하기가 쉽고
   각 인자가 어떤 의미인지 알기 쉽다.
▶ setter메서드가 없으므로 변경 불가능한 객체를 만들수있다.
▶ 한번에 객체를 생성하므로 객체일관성이 깨지지 않는다.
▶ build()함수가 null인지 체크해주므로 검증이 가능하다.
❌ 안그러면 set하지않은 객체에대해 get을 하게되는경우 nullPointerExcetpion발생 등등의 문제
</pre>
</div>
</details> 
<details>
<summary> Builder 패턴의 생성 설명 </summary>
<div markdown="1">
<pre>
◾ A클래스 내부에 빌더클래스를 생성한다.
◾ 각 멤버변수별 메서드를 작성하는데, 각 메소드는 변수에 값을 set하고 빌더객체를 리턴한다.
◾ build()메서드는 필수 멤버변수의 null체크를 하고 지금까지 set된 builder를 바탕으로 A클래스의 생성자를 호출하고 인스턴스를 리턴한다.

❗ setter에 리턴자료형을 Builder객체로 지정함으로써, 메서드체이닝기법을 적용하고 정보를 다 넣은경우 build()메서드로 객체를 생성한다.
❗ build( ) 메서드를 쓴 이후 에는 PersonInfo 클래스의 멤버변수를 변경 할 수 있는 방법은 리플렉션 기법(동적시점에 변경) 빼곤 존재하지 않는다.

⭕ 따라서, 데이터 일관성, 객체불변성 등을 만족시킨다. 또한 코드 가독성 역시 올라간다.
</pre>
</div>
</details>

```` java
// builder 패턴 예시
PersonInfo personinfo = new PersonInfo
    .Builder("Runa")    		// 필수값 입력 ( 빌더클래스 생성자로 빌더객체 생성)
    .setAge(16)  			      // 값 set
    .setPhoneNumber(1004)
    .build() 				        // build() 가 객체를 생성해 돌려준다.
// 이런식으로 간결하게, 필요한 인자값들을 설정 할 수 있다.
````

#### 3) Lombok @Builder
: 빌더클래스를 직접 만들지 않아도 롬복플러그인이 지원해주는 어노테이션 하나로 클래스를 생성할 수 있다.<br>
즉 클래스 또는 생성자 위에 @Builder어노테이션을 붙여주면 빌더패턴 코드가 빌드된다.<br>
생성자 상단에 선언시 생성자에 포함된 필드만 빌더에 포함! <br>

```` java
// @Builder 어노테이션을 해당 Class에 붙이면 적용된다.
@Builder
public class Person {
    private final String name;
    private final int age;
    private final int phone;
}

// 아래와 같이 간단히 객체 생성이 가능해진다.
Person person = Person.builder() 
    .name("Runa")
    .age(20)
    .phone(1004)
    .build();
````
