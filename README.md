# Implementation-of-useEffect-Hook-in-React
Our goal in this tutorial is to learn everything there is about the useEffect hook. We have two React components where the parent component manages the state with React's useState Hook:
```javascript
import * as React from "react";
import {Toggler} from "./Toggler.js";

const App = () => {
  const [toggle, setToggle] = React.useState(true);

  const handleToggle = () => {
    setToggle(!toggle);
  };

  return <Toggler toggle={toggle} onToggle={handleToggle} />;
};
export default App;
``` 
And its child component consumes the state and modifies the state with a callback event handler:

```javascript
const Toggler = ({ toggle, onToggle }) => {
  return (
    <div>
      <button type="button" onClick={onToggle}>
        Toggle
      </button>
      {toggle && <div>Hello React</div>}
    </div>
  );
};
export {Toggler};
``` 
Based on the stateful boolean flag coming from the parent component, the child component renders "Hello React" conditionally. It's important to state that useEffect run only when the component mounts, when the component renders, or only when the component re-renders, and so on. Let's walk through various examples to demonstrate its usage.
<hr>
### REACT USEEFFECT HOOK: ALWAYS
Let's pass in the side-effect function as an argument:

```javascript
import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  /*Here*/
  React.useEffect(() => {
    console.log('I run on every render: mount + update.');
  });

  return (
    <div>
      <button type="button" onClick={onToggle}>
        Toggle
      </button>

      {toggle && <div>Hello React</div>}
    </div>
  );
};
export {Toggler};
``` 
This function will render on every render i.e** it runs on the first render of the component** (also called on mount or mounting of the component) and **on every re-render of the component** (also called on update or updating of the component).
<hr>
### REACT USEEFFECT HOOK: MOUNT
If we want to run React's useEffect Hook **only on the first render** of a component (also called **only on mount**), then we can pass in a second argument to useEffect:

```javascript
import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  React.useEffect(() => {
    console.log('I run only on the first render: mount.');
  }, []);  /*Here*/

  return (
    <div>
      <button type="button" onClick={onToggle}>
        Toggle
      </button>

      {toggle && <div>Hello React</div>}
    </div>
  );
};
export {Toggler};
```
The second argument -- here an empty array -- is called dependency array. If the dependency array is empty, the side-effect function used in React's useEffect Hook has no dependencies, meaning it runs only the first time a component renders. 
<hr>
