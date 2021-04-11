# Learning React
Source: https://reactjs.org/tutorial/tutorial.html

## Overview
React is a declarative JavaScript library for building user interfaces. It lets you compose complex UIs from small and isolated pieces of code called _components_. Components are used to tell React what we want to see on the screen and when our data changes, React efficiently updates and re-renders our components.

A component takes in parameters, called `props` short for "properties" and returns a hierarchy of views to dsiplay via the `render` method. This method returns a _description_ of what you want to see on the screen. Each component is encapsulated and can operate independently.

# Tic-Tac-Toe Tutorial
This tutorial runs through React basics by building a Tic-Tac-Toe app.

## Passing Data Through Props
To pass data from a _parent component_ to a _child component_, we use _props_. 

```javascript
class Board extends React.Component {
  renderSquare(i) {
    return <Square value={i} />;
  }
}
```
This parent Board component renders a number of squares and can pass parameters onto each child Square. In this case, the property passed is `value` and stores the number of the square rendered.

## Making an Interactive Component
We want a Square to be filled with an "X" when clicked. We start by doing an action when a button is clicked. We change the button tag that is returned from the Square component's `render()` function to show an _alert_ when clicked:
```javascript
class Square extends React.Component {
  render() {
    return (
      <button className="square" onClick={function() { alert('click'); }}>
        {this.props.value}
      </button>
    );
  }
}
```
An alternative to being extra verbose is using arrow function syntax:
```javascript
class Square extends React.Component {
  render() {
    return (
      <button className="square" onClick={() => alert('click')}>
        {this.props.value}
      </button>
    );
  }
}
```
The `onClick` prop needs a function as its argument. React will only call this function after a click. Without `() =>` is a common mistake and would fire the alert every time the component re-renders.

## Introducing State
We want components to be able to remember certain things (e.g. the Square should remember that it got clicked, and so it should be filled with an "X"). To do this, components use **state**. Components can have state by setting `this.state` in their constructors. `this.state` should be considered private to the component it's defined in.

To store the current value of the Square in `this.state`, and change when clicked:
- Add a constructor to the class to initialize the state
```javascript
class Square extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: null,
    };
  }

  render() {
    return (
      <button className="square" onClick={() => alert('click')}>
        {this.props.value}
      </button>
    );
  }
}
```

**Note: Constructors in JavaScript subclasses need to call `super`. All component classes that have a constructor should start with a `super(props)` call.**

When `this.setState` is called from an `onClick` handler in the Square's `render` method, React will re-render that Square whenever its `<button>` is clicked. After the update, the Square's `this.state.vaue` will be `'X'` so the square will be filled with `X`.

**Note: When you call `setState` in a component, the child components inside are udated automatically.**
