오픈소스SW개론

# 쉘 스크립트 getopt 및 getopts 명령어

  
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

```
getopt_test.sh -m SET

getopt_test.sh -u jungfo

getopt_test.sh -m SET -u jungfo
```

인자가 없는 실행 옵션에서는 옵션만 정의해준다.
```
getopt_test.sh -f -q -v -d -g

getopt_test.sh -fqvdg

getopt_test.sh -m GET -u leo -fqvdg
```


```
# short 옵션 지정은 -o 옵션으로 한다.
# ':' 에 따라서 옵션 -a 는 옵션 인수를 갖는다. 
getopt -o a:bc

# long 옵션 지정은 -l 옵션으로 하고 옵션명은 ',' 로 구분한다.
# ':' 에 따라서 옵션 --path 와 --name 은 옵션 인수를 갖는다.
# 명령 라인에서 옵션 인수 사용은 "--name foo" 또는 "--name=foo" 두 가지 모두 가능하다.
getopt -l help,path:,name: 

# 명령 마지막에는 -- 와 함께 "$@" 를 붙인다.
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


# 리눅스 sed 및 awk 명령어

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
  변경되는 값을 실제로 파일에 저장하는 옵션이다.
  이 옵션은 출력되는 값이 없이 바로 실제 파일에 적용되는 것이 특징이다.


  **n** : 
  특정 값이 들어간 줄만 출력해주는 기능입니다. 주로 p 명령어와 사용된다고 합니다.

 
  **f** : 
  스크립트를 파일로부터 읽어들이며 명령어를 지정하는 명령어라고 하는데 저는 거의 사용하지 않는다.

 
 
#### - sed 명령어

 
  d : 줄 삭제 명령어
  
  a\ : 해당 값이 있는 줄 다음에 입력. 그냥 밑에 줄 하나 삽입되는 정도로 알고 있으면 됩니다.

  i\ : 줄 앞에 첨가 명령어

  c\ : 해당 줄을 변경하는 명령어

  g : 한 줄에 해당하는 값이 여러개 있을 경우에 모두 변경하는 명령어. 이 명령어를 해당 줄의 제일 앞에 내용만 변경됩니다.

  p : 조건에 부합하는 라인을 출력하는 명령어(쉘 스크립트 작성 시 자주 사용.)--개인적인 생각임

  s/orgin/change/ : 앞에 것을 뒤에 것으로 변경하는 명령어

 
#### - sed 사용방법


 p (출력 명령어)
  
  ```
  sed p 파일명         # 파일 전체 출력 (cat과 같은 기능)

  sed 3p 파일명        # 3번째 줄 한번 더 출력

  sed 3,4p 파일명      # 3,4번째 줄 한번 더 출력

  sed /man/p 파일명    # man이 포함된 줄 한번 더 출력

  sed -n 3p 파일명     # 3번째 줄만 출력(n이 붙으면 조건에 해당하는 줄만 출력됨)
```
 

d (삭제 명령어) 

```
  sed 3d 파일명        # 3번째 줄 삭제하고 나머지 줄 출력

  sed /man/d 파일명    # man이 포함된 줄 삭제 나머지 줄 출력
```
 

s (치환 명령어)

```
  sed 's/man/girl/g' 파일명      # man을 girl로 치환

  sed -n 's/man/girl/p' 파일명   # man을 girl로 치환하고 치환된 줄을 출력
```
 

공백 줄 제거하기
```
sed '/^$/d' 파일명
```
 

모든 줄마다 공백을 추가하기
```
sed 'a\\' 파일명
```
 

주석이 처리된 줄 모두 삭제
```
sed '/^#/d' 파일명
```

---

## awk 명령어


awk 명령어는 기능을 개발한 사람들의 이니셜을 조합하여 (Aho + Weinberger + Kernighan) 명령어 이름이 awk로 사용됩니다.
 
awk는 데이터를 조작 및 연산하고 생성하는 데 사용되는 스크립팅 언어이다. 
awk 명령 프로그래밍 언어는 컴파일 할 필요가 없으며 변수, 숫자 함수, 문자열 함수 및 논리 연산자를 사용할 수 있습니다.
awk는 프로그래머가 문서의 각 줄에서 검색 할 텍스트 패턴과 문서에서 일치하는 항목을 찾을 때 수행 할 작업을 정의하는 문장 형식으로 작지만 효과적인 프로그램을 작성할 수있는 유틸리티입니다. 
awk는 주로 패턴 스캐닝 및 처리에 사용됩니다. 
하나 이상의 파일을 검색하여 지정된 패턴과 일치하는 행이 있는지 확인한 후 연관된 조치를 수행합니다.
 
1. AWK 작업 :
 (a) 파일을 한 줄씩 스캔합니다.
 (b) 각 입력 줄을 필드로 나눕니다.
 (c) 입력 줄 / 필드를 패턴과 비교합니다.
 (d) 일치하는 줄에서 작업을 수행합니다.
 
awk 스크립트를 사용하려면 다음과 같이 작은 따옴표로 묶은 중괄호를 사용해야 합니다.
사용법
 awk [option] 'pattern {action}' [input-file]
 
2. awk 옵션
 -F fs                    파일 구분 기호를 지정합니다. 
 -f file                   awk 스크립트가 포함 된 파일 을 지정합니다. 
 -v var = value       변수를 선언합니다.
 
3. awk 사용 예제
$ cat testfile 

출력
This is a apple
linux is best os
I am the best programmer.
My hobby is riding a bicycle.
Coding is so much fun.
 
각 줄의 첫 단어를 출력
$ awk '{print $1}' testfile

출력
This
linux
I
My
Coding
 
-F 옵션을 사용하여 ':' 구분자 기준으로 첫 번째 필드 출력
$ awk -F : '{print $ 1}' /etc/passwd

출력
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
uucp
operator
games
gopher
ftp
nobody
dbus
vcsa
abrt
haldaemon
ntp
saslauth
postfix
sshd
tcpdump
cobin66
 
여러 명령 실행은 ';' 으로 구분합니다.
첫 번째 명령은 $3 필드 값을 변경하고 두 번째 명령은 전체 줄을 출력합니다.
$ echo "simple is best" | awk '{$3 = "good"; print $0}'

출력
simple is good
 
파일에서 스크립트 읽기
awk 스크립트를 파일에 입력하고 -f 옵션을 사용하여 해당 파일을 지정할 수 있습니다.
$ cat scriptfile 

출력
{print $1 " &&&& " $2 " " $3}
$ awk -f scriptfile testfile 

출력
This &&&& is a
linux &&&& is best
I &&&& am the
My &&&& hobby is
Coding &&&& is so
 
일치하는 패턴의 전체 행 출력
/패턴/ 형태로 입력합니다.
$ awk '/Coding/ {print $0}' testfile 

출력
Coding is so much fun.
 
패턴별로 열 인쇄
패턴 일치가 성공하면 AWK는 기본적으로 전체 레코드를 인쇄합니다. 그러나 특정 필드 만 인쇄하도록 AWK에 지시 할 수 있습니다. 예를 들어, 다음 예는 패턴 일치가 성공하면 세 번째 및 네 번째 필드를 인쇄합니다.
$ awk '/a/ {print $3 "\t" $4}' testfile 

출력
a	apple
the	best
is	riding
 
일치 패턴 계산 및 인쇄
패턴 일치에 성공한 줄 수를 세고 인쇄 할 수있는 예를 살펴 보겠습니다.
$ awk '/best/{++cnt} END {print "Count = ", cnt}' testfile

출력
Count =  2
 
20자를 초과하는 줄 인쇄
20자를 초과하는 줄만 인쇄하겠습니다.
$ awk 'length($0) > 20' testfile

출력
I am the best programmer.
My hobby is riding a bicycle.
Coding is so much fun.
 
ARGV
명령 행 인수를 저장하는 배열입니다. 배열의 유효한 인덱스 범위는 0에서 ARGC-1입니다.
$ awk 'BEGIN { 
   for (i = 0; i < ARGC - 1; ++i) {
      printf "ARGV[%d] = %s\n", i, ARGV[i]
   }
}' one two three four

출력
ARGV[0] = awk
ARGV[1] = one
ARGV[2] = two
ARGV[3] = three
 
파일 이름
현재 파일 이름을 나타냅니다.
$ awk 'END {print FILENAME}' testfile 

출력
testfile
 
NF
현재 레코드의 필드 수를 나타냅니다. 예를 들어 다음 예제는 필드가 두 개 이상 포함 된 행만 인쇄합니다.
$ cat testfile | awk '{print NF}'

출력
4
4
5
6
5
$ cat testfile | awk 'NF > 4'

출력
I am the best programmer.
My hobby is riding a bicycle.
Coding is so much fun.
 
awk script 파일로 문자열 가공하기
$ vi testlog2

2020-07-13 09:21:31 KST [ISP.0090.0003C] BIS_ITS_Formation_BY100 [ BIS TO ITS ] ###################################################
2020-07-13 09:21:31 KST [ISP.0090.0003C] BIS_ITS_Formation_BY100 [ BIS TO ITS ] # Service Start Time = 20200713 092131288
2020-07-13 09:21:31 KST [ISP.0090.0003C] BIS_ITS_Formation_BY100 [ BIS TO ITS ] # The service was consumed a 42 millisecond
2020-07-13 09:21:31 KST [ISP.0090.0003C] BIS_ITS_Formation_BY100 [ BIS TO ITS ] # Service End Time = 20200713 092131330
2020-07-13 09:21:31 KST [ISP.0090.0003C] BIS_ITS_Formation_BY100 [ BIS TO ITS ] ###################################################
2020-07-13 09:21:33 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] ###################################################
2020-07-13 09:21:33 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # Service Start Time = 20200713 092133094
2020-07-13 09:21:33 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # Total Count = 3
2020-07-13 09:21:33 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # The service was consumed a 17 millisecond
2020-07-13 09:21:33 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # Service End Time = 20200713 092133111
2020-07-13 09:21:33 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] ###################################################
2020-07-13 09:21:38 KST [ISP.0090.0003C] CMM_AML_Amail_Send_mysql [ CMM TO AML ] ###################################################
2020-07-13 09:21:38 KST [ISP.0090.0003C] CMM_AML_Amail_Send_mysql [ CMM TO AML ] # Service Start Time = 20200713 092138072
2020-07-13 09:21:38 KST [ISP.0090.0003C] CMM_AML_Amail_Send_mysql [ CMM TO AML ] # Total Count = 19
2020-07-13 09:21:38 KST [ISP.0090.0003C] CMM_AML_Amail_Send_mysql [ CMM TO AML ] # The service was consumed a 82 millisecond
2020-07-13 09:21:38 KST [ISP.0090.0003C] CMM_AML_Amail_Send_mysql [ CMM TO AML ] # Service End Time = 20200713 092138154
2020-07-13 09:21:38 KST [ISP.0090.0003C] CMM_AML_Amail_Send_mysql [ CMM TO AML ] ###################################################
2020-07-13 09:21:39 KST [ISP.0090.0003C] CMM_SMS_SMS_Send [ CMM TO EAI ] ###################################################
2020-07-13 09:21:39 KST [ISP.0090.0003C] CMM_SMS_SMS_Send [ CMM TO EAI ] # Service Start Time = 20200713 092139062
2020-07-13 09:21:39 KST [ISP.0090.0003C] CMM_SMS_SMS_Send [ CMM TO EAI ] # Total Count = 6
2020-07-13 09:21:39 KST [ISP.0090.0003C] CMM_SMS_SMS_Send [ CMM TO EAI ] # The service was consumed a 51 millisecond
2020-07-13 09:21:39 KST [ISP.0090.0003C] CMM_SMS_SMS_Send [ CMM TO EAI ] # Service End Time = 20200713 092139113
2020-07-13 09:21:39 KST [ISP.0090.0003C] CMM_SMS_SMS_Send [ CMM TO EAI ] ###################################################
2020-07-13 09:21:42 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] ###################################################
2020-07-13 09:21:42 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # Service Start Time = 20200713 092142129
2020-07-13 09:21:42 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # Total Count = 5
2020-07-13 09:21:42 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # The service was consumed a 21 millisecond
2020-07-13 09:21:42 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] # Service End Time = 20200713 092142150
2020-07-13 09:21:42 KST [ISP.0090.0003C] CMM_TLK_Talk_Send [ CMM TO TLK ] ###################################################
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] ###################################################
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] # Service Start Time = 20200713 092148085
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] # Total Head Count = 6
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] # Total Detail Count = 6
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] # The service was consumed a 102 millisecond
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] # Service Start Time = 20200713 092148187
2020-07-13 09:21:48 KST [ISP.0090.0003C] SRV_ESH_QnA_Register_Modify [ SRV TO ESH ] ###################################################
2020-07-13 09:21:56 KST [ISP.0090.0003C] BIS_ESH_Broad_Order [ BIS TO ESH ] ###################################################
2020-07-13 09:21:56 KST [ISP.0090.0003C] BIS_ESH_Broad_Order [ BIS TO ESH ] # Service Start Time = 20200713 092156027
2020-07-13 09:21:56 KST [ISP.0090.0003C] BIS_ESH_Broad_Order [ BIS TO ESH ] # Total_Count = 30
2020-07-13 09:21:56 KST [ISP.0090.0003C] BIS_ESH_Broad_Order [ BIS TO ESH ] # The service was consumed a 359 millisecond
2020-07-13 09:21:56 KST [ISP.0090.0003C] BIS_ESH_Broad_Order [ BIS TO ESH ] # Service End Time = 20200713 092156386
2020-07-13 09:21:56 KST [ISP.0090.0003C] BIS_ESH_Broad_Order [ BIS TO ESH ] ###################################################
2020-07-13 09:22:04 KST [ISP.0090.0003C] CST_AML_Customer_Person [ CST TO AML ] ###################################################
2020-07-13 09:22:04 KST [ISP.0090.0003C] CST_AML_Customer_Person [ CST TO AML ] # Service Start Time = 20200713 092204071
2020-07-13 09:22:04 KST [ISP.0090.0003C] CST_AML_Customer_Person [ CST TO AML ] # Total Count = 17
2020-07-13 09:22:04 KST [ISP.0090.0003C] CST_AML_Customer_Person [ CST TO AML ] # The service was consumed a 162 millisecond
2020-07-13 09:22:04 KST [ISP.0090.0003C] CST_AML_Customer_Person [ CST TO AML ] # Service End Time = 20200713 092204233
2020-07-13 09:22:04 KST [ISP.0090.0003C] CST_AML_Customer_Person [ CST TO AML ] ###################################################                                                                                                                                   
$ vi test.awk


/Service Start Time = / {
service = $5;
ms[service] = substr($17, 7)
next
}
/# Total Count/ {
count[service] = $NF;
next
}
/# The service was consumed a / {
timestamp = $1 "T" $2 "." ms[service] "+09:00";
elapsed_ms = $17;
if ( service ) printf "%s host=%s service=%s count=%d elapsed_ms=%d\n", timestamp, host, service, count[service], elapsed_ms
fflush();
}
$ cat testlog2 | awk -v host=$( hostname ) -f test.awk

출력
2020-07-13T09:21:31.288+09:00 host=MacBook-Pro.local service=BIS_ITS_Formation_BY100 count=0 elapsed_ms=42
2020-07-13T09:21:33.094+09:00 host=MacBook-Pro.local service=CMM_TLK_Talk_Send count=3 elapsed_ms=17
2020-07-13T09:21:38.072+09:00 host=MacBook-Pro.local service=CMM_AML_Amail_Send_mysql count=19 elapsed_ms=82
2020-07-13T09:21:39.062+09:00 host=MacBook-Pro.local service=CMM_SMS_SMS_Send count=6 elapsed_ms=51
2020-07-13T09:21:42.129+09:00 host=MacBook-Pro.local service=CMM_TLK_Talk_Send count=5 elapsed_ms=21
2020-07-13T09:21:48.085+09:00 host=MacBook-Pro.local service=SRV_ESH_QnA_Register_Modify count=0 elapsed_ms=102
2020-07-13T09:21:56.027+09:00 host=MacBook-Pro.local service=BIS_ESH_Broad_Order count=0 elapsed_ms=359
2020-07-13T09:22:04.071+09:00 host=MacBook-Pro.local service=CST_AML_Cust




Hello world!
