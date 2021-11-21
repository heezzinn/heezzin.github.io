heezzin.github.io
# 오픈소스SW개론

### 쉘 스크립트 getopt 및 getopts 명령어

  
  #### short 옵션의 특징
  
  short 옵션은 여러 가지 방법으로 사용할 수 있다.
  getopt/getopts 명령을 이용하지 않을 경우 스크립트가 복잡해질 수 있다.
  
  
  ```
  $ command -a -b -c              # 일반적인 옵션을 3개 준 경우

  $ command -abc                  # 옵션을 붙여 쓴 경우
  
  $ command -b -ac                # 옵션 순서가 바뀐 경우

  $ command -a xxx -b -c yyy      # 옵션 argument를 준 경우

  $ command -axxx -bcyyy          # argument와 옵션을 붙여 쓴 경우
  ```
  
  
  #### long 옵션의 특징
  
  long 옵션은 short 옵션과 달리 붙여 쓸 수가 없기 때문에 사용방법이 간단하여 직접 해석해서 처리하는 것이 어렵지 않다.
  
  `--posix`, `--wrning level` 등이 있다.
  
  ---
  
  
  
  ## getopts
  
  **getopts**는 옵션처리 명령어로 short 옵션을 처리한다.
  
  쉘이나 스크립트 파일, 함수에서 옵션을 사용할 때 옵션 해석 작업을 쉽게 도와준다.
  
  **OPTARG**와 **OPTIND**와 함께 사용한다.
  
  |변수이름|저장값|
  |------|---------|
  |**OPTARG**|옵션에 붙어있는 argument값|
  |**OPTIND**|옵션을 처리하고 남은 다음 index 번호|
  

  
  모든 명령들을 수행해주기 위해 while문, case문을 이용해 loop해준다.
  
  
  
  ```
  #!/bin/bash

# ===== main ===== #
while getopts "ahd" opt; do
    case $opt in
        a)
            -a 옵션이 들어오면 할 일
            ;;
        h)
            -h 옵션이 들어오면 할 일
            ;;
        t)
            -t 옵션이 들어오면 할 일
            ;;
        \?)	# 지정이외 옵션은 이 문자로 할당된다.
            echo $@ is not valid option
            exit 0
            ;;            
    esac
done
```
  
  
  #### OPTIND
  
  만약 명령에서 `-a`, `-b`, `-c` 세 개의 옵션을 사용한다면 getopts 명령에 설정하는 optstring 값은 `abc`가 된다.
  
  args에는 명령 실행시 사용된 인수 값들이 오는데 생략할 경우 `"$@"`가 사용된다.
  
  쉘이 처음 실행도면 $OPTIND 값은 1을 가리키고 getopts 명령이 실행될 때마다 다음 옵션은 index 값을 가리키게 됩니다
  
  
 ``` 
  # 다음 set 명령은 실제 "command -a -bc hello world" 명령을 실행했을 때와 같이
# positional parameters 를 설정합니다.
$ set -- -a -bc hello world
$ echo "$@"
-a -bc hello world

# 처음 $OPTIND 값은 1 로 -a 를 가리킵니다.
$ echo $OPTIND
1

$ getopts abc opt "$@"            # getopts 명령을 실행할 때마다 opt 변수값과
$ echo $opt, $OPTIND              # OPTIND 값이 변경되는 것을 볼 수 있습니다.
a, 2                              # 다음 옵션 "b" 의 index 값은 2 가 됩니다.

$ getopts abc opt "$@"            # 다음 옵션 "c" 는 "b" 와 붙여 쓰기를 하여 같은
$ echo $opt, $OPTIND              # OPTIND 값을 가집니다.
b, 2                              # ( sh 의 경우에는 b, 3 가 출력됩니다. )

$ getopts abc opt                 # args 부분을 생략하면 default 값은 "$@" 입니다.
$ echo $opt, $OPTIND
c, 3

# 옵션 스트링을 처리하고 난후 다음과 같이 shift 를 하면 나머지 명령 인수만 남게 됩니다.

$ echo "$@"
-a -bc hello world

$ shift $(( OPTIND - 1 ))
$ echo "$@"
hello world
```

#### OPTARG
옵션은 옵션 인수(argument)를 가질 수 있다.
옵션 스트링에서 해당 옵션 문자 뒤에 : 을 붙인다.
그러면 getopts 명령은 옵션인수 값를 $OPTARG 변수에 설정해 준다.

```
$ OPTIND=1
$ set -- -a xyz -b -c hello world

# 옵션 a 가 옵션인수를 가지므로 옵션 스트링으로 a: 를 설정하였습니다.
$ getopts a:bc opt
$ echo $opt, $OPTARG, $OPTIND        
a, xyz, 3                        # 옵션인수가 포함되므로 OPTIND 값이 3 이 되었음.

$ getopts a:bc opt
$ echo $opt, $OPTARG, $OPTIND
b, , 4

$ getopts a:bc opt
$ echo $opt, $OPTARG, $OPTIND
c, , 5
```

  
  #### loop문을 이용해 처리
  명령문에서 사용된 모든 옵션을 처리하기 위해서 다음과 같이 while과 case문을 이용한다.
  ```
  #!/bin/bash

while getopts "a:bc" opt; do
  case $opt in
    a)
      echo >&2 "-a was triggered!, OPTARG: $OPTARG"
      ;;
    b)
      echo >&2 "-b was triggered!"
      ;;
    c)
      echo >&2 "-c was triggered!"
      ;;
  esac
done

shift $(( OPTIND - 1 ))
echo "$@"
..................................

$ ./test.sh -a xyz -bc hello world
-a was triggered!, OPTARG: xyz
-b was triggered!
-c was triggered!
hello world
```

---

## getopt

goetopt 은 외부명령이다.
기본적으로 short 옵션과 long 옵션을 모두 지원한다.
옵션 인수를 가질 경우 : 문자를 사용한다.
인자가 있는 실행 옵션에서는 옵션 뒤에 :를 붙여준다.
ex) getopt_test.sh -m SET

getopt_test.sh -u jungfo

getopt_test.sh -m SET -u jungfo

인자가 없는 실행 옵션에서는 옵션만 정의해준다.
ex)
getopt_test.sh -f -q -v -d -g

getopt_test.sh -fqvdg

getopt_test.sh -m GET -u leo -fqvdg

```
# short 옵션 지정은 -o 옵션으로 합니다.
# ':' 에 따라서 옵션 -a 는 옵션 인수를 갖습니다. 
getopt -o a:bc

# long 옵션 지정은 -l 옵션으로 하고 옵션명은 ',' 로 구분합니다.
# ':' 에 따라서 옵션 --path 와 --name 은 옵션 인수를 갖습니다.
# 명령 라인에서 옵션 인수 사용은 "--name foo" 또는 "--name=foo" 두 가지 모두 가능합니다.
getopt -l help,path:,name: 

# 명령 마지막에는 -- 와 함께 "$@" 를 붙입니다.
getopt -o a:bc -l help,path:,name: -- "$@"
```


특징은 사용자가 입력한 옵션들을 case 문에서 사용하기 좋게 정렬해준다는 것이다.
```
#!/bin/bash

options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
-----------------------------------------------------

# 1. -a123 옵션이 -a '123' 로 분리.
# 2. -bc 옵션이 -b -c 로 분리.
# 3. 옵션에 해당하지 않는 hello.c 는 '--' 뒤로 이동
$ ./test.sh -a123 -bc hello.c
-a '123' -b -c -- 'hello.c'

# --path=/usr/bin 옵션이 --path '/usr/bin' 로 분리
# '--' 는 항상 끝부분에 붙는다.
$ ./test.sh --name foo --path=/usr/bin
--name 'foo' --path '/usr/bin' --

# '--' ( end of options ) 처리도 해줍니다.
$ ./test.sh -a123 -bc hello.c -- -x --yyy
 -a '123' -b -c -- 'hello.c' '-x' '--yyy'
 ```


### 리눅스 명령어 sed 및 awk

## sed 명령어

sed 명령어는 비 대화형 모드의 줄 단위 편집기이다.
vi 편집기처럼 직접 파일을 열어 고치지 않고 커맨드 창 또는 스크립트에서 동작을 하여 원하는 부분만 변경해준다는 특징이 있다.

#### - sed의 특징

sed는 쉘 또는 스크립트에서 파이프(|)와 같이 사용될 수 있는 명령어다.

정규표현식이 사용가능하기 때문에 정규표현식을 알면 sed를 이용하기 수월하다.

주의할 점은 정규표현식을 사용하기 때문에 특수문자 앞에 역 슬래시(\,＼)를 붙여주어야 합니다. \를 쓰면 리눅스에서는 역슬래시로 받아들입니다.

```
sed 's/\$man/man/g' test.txt
```

#### - sed 옵션 종류

  **e** : 
  sed를 사용하였을 때 출력되는 값을 보여준다. 
  이 옵션은 기본값으로 굳이 안 써도 되지만 다중 명령어를 쓸 때는 반드시 써야 한다.
  ```
  sed /man/p -e /girl/p 파일명   # man과 girl이 들어있는 줄을 한번 더 출력
  ```
 
  **i** : 
  변경되는 값을 실제로 파일에 저장하는 옵션입니다.
이 옵션은 출력되는 값이 없이 바로 실제 파일에 적용되는 것이 특징입니다.

 

n: 특정 값이 들어간 줄만 출력해주는 기능입니다. 주로 p 명령어와 사용된다고 합니다.

 

f: 스크립트를 파일로부터 읽어들이며 명령어를 지정하는 명령어라고 하는데 저는 거의 사용하지 않습니다.

 

4. sed 명령어

 

d: 줄 삭제 명령어

a\: 해당 값이 있는 줄 다음에 입력. 그냥 밑에 줄 하나 삽입되는 정도로 알고 있으면 됩니다.

i\: 줄 앞에 첨가 명령어

c\: 해당 줄을 변경하는 명령어

g: 한 줄에 해당하는 값이 여러개 있을 경우에 모두 변경하는 명령어. 이 명령어를 해당 줄의 제일 앞에 내용만 변경됩니다.

p: 조건에 부합하는 라인을 출력하는 명령어(쉘 스크립트 작성 시 자주 사용.)--개인적인 생각임

s/orgin/change/: 앞에 것을 뒤에 것으로 변경하는 명령어

 

5. sed 사용방법

 

기본적으로 p,d,s 옵션들을 가장 많이 사용한다고 알려져있습니다. 

세가지만 예시로 들겠습니다.

궁금하신 부분은 댓글로 남겨주세요~!

 

p 

기능: 출력

sed p 파일명: 파일 전체 출력 (cat과 같은 기능)

sed 3p 파일명: 3번째 줄 한번 더 출력

sed 3,4p 파일명: 3,4번째 줄 한번 더 출력

sed /man/p 파일명: man이 포함된 줄 한번 더 출력

sed -n 3p 파일명: 3번째 줄만 출력(n이 붙으면 조건에 해당하는 줄만 출력됨)

 

d 

기능: 삭제

sed 3d 파일명: 3번째 줄 삭제하고 나머지 줄 출력

sed /man/d 파일명: man이 포함된 줄 삭제 나머지 줄 출력

 

s

기능: 치환

sed 's/man/girl/g' 파일명: man을 girl로 치환

sed -n 's/man/girl/p' 파일명: man을 girl로 치환하고 치환된 줄을 출력

 

6. 조금 더 유용한 sed 이용방법

 

공백 줄 제거하기

sed '/^$/d' 파일명

 

모든 줄마다 공백을 추가하기

sed 'a\\' 파일명

 

주석이 처리된 줄 모두 삭제

sed '/^#/d' 파일명


## awk 명령어





Hello world!
