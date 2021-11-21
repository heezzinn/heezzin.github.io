heezzin.github.io
# 오픈소스SW개론

### 쉘 스크립트 getopt 및 getopts 명령어

  getopt 옵션처리 명령어
  
  쉘이나 스크립트 파일, 함수에서 옵션을 사용할 때 옵션 해석 작업을 쉽게 도와주는 명령어
  
  옵션에는 short 옵션과 long 옵션이 있는데 getopts 명령은 short옵션을 처리한다.
  
  
  #### short 옵션의 특징
  
  > getopts 명령을 이용하지 않을 경우 스크립트가 복잡해질 수 있음
  
  
  예제
  
  일반적으로 옵션을 3개 준 경우
  ```
  $ command -a -b -c
  ```
      
  옵션을 붙여 쓴 경우와 순서가 바뀐 경우
  ```
  $ command -abc
  $ command -b -ac
  ```
  
  옵션 인수를 준 경우
  ```
  $ command -a xxx -b -c yyy
  ```
  
  옵션 인수와 옵션을 붙여 쓴 경우
  ```
  $ command -axxx -bcyyy
  ```
  
  
  #### long 옵션의 특징
  
  long 옵션은 short 옵션과 달리 붙여 쓸 수가 없기 때문에 사용방법이 간단하여 직접 해석해서 처리하는 것이 어렵지 않다.
  
  #### Option string
  
  쉘 스크립트를 실행하면 명령어와 옵션 값들은 자동으로 쉘 변수 $0 .. N 으로 저장된다.
    
  그리고 -a , -p 는 명령행 옵션으로 이를 처리하기 위해서는 getopt 함수를 OPTARG, OPTIND와 함께 사용하면 된다.
  OPTARG는 getops 했을 때 붙어있는 인자값이다.
  OPTIND는 getopt로 처리하고 남은 다음 처리할 인덱스 번호가 된다.
  
  실행 예제
  
  ```
  $./getopts.sh-a-p 'testArg'/home/user1/docs
  ```

---

### 리눅스 명령어sed 및 awk



Hello world!
