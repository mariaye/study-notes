# study-notes
## React
- State Hooks
```javascript
# 
const [index, setIndex] = useState(0);
const [liked, setLiked] = useState(true);
const [text, setText] = useState("");
const [form, setForm] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com',
  });
const [example, setExample] = useState(initfunction); // init function as initializer, don't add () to its end
function handleClick() {
  setIndex(2);
  setIndex(a => a + 1);
   setAge(a => a + 1); // setAge(42 => 43)
  setAge(a => a + 1); // setAge(43 => 44)
  setAge(a => a + 1); // setAge(44 => 45)
  // ...
}
function handleTextChange(e){
    setText(e.target.value);
}
function handleChange(e){
    setLiked(e.target.checked);
}
return (
    <>
      <label>
        <input
          type="checkbox"
          checked={liked}
          onChange={handleChange}
        />
        I liked this
      </label>
      <p>You {liked ? 'liked' : 'did not like'} this.</p>
      <label>
        First name:
        <input
          value={form.firstName}
          onChange={e => {
            setForm({
              ...form,
              firstName: e.target.value
            });
          }}
        />
      </label>
    </>
  );
```
- Context Hooks
```javascript
// ThemeContext.js
import React from 'react';

// 创建一个 Context，初始值设置为 'light'
const ThemeContext = React.createContext('light');

export default ThemeContext;
```
```javascript
// ThemeProvider.js
import React, { useState } from 'react';
import ThemeContext from './ThemeContext';

const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light'); // 初始主题为 'light'

  // 切换主题的函数
  const toggleTheme = () => {
    setTheme(theme => theme === 'light' ? 'dark' : 'light');
  };

  // 传递当前主题和切换函数
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export default ThemeProvider;
```
```javascript
// ThemeButton.js
import React, { useContext } from 'react';
import ThemeContext from './ThemeContext';

const ThemeButton = () => {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button onClick={toggleTheme}>
      Current Theme: {theme} (click to toggle)
    </button>
  );
};

export default ThemeButton;
```
```javascript
// App.js
import React from 'react';
import ThemeProvider from './ThemeProvider';
import ThemeButton from './ThemeButton';

function App() {
  return (
    <ThemeProvider>
      <div>
        <ThemeButton />
      </div>
    </ThemeProvider>
  );
}

export default App;
```

- Ref Hooks
```javascript
// 使用 useRef 访问 DOM 元素
import React, { useRef } from 'react';

function InputFocus() {
  const inputRef = useRef(null);  // 创建 ref 对象

  const focusInput = () => {
    // 当按钮点击时，使输入框获得焦点
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="Enter something..." />
      <button onClick={focusInput}>Focus the input</button>
    </div>
  );
}
export default InputFocus;
```
```javascript
// 使用 Ref 保存数据
/*
在这个例子中，countRef 用来保存点击次数，即使组件重新渲染，
这个值也不会被重置。每次点击按钮时，
increment 函数都会增加 countRef.current 的值，并且输出到控制台。
注意，改变 ref 中的值不会触发组件重新渲染，这是与状态变量（通过 useState 创建）的主要区别。
*/
import React, { useRef } from 'react';

function Counter() {
  const countRef = useRef(0);  // 初始化计数器为 0

  const increment = () => {
    countRef.current += 1;
    console.log(`Clicked ${countRef.current} times`);
  };

  return (
    <button onClick={increment}>Increment</button>
  );
}

export default Counter;
```
```javascript
/*
在这个组件中：

点击 "Increment State Count" 会看到两个数字都更新（如果同时触发重新渲染），并在控制台看到组件渲染的日志。
点击 "Increment Ref Count" 只会在控制台看到 refCount 的更新，页面上的 Ref Count 不会立即更新，因为修改 ref 不触发重新渲染。
这个示例清楚地展示了 useState 和 useRef 的基本区别，以及它们如何影响组件的行为和渲染。
*/
import React, { useState, useRef, useEffect } from 'react';

function RefVsStateCounter() {
  // 使用 useState
  const [stateCount, setStateCount] = useState(0);

  // 使用 useRef
  const refCount = useRef(0);

  // 函数用于增加 useState 的计数器
  const incrementState = () => {
    setStateCount(stateCount + 1);
  };

  // 函数用于增加 useRef 的计数器
  const incrementRef = () => {
    refCount.current += 1;
    console.log(`Ref count: ${refCount.current}`);
  };

  // 每次组件渲染时记录
  useEffect(() => {
    console.log(`Component rendered. State count: ${stateCount}`);
  });

  return (
    <div>
      <p>State Count: {stateCount}</p>
      <p>Ref Count: {refCount.current}</p>
      <button onClick={incrementState}>Increment State Count</button>
      <button onClick={incrementRef}>Increment Ref Count</button>
    </div>
  );
}

export default RefVsStateCounter;
```
- Effect Hooks
```javascript
// 这个计时器会在组件加载后开始计数，并在组件卸载时停止计数。
import React, { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(seconds => seconds + 1);
    }, 1000);

    return () => clearInterval(interval); // 清理函数，组件卸载时执行
  }, []); // 空依赖数组表示这个 effect 只在组件挂载时执行一次

  return (
    <div>
      Seconds: {seconds}
    </div>
  );
}

export default Timer;
```
```javascript
/*
要在 useEffect 中使用 async 函数，你需要注意直接在 useEffect 的回调函数中使用 async 是不允许的，因为这会导致回调函数返回一个 Promise，而不是一个清理函数或者 undefined。但是，你可以在 useEffect 回调函数内部定义一个 async 函数，并立即调用它。

下面是如何将你的例子改写为使用 async/await 的形式：
*/
import React, { useState, useEffect } from 'react';

function UserData({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const fetchUserData = async () => {
      try {
        const response = await fetch(`https://api.example.com/users/${userId}`);
        const data = await response.json();
        setUser(data);
      } catch (error) {
        console.error('Failed to fetch user:', error);
        setUser(null); // 处理错误，例如清除之前的用户数据
      }
    };

    fetchUserData();
  }, [userId]); // 依赖数组中包含 userId，当 userId 变化时重新执行 effect

  return (
    <div>
      {user ? `User Name: ${user.name}` : "Loading..."}
    </div>
  );
}

export default UserData;
```

- __何时需要清理函数？清理函数在以下几种情况下非常重要：__

1. 订阅和事件监听：如果你在 useEffect 中设置了订阅或事件监听器（如 WebSocket 连接、DOM 事件监听等），你需要在组件卸载前取消这些订阅和监听，以避免内存泄漏和潜在的性能问题。

2. 设置定时器：如果你使用定时器如 setTimeout 或 setInterval，应在组件卸载时清除这些定时器，防止它们在不应该执行时运行。

3. 正在进行的副作用：对于可以取消的副作用（如某些可以取消的异步请求），如果组件在完成前卸载，你应该取消这些操作。这可以用来防止无效的状态更新或资源浪费。


- Performance Hooks
```javascript
// useMemo
// useCallback
// useTransition
// useDeferredValue
```