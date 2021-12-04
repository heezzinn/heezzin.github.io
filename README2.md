오픈소스SW개론
# VimGolf


### 1번

문제 번호 : 5f0f5fbe280fbf000c233304

최고점 : 8

내 점수 : 9

코드 : 
`
GWI"<End>"<Esc>ZZ
`

코드 설명 :
```
```

---

### 2번


문제 번호 : 603ba26a01b4d00009c10a49

최고점 : 19

내 점수 : 27

코드 : 
`
:%s/sublime\|emacs/vim/g<CR>ZZ

---

### 3

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


문제 번호 : 9v0060da5177000000000209

최고점 : 34

내 점수 : 



---

### 5


문제 번호 : 6013804df3308e0009368f1c

최고점 : 19

내 점수 : 55

코드 : 
`
Gb<Right>i,,,<Esc>{<Up><Up><Up><Up>wywGb<Right>P{<Up><Up><Up>wywGb<Right>P{<Up><Up>wywGb<Right>P{<Up>wywGb<Right>PZZ
`

코드 설명 :
```
```
