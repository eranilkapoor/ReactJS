# Pure Component :- 
* For more performance and simplicity, React also allows us to create pure, stateless components using a normal JavaScript function.
* A Pure component can replace a component that only has a render function.
* Pure components are the simplest, fastest components we can write.
* Simplest One 
```
const HelloWorld = () => (<div>Hello world</div>);

```
* A Notification component 
```

const Notification = (props) => {
    const { level, message } = props;
    const classNames = ['alert', 'alert-'+ level];

    return (
        <div className={className}>
            {message}
        </div>
    )
};

```
## Advantages :-
* We can do away with the heavy lifting of components, no constructor, state, life-cycle madness, etc.
* There is no `this` keyword (i.e. no need to bind)
* Presentational components (also called dumb components) emphasize UI over business logic (i.e. no state manipulation in the component)
* Encourages building smaller, self-contained components
* Highlights badly written code (for better refactoring)
* Fast fast
* They are easy to reuse

## Disadvantages :-
* No life-cycle callback hooks
* Limited functionality
* There is no this keyword 