## React-questions

리액트를 사용하며 겪었던 문제들을 풀어내 리액트의 이해를 돕습니다.  
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

  console.log(text)
  
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
컴포넌트가 렌더링 될 때는 state, props가 변할 때입니다. javascript에서는 object를 주소값으로 비교합니다. `handleChangeText`는 Function object입니다. 매 렌더링시에 `handleChangeText`는 새로운 주소값에 할당되어 `MyInput`의 props(onChange)가 변한 것으로 React는 판단합니다.  
이 문제를 해결하기 위해 (일반적으로)[React.useCallback](https://ko.reactjs.org/docs/hooks-reference.html#usecallback) 혹은 [React.memo](https://ko.reactjs.org/docs/react-api.html#reactmemo)을 사용할 수 있습니다.


</p>
</details>

---