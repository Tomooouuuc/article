---
title: React笔记(2)
date: 2024/12/10
tags: react
category: 前端
---

## 响应事件

如需添加一个事件处理函数，你需要先定义一个函数，然后将其作为 prop 传入合适的 JSX 标签

```react
export default function Button() {
  function handleClick() {
    alert('你点击了我！');
  }

  return (
    <button onClick={handleClick}>
      点我
    </button>
  );
}
```

事件处理函数：1、通常在你的组件内部定义。2、名称以 `handle` 开头，后跟事件名称。

也可以使用内联的事件处理函数或箭头函数。事件处理函数不需要是纯函数

```react
<button onClick={function handleClick() {
  alert('你点击了我！');
}}>

<button onClick={() => {
  alert('你点击了我！');
}}>
```

1. 在事件处理函数中读取 props

由于事件处理函数声明于组件内部，因此它们可以直接访问组件的 props：

```react
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <AlertButton message="正在播放！">
        播放电影
      </AlertButton>
      <AlertButton message="正在上传！">
        上传图片
      </AlertButton>
    </div>
  );
}
```

2. 将事件处理函数作为 props 传递

通常，我们会在父组件中定义子组件的事件处理函数。

```react
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function PlayButton({ movieName }) {
  function handlePlayClick() {
    alert(`正在播放 ${movieName}！`);
  }

  return (
    <Button onClick={handlePlayClick}>
      播放 "{movieName}"
    </Button>
  );
}

function UploadButton() {
  return (
    <Button onClick={() => alert('正在上传！')}>
      上传图片
    </Button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <PlayButton movieName="魔女宅急便" />
      <UploadButton />
    </div>
  );
}
```

3. 命名事件处理函数 prop

组件的事件处理函数名称可以自定义

```react
function Button({ onSmash, children }) {
  return (
    <button onClick={onSmash}>
      {children}
    </button>
  );
}

export default function App() {
  return (
    <div>
      <Button onSmash={() => alert('正在播放！')}>
        播放电影
      </Button>
      <Button onSmash={() => alert('正在上传！')}>
        上传图片
      </Button>
    </div>
  );
}
```

4. 事件传播

事件会沿着树向上“冒泡”或“传播”。事件处理函数还将捕获任何来自子组件的事件

在 React 中所有事件都会传播，除了 `onScroll`，它仅适用于你附加到的 JSX 标签

```react
export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('你点击了 toolbar ！');
    }}>
      <button onClick={() => alert('正在播放！')}>
        播放电影
      </button>
      <button onClick={() => alert('正在上传！')}>
        上传图片
      </button>
    </div>
  );
}
// 如果你点击任一按钮，它自身的 onClick 将首先执行，然后父级 <div> 的 onClick 会接着执行。因此会出现两条消息。如果你点击 toolbar 本身，将只有父级 <div> 的 onClick 会执行
```

5. 阻止传播

事件处理函数接收一个 **事件对象** 作为唯一的参数。按照惯例，它通常被称为 `e` ，代表 “event”（事件）。你可以使用此对象来读取有关事件的信息

```react
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      // 阻止传播
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('你点击了 toolbar ！');
    }}>
      <Button onClick={() => alert('正在播放！')}>
        播放电影
      </Button>
      <Button onClick={() => alert('正在上传！')}>
        上传图片
      </Button>
    </div>
  );
}
```

> 捕获阶段事件
>
> 通过在事件名称末尾添加 `Capture` 来实现
>
> ```react
> <div onClickCapture={() => { /* 这会首先执行 */ }}>
>   <button onClick={e => e.stopPropagation()} />
>   <button onClick={e => e.stopPropagation()} />
> </div>
> ```
>
> 每个事件分三个阶段传播：
>
> 1. 它向下传播，调用所有的 `onClickCapture` 处理函数。
> 2. 它执行被点击元素的 `onClick` 处理函数。
> 3. 它向上传播，调用所有的 `onClick` 处理函数。

6. 阻止默认行为

```react
export default function Signup() {
  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert('提交表单！');
    }}>
      <input />
      <button>发送</button>
    </form>
  );
}
```

- [`e.stopPropagation()`](https://developer.mozilla.org/docs/Web/API/Event/stopPropagation) 阻止触发绑定在外层标签上的事件处理函数。
- [`e.preventDefault()`](https://developer.mozilla.org/docs/Web/API/Event/preventDefault) 阻止少数事件的默认浏览器行为。



参考：[快速入门 – React 中文文档](https://zh-hans.react.dev/learn)