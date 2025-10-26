# React_Interview_Questions

### What is useEffect?

#### useEffect is a React Hook that lets you run side effects in functional components — things like:

1. Fetching data from an API

2. Subscribing to or cleaning up events

3. Updating the DOM manually

4. Setting up timers or intervals

```
import React, { useEffect } from "react";

function MyComponent() {
  useEffect(() => {
    // ✅ Side effect code here
    console.log("Component mounted or updated!");

    // 🧹 Optional cleanup function
    return () => {
      console.log("Component unmounted or effect cleaned up!");
    };
  }, []); // dependency array

  return <h1>Hello useEffect!</h1>;
}
```
##### Runs on every render (if no dependencies):

```
useEffect(() => {
  console.log("Runs on every render");
});
```
##### Runs only once (on mount) (empty dependency array):

```
useEffect(() => {
  console.log("Runs only once (like componentDidMount)");
}, []);
```
#### Runs when specific values change:

```
const [count, setCount] = useState(0);

useEffect(() => {
  console.log("Runs when count changes:", count);
}, [count]);
```
#### With cleanup (e.g. unsubscribe, clear interval):

```
useEffect(() => {
  const interval = setInterval(() => {
    console.log("Tick");
  }, 1000);

  // Cleanup when component unmounts
  return () => clearInterval(interval);
}, []);

```

#### Fetch API data with useEffect

```
import React, { useState, useEffect } from "react";

function UserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    async function fetchData() {
      const response = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await response.json();
      setUsers(data);
    }

    fetchData();
  }, []); // Runs once

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UserList;
```

#### Multiple useEffect hooks in in single component

```
import React, { useState, useEffect } from "react";

function MultiEffectExample() {
  const [count, setCount] = useState(0);
  const [users, setUsers] = useState([]);

  // 🧠 1️⃣ useEffect → Runs only once (on mount)
  useEffect(() => {
    console.log("✅ Component mounted");

    async function fetchUsers() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();
      setUsers(data);
    }

    fetchUsers();
  }, []); // Empty dependency → runs once

  // 🧠 2️⃣ useEffect → Runs whenever `count` changes
  useEffect(() => {
    console.log(`🔢 Count changed: ${count}`);

    // Update document title
    document.title = `Count: ${count}`;
  }, [count]); // Runs when `count` updates

  // 🧠 3️⃣ useEffect → Cleanup example (timer)
  useEffect(() => {
    const timer = setInterval(() => {
      console.log("⏰ Timer running...");
    }, 3000);

    // Cleanup when component unmounts
    return () => {
      clearInterval(timer);
      console.log("🧹 Timer cleaned up!");
    };
  }, []); // Runs once

  return (
    <div style={{ fontFamily: "sans-serif" }}>
      <h2>Multiple useEffect Example</h2>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>

      <h3>Fetched Users:</h3>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default MultiEffectExample;
```
#### automatically fetching data every 10 seconds ?
```
import React, { useState, useEffect } from "react";

function AutoRefreshExample() {
  const [users, setUsers] = useState([]);
  const [count, setCount] = useState(0);

  // 🧠 1️⃣ Fetch users function (used by multiple effects)
  const fetchUsers = async () => {
    console.log("🌐 Fetching users...");
    const res = await fetch("https://jsonplaceholder.typicode.com/users");
    const data = await res.json();
    setUsers(data);
  };

  // 🧠 2️⃣ useEffect → Run once when component mounts
  useEffect(() => {
    fetchUsers(); // initial fetch
  }, []);

  // 🧠 3️⃣ useEffect → Auto-refresh every 10 seconds
  useEffect(() => {
    const interval = setInterval(() => {
      fetchUsers();
      setCount(prev => prev + 1); // track refresh count
    }, 10000); // 10 seconds

    // Cleanup timer when component unmounts
    return () => clearInterval(interval);
  }, []);

  // 🧠 4️⃣ useEffect → Update document title when data refreshes
  useEffect(() => {
    document.title = `Refresh count: ${count}`;
  }, [count]);

  return (
    <div style={{ fontFamily: "sans-serif", padding: "20px" }}>
      <h2>🌀 Auto Refresh Example</h2>
      <p>Refreshed: {count} times</p>

      <button onClick={fetchUsers}>🔁 Manual Refresh</button>

      <h3>User List:</h3>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default AutoRefreshExample;
```



