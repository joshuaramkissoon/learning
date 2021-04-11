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
