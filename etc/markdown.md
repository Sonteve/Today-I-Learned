# Markdown(마크다운) 사용법 이해하기

# 1.마크다운에 관하여

마크다운(markdown)은 일반 텍스트 기반의 경량 마크업 언어다. 일반 텍스트로 서식이 있는 문서를 작성하는 데 사용되며, 일반 마크업 언어에 비해 문법이 쉽고 간단한 것이 특징이다. HTML 등 서식 문서로 쉽게 변환이 가능하기때문에 소프트웨어 배포시 관련 정보를 기록하는데 많이 사용된다. 깃헙 repository에 존재하는 README.md파일을 모르는 사람이 없을만큼 유명하다. 한번 알아놓으면 문서화 및 협업도구로서 유용할것이다.

# 2.태그

## 2.1 제목

- 제목을 표시할때 쓰며 `#`이 여러개 붙을수록 세부 제목(부 제목)이 된다.
- `#`을 하나만썼을경우 제일 중요한 제목이되며 자동으로 밑줄이 생긴다.

> 작성법

```code
# Heading1
## Heading1
### Heading1
#### Heading1
##### Heading1
```

> 결과

# Heading1

## Heading1

### Heading1

#### Heading1

##### Heading1

<br/>
<br/>

## 2.2 인용구

- `>` 로 시작한다.
- `>>`, `>>>`등의 방법으로 사용할경우 여러 depth로 사용가능하다.

> 작성법

```code
> 블록 인용구
>> depth1
>>> depth2
```

> 결과

> 블록 인용구
>
> > depth1
> >
> > > depth2

<br/>
<br/>

## 2.3 순서가없는 리스트

- `*`나 `-`를 붙이고 한칸 띄워 쓰면 리스트 형식으로 들여쓰기가 된다.

> 작성법

```code
커피:
* 에스프레소
* 아메리카노
* 카페라뗴
- 콜드브루
- 화이트초코라떼
```

> 결과

커피:

- 에스프레소
- 아메리카노
- 카페라뗴

* 콜드브루
* 화이트초코라떼

<br/>
<br/>

## 2.4 숫자표기 리스트

- 원하는 숫자를 붙여서 `순서가 있는 리스트`를 표시할 수 있다.

> 작성법

```code
오늘할일:
1. 씻기
2. 밥먹기
3. 출근하기
```

> 결과

오늘할일:

1. 씻기
2. 밥먹기
3. 출근하기

<br/>
<br/>

## 2.5 링크

- 클릭시 원하는 주소르 이동할수있는 링크를 만들어준다.
- `[]`안에 링크에 들어갈 텍스트를 입력하고 `()`안에 실제 주소를 입력해준다.

> 작성법

```code
[여기 클릭](https://google.com)
```

> 결과

[여기 클릭](https://google.com)

<br/>
<br/>

## 2.6 이미지

- 앞에 !를붙이고 []에 이미지에 대한 설명을 적고 ()안에 이미지 주소를 추가하면 이미지를 표현할 수 있다.

> 작성법

```code
![맥북 이미지](https://post-phinf.pstatic.net/MjAyMDA3MDlfMjQ5/MDAxNTk0MjcyODE0Mzc5.kEPwGSkoaj9bn6mt9_na4RZrTcaxNyLsqW01osqwrv4g.NaLHwsTKxiWGtQt1F5SqZfnQOYKvRriObW-tjv44QAgg.JPEG/DSC04981.jpg?type=w1200)
```

> 결과

![맥북 이미지](https://post-phinf.pstatic.net/MjAyMDA3MDlfMjQ5/MDAxNTk0MjcyODE0Mzc5.kEPwGSkoaj9bn6mt9_na4RZrTcaxNyLsqW01osqwrv4g.NaLHwsTKxiWGtQt1F5SqZfnQOYKvRriObW-tjv44QAgg.JPEG/DSC04981.jpg?type=w1200)

<br/>
<br/>

## 2.7 표(테이블)

- 특수문자 `pipe(|)`를 이용해서 테이블의 모양처럼 작성하면 간단하게 테이블을 표현할수있다.

- `:`를 어떻게 붙이냐에 따라 테블 셀안의 텍스트의 정렬을 원하는대로 바꿀수 있다.

> 사용법

```code
|title|content|description|
|:------|:--------:|---------:|
|왼쪽|가운데|오른쪽|
|왼쪽|가운데|오른쪽|
|왼쪽|가운데|오른쪽|
```

> 결과

| title | content | description |
| :---- | :-----: | ----------: |
| 왼쪽  | 가운데  |      오른쪽 |
| 왼쪽  | 가운데  |      오른쪽 |
| 왼쪽  | 가운데  |      오른쪽 |

<br/>
<br/>

## 2.8 코드블럭

- \```(백틱 3개)를 코드의 시작과 끝을 감싸주면 가독성이 좋은 코드블럭안에 표시된다.
- 작성된 코드의 language를 같이 적어주면 지원되는 언어일 경우 문법에맞게 색이 변한다.

> 작성법

````code
```javscript
const obj = {
    name : 'steve',
    age: 30
}
console.log('test);
``` .
````

> 결과

```typescript
const obj = {
    name : 'steve',
    age: 30
}
console.log('test);
```

<br/>
<br/>

## 2.9 강조

- 원하는 텍스트의 앞뒤를 \*를 한번 감쌀경우 Italic으로 표시된다. 두번감쌀경우 Bold로 표시되고
  세번감쌀경우 Italic과 Bold가 합쳐지게된다.

> 사용법

```code
Normal
안녕하세요 저는 Sonteve입니다.

Italic
안녕하세요 저는 *Sonteve*입니다.

Bold
안녕하세요 저는 **Sonteve**입니다.

Italic + Bold
안녕하세요 저는 ***Sonteve***입니다.
```

> 결과

Normal  
안녕하세요 저는 Sonteve입니다.

Italic  
안녕하세요 저는 *Sonteve*입니다.

Bold  
안녕하세요 저는 **Sonteve**입니다.

Italic + Bold  
안녕하세요 저는 **_Sonteve_**입니다.
