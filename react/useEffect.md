## useEffect 

리액트 컴포넌트가 렌더링될 떄마다 특정 작업을 수행하도록 설정할 수 있는 Hook입니다. 



마운트 될때만 실행하고 싶다. 그러면 빈 배열을 인풋에 추가해주면 된다. 

```javascript
useEffect(()=>
{console.log('마운트 될 떄만 실행')
}, [])
```



특정 값이 업데이트될 때만 실행하고 싶다.

```javascript
useEffect(()=>{
console.log(name)}, [name])
```



뒷정리도하고 싶다. 컴포넌트가 언마운트괴기 전이나 업데이트 되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect에서 뒷정리 함수를 반환해 주세요.



```javascript
useEffect(()=>{
    console.log('effect')
    console.log(name)
    return () => {
		console.log('cleanup')
        console.log(name)
    }
}
```

이러면 렌더링될때마다 뒷정리 함수가 실행된다. 오직 언마운트 될 때만 뒷정리 함수를 호출하고 싶다면 useEffect 함수 두번 째 파라미터에 비어있는 배열을 넣으면 된다. 