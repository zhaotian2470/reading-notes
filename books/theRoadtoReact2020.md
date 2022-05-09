# Fundamentals of React
begin react test:
* create project: `npx create-react-app hacker-stories`
* run under project: `npm run start`

## JavaScript
JavaScript attributes:
* function declaration: normal/arrow, arrow function declaration:
   * if there is one parameter, the parentheses is not required, such as `list => {return list}`
   * if only returns something, the block body (curly braces) is not required, such as `() => a`;
* shorthand object initializer notation
```
const firstName = 'Robin';
const user = { firstName: firstName,
};
console.log(user);
// { firstName: "Robin" }
```
* array destructuring, such as `const [firstItem, secondItem] = list`;
* object destructuring, such as 
```
const {
	firstName, 
	pet: {
		name,
	},
	} = user;
```
* spread operator, such as
```
const profile = { 
	firstName: 'Robin', 
	lastName: 'Wieruch',
};

const user = {
	...profile, 
	gender: 'male', 
	...address,
};

console.log(user);
// {
//   firstName: "Robin",
//   lastName: "Wieruch",
//   gender: "male"
// }
```
* rest operator, such as 
```
const user = {
	id: '1',
	firstName: 'Robin', 
	lastName: 'Wieruch', 
};

const { id, ...userWithoutAddress } = user;
console.log(userWithoutAddress);
// {
//   firstName: "Robin",
//   lastName: "Wieruch"
// }
```
* bind method, such as `onRemoveItem.bind(null, item)`;
* async/await: transform async to sync;
* extra variable: localStorage, fetch, axios(replace fetch for headless browser environment);

## JSX
JSX resembles HTML, but has the following exceptions:
* extend HTML attributes, [supported HTML attributes](https://reactjs.org/docs/dom-elements.html#all-supported-html-attributes);
* extend HTML handlers, such as onChange, onClick;
   * JSX support functions to prevent native browser behavior;
* everything in curly braces is JavaScript expression;

## component
component has the following attributes:
* javascript function;
   * parameter: props;
      * children: component children;
   * return JSX;
* render by ReactDOM;
* support some hooks:
   * useCallback: return memoized callback function
   * useEffect: if one of variables changes, call side-effect functions;
   * useReducer: replace useState for complex logic;
   * userRef(no re-render if update): persist value/DOM element between renders;
   * useState(re-render if update): persist state between renders;

# React's Legacy

## React Class Components
React components have undergone many changes, from createClass components over class components, to function components. 
* class component:
```
class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            searchTerm: 'React',
        };
    } render() {
        const { searchTerm } = this.state;
        return (
            <div>
                <h1>My Hacker Stories</h1>
                <SearchForm
                    searchTerm={searchTerm} onSearchInput={() => this.setState({
                        searchTerm: event.target.value
                    })}
                /> </div>
        );
    }
}
```
* function component:
```
const InputWithLabel = ({ id, value, type = "text", onInputChange, isFocused, children, }) => {

    const inputRef = React.useRef();
    React.useEffect(() => {
        if (isFocused && inputRef.current) {
            inputRef.current.focus();
        }
    }, [isFocused]);

    return (
        <>
            <label htmlFor={id}>{children}</label> &nbsp;
            <input
                ref={inputRef}
                id={id}
                type={type}
                value={value}
                onChange={onInputChange}
            />
        </>
    )
};
```

## Imperative React
the ref and its usage had a few versions:
* String Refs (deprecated)
* Callback Refs
* createRef Refs (exclusive for Class Components)
* useRef Hook Refs (exclusive for Function Components)

# Styling in React

reference: 
* [popular UI library suited for React](https://www.robinwieruch.de/react-libraries/);

## CSS in React
common CSS in React is similar to the standard CSS:
* give HTML elements a class(in React it's className) attribute;
* define style in CSS file;

## CSS Modules in React
CSS Modules are an advanced CSS-in-CSS approach. The CSS file stays the same, where you could apply CSS extensions like Sass, but its use in React components changes:
* special className, such as: `<div className={styles.container}>`;
* style attribute, such as: `<span style={{ width: '40%' }}>`;
The advantage of CSS modules is that we receive an error in the JavaScript each time a style isn’t defined. In the standard CSS approach, unmatched styles in the JavaScript and CSS files might go unnoticed.

## Styled Components in React
Styled Components is the most popular CSS-in-CSS appproaches.
* install styled-components: `npm install styled-components`;
* import styled-components in js: `import styled from 'styled-components'`;
* define style in js, such as:
```
const StyledContainer = styled.div` height: 100vw;
padding: 20px;
  background: #83a4d4;
  background: linear-gradient(to left, #b6fbff, #83a4d4);
  color: #171212;
`;
```
* use style in js, such as:
```
<StyledContainer>
</StyledContainer>
```

## SVGs in React
React can import SVG and used as component

# React Maintenance

## Performance in React
* don't run useEffect Hook on first render: keep mount status;
* don't re-render if not needed: use React.memo/React.useCallback;
* don't rerun expensive computations: use React.useMemo;

## TypeScript in React
TypeScript presents type errors during compiole time;


## Unit Testing to Integration Testing
run test: `npm test`

## React Project Structure
Put every React component into one directory

# Real World React
real examples:
* sorting;
* reverse sort;
* remember last searches;
* paginated fetch;

# Deploying a React Application

## Build Process
deploy with build:
* create build/ folder with bundled application, and take this folder and deploy it on a hosting provider: `npm run build`;
* if you want to use a local server, install an HTTP server and serve your application: `npm install -g http-server; http-server build/`;

## Deploy to Firebase

# Outline
give some suggestiongs for future reading
