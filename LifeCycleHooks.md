# LifeCycle Hooks :-

* What does mounting mean?
* Since we're defining virtual representations of nodes in our DOM tree with React, we're not actually defining DOM nodes. Instead, we're building up an in-memory view that React maintains and manages for us.
* When we talk about mounting, we're talking about the process of converting the virtual components into actual DOM elements that are placed in the DOM by React.

## Four phases of a React component
* The React component, like anything else in the world, goes through the following phases
    - Initialization
    - Mounting (Birth of your component)
    - Update (Growth of your component)
    - Unmounting (Death of your component)

* The methods of ReactJS lifecycle
    - Initialization (setup props and state)
    - Mounting (componentWillMount, render, componentDidMount)
    - Updation 
        -- props (componentWillReceiveProps, shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate)
        -- states (shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate)
    - Unmounting (componentWillUnmount)

## Packaging and PropTypes 
* React provides a method for defining and validating these types that allow us to easily expose a component API.
* Not only is this a good practice for documentation purposes, It's great for building reusable react components.
* The prop-types object exports a bunch of different types which we can use to define what type a component's prop should be.

Default props

