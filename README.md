# React_Interview_Questions

#### 1. What is useEffect?

## useEffect is a React Hook that lets you run side effects in functional components — things like:

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
## 2. Runs on every render (if no dependencies):

```
useEffect(() => {
  console.log("Runs on every render");
});
```
## 3. Runs only once (on mount) (empty dependency array):

```
useEffect(() => {
  console.log("Runs only once (like componentDidMount)");
}, []);
```
## 4. Runs when specific values change:

```
const [count, setCount] = useState(0);

useEffect(() => {
  console.log("Runs when count changes:", count);
}, [count]);
```
## 5. With cleanup (e.g. unsubscribe, clear interval):

```
useEffect(() => {
  const interval = setInterval(() => {
    console.log("Tick");
  }, 1000);

  // Cleanup when component unmounts
  return () => clearInterval(interval);
}, []);

```

## 6. Fetch API data with useEffect

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

## 7. Multiple useEffect hooks in in single component

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

## 8.Automatically fetching data every 10 seconds ?
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

## React Crud Operation Hooks

## 1. Setup and Installation:

- Create a React project: npx create-react-app my-crud-app
- Install Axios: npm install axios
  
## 2. Data Service (e.g., http-common.js):

- Create a file to configure Axios with your API base URL and headers.
```
    // src/http-common.js
    import axios from "axios";

    export default axios.create({
      baseURL: "http://localhost:8080/api", // Replace with your API base URL
      headers: {
        "Content-type": "application/json"
      }
    });

```
## 3. Custom Hook for CRUD (e.g., useCrud.js):

- Create a custom hook to encapsulate the CRUD logic, managing state and making Axios calls.

```
    // src/hooks/useCrud.js
    import { useState, useEffect } from "react";
    import http from "../http-common"; // Your Axios instance

    const useCrud = (endpoint) => {
      const [data, setData] = useState([]);
      const [loading, setLoading] = useState(true);
      const [error, setError] = useState(null);

      // Read (Fetch all items)
      const fetchData = async () => {
        setLoading(true);
        try {
          const response = await http.get(endpoint);
          setData(response.data);
        } catch (err) {
          setError(err);
        } finally {
          setLoading(false);
        }
      };

      useEffect(() => {
        fetchData();
      }, [endpoint]);

      // Create
      const createItem = async (newItem) => {
        try {
          const response = await http.post(endpoint, newItem);
          setData((prevData) => [...prevData, response.data]);
        } catch (err) {
          setError(err);
        }
      };

      // Update
      const updateItem = async (id, updatedItem) => {
        try {
          const response = await http.put(`${endpoint}/${id}`, updatedItem);
          setData((prevData) =>
            prevData.map((item) => (item.id === id ? response.data : item))
          );
        } catch (err) {
          setError(err);
        }
      };

      // Delete
      const deleteItem = async (id) => {
        try {
          await http.delete(`${endpoint}/${id}`);
          setData((prevData) => prevData.filter((item) => item.id !== id));
        } catch (err) {
          setError(err);
        }
      };

      return { data, loading, error, createItem, updateItem, deleteItem, fetchData };
    };

    export default useCrud;
```
## 4. Using the Hook in a Component:

- Import and use the useCrud hook in your React components to perform CRUD operations.

```
    // src/hooks/useCrud.js
    import { useState, useEffect } from "react";
    import http from "../http-common"; // Your Axios instance

    const useCrud = (endpoint) => {
      const [data, setData] = useState([]);
      const [loading, setLoading] = useState(true);
      const [error, setError] = useState(null);

      // Read (Fetch all items)
      const fetchData = async () => {
        setLoading(true);
        try {
          const response = await http.get(endpoint);
          setData(response.data);
        } catch (err) {
          setError(err);
        } finally {
          setLoading(false);
        }
      };

      useEffect(() => {
        fetchData();
      }, [endpoint]);

      // Create
      const createItem = async (newItem) => {
        try {
          const response = await http.post(endpoint, newItem);
          setData((prevData) => [...prevData, response.data]);
        } catch (err) {
          setError(err);
        }
      };

      // Update
      const updateItem = async (id, updatedItem) => {
        try {
          const response = await http.put(`${endpoint}/${id}`, updatedItem);
          setData((prevData) =>
            prevData.map((item) => (item.id === id ? response.data : item))
          );
        } catch (err) {
          setError(err);
        }
      };

      // Delete
      const deleteItem = async (id) => {
        try {
          await http.delete(`${endpoint}/${id}`);
          setData((prevData) => prevData.filter((item) => item.id !== id));
        } catch (err) {
          setError(err);
        }
      };

      return { data, loading, error, createItem, updateItem, deleteItem, fetchData };
    };

    export default useCrud;
```
## 5. Using the Hook in a Component:

- Import and use the useCrud hook in your React components to perform CRUD operations.

```
    // src/components/TutorialList.js
    import React from "react";
    import useCrud from "../hooks/useCrud";

    function TutorialList() {
      const { data: tutorials, loading, error, deleteItem } = useCrud("/tutorials");

      if (loading) return <p>Loading tutorials...</p>;
      if (error) return <p>Error: {error.message}</p>;

      const handleDelete = async (id) => {
        await deleteItem(id);
        // Optionally, refetch data if needed, though deleteItem updates local state
      };

      return (
        <div>
          <h2>Tutorials</h2>
          <ul>
            {tutorials.map((tutorial) => (
              <li key={tutorial.id}>
                {tutorial.title}
                <button onClick={() => handleDelete(tutorial.id)}>Delete</button>
                {/* Add Update button and form */}
              </li>
            ))}
          </ul>
          {/* Add Create form */}
        </div>
      );
    }

    export default TutorialList;
```
## Key Concepts:

- useState: To manage the data, loading state, and error state within the custom hook.
- useEffect: To fetch data when the component mounts or when dependencies change.
- Axios: For making HTTP requests (GET, POST, PUT, DELETE) to your API.
- Custom Hooks: To encapsulate reusable logic and stateful behavior, making your components cleaner and more maintainable.





