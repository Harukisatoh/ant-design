---
order: 1
title:
  zh-CN: 异步加载
  en-US: Asynchronous loading
---

## zh-CN

匹配内容列表为异步返回时。

## en-US

async

```jsx
import React, { useState, useRef, useMemo } from 'react';
import { Mentions } from 'antd';
import debounce from 'lodash/debounce';

const { Option } = Mentions;

export default () => {
  const [loading, setLoading] = useState(false);
  const [users, setUsers] = useState([]);
  const searchRef = useRef();

  const loadGithubUsers = useMemo(
    () =>
      debounce(key => {
        if (!key) {
          setUsers([]);
          return;
        }

        fetch(`https://api.github.com/search/users?q=${key}`)
          .then(res => res.json())
          .then(({ items = [] }) => {
            if (searchRef.current !== key) return;
            setLoading(false);
            setUsers(items.slice(0, 10));
          });
      }, 800),
    [],
  );

  const onSearch = search => {
    setLoading(!!search);
    setUsers([]);
    searchRef.current = search;
    console.log('Search:', search);
    loadGithubUsers(search);
  };

  return (
    <Mentions style={{ width: '100%' }} loading={loading} onSearch={onSearch}>
      {users.map(({ login, avatar_url: avatar }) => (
        <Option key={login} value={login} className="antd-demo-dynamic-option">
          <img src={avatar} alt={login} />
          <span>{login}</span>
        </Option>
      ))}
    </Mentions>
  );
};
```

<style>
.antd-demo-dynamic-option img {
  width: 20px;
  height: 20px;
  margin-right: 8px;
}
</style>
