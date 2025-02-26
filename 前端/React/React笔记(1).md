---
title: React笔记(1)
date: 2024/12/09
tags: react
category: 前端
---
## 1、组件的导入和导出
react组件有两种导出方式：`默认导出`和`具名导出`
一个文件里有且仅有一个默认导出，但是可以有任意多个具名导出

```react
// 默认导入和导出
export default function Button(){}
import Button from './Button.js';

// 具名导入和导出
export function Button() {}
import { Button } from './Button.js';
```

当使用默认导入时，你可以在 `import` 语句后面进行任意命名。比如 `import Banana from './Button.js'`

React组件是一段可以使用标签进行扩展的JavaScript函数

## 2、JSX
JSX：将标签引入 JavaScript。
JSX 是一种语法扩展
规则：
1. 只能返回一个根元素
   如果想要在一个组件中包含多个元素，使用`<div></div>`或`<></>`包裹起来
2. 标签必须闭合
   `<img>` 写成 `<img />`，`<li>oranges` 写成 `<li>oranges</li>`
3. 使用驼峰式命名法给大部分属性命名
   需要用 `strokeWidth` 代替 `stroke-width`。由于 `class` 是一个保留字，所以在 React 中需要用 `className` 来代替
   >这是因为JavaScript变量名称不能包含 `-` 符号或者像 `class` 这样的保留字

## 3、在 JSX 中通过大括号使用 JavaScript
1. 引入JavaScript变量
```react
export default function TodoList() {
  const name = 'Gregorio Y. Zara';
  return (
    <h1>{name}'s To Do List</h1>
  );
}
```

2. 调用JavaScript函数
```react
const today = new Date();

function formatDate(date) {
  return new Intl.DateTimeFormat(
    'zh-CN',
    { weekday: 'long' }
  ).format(date);
}

export default function TodoList() {
  return (
    <h1>To Do List for {formatDate(today)}</h1>
  );
}
```

3. 使用JavaScript对象
   使用双大括号来书写css和对象
```react
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Improve the videophone</li>
      <li>Prepare aeronautics lectures</li>
      <li>Work on the alcohol-fuelled engine</li>
    </ul>
  );
}
```

> 内联 `style` 属性 使用驼峰命名法编写。例如，HTML `<ul style="background-color: black">` 在你的组件里应该写成 `<ul style={{ backgroundColor: 'black' }}>`

## 4、props
1. props是传递给JSX标签的信息
```react
function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
    </div>
  );
}
```

2. props是react组件的唯一参数
```react
function Avatar(props) {  
	let person = props.person;  
	let size = props.size;  
	// ...
}
```

3. 给props一个默认值：
```react
function Avatar({ person, size = 100 }) {  
	// ...  
}
```

4. 使用 JSX 展开语法传递 props
```react
rfunction Profile(props) {  
	return (  
		<div className="card">  
		<Avatar {...props} />  
		</div>  
	);
}
```

5. 将 JSX 作为子组件传递
   父组件在名为 `children` 的 props 中接收并渲染子组件
```react
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```

> props是只读的，不能尝试更改

## 5、条件渲染
1. 使用`if/else`判断条件
```react
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✅</li>;
  }
  return <li className="item">{name}</li>;
}
```

2. 可以根据条件返回null
```react
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}
```

3. 三目运算符
```react
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✅'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}
```

4. 与运算符（`&&`）
```react
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✅'}
    </li>
  );
}
```

> 在 JSX 里，React 会将 `false` 视为一个“空值”，就像 `null` 或者 `undefined`，这样 React 就不会在这里进行任何渲染

5. 选择性地将 JSX 赋值给变量
```react
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = (
      <del>
        {name + " ✅"}
      </del>
    );
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride 的行李清单</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="宇航服" 
        />
        <Item 
          isPacked={true} 
          name="带金箔的头盔" 
        />
        <Item 
          isPacked={false} 
          name="Tam 的照片" 
        />
      </ul>
    </section>
  );
}
```

## 6、渲染列表
1. 使用map()方法从数组中渲染数据
```react
const people = [
  '凯瑟琳·约翰逊: 数学家',
  '马里奥·莫利纳: 化学家',
  '穆罕默德·阿卜杜勒·萨拉姆: 物理学家',
  '珀西·莱温·朱利亚: 化学家',
  '苏布拉马尼扬·钱德拉塞卡: 天体物理学家',
];

export default function List() {
  const listItems = people.map(person =>
    <li>{person}</li>
  );
  return <ul>{listItems}</ul>;
}
```

2. 使用filter()对数组项进行过滤
```react
const people = [
  {
    id: 0,
    name: '凯瑟琳·约翰逊',
    profession: '数学家',
    accomplishment: '太空飞行相关数值的核算',
    imageId: 'MK3eW3A',
  },
  {
    id: 1,
    name: '马里奥·莫利纳',
    profession: '化学家',
    accomplishment: '北极臭氧空洞的发现',
    imageId: 'mynHUSa',
  },
];

export default function List() {
  const chemists = people.filter(person =>
    person.profession === '化学家'
  );
  const listItems = chemists.map(person =>
    <li>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        因{person.accomplishment}而闻名世界
      </p>
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

3. 用 `key` 保持列表项的顺序

在渲染列表时，需要为每一项都指定一个key，这个key应该是静态的，而不是在运行时动态生成的

```react
<li key={person.id}>...</li>
```

4. 为每个列表项显示多个 DOM 节点

把生成的节点用一个 `<div>` 标签包裹起来，或者使用长一点但更明确的 `<Fragment>` 写法

```react
import { Fragment } from 'react';

// ...
// Fragment标签本身不会出现在 DOM 上
const listItems = people.map(person =>
  <Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </Fragment>
);
```

## 7、保持组件纯粹

> 纯函数：
>
> - **只负责自己的任务**。它不会更改在该函数调用前就已存在的对象或变量。
> - **输入相同，则输出相同**。给定相同的输入，纯函数应总是返回相同的结果。

React假设你编写的所有组件都是纯函数，对于相同的输入，总是返回相同的 JSX。组件应该只返回它们的 JSX，而不改变在渲染前，就已存在的任何**外部**对象或变量。

严格模式可以检测不纯的组件。用 `<React.StrictMode>` 包裹根组件来引入严格模式。严格模式在生产环境下不生效，因此它不会降低应用程序的速度

在严格模式下开发时，它将会调用每个组件函数两次。



参考：[快速入门 – React 中文文档](https://zh-hans.react.dev/learn)
