---
title: React笔记(4)
date: 2024/12/10
tags: react
category: 前端
---

## 用state响应输入

React使用声明式UI。将所有的交互变为 state 的改变

当开发一个组件时：

1. 写出你的组件中所有的视图状态。
2. 确定是什么触发了这些 state 的改变。
3. 通过 `useState` 模块化内存中的 state。
4. 删除任何不必要的 state 变量。
5. 连接事件处理函数去设置 state。

## 构建state的原则

1. 合并关联的state

**如果某两个 state 变量总是一起变化，则将它们统一成一个 state 变量可能更好**

2. 避免矛盾的state

可以创建一个state变量来替代矛盾的state所表达的状态

3. 避免冗余的state

如果你能在渲染期间从组件的 props 或其现有的 state 变量中计算出一些信息，则不应将这些信息放入该组件的 state 中

> 不要将props放入state中，只有当你想要忽略特定 props 属性的所有更新时，才应该放入

4. 避免重复的state

重复的state难以保持同步

5. 避免深度嵌套的state

深度嵌套的state难以更新，尽量使其扁平化

## 在组件间共享状态

状态提升：将相关state放到它们的公共父级，再通过 props 将 state 传递给这两个组件

```react
import { useState } from 'react';

export default function Accordion() {
  const [activeIndex, setActiveIndex] = useState(0);
  return (
    <>
      <h2>哈萨克斯坦，阿拉木图</h2>
      <Panel
        title="关于"
        isActive={activeIndex === 0}
        onShow={() => setActiveIndex(0)}
      >
        阿拉木图人口约200万，是哈萨克斯坦最大的城市。它在 1929 年到 1997 年间都是首都。
      </Panel>
      <Panel
        title="词源"
        isActive={activeIndex === 1}
        onShow={() => setActiveIndex(1)}
      >
        这个名字来自于 <span lang="kk-KZ">алма</span>，哈萨克语中“苹果”的意思，经常被翻译成“苹果之乡”。事实上，阿拉木图的周边地区被认为是苹果的发源地，<i lang="la">Malus sieversii</i> 被认为是现今苹果的祖先。
      </Panel>
    </>
  );
}

function Panel({
  title,
  children,
  isActive,
  onShow
}) {
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? (
        <p>{children}</p>
      ) : (
        <button onClick={onShow}>
          显示
        </button>
      )}
    </section>
  );
}
```

> ==受控组件==：当组件中的重要信息是由 `props` 而不是其自身状态驱动时，就可以认为该组件是“受控组件”。这样就允许父组件完全指定其行为
>
> ==非受控组件==：组件中的重要信息是由 `state` 提供的
>
> 通常通过`props`提供的状态是受控的，通过`state`提供的状态是不受控的

每一个状态都应该存在且只存在于一个指定的组件中作为 state。状态在不同的组件中应当传递而不是复制。

## 对state进行保留和重置

状态是由 React 保存的。React 通过组件在渲染树中的位置将它保存的每个状态与正确的组件关联起来

React 在移除一个组件时，也会销毁它的 state

**只要一个组件还被渲染在 UI 树的相同位置，React 就会保留它的 state。** 如果它被移除，或者一个不同的组件被渲染在相同的位置，那么 React 就会丢掉它的 state。

一般来说，**如果你想在重新渲染时保留 state，几次渲染中的树形结构就应该相互“匹配”**。结构不同就会导致 state 的销毁，因为 React 会在将一个组件从树中移除时销毁它的 state。

### 在相同的位置重置state

有两个方法可以在相同的位置重置state

1. 将组件渲染在不同的位置

```react
// 这样会将state重置
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        下一位玩家！
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person} 的分数：{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        加一
      </button>
    </div>
  );
}

// 这样不会将state重置
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter person="Taylor" />
      ) : (
        <Counter person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        下一位玩家！
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person} 的分数：{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        加一
      </button>
    </div>
  );
}
```

2. 使用key来重置state

可以使用 key 来让 React 区分任何组件。key并不是全局唯一的，只能区分同一个父组件内部的子组件

```react
// 这样会重置state
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        下一位玩家！
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person} 的分数：{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        加一
      </button>
    </div>
  );
}
```

### 使用key来重置表单

```react
import { useState } from 'react';

function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'跟 ' + contact.name + ' 聊一聊'}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>发送到 {contact.email}</button>
    </section>
  );
}

function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat key={to.id} contact={to} />
    </div>
  )
}
```

这样确保了当你选择一个不同的收件人时， `Chat` 组件——包括其下方树中的任何 state——都将从头开始重新创建。 React 还将重新创建 DOM 元素，而不是复用它们。

> 为被移除的组件保留state
>
> 1. 将所有的子组件都渲染出来，但是通过css来把其他所有子组件隐藏起来
> 2. 状态提升，将所有子组件的状态保存在父组件中
> 3. 除了react的state之外，使用其他数据源来保存状态信息

## 迁移状态逻辑到Reducer中

reducer是一个外部函数，用来处理组件的所有状态更新逻辑

```react
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  // 接收一个reducer函数和一个初始的state，返回一个有状态的值和一个dispatch函数
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false}
];
```

现在，事件处理程序只通过派发 `action` 来指定 **发生了什么**，而 `reducer` 函数通过响应 `actions` 来决定 **状态如何更新**

> reducer函数必须是纯粹的。reducer 是在渲染时运行的！（actions 会排队直到下一次渲染)
>
> 每个 action 都描述了一个单一的用户交互，即使它会引发数据的多个变化

### 使用 Immer 简化 reducer

[`useImmerReducer`](https://github.com/immerjs/use-immer#useimmerreducer) 让你可以通过 `push` 或 `arr[i] =` 来修改 state

Reducer 应该是纯净的，所以它们不应该去修改 state。而 Immer 为你提供了一种特殊的 `draft` 对象，你可以通过它安全地修改 state。在底层，Immer 会基于当前 state 创建一个副本。这就是通过 `useImmerReducer` 来管理 reducer 时，可以修改第一个参数，且不需要返回一个新的 state 的原因

```react
import { useImmerReducer } from 'use-immer';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added': {
      draft.push({
        id: action.id,
        text: action.text,
        done: false,
      });
      break;
    }
    case 'changed': {
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    }
    case 'deleted': {
      return draft.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action：' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useImmerReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false},
];
```

## 使用 Context 深层传递参数

Context 让父组件可以为它下面的整个组件树提供数据

```react
import { useContext } from 'react';
import { createContext } from 'react';

const LevelContext = createContext(0);

function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}

function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 0:
      throw Error('Heading 必须在 Section 内部！');
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('未知的 level：' + level);
  }
}

// Heading从最近的Section父组件中拿到level值
export default function Page() {
  return (
    <Section>
      <Heading>主标题</Heading>
      <Section>
        <Heading>副标题</Heading>
        <Heading>副标题</Heading>
        <Heading>副标题</Heading>
        <Section>
          <Heading>子标题</Heading>
          <Heading>子标题</Heading>
          <Heading>子标题</Heading>
          <Section>
            <Heading>子子标题</Heading>
            <Heading>子子标题</Heading>
            <Heading>子子标题</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```react
// Heading从最近的Section父组件中拿到level值
export default function ProfilePage() {
  return (
    <Section>
      <Heading>My Profile</Heading>
      <Post
        title="旅行者，你好！"
        body="来看看我的冒险。"
      />
      <AllPosts />
    </Section>
  );
}

function AllPosts() {
  return (
    <Section>
      <Heading>帖子</Heading>
      <RecentPosts />
    </Section>
  );
}

function RecentPosts() {
  return (
    <Section>
      <Heading>最近的帖子</Heading>
      <Post
        title="里斯本的味道"
        body="...那些蛋挞！"
      />
      <Post
        title="探戈节奏中的布宜诺斯艾利斯"
        body="我爱它！"
      />
    </Section>
  );
}

function Post({ title, body }) {
  return (
    <Section isFancy={true}>
      <Heading>
        {title}
      </Heading>
      <p><i>{body}</i></p>
    </Section>
  );
}
```

## 结合使用 reducer 和 context









参考：[快速入门 – React 中文文档](https://zh-hans.react.dev/learn)
