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
### REACT USEEFFECT HOOK: UPDATE
Earlier we learned about React's useEffect Hook's dependency array. This array can be used to run the side-effect function of useEffect only if a certain variable changes:

```javascript
import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  React.useEffect(() => {
    console.log('I run only if toggle changes (and on mount).');
  }, [toggle]); /*Here*/

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
The side-effect function for this React component runs when the variable in the dependency array changes in addition to it running on the first render (mount). It's also important to note that the dependency array can grow in size since it's an array after all i.e you can pass in more than one variable. Let's check this out with the following addition to our component:

```javascript
 import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  /*Here*/
  const [title, setTitle] = React.useState('Hello React');

  React.useEffect(() => {
    console.log('I still run only if toggle changes (and on mount).'); /*Here*/
  }, [toggle]);
  
/*Here*/
  const handleChange = (event) => {
    setTitle(event.target.value);
  };

  return (
    <div>
      {/*Here*/}
      <input type="text" value={title} onChange={handleChange} />

      <button type="button" onClick={onToggle}>
        Toggle
      </button>

      {/*Here*/}
      {toggle && <div>{title}</div>}
    </div>
  );
};
export {Toggler};
``` 
The side-effect function in React's useEffect Hook still only runs when the one variable in the dependency array changes. Even though the component updates whenever we type something into the input element, useEffect will not run on this update. Only if we provide the new variable in the dependency array, the side-effect function will run for both updates:

```javascript
import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  /*Here*/
  const [title, setTitle] = React.useState('Hello React');

  /*Here*/
  React.useEffect(() => {
    console.log('I still run only if toggle changes (and on mount).'); 
  }, [toggle, title]);
  
  /*Here*/
  const handleChange = (event) => {
    setTitle(event.target.value);
  };

  return (
    <div>
      {/*Here*/}
      <input type="text" value={title} onChange={handleChange} />

      <button type="button" onClick={onToggle}>
        Toggle
      </button>

      {/*Here*/}
      {toggle && <div>{title}</div>}
    </div>
  );
};
export {Toggler};
``` 
However, in this case you could leave out the second argument -- the dependency array -- of useEffect entirely, because only these two variables trigger an update of this component, so by not having a second argument the side-effect would run on every re-render anyway.
<hr>
### REACT USEEFFECT HOOK: ONLY ON UPDATE
We now know that React's useEffect Hook with an array of dependencies run for the first render of the component too. What if you would want to run **this effect only on the update**? We can achieve this by using React's useRef Hook for an instance variable:

```javascript
import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  /*Here*/
  const didMount = React.useRef(false);

  React.useEffect(() => {
    /*Here*/
    if (didMount.current) {
      console.log('I run only if toggle changes.');
    } else {
      didMount.current = true;
    }
  }, [toggle]);

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
When the side-effect function runs for the first time on mount, it only flips the instance variable and doesn't run the implementation details (here console.log) of the side-effect. Only for the next time the side-effect runs (on the first re-render / update of the component), the real implementation logic runs.
<hr>
### REACT USEEFFECT HOOK: ONLY ONCE
So far so good, we learnt we can run React's useEffect Hook's function only once by passing an empty dependency array. This runs the function only once, however, only on the component's first render. What if you would want to run the effect function for a different case -- for example, **only once when a variable updates?**🤔 Let's see:

```javascript
import * as React from "react";

const Toggler = ({ toggle, onToggle }) => {
  /*Here*/
  const calledOnce = React.useRef(false);

  React.useEffect(() => {
    /*Here*/
    if (calledOnce.current) {
      return;
    }

    if (toggle === false) {
      console.log('I run only once if toggle is false.');

      calledOnce.current = true;
    }
  }, [toggle]);

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
Once our condition is met, for example here that the boolean flag is set to false, we remember that we have called the effect's function and don't call it ever again.
<hr>
