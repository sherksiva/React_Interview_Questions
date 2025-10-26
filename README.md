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
