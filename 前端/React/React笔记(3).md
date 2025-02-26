---
title: React笔记(3)
date: 2024/12/10
tags: react
category: 前端
---

## State：组件的记忆

`useState`：保存数据并在更新数据时出发重新渲染

```react
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);

  function handleClick() {
    setIndex(index + 1);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
    </>
  );
}
```

state是隔离且私有的，state 完全私有于声明它的组件

## React渲染步骤

1. 触发渲染

导致组件渲染的原因：

> 1. 组件的 **初次渲染。**
> 2. 组件（或者其祖先之一）的 **状态发生了改变。**

2. 渲染组件

渲染组件的过程是递归的，会渲染所有的子组件

3. 提交DOM

React 将应用最少的必要操作（在渲染时计算！），仅在渲染之间存在差异时才会更改 DOM 节点

最后进行浏览器渲染

## state代表了当前UI的快照

```react
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);
  // number的值一直是0
  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);// number 是 0 所以 setNumber(0 + 1)，React 准备在下一次渲染时将 number 更改为 1。
        setNumber(number + 1);// number 是 0 所以 setNumber(0 + 1)，React 准备在下一次渲染时将 number 更改为 1。
        setNumber(number + 1);// number 是 0 所以 setNumber(0 + 1)，React 准备在下一次渲染时将 number 更改为 1。
      }}>+3</button>
    </>
  )
}
```

## 把一系列 state 更新加入队列 

多次更新state

```react
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}
```

在这里，`n => n + 1` 被称为 **更新函数**。当你将它传递给一个 state 设置函数时：

1. React 会将此函数加入队列，以便在事件处理函数中的所有其他代码运行后进行处理。
2. 在下一次渲染期间，React 会遍历队列并给你更新之后的最终 state。

可以传递给 `setNumber` state 设置函数的内容：**一个更新函数**或者**任何其他的值**

事件处理函数执行完成后，React 将触发重新渲染。在重新渲染期间，React 将处理队列。更新函数会在渲染期间执行，因此 **更新函数必须是纯函数** 并且只 **返回** 结果。不要尝试从它们内部设置 state 或者执行其他副作用。

## 更新state中的对象

state是只读的，只能使用setState()更新

1. 使用展开语法复制对象，`...` 展开语法本质是是“浅拷贝”——它只会复制一层

```react
setPerson({
  ...person, // 复制上一个 person 中的所有字段
  firstName: e.target.value // 但是覆盖 firstName 字段 
});
```

2. 使用 `[` 和 `]` 括号来实现属性的动态命名

```react
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com'
  });

  function handleChange(e) {
    setPerson({
      ...person,
      [e.target.name]: e.target.value
    });
  }

  return (
    <>
      <label>
        First name:
        <input
          name="firstName"
          value={person.firstName}
          onChange={handleChange}
        />
      </label>
      <label>
        Last name:
        <input
          name="lastName"
          value={person.lastName}
          onChange={handleChange}
        />
      </label>
      <label>
        Email:
        <input
          name="email"
          value={person.email}
          onChange={handleChange}
        />
      </label>
      <p>
        {person.firstName}{' '}
        {person.lastName}{' '}
        ({person.email})
      </p>
    </>
  );
}
```

在这里，`e.target.name` 引用了 `<input>` 这个 DOM 元素的 `name` 属性。

3. 更新嵌套对象

```react
const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});

// 第一种方式：
const nextArtwork = { ...person.artwork, city: 'New Delhi' };
const nextPerson = { ...person, artwork: nextArtwork };
setPerson(nextPerson);

// 第二种方式
setPerson({
  ...person, // 复制其它字段的数据 
  artwork: { // 替换 artwork 字段 
    ...person.artwork, // 复制之前 person.artwork 中的数据
    city: 'New Delhi' // 但是将 city 的值替换为 New Delhi！
  }
});
```

4. Immer

可以直接修改state

```react
import { useImmer } from 'use-immer';

export default function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://i.imgur.com/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    updatePerson(draft => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e) {
    updatePerson(draft => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e) {
    updatePerson(draft => {
      draft.artwork.image = e.target.value;
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img 
        src={person.artwork.image} 
        alt={person.artwork.title}
      />
    </>
  );
}
```

## 更新state中的数组

|          | 避免使用 (会改变原始数组)     | 推荐使用 (会返回一个新数组）  |
| -------- | ----------------------------- | ----------------------------- |
| 添加元素 | `push`，`unshift`             | `concat`，`[...arr]` 展开语法 |
| 删除元素 | `pop`，`shift`，`splice`      | `filter`，`slice`             |
| 替换元素 | `splice`，`arr[i] = ...` 赋值 | `map`                         |
| 排序     | `reverse`，`sort`             | 先将数组复制一份              |

也可以使用Immer，直接修改数组

1. 添加元素

```react
setArtists( // 替换 state
  [ // 是通过传入一个新数组实现的
    ...artists, // 新数组包含原数组的所有元素
    { id: nextId++, name: name } // 并在末尾添加了一个新的元素
  ]
);

setArtists([
  { id: nextId++, name: name },
  ...artists // 将原数组中的元素放在末尾
]);
```

2. 删除元素

```react
setArtists(
  artists.filter(a => a.id !== artist.id)
);
```

3. 替换元素

```react
const nextCounters = counters.map((c, i) => {
    if (i === index) {
        // 递增被点击的计数器数值
        return c + 1;
    } else {
        // 其余部分不发生变化
        return c;
    }
});
setCounters(nextCounters);
```

4. 插入元素

```react
const nextArtists = [
    // 插入点之前的元素：
    ...artists.slice(0, insertAt),
    // 新的元素：
    { id: nextId++, name: name },
    // 插入点之后的元素：
    ...artists.slice(insertAt)
];
setArtists(nextArtists);
```

5. 更新数组内部的对象

```react
import { useState } from 'react';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [myList, setMyList] = useState(initialList);
  const [yourList, setYourList] = useState(
    initialList
  );

  function handleToggleMyList(artworkId, nextSeen) {
    setMyList(myList.map(artwork => {
      if (artwork.id === artworkId) {
        // 创建包含变更的*新*对象
        return { ...artwork, seen: nextSeen };
      } else {
        // 没有变更
        return artwork;
      }
    }));
  }

  function handleToggleYourList(artworkId, nextSeen) {
    setYourList(yourList.map(artwork => {
      if (artwork.id === artworkId) {
        // 创建包含变更的*新*对象
        return { ...artwork, seen: nextSeen };
      } else {
        // 没有变更
        return artwork;
      }
    }));
  }

  return (
    <>
      <h1>艺术愿望清单</h1>
      <h2>我想看的艺术清单：</h2>
      <ItemList
        artworks={myList}
        onToggle={handleToggleMyList} />
      <h2>你想看的艺术清单：</h2>
      <ItemList
        artworks={yourList}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

通常来讲，**你应该只直接修改你刚刚创建的对象**。如果你正在插入一个**新**的 artwork，你可以修改它，但是如果你想要改变的是 state 中已经存在的东西，你就需要先拷贝一份了



参考：[快速入门 – React 中文文档](https://zh-hans.react.dev/learn)