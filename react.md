# About React

## 1) How would you optimize a React application?

1. Move the state to where it is needed (colocation); This avoids re-rendering
2. Use the `shouldComponentUpdate` lifecycle method or the `PureComponent/React.memo` to prevent unnecessary re-renders of components.
3. Implement code splitting and lazy loading to load components and resources only when they are needed, improving initial load time.
4. Employ memoization techniques such as memoizing expensive calculations or function results using libraries like **`memoize-one`** or **`reselect`**.
5. Optimize network requests by using techniques like caching, pagination, or reducing the amount of data fetched.
6. Implement virtualization for long lists or large data sets using libraries like **`react-virtualized`** to render only the visible portion of the list, improving performance.
7. Profile your application using tools like React DevTools or browser dev tools to identify and address performance bottlenecks.

## 2) Can you explain the concept of "lifting state up" in React?

"Lifting state up" refers to the process of moving the state from a component to its closest common ancestor component, which is shared by two or more child components. By doing so, the state becomes a part of the parent component's state, and it can be passed down to the child components as props.

This approach is useful when multiple components need access to the same data or when the state needs to be synchronized among different components. It promotes a single source of truth and ensures consistency in the application's state management.

## 3) How do you handle forms in React, particularly when it comes to managing form input state and handling form submission?

Handling forms consists on managing the state of a form inputs and handling form submission. Here's an example: this form only take name and email.

```jsx
function MyForm() {
	const [formState, setFormState] = useState({
		name: '',
		email: ''
	});
	
	function handleFormInputChange(event) {
		const {name, value} = event.target;
		setFormState(...formState, [name]: value);
	}

	function handleSubmit() {
		event.preventDefault();
		// my logic
	}
	
	return (
		<form onSubmit={handleSubmit}>
			<input
				type="text"
				name="name"
				value={formData.name}
				onChange={handleFormInputChange} />
			<input
				type="text"
				name="email"
				value={formData.email}
				onChange={handleFormInputChange} />
		</form>
	);

}
```

## 4) Can you explain the concept of React Hooks and provide some examples of built-in hooks?

Hooks allows you to reuse logic and manage component behavior without using class components.

Here's some examples of built-in hooks:

1. **useState** allows us to manage state within functional components.
2. **useEffect** can manage side effects in functional components. It allows to perform actions after rendering like fetching data. subscribing to events, or updating the DOM.
3. **useMemo** memoizes expensive calculation. It takes a function and dependency array as arguments and returns a memoized value;
4. **useCallback** caches a function definition between re-renders. It's useful when you pass a function as prop to a child component since **`function () {}` or `() => {}` always creates a *different* function.** That avoids the component to re-render itself.

## 5) What's the difference between useMemo and useCallback

- **useMemo**: caches values. The function is called during the rendering process, and its return value is memoized. The memoized value is only recalculated when any of the dependencies in the dependency array change. If the dependencies remain the same, **`useMemo`** returns the cached value.
- **useCallback**: caches function definitions. It is particularly useful when passing callbacks to child components that rely on reference equality. It takes a function and a dependency array as arguments. The function is memoized and returned. The memoized function will only change if any of the dependencies in the dependency array change.

```jsx
import { useMemo } from 'react';

function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  // ...
}

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
```

## 6) How do you handle errors in React applications, and what approaches or tools can be used for error handling?

1. **Error Boundaries**: if the application throws an error during rendering, React won't put the UI in the screen. To avoid this, we could wrap part of the ui with an error boundary. An error boundary allows us to catch the error and render a fallback UI & add extra logic to log the error to an analytics service.
    
    ```jsx
    class ErrorBoundary extends React.Component {
      constructor(props) {
        super(props);
        this.state = { hasError: false };
      }
    
      static getDerivedStateFromError(error) {
        // Update state so the next render will show the fallback UI.
        return { hasError: true };
      }
    
      componentDidCatch(error, info) {
        // Example "componentStack":
        //   in ComponentThatThrows (created by App)
        //   in ErrorBoundary (created by App)
        //   in div (created by App)
        //   in App
        logErrorToMyService(error, info.componentStack);
      }
    
      render() {
        if (this.state.hasError) {
          // You can render any custom fallback UI
          return this.props.fallback;
        }
    
        return this.props.children;
      }
    }
    
    <ErrorBoundary fallback={<p>Something went wrong</p>}>
      <Profile />
    </ErrorBoundary>
    ```
    
    If `Profile` or its child component throws an error, `ErrorBoundary` will “catch” that error, display a fallback UI with the error message you’ve provided, and send a production error report to your error reporting service.
    
    You can define your own Error Boundary component or use third-party libraries like **`react-error-boundary`** to simplify the implementation.
    
2. **Try/Catch**
3. **Global Error Handling**: you can implement global error handling by attaching a listener for the `error` event on the `window` object. With that you can centrally handle uncaught errors in your application.
4. **Error reporting and logging tools**: like Bugsnag

## 7) Functional vs Class components

**********Class**********

They're written using the class syntax and extend from `React.Component`. They have lifecycle methods that allow you to perform actions at specific stages of a component's lifecycle. Class components also have access to `this` keyword and have state management by using the `setState` method.

**Functional** 

They're written as plain JS functions. They also have own state and manage side effects using hooks. With the introduction with Hooks, functional components have gained more popularity due to their simplicity and reusability.

React 16.8 suggest functional components as the way to go when creating components as they can benefit from Hooks.

## 8) What is “prop drilling” and how to mitigate it?

It occurs when you need to pass props through multiple layers of components, that probably don't need the props. It can make the codebase harder to maintain and introduce some unnecessary coupling between components.

To mitigate this situation, we can use the following approaches:

1. **Context API:** this API creates a context where values can be accessed by any component within the context's scope. By lifting the values to a context provider, we can avoid prop drilling and make the values accessible to deeply nested components.
2. **State Management Libraries:** they provide a centralized store where values can be accessed by whole application.

It's worth to mention that these approaches bring complexity to the application. Perhaps prop drilling isn't a big problem in small applications

## 9) Can you explain the concept of "controlled components" and "uncontrolled components" in React, and when you would choose to use each approach?

**********************Controlled component**********************

They're those in which the data is driven by the props rather than its local state. This let's the parent component fully specify its behavior.

********************************************Uncontrolled component********************************************

The component has an internal state and the parent cannot influence the behavior.

Uncontrolled components are easier to use within their parents because they require less configuration. But they're less flexible when you want to coordinate them together.

Controlled components are super flexible, but require more configuration from their parents.

More [here](https://www.notion.so/Scalable-Capital-Berlin-Berlin-Germany-Remote-ac8ca2aa2c5f49738fc561709359b8c5?pvs=21)

## 10) What is "virtual DOM" in React and its advantages?

TL;DR: it's a tool that helps React apps achieve better performance and a smoother UX by optimizing the process of updating UI.

**Concept**

It's a lighter replica of the actual DOM, that's saved in the browser memory and doesn't directly change what is show on the user's browser.

**************Problem**************

The virtual DOM isn't faster than the actual DOM. However, the virtual DOM allows that the actual DOM to compute minimal operations when re-rendering UI.

When doing an UI update on actual DOM, it will re-render the element and all the children. In apps with tons of interactivity and state changes, updating UI is slow and inefficient.

**Solution**

The virtual DOM comes to save from this burden: when a state changes, React updates the virtual DOM before directly changing the real one (**render** phase). Then, React compares the current virtual DOM with a virtual snapshot taken just the before the update, and determines which elements changed (**reconciliation** phase). Only then it updates the elements on the real DOM (**commit** phase).

The object created by react (JSX or createElement) is the representation of that element in the virtual DOM.

**The advantages are:**

- **batched updates**: React batches multiple updates and apply them in a single pass. This brings the next advantage.
- **efficiency**: it minimizes the direct manipulation to the real DOM, saving resources
- **avoids layout thrashing**: React calculates the optimal way to update the DOM, avoiding unnecessary reflows and flickering;

**Reference**

[What is the virtual DOM in React? - LogRocket Blog](https://blog.logrocket.com/virtual-dom-react/)

## 11) Explain the concept of "render props" in React and provide an example of how they can be used to share functionality between components

It's a technique for sharing code between components using a prop whose value is a function and it returns a React element. In this way you leave to caller specify its own the render logic.

**Anatomy**

```jsx
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>

function DataProvider({render}) {
	 return render({target: 'Bernardo'});
}
```

********************References********************

[Render Props – React](https://legacy.reactjs.org/docs/render-props.html)

[cloneElement – React](https://react.dev/reference/react/cloneElement#passing-data-with-a-render-prop)

## 12) What's Fragments and why it's used?

**React Fragments**, represented by the **`<React.Fragment>`** tag or the shorthand **`<>`**, provide a way to group multiple elements without introducing an additional wrapping element in the DOM.

Here's why they are used:

1. **Avoiding Extra DOM Elements**: In React, when you return multiple elements adjacent to each other, you typically need to wrap them in a single parent element. However, sometimes you don't want an extra DOM element just for the purpose of grouping. This is where React Fragments come in. They allow you to group elements without adding any extra elements to the DOM.
2. **Cleaner JSX**: React Fragments improve the readability and cleanliness of your JSX code. Instead of introducing wrapper elements that don't serve any semantic purpose, you can use Fragments to keep your JSX more concise.
3. **Avoiding CSS and Layout Issues**: Introducing unnecessary wrapper elements can sometimes cause unexpected CSS and layout issues. Fragments help avoid such problems by not adding extra elements into the rendered HTML.

## 13) Difference between direct and functional updates

```tsx
// Direct update. 
// Updates are asynchronous and done in batches
// When we call for an update, React schedules the process
// Bellow, the setX will work with the same value of X
useEffect(() => {
	// x = 1
  setX(x + 1); // 1
  setX(x + 1); // 1
  setX(x + 1); // 1
});

// Functional update
// Here, React guarantees that you're working with the most-update value
// when calling setX
useEffect(() => {
	// x = 1
  setX((x) => x + 1); // 2
  setX((x) => x + 1); // 3
  setX((x) => x + 1); // 4
});
```