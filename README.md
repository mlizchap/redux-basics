# Redux 

- **Redux**: state management in React 

## The Store
- **state** -  is contained in a single object and is *read only*
- **store** - holds the state, contains 3 important methods: 
    1. `store.getState()` - retrieves the current state of the redux store 
    2. `store.dispatch(<action>)` - lets you dispatch actions to change the state of the app 
        - ex: `store.dispatch({ type: 'INCREMENT' })`
    3. `store.subscribe(fn)` - lets you register a callback function that will be called any time an action has been dispatched.  
        - ex: `store.subscribe(() => document.body.innerHTML = store.getState())`
- Accepts an argument of the top level reducer and returns the store of the appliation which is assigned to a variable

### The Store Setup
- install libraries:
    ```javascript
    $ npm install react-redux redux
    ```
- import the libraries, specifically the `Provider` component from `react-redux` and the `createStore` function from redux
    ```javascript
    import { Provider } from 'react-redux';
    import { createStore } from 'redux';
    ```
- use the createStore function from redux to create the store with reducers and assign to a variable
    ```javascript
    const store = createStore(() => {}) // function will eventually be top level reducer
    ```
 - wrap the app around a `Provider` tag that has the store property 
    ```javascript 
    <Provider store={store}>
        /* app will go here  */
    </Provider>
    ```
    
## Reducers 
### Create the reducers
- **reducer**: - accepts an action and state and manipulates the state to return new state 
    ```javascript
    export default function(state = null, action) {
        // switch statement to filter through action types will eventually go here 
        return state;
    }
    ```

### Combine the reducers 
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

### Wire the reducer to the store
- this allows the reducers to return the nessessary state to be contained in the store and eventually dispatched to the appropriate containers
    ```javascript
    import reducers from './reducers';

    const store = createStore(reducers);
    ```
### Wire up the reducer to component (component --> container)    
- containers = a component connected to redux
- to make a component a container user `connect`
    ```javascript
    import { connect } from 'react-redux'
    /*....*/
    export default connect(mapStateToProps)(BookList);
    ```
- use the function `mapStateToProps` to have access to the application state and use in the component 
    ```javascript
    function mapStateToProps(state) {
        return { books: state.books } /* whatever is returned will show up as props inside of BookList */
    }
    ```
- the state returned from mapStateToProps can be used in the component 
    ```javascript
    <div>Title: {this.props.book.title}</div>
    ```

## Actions 
### Create the action with an action creator
 - **action** - triggers a manipulation in state. The action object that will be filtered through reducers based on its `type` property 
- **action creator** - a function that returns an action 
    ```javascript
    export function createBook(userInput) {
        return {
            type: 'CREATE_BOOK', // types are usually in a different file
            payload: userInput
        };
    }
    ```
### insert the action into the reducer 
```javascript
export default function(state = null, action) {
    switch (action.type) {
        case CREATE_BOOK: 
            return [...state, {name: action.payload, completed: false }];
    }
    return state;
}
```

### Wire up the action to a component 
- `bindActionCreators`: makes sure the action flows thru all the diff reducers
- import the action, `bindActionCreators` from redux, and `connect` from react-redux
    ```javascript
    import { createBook } from '../actions/index';
    import { connect } from 'react-redux';
    import { bindActionCreators } from 'redux';
    ```
- use `mapDispatchToProps` - returns a function that makes sure the action is passed to the reducers 
- the function returned from the function can be used as props in the container, when the function is called, the result will be passed (dipatched) to all the reducers.
    - **longhand version**: 
        ```javascript
        function mapDispatchToProps(dispatch) {
            return bindActionCreators({ createBook: createBook }, dispatch) 
        }
        ```
    - connect the dispatch to the component 
        ```javascript
        export default connect(mapStateToProps, mapDispatchToProps)(BookList);
        ```
    - **shorthand version**: 
        ```javascript
        export default connect(mapStateToProps, { createBook })(PostsIndex);
        ```
- After you `mapDispatchToProps`, you can use inside the component as props
    ```javascript
    <li onClick={() => this.props.createBook(this.state.input)}
    ```

## Advanced Redux - Working with Async Code and Data 

### Redux Promise 
- if the lib sees a payload that is a promise - the action is stopped until the promise is resolved and sent to all the reducers
- setup: 
    ```javascript
    import promise from 'redux-promise';
    import { createStore, applyMiddleware } from 'redux';

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



