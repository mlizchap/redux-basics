# Redux 

- **Redux**: state management in React 

- **state**: is contained in a single object and is *read only*
- **createStore**: a function from the Redux library that creates the *store*. 
- **store**: holds the state of the application
- **Reducers**: accepts an action and state and returns new state 
 - **actions**: an object has a type that will be filtered through reducers 
 - **actionCreators**: a function that returns an object (an action)
 
 ## createStrore
- Accepts an argument of the top level reducer and returns the store of the appliation which is assigned to a variable
    ```java
    const store = createStore(counter);
    ```

## The Store 
- contains 3 important methods: 
    1. `store.getState()` - retrieves the current state of the redux store 
    2. `store.dispatch(<action>)` - lets you dispatch actions to change the state of the app 
        - ex: `store.dispatch({ type: 'INCREMENT' })`
    3. `store.subscribe(fn)` - lets you register a callback function that will be called any time an action has been dispatched.  
        - ex: `store.subscribe(() => document.body.innerHTML = store.getState())`

## Reducers 
- accepts an action and state and returns new state 
- state should be initialized and return a default value

## combineReducers
- combine reducers in the root reducer
- import the `combineReducers` functions as well as the reducers you want to use
    ```javascript
    import { combineReducers } from 'redux';
    import BooksReducer from './reducer_books';
    import ActiveBook from './reducer_active_book';
    ```
- the key is the peice of state the reducer returns and the value is the reducer 
    ```javascript
    const rootReducer = combineReducers({
    books: BooksReducer,
    activeBook: ActiveBook
    });
    ```
- export the root reducer
    ```javascript
    export default rootReducer;
    ```

## Actions and Action Creators 
<< LIFECYCLE OF AN ACTION >>
- starts off with an event triggered by a user or a page initially loading 
- calls an **action creator** - a function that returns an action (an object)
- the action is sent to all **reducers**
- reducers can chose to return a different piece of **state**, depending on the action 

### Actions 
- must have a type property (this is how they flow thru reducers)
- can have optional properties 
    ```javascript
    {
        type: BOOK_SELECTED, 
        book: { title: 'book 1' }
    }
    ```
### Action Creators
```javascript
    export function selectBook(book) {
        return {
            type: 'BOOK_SELECTED', // types are usually in a different file
            payload: book
        };
    }
```

## React-Redux
- the render method is called every time the store's state changes 
- Redux: for dealing with the data of the app
- React: for dealing with the views of the app 
- **containers**: the most parent component that cares about a piece of state should be a container

### Connecting Reducers with Components
- use the function `mapStateToProps` to have access to the application state and use in the component 
    ```javascript
    function mapStateToProps(state) {
        /* whatever is returned will show up as props inside of BookList */
        return {
            books: state.books
        }
    }
    ```
- to make a component a container user `connect`
    ```javascript
    import { connect } from 'react-redux'
    /*....*/
    export default connect(mapStateToProps)(BookList);
    ```

### Connecting Actions with Components
- `bindActionCreators`: makes sure the action flows thru all the diff reducers 
    ```javascript
    import { bindActionCreators } from 'redux';
    ```
- import the action and `bindActionCreators` from redux
    ```javascript
    import { selectBook } from '../actions/index';
    ```
- use `mapDispatchToProps` - returns a function that makes sure the action is passed to the reducers 
    ```javascript
    /** anything returned from this function will return as props on the BookList container **/
    function mapDispatchToProps(dispatch) {
        /** whenever selectBook is called, the result should be passed (dispatched) to all of our reducers **/
        return bindActionCreators({ selectBook: selectBook }, dispatch) 
    }
    ```
- connect the dispatch to the component 
```javascript
export default connect(mapStateToProps, mapDispatchToProps)(BookList);
```
- creating the action
- `dispatchToProps` allows the action to be used inside the component as props
    ```javascript
    <li onClick={() => this.props.selectBook(book)}
    ```