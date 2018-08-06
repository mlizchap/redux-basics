# Redux 

- **Redux**: state management in React 

## Initial Setup 



## Setup the Store
- `$ npm install react-redux`
- **state** -  is contained in a single object and is *read only*
- **store** - holds the state, contains 3 important methods: 
    1. `store.getState()` - retrieves the current state of the redux store 
    2. `store.dispatch(<action>)` - lets you dispatch actions to change the state of the app 
        - ex: `store.dispatch({ type: 'INCREMENT' })`
    3. `store.subscribe(fn)` - lets you register a callback function that will be called any time an action has been dispatched.  
        - ex: `store.subscribe(() => document.body.innerHTML = store.getState())`
- Accepts an argument of the top level reducer and returns the store of the appliation which is assigned to a variable
    ```java
    const store = createStore(counter);
    ```
- wrap the app around a `Provider` tag that has the store property 
```javascript 
<Provider store={createStoreWithMiddleware(reducers)}>
    /* app will go here  */
</Provider>
```
## Actions 
 - **action** - an object that will be filtered through reducers based on its `type` property 
    ```javascript
    {
        type: FETCH_POSTS,
        payload: request 
    }
    ```
## Create the Action creator
- **action creator** - a function that returns an action 
    ```javascript
    export function selectBook(book) {
        return {
            type: 'BOOK_SELECTED', // types are usually in a different file
            payload: book
        };
    }
    ```



## Create the reducers
- **reducer**: - accepts an action and state and manipulates the state to return new state 
    ```javascript
    export default function(state = null, action) {
        switch (action.type) {
            case 'BOOK_SELECTED': 
                return action.payload /*piece of state used in application */
        }
        return state;
    }
    ```

## Combine the reducers 
- creates a top level reducer to be used to create the store 
```javascript
import { combineReducers } from 'redux';
import BooksReducer from './reducer_books';
import ActiveBook from './reducer_active_book';

const rootReducer = combineReducers({
  books: BooksReducer,
  activeBook: ActiveBook
});

export default rootReducer;
```

### Create the Containers
- containers = a component connected to redux
- use the react-redux library
    ```javascript
    $ npm install react-redux
    ```

## Wire Reducer into Component
- to make a component a container user `connect`
    ```javascript
    import { connect } from 'react-redux'
    /*....*/
    export default connect(mapStateToProps)(BookList);
    ```
- use the function `mapStateToProps` to have access to the application state and use in the component 
    ```javascript
    function mapStateToProps(state) {
        return {
            books: state.books /* whatever is returned will show up as props inside of BookList */
        }
    }
    ```
- the state returned from mapStateToProps can be used in the component 
    ```javascript
    <div>Title: {this.props.book.title}</div>
    ```

### Wire up the action to a component 
- `bindActionCreators`: makes sure the action flows thru all the diff reducers 
    ```javascript
    import { bindActionCreators } from 'redux';
    ```
- import the action and `bindActionCreators` from redux
    ```javascript
    import { selectBook } from '../actions/index';
    ```
- use `mapDispatchToProps` - returns a function that makes sure the action is passed to the reducers 
    - longhand version: 
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
    - shorthand version: 
        ```javascript
        export default connect(mapStateToProps, { fetchPosts })(PostsIndex);
        ```
- `dispatchToProps` allows the action to be used inside the component as props
    ```javascript
    <li onClick={() => this.props.selectBook(book)}
    ```

## Advanced Redux - working with data

### Redux Promise 
- if the lib sees a payload that is a promise - the action is stopped until the promise is resolved and sent to all the reducers
- setup: 
    ```javascript
    import promise from 'redux-promise';

    const createStoreWithMiddleware = applyMiddleware(promise)(createStore);

     <Provider store={createStoreWithMiddleware(reducers)}>
     /* app goes here */
     </Provider>
    ```
- using `redux-promise` in actions
```javascript
import axios from 'axios';
export const FETCH_POSTS = 'fetch_posts';

export function fetchPosts() {
    const request = axios.get(`${ROOT_URL}/posts${API_KEY}`);
    return {
        type: FETCH_POSTS, 
        payload: request // action does not go to reducer until promise is resolved
    }
}
```
- when dealing with data, it is better to convert arrays into hashes for faster loopup times 
```javascript
import { FETCH_POSTS } from '../actions';
import _ from 'lodash';

export default function(state = {}, action) {
    switch (action.type) {
        case FETCH_POSTS:
            return _.mapKeys(action.payload.data, 'id'); // returns a dictionary with id being the key
            
        default:
            return state;
    }
}
```









- **createStore**: a function from the Redux library that creates the *store*. 

- **Reducers**: accepts an action and state and returns new state 

 - **actionCreators**: a function that returns an object (an action)
 
 ## createStrore

## The Store 



## Reducers 
- state should be initialized and return a default value

## combineReducers


## Actions and Action Creators 
<< LIFECYCLE OF AN ACTION >>
- starts off with an event triggered by a user or a page initially loading 
- calls an **action creator** - a function that returns an action (an object)

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


## React-Redux
- the render method is called every time the store's state changes 
- Redux: for dealing with the data of the app
- React: for dealing with the views of the app 
- **containers**: the most parent component that cares about a piece of state should be a container

### Connecting Reducers with Components

### Connecting Actions with Components
