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
이 문제를 해결하기 위해 (일반적으로)[React.useCallback](https://ko.reactjs.org/docs/hooks-reference.html#usecallback) 혹은 [React.memo](https://ko.reactjs.org/docs/react-api.html#reactmemo)을 사용할 수 있습니다.


</p>
</details>

---

###### 1. MyList는 add버튼 클릭시에 렌더링이 왜 *안* 되나요?

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

const App =() => {
  const [datas, setDatas] = useState(['hello', 'hola', 'bonjour'])

  const addData = () => {
    datas.push('안녕')

    setDatas(datas)
  }

  return (
    <div className="App">
      <button onClick={addData}>add</button>
      <MyList datas={datas} />
    </div>
  )
}
```

<details><summary><b>정답</b></summary>
<p>

#### datas는 사실 바뀌지 않았습니다.
`datas`의 push를 한다고 해서 `datas`의 주소값이 바뀌진 않습니다. React는 주소값이 바뀌는 걸 통해 변화를 감지합니다.<sup><a href="#1-myinput은-text입력시마다-왜-계속-렌더링-되나요">\*1번 문제 참고</a></sup> 즉, `datas`가 바뀌지 않은 걸로 판단한 리액트가 똑똑하게 렌더링하지 않은 겁니다.<br>
이 문제를 해결하기 위해선 [불변성](https://www.google.com/search?q=Immutability&oq=Immutability&aqs=chrome..69i57j69i60j69i61.242j0j7&sourceid=chrome&ie=UTF-8)에 대해 알아야합니다. [전개 구문](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)은 불변성을 지키는 똑똑한 방법입니다.

</p>
</details>

---
