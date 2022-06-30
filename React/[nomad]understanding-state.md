# State 이해하기

2022.6.28 화
1. 낮에 비바람이 오락가락 하는게 날씨가 약간 미친거같다.
2. 화장품을 샀다. 쿨톤으로 도전


## state
`state` : 데이터를 저장할 수 있는 것을 의미
<br>1씩 증가하는 counter 변수 처럼 변경 되는 값을 저장할 수 있다.


### 첫 번째 구현방법 (비교적 효율적이지 못함)
변경할 때마다 렌더링 해주기
```
// 1. 변수 선언
let counter = 0;

// 2. 증가 함수 만들고, 버튼의 이벤트리스너 등록
function countUp() {
  counter = counter + 1;
  render();
}

function render() {
    ReactDOM.render(<Container />, root);
}

function Container() {
  return 
  <div>
    <h3>Total Click : {counter} </h3>
    <button onClick={countUp}>Click me</button>
  </div>
}

```

> 콘솔에서 counter의 값이 변경되기는 하지만 화면이 업데이트 X. 이는 브라우저가 처음 시작될 때 단한번만 render 되어 그려졌기 때문이다. render() 함수를 통해 업데이트를 할 때마다 렌더링 하면 업데이트를 할 수 있다.

그럼 여기서 의문점, 그럼 데이터가 바뀌는 부분이 있을 때마다 이걸 호출해줘야 할까? 그게 더 비효율적일 것 같은데..


### 두 번째 구현방법 (자동 reRender) 
```
function App() {
   const data = React.useState(0);
   console.log(data); 
}
```
React.useState() 함수를 사용 시 콘솔은 [0, f] 배열형태로 반환한다.
- 0 : `const counter=0`과 같은 초기값
- f : counter을 변경시킬 함수

이는 let 변수를 선언하고, function countUp() 함수를 쓰는 것과 같은 의미임.
data[0], data[1] 처럼 값에 접근할 수도 있으나, 비효율적이라 아래처럼 표기 가능

```
function App() {
    const [counter, modifier] = React.useState(0);
}
```

#### 그럼 굳이 modifier 역할(함수)은 왜 필요한가?
count = 123; 으로 재할당하면 안될까?
<br> modifier() 는 **값의 재할당**과 **리렌더링 과정**을 포함하고 있다. 값의 재할당, 렌더링 두 줄의 소스코드를 작성하는 것보다, 소스코드 한 줄로 값의 수정과 동시에 렌더링 트리거를 일으켜주는 함수이기 때문에 사용한다.

#### const는 상수변수라서 값 재할당이 안되는거 아닌가?
[공식문서](https://reactnative.dev/docs/intro-react#:~:text=You%20might%E2%80%99ve%20noticed,of%20isHungry.) 에 따르면, modifier 함수가 호출되면, 컴포넌트 자체가 리렌더링 된다. 
그럼 `App` 함수가 재실행 되면서 useState의 변수도 수정된 값으로 설정되는 것이다.
이게 react.js 의 핵심!!

#### 값을 변경하는 다른 방법
현재 state 값을 이용해 다음 state를 계산해내는 방법 중 첫 번째 방법보다 두 번째 방식으로 적는게 더 안전하다.
현재값을 이용해서 계산한다는 것을 보장하기 때문이다.
```
setCounter(counter + 1);                // 직접 값 전달하기
setCounter((current) => current + 1);   // 함수 이용하기
setFlipped((current) => !current);      // boolean 타입
```

<br><br>

### 변경된 부분만 업데이트를 해준다? (중요!)
vanilla.js와 다른 React.js의 장점 : UI의 변경되는 부분만 업데이트 해준다는 점
<br>= 매우 `interactive`한 어플리케이션을 만들 수 있음

>React DOM은 해당 엘리먼트와 그 자식 엘리먼트를 이전의 엘리먼트와 비교하고 DOM을 원하는 상태로 만드는데 필요한 경우에만 DOM을 업데이트합니다. (공식문서)

좀 더 찾아보니, React는 컴포넌트나 DOM요소가 타입만 바뀌지 않는다면, 최대한 렌더링을 하지 않는 방향으로 **최소한의 변경사항만 업데이트** 한다고 한다.
React가 실제 DOM이 아닌 가상DOM을 이용하기 때문인데, `가상DOM`이란 실제DOM을 만들 때 필요한 정보를 담은 객체이다.
업데이트 할 내용이 생기면 가상DOM 내부의 프로퍼티만 수정한 뒤, 모든 노드에 걸친 업데이트가 끝나면 그 때 단한번 실제 DOM으로의 렌더링을 시도한다. 이로서 DOM 조작의 오버헤드를 줄일 수 있게 된다.

<br><br>

---


### 공부하면서 궁금했던 것
- what is rendering?


### reference
- virtual-DOM : https://github.com/Matt-Esch/virtual-dom
- react의 렌더링 알고리즘 : https://www.huskyhoochu.com/virtual-dom/

### 기타
- state 연습문제 : 단위변환기 (link)