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

## 2.org.apache.ibatis.exceptions.PersistenceException Error
### 내가 겪은 에러

### 해결 방법

### 간단한 설명
