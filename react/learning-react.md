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

## Lifting State Up
Currently, each Square component maintains the game's state. To check for a rinner, we need to maintain the state of all 9 squares in one location. Instead of the parent Board asking each child Square for its state, its recommended to store the game's state in the parent Board. This makes the code easier to understand, less susceptible to bugs and easier to refactor.

We add a constructor to the `Board` and initialise its state to contain an array of 9 `null` corresponding to the 9 squares. The `renderSquare` method uses the prop passing mechanism to let each child Square know its current value (`X`, `O`, or `null`). 
```javascript
renderSquare(i) {
    return <Square value={this.state.squares[i]} />;
}
```

Now we have to change what happends when a Square is clicked - the Board maintains which squares are filled but we need to create a way for the Square to update the Board's state. Since state is considered private to the component that defines it, we cannot update the Board's state directly from Square. The solution to this is to pass down a function from Board to Square to be called when a square is clicked. We change the `renderSquare` method in Board to pass the function:

```javascript
renderSquare(i) {
  return (
    <Square
      value={this.state.squares[i]}
      onClick={() => this.handleClick(i)}
    />
  );
}
```

Now, 2 props are being passed from Board to Square: `value` and `onClick`. `onClick` is a function that Square can call when clicked to update the Board's state. We update Square's `render` method to call the function passed down from Board when clicked.

```javascript
class Square extends React.Component {
  render() {
    return (
      <button
        className="square"
        onClick={() => this.props.onClick()}
      >
        {this.props.value}
      </button>
    );
  }
}
```

Since the Board passed `onClick={() => this.handleClick(i)}` to Square, Square essentially calls `this.handleClick(i)` when clicked.

We now add the `handleClick` method to `Board` to update the Board's state when a Square is clicked.

```javascript
handleClick(i) {
  const squares = this.state.squares.slice();
  squares[i] = 'X';
  this.setState({squares: squares});
}
```

**Note: in `handleClick` we call `.slice()` to create a copy of the `squares` array to modify instead of modifying the existing array.**

## Importance of Immutability
A main benefit of immutability is that it helps build _pure components_. Immutable data can easily determine if changes have been made, which helps to determine when a copmonent requires re-rendering.

## Function Components
**Function components** are a simpler way to write components that only contain a `render` method and don't have their own state. Instead of defining a class that extends `React.Component`, we can write a function that takes `props` as input and returns what should be rendered. We can replace the Square class with a function:

```javascript
function Square(props) {
  return (
    <button className="square" onClick={props.onClick}>
      {props.value}
    </button>
  );
}
```

Notice that we changed `this.props` to `props` since this is no longer a class.
