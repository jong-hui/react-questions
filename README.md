## React-questions

리액트를 사용하며 겪었던 문제들을 풀어내 리액트의 이해를 돕습니다.  
하루 한 문제 목표 🤔  
컨트리뷰트는 대환영입니다. 🤗


###### 1. MyInput은 text입력시마다 왜 계속 렌더링 되나요?

```javascript
const MyInput = (props) => {
  console.log('why render every times T.T')

  return (
    <input {...props} />
  )
}

const App = () => {
  const [text, setText] = useState("")

  const handleChangeText = (e) => {
    setText(e.target.value)
  }
  
  return (
    <>
      <MyInput onChange={handleChangeText} />
    </>
  )
}
```

<details><summary><b>정답</b></summary>
<p>

#### `handleChangeText`는 매 렌더링시에 새로운 주소에 *다시* 저장됩니다.
컴포넌트가 렌더링 될 때는 state, props가 변할 때입니다. javascript에서는 object를 주소값으로 비교합니다. `handleChangeText`는 Function object입니다. 매 렌더링시에 `handleChangeText`는 새로운 주소값에 할당되어 `MyInput`의 props(onChange)가 변한 것으로 React는 판단합니다.<br>
*이 문제를 해결하기 위해* (일반적으로)[React.useCallback](https://ko.reactjs.org/docs/hooks-reference.html#usecallback) 혹은 [React.memo](https://ko.reactjs.org/docs/react-api.html#reactmemo)을 사용할 수 있습니다.


</p>
</details>

---

###### 2. MyList는 add버튼 클릭시에 렌더링이 왜 *안* 되나요?

```javascript
const MyList = ({datas}) => {
  return (
    <ul>
      {datas.map(x => (
        <li key={`li-${x}`}>
          {x}
        </li>
      ))}
    </ul>
  )
}

const App = () => {
  const [datas, setDatas] = useState(['hello', 'hola', 'bonjour'])

  const addData = () => {
    datas.push('안녕')

    setDatas(datas)
  }

  return (
    <>
      <button onClick={addData}>add</button>
      <MyList datas={datas} />
    </>
  )
}
```

<details><summary><b>정답</b></summary>
<p>

#### `datas`는 사실 바뀌지 않았습니다.
`datas`에 push를 해도 `datas`의 주소값이 바뀌진 않습니다. 하지만, React는 주소값이 바뀌는 걸 통해 변화를 감지합니다.<sup><a href="#1-myinput은-text입력시마다-왜-계속-렌더링-되나요">\*1번 문제 참고</a></sup> 즉, 리액트는 `datas`가 바뀌지 않은 걸로 판단하여 똑똑하게 렌더링하지 않은 겁니다.<br>
*이 문제를 해결하기 위해선* [불변성](https://www.google.com/search?q=Immutability&oq=Immutability&aqs=chrome..69i57j69i60j69i61.242j0j7&sourceid=chrome&ie=UTF-8)에 대해 알아야합니다. 또, [전개 구문](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)은 불변성을 지키는 똑똑한 방법입니다.

</p>
</details>

---

###### 3. MyTodoList의 checkbox는 왜 움직이지 않나요?
![3](https://user-images.githubusercontent.com/42797995/87059853-c5c7ed00-c244-11ea-9c2d-ac199bc50fee.gif)

```javascript
const MyTodoList = ({todos}) => {
  return (
    <>
      {todos.map((todo, index) => (
        <li key={index}>
          {todo.name}
          <input type="checkbox" />
        </li>
      ))}
    </>
  )
}

const App = () => {
  const [todos, setTodos] = useState([
    { name: 'homework' },
    { name: 'washing dishes' },
    { name: 'make todoList' },
  ])

  const handleClick = () => {
    setTodos([...todos].reverse())
  }

  return (
    <>
      <button onClick={handleClick}>I don't care, just shake!</button>
      <ul>
        <MyTodoList todos={todos} />
      </ul>
    </>
  )
}
```

<details><summary><b>정답</b></summary>
<p>

#### 리스트 렌더링에서 `인덱스`를 `key`로 지정하지 마세요.

React는 리스트 렌더링에서 `key`로 어떤 항목을 변경, 추가 또는 삭제할지 판단합니다. index를 key로 사용한다면, 우리가 배열을 `reverse`했을 때에 React는 이렇게 판단합니다. 아하! `homework`가 `make todoList`라는 문자열로 바뀌었구나? 그럼 문자열만 바꿔주면 되겠다!<br/>
이 문제를 해결하기 위해 `key`에 `id`를 넘겨주세요. 고유한 `id`값을 넘겨주면 원하는 대로 작동할 것 입니다. 읽어보면 좋은 글로는, [왜 key가 필요한가에 대한 더 자세한 설명](https://ko.reactjs.org/docs/reconciliation.html#recursing-on-children) 그리고 [인덱스를 key로 사용할 경우 부정적인 영향에 대한 상세 설명](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)이 있습니다.

</p>
</details>

---

###### 4. MyProduct는 왜 에러가 나나요?
![3](https://user-images.githubusercontent.com/42797995/87059853-c5c7ed00-c244-11ea-9c2d-ac199bc50fee.gif)

```javascript
const MyProduct = ({ product, isTimeSale }) => {
  const [salePercent, setSalePercent] = useState(0)

  if (isTimeSale) {
    useEffect(() => {
      setSalePercent(getSalePercent(product.id))
    }, [])
  }

  return (
    <>
      <li>
        {product.name}
        {salePercent > 0 ?`${salePercent}% sale!!` : ''}
      </li>
    </>
  )
}


const App = () => {
  const [title, setTitle] = useState("is the title")

  if (title !== "") {
    useEffect(() => {
      console.log(title)
    })
  }

  const [content, setContent] = useState("is the content")

  if (content !== "") {
    useEffect(() => {
      console.log(content)
    })
  }

  return (
    ...
  )
}
```

<details><summary><b>정답</b></summary>
<p>

#### `조건문` 안에 `Hook(use~)`를 사용하지 마세요!

정확한 의미로는, **무조건** 함수의 최상위에서만 `Hook`을 사용해야만 합니다. 이는 `React`가 `Hook`의 상태를 올바르게 유지할 수 있게 도와줍니다. 더 설명하자면, React는 `Hook`이 **실행 순서**에 의존해서 `state`(이는 `useEffect`의 함수도 될 수 있습니다)를 저장합니다.<br/>
예를 들어, 위의 예제에서는(조건문을 빼면), `["is the title", () => { console... }, "is the content", () => { console... }]`의 상태로 React는 저장합니다. 만약 조건문의 조건이 `false`가 되어 `Hook`의 실행 순서가 변한다면 어떨까요? `state`와 `Hook`을 연결시켜주지 못해 버그가 발생합니다.<br>
*이 문제를 해결하려면* 조건문을 `useEffect` 안으로 넣으세요! 오직 그것 뿐입니다. 더 중요한 건, 모든 `Hook`은 최상위에서만 호출하세요! 읽어보면 좋은 글로는, [Hook의 첫번째 규칙](https://itnext.io/the-first-rule-of-react-hooks-in-plain-english-1e0d5ae32009) 그리고 [Hook의 규칙](https://ko.reactjs.org/docs/hooks-rules.html#explanation)이 있습니다.

</p>
</details>

---
