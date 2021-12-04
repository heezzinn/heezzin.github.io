오픈소스SW개론
# VimGolf


### 1번

![image](https://user-images.githubusercontent.com/94598039/144725723-070abe15-3f8f-454b-973f-e03e493c98a8.png)


문제 번호 : 5f0f5fbe280fbf000c233304

최고점 : 8

![과제1 mp4_000002788](https://user-images.githubusercontent.com/94598039/144727108-d5972bc6-bfae-4497-907c-bda32b653c23.gif)


내 점수 : 9

코드 : 
`
GWI"<End>"<Esc>ZZ
`

코드 설명 :
```
GW                          # 위치 이동
I"                          # 입력모드로 변환 후 " 삽입
<End>"                      # 끝으로 이동하여 " 삽입
<Esc>ZZ                     # 일반모드로 변환 후 종료
```

---

### 2번

![image](https://user-images.githubusercontent.com/94598039/144725746-c1a2cbea-2521-47f1-bed8-ea9471b48b48.png)

문제 번호 : 603ba26a01b4d00009c10a49

최고점 : 19

![과제2 mp4_000001982](https://user-images.githubusercontent.com/94598039/144727223-ba892272-0785-4086-9dcb-7fd5223afaaf.gif)


내 점수 : 27

코드 : 
`
:%s/sublime\|emacs/vim/g<CR>ZZ
`

코드 설명 :
```
:%s/sublime\|emacs/vim/g<CR>ZZ          # 'sublime'과 'emacs'를 'vim'으로 치환
```

---

### 3

![image](https://user-images.githubusercontent.com/94598039/144725786-06683b1a-4048-412b-b650-83a4b38ba0ba.png)


문제 번호 : 5f1063aa8361810006e73210

최고점 : 20

내 점수 : 45

코드 : `<Down><Down><Down><Right>ywi// m<CR><Down>// m<CR><Esc><Up><Up>bPG<Up><Right>ywbP:%s/m/TODO/g<CR>ZZ`


코드 설명 :
```
<Down><Down><Down><Right>             # 'Version' 앞까지 이동

yw                                    # 'Version' 단어 복사

i// m<CR>                             # 입력모드로 실행 후 '// m' 입력

<Down>                                # 'Debug' 앞으로 이동

// m<CR><Esc>                         # '// m' 입력 후 일반모드로 나감

<Up><Up>b                             # '//'와 ' m' 사이로 이동

P                                     # 복사해놨던 'Version' 붙여넣기

G<Up><Right>                          # 'Debug' 앞으로 이동

ywb                                   # 'Debug' 단어 복사 루 '//'와 'm' 사이로 이동

P                                     # 복사해놨던 'Debug' 붙여넣기

:%s/m/TODO/g<CR>ZZ                    # 'm'을 'TODO'로 치환
```

---

### 4

![image](https://user-images.githubusercontent.com/94598039/144725797-80ba4586-7f30-4d9a-abc2-512e1fd034c2.png)


문제 번호 : 9v0060da5177000000000209

최고점 : 34

내 점수 : 88

코드 :
  `
  :%s/y1/abs(y1)/g<CR>:.s/1/4/g<CR>:.s/k/g/g<CR><Up>:.s/1/3/g<CR>:.s/k/r/g<CR><Up>:.s/1/2/g<CR>:.s/k/b/g<CR>ZZ
  `
코드 설명 :
  ```
  :%s/y1/abs(y1)/g<CR>                # 'y1'을 검색하여 'abs(y1)'으로 치환
  
  :.s/1/4/g<CR>                       # (현재 4번째 라인) '1'을 '4'로 치환
  :.s/k/g/g<CR>                       # 'k'를 'g'로 치환
  
  <Up>                                # 3번째 라인으로 이동
  :.s/1/3/g<CR>                       # '1'을 '3'으로 치환
  :.s/k/r/g<CR>                       # 'k'를 'r'로 치환
  
  <Up>                                # 2번째 라인으로 이동
  :.s/1/2/g<CR>                       # '1'을 '2'로 치환
  :.s/k/b/g<CR>ZZ                     # 'k'를 'b'로 치환
  ```


  비주얼 블록 모드로 풀고 싶었지만..


  CTRL+V도 안먹고..
  

  CTRL+Q도 안먹어서..

  결국 검색, 치환으로 풀었습니다..

---

### 5
![image](https://user-images.githubusercontent.com/94598039/144725805-8b311a85-7e4c-4074-a5ed-55ab3b2d78f5.png)


문제 번호 : 6013804df3308e0009368f1c

최고점 : 19

내 점수 : 55

코드 : 
`
Gb<Right>i,,,<Esc>{<Up><Up><Up><Up>wywGb<Right>P{<Up><Up><Up>wywGb<Right>P{<Up><Up>wywGb<Right>P{<Up>wywGb<Right>PZZ
`

코드 설명 :
```
Gb<Right>                     # ""으로 이동
i,,,<Esc>                     # 입력모드에서 ',,,'삽입 후 일반모드로 변환

{<Up><Up><Up><Up>             # 복사할 단어 위치로 이동
wy                            # 'student_id' 단어 복사
wGb<Right>                    # 붙여넣을 위치 이동
P                             # 'student_id' 붙여넣기

{<Up><Up><Up>                 # 복사할 단어 위치 이동
wy                            # 'name' 단어 복사
wGb<Right>                    # 붙여넣을 위치 이동
P                             # 'name' 붙여넣기

{<Up><Up>                     # 복사할 단어 위치로 이동
wy                            # 'age' 단어 복사
wGb<Right>                    # 붙여넣을 위치 이동
P                             # 'age' 붙여넣기

{<Up>                         # 복사할 단어 위치로 이동
wy                            # 'score' 단어 복사
wGb<Right>                    # 붙여넣을 위치 이동
PZZ                           # 'score' 붙여넣기
```
