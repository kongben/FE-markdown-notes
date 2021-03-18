### 什么是Hook？

### State Hook

声明`state`变量

#### 初始化

```typescript
//声明一个叫'count'的变量和可以修改变量的函数'setCount'
const [count, setCount] = useState(0)
```

useState参数是state的`默认值`，返回值是当前`state`和更新state的`函数`。

#### 读取state

直接使用`{}`可以在DOM中读取值。

`<p>you click {count} times</p>`

#### 更新state

```html
<button onClick={ ()=>setCount(count+1) }>click me</button>
```

通过`setState`函数，将需要更新的值传入即可。

React会重新渲染组件，并将最新的值传给`state`。

### Effect Hook

在组件中执行副作用，并且与生命周期函数极为类似。

React会保存副作用函数，在DIY一个渲染后和每次DOM更新后，执行这个函数。

```typescript
useEffect(()=>{
	console.log('DOM 更新了')
})
```

#### 需要消除的effect

如果给定的副作用是需要消除的，可以将需要清除操作返回给effect，例如添加了事件监听器，需要在`组件注销`的时候，注销事件监听。

```typescript
useEffect(()=>{
	window.addEventListener('click',()=>{console.log('点击了鼠标')})
  
  return ()=>{
    window.removeEventListener('click')
  }
})
```

每当DOM更新的时候，就会执行一次`removeEventListener`，再执行一次`addEventListener`

#### 跳过effect

如果我们的需求仅需要在数据发生变化的时候，执行effect，我们就可以通过第二个可选参数跳过React对effect的调用。

```typescript
useEffect(()=>{
	console.log('count 发生了改变')
},[count]) // 仅当count发现改变时，才会执行effect

```

如果仅需要在组件挂载和卸载的时候，执行effect，仅需返回一个`[]`即可。

```typescript
useEffect(()=>{
	window.addEventListener('click',()=>{console.log('点击了鼠标')})
  
  return ()=>{
    window.removeEventListener('click')
  }
},[])
```

> 未来版本可能会自动添加第二个参数

### Context Hook

父子组件传值

父组件 app.tsx

```typescript
import { createContext, useState } from 'react';
import ElementC from "./components/ElementC";

export const ValueContext = createContext('');

const App = () => {
  const [myValue, setValue] = useState('1')
  const handleClick = () => {
    setValue(myValue + 1)
  }
  return (
    <ValueContext.Provider value={myValue}>
      <ElementC />
      <button onClick={handleClick}>click here</button>
    </ValueContext.Provider>
  )
}

export default App;
```

子组件 ElementC.tsx

```typescript
import { useContext } from "react";
import { ValueContext } from "../App";
function ElementC() {
    const user = useContext(ValueContext)
    return (
        <div>{ user }</div>
    )
}

export default ElementC;
```

