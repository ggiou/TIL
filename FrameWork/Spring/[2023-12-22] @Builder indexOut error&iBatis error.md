#  ❤️ [2023-12-22]
## 1. @Builder indexOut Error
### 내가 겪은 에러
: 프로젝트내에서 해당 DTO가 기존에는 우리 쪽 DB에서 데이터를 받아와 생성해 사용했는데,<br> 
특정 이유때문에, 다른 쪽에서 보내주는 데이터를 가공해 해당 DTO를 새로 생성해 특정 이유에 사용해야하는 상황이 왔다.<br><br>
(이 DTO의 이름을 편의상 TimeDto 라 지칭) 이때 데이터를 받아서 가공 할때, 받아온 데이터에서<br>
TimeDto의 모든 변수의 값을 넣어 생성해야 했기에, @Setter나 생성자로 생성하는 것보다, <br>
@builder을 이용해 생성하는 것이 코드가 간결할 거 같아 builder을 이용해 변환해주는 함수를 만들었다.<br>
하지만 그 후 소스를 실행해보니, 해당 데이터를 받아 변환하는 부분이 아닌, 기존의 TimeDto를 생성해 사용하는 다른 부분에서<br>
(우리쪽 db mybatis를 통해 생성..) java-lang-indexoutofboundsexception-index-7-size7 에러가 발생했다. <br>
### 해결 방법

### 간단한 설명

## 2.org.apache.ibatis.exceptions.PersistenceException Error
### 내가 겪은 에러

### 해결 방법

### 간단한 설명
