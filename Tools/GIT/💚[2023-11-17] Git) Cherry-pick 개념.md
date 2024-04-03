# 💚 [2023-11-17]
## 1. Git Cherry-pick 개념
### (1) 기초적인 개념 & 사용법 
- 필요한 commit만 골라서 가져오는 명령어이다.
<br> = 쉽게 말해 다른 브랜치에 있는 commit들 중에서 원하는, 필요한 commit을 지금 내 브랜치에 가져와 commit을 하는 것이다.
- ❗ 참고로 해당 commit을 branch 끼리 옮기는게 아니라, 동일한 commit을 다른 branch에 복사하는 개념이다. <br>(실제 commit이 추가됨)
  <br>
 <details>
  <summary> 💡 TIP : cherry pick 표현</summary>
  <div markdown="1">
  <pre>
  cherry pick이란, 
    체리 한 바구니에서제일 좋은 체리만 고르는 것에서 유래한 표현이라고 한다.
    일반적으로는 제일 좋은 걸 선별한다는 뜻으로 쓰인다.</pre>
  </div>
  </details>
 <details>
  <summary> 💡 TIP :  cherry-pick은 언제 써야 할까?</summary>
  <div markdown="1">
  <pre>
유용하긴 하지만 cherry-pick이 항상 권장되는 것은 아니라고 한다.
같은 commit이 여러 번 쌓이는 일도 발생할 수 있기 때문에 cherry-picking이 가능한 상황에서 보통은 일반적인 merge를 사용하는 걸 선호한다고 한다.
그렇다면 cherry-pick은 어떤 상황에서 유용할까? <br>
  
  <b>✅[팀으로 협업할 때]</b>
       만일 팀이랑 사이드 프로젝트를 진행하고 있다고 하자. 
       본인은 백엔드다. 
       현재 내가 맡은 기능이 프론트까지 더해졌을 때 어떻게 돌아가는지 확인하고 싶은데 프론트엔드를 맡은 동료가 지금 한창 작업 중이라고 한다. 
       근데 아까 CSS 파일을 완성해서 commit해 뒀다고 한다. 
       그럼 cherry-pick을 통해 그 commit 하나만 찾아서 내 branch에 가져오면 된다. CSS만 입혀볼 수 있게 되는 것이다.

  <b>✅[버그 수정]</b>
       본인은 며칠 전 A 기능을 완성한 개발자다. 
       A가 끝나고 B 기능을 개발하는 중이었는데, 알고 보니 A 기능에 버그가 있다고 한다. 
       더 많은 유저들이 이 버그로 인한 현상을 겪기 전에 빠르게 버그 패치를 해서 내 branch에 commit한다. 
       방금 commit한 이 패치는 git cherry-pick을 통해 main branch에 바로 반영되었다.

  <b>✅[반영되지 않은 pr]</b>
       실수로 pull request를 merge하기 전에 닫아버렸다. 
       당황하지 않고 git cherry-pick을 통해 해당 commit을 가져옴으로써 살릴 수 있게 됐다.</pre>
  </div>
  </details> 
EX)

![캡처](https://github.com/FeedTypeSNS/noldaga/assets/110371892/de980bec-8d7d-4a41-9ed9-1e9e7e1a4c6c)
<br>👩‍💻 예를 들어 두 개의 브랜치 'branch X'와 'branch y'가 있다 정한다. 
<br>  현재 'branck y'를 개발하고 있는 상황에서 'branch X'에 적용되어 있는 전체 커밋 중, 
<br>  3개의 커밋을 각각 가져와 반영하고 싶은 상황일떄, 이럴때, git cherry-pick 명령을 이용하면된다.
<br>

<details>
    <summary>✔[사용법1] 다른 브랜치 commit 가져 오는 방법</summary>
    <div markdown="1">
    <pre>git을 사용해 코드를 관리하다 보면, 각 개발 기법에 따라 여러 bracnch를 통해 코드를 관리할 떄가 많다. 
  이때 다른 branch에 적용된 commit을 가져와 내 브렌트에 적용하고 싶은 경우가 있다.
  
    - 브랜치 전체 commit 가져오기
    👉그떄 주로 commit을 가져가고 싶은 branck에 checkout해 이동 후
    👉모든 원격 브랜치를 업데이트하여 최신 상태로 갱신한 다음 (git 사이트에 올라간 상태와 동일해짐)
    👉commit을 가져가고 싶은 branck를 pull 받는 방법을 사용한다. 
      (해당 명령어)
        $ git chekout 'commit을 받을 브랜치 명'
        $ git remote update 
        $ git pull origin '브랜치 명'</pre>
    </div>
</details>

<details>
  <summary>✔[사용법2]  git Cherry-pick 방법들.. </summary>
  <div markdown="1">
  <pre>
  1. cherry-pick 명령 뒤에 커밋 해시 값을 명시해주면 기본적으로 실행된다.
      $ git cherry-pick [commit hash]

  2. cherry-pick으로 한번에 여러개의 커밋을 반영하고 싶은 경우
    👉(이렇게 여러개를 뒤쪽에 입력해도 되고)
      $ git cherry-pick 555f8b4 8f618a0 480b6bb<br>
    👉(가져오고 싶은 커밋 범위의 첫번째와, 마지막 커밋 해시 값을 ..문자로 이어주면 
      둘 사이의 있는 모든 커밋들을 가져와 현재 브랜치에 반영하게 된다.
     -> 첫 번째 입력한 커밋은 반영 안되고, 그 다음부터 마지막 커밋까지 범위가 반영됨)
      $ git cherry-pick 555f8b4..480b6bb <br>
  ![캡처2](https://github.com/FeedTypeSNS/noldaga/assets/110371892/d6dd9f9c-c74a-4446-a53f-0d596b5751c4)
  이런 식으로, 다른 브랜치에 적용된 커밋 사항을 현재 브랜치로 가져올 수 있다.
  이때 코드에 대한 수정사항은 물론이고, 커밋 로그와 작성자 역시 그대로 가져와진다.</pre>
  </div>
</details> 
<br>

## 2. Git Cherry-pick 부가 기능
### (1) Git Cherry-pick 옵션 (-continue, -abort)
내용 : cherry-pick 자체가, 다른 브랜치의 커밋 사항을 가져오는 동작이다보니, <br>
  수정 사항이 현재 브랜치의 코드에 맞지 않는 경우가 있다. 이 경우 충돌(Conflict)가 발생된다. <br>
  rebase나 merge 할때도 코드 충돌이 발생하는데, cherry-pick 에서도 같은 이유로 충돌리 발생할 수 있다.<br>
  이 경우, cherry-pick을 포기하거나, 충돌 상황을 해결하고 cherry-pick을 이어 진행할 수 있다.
<br>
#### 1)<b>abort</b>
  <details>
  <summary>⭕간단 설명</summary>
  <div markdown="1">
  <pre>⭕ Conflict를 해결하고 cherry-pick을 진행시킨다.
    (1) Conflict을 해결하기 위해 코드를 수정한다.
    (2) git add <path> 명령어로 수정된 코드를 올린다. (커밋은 다시 할 필요 없다.)
    (3) git cherry-pick –continue 명령어를 사용하면 다시 진행이 시작 된다.
  </pre>
  </div>
  </details> 
$ git cherry-pick -abort <br>
  👉 만약 cherry-pick을 중단하고 싶으면, -abort 옵션을 이용해, cherry-pick을 중단할 수 있다.<br>
이 경우, cherry-pick을 실행하기 전 상황으로 코드가 돌아간다.
      
#### 2) <b>continue</b> 
  <details>
  <summary>⭕간단 설명</summary>
  <div markdown="1">
  <pre>⭕ cherry-pick을 중단한다.
  git cherry-pick –abort 명령어를 사용해 cherry-pick을 중단하면, cherry-pick을 하기 전 상태로 돌아 갈 수 있다.
  </pre>
  </div>
  </details>
$ vi $file_path # 파일 수정
<br> $ git add $file_path
<br> $ git cherry-pick -continue
  <br>  👉 충돌이 발생한 코드를 에디터로 열어서 수정한다. 그 다음 git add <file path> 명령으로 수정된 코드를 추가한다. <br>
  이후, git cherry-pick-continue 명령을 실행하면 충돌을 수정한 코드가 반영되고, cherry-pick이 재개 된다.

### (2) Git Cherry-pick Merge
👉 merge 커밋을 cherry-pick 하고 싶으면 실행하는 명령어이다.
<br> $ git cherry-pick -m 1 <merge 커밋 해시>


