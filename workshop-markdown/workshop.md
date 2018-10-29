# React Router

#workshop

---

### What kind of knowledge am I assuming for this Workshop?

- Basic Javascript / HTML
- Basic React
  _ UI as a function of props and state.
  _ Local component state management. _Using_ `this.setState` \* Passing down callback function to children to update parent state.
- JSX
- ES6 arrow functions

### What is React Router?

- React Router is a Routing Library for React written by Ryan Florence and Michael Jackson.
  _What does this mean?_ When you are building Single page applications (SPA) with React, you still want to be able to emulate having multiple pages in your application. Or more generally, you sometimes want to react (no pun intended) to changes in your application depending on the current URL path. For example, if you wanted to share a particular part of your application with your friend, you could send them the URL path and they could easily navigate to that URL using the address bar, and see exactly what you want them to see. This is where React Router comes in! React Router lets us map a path to a React component! \* _Example_ if current url is `“/“` then show the `Home` component.

## Why use React Router?

- It is very easy to set up and use!
- It is the most popular routing library for React! 33,000 ⭐️ on Github! Over [] Downloads this week!

### Let’s get started!

#### What will we be building?

Since my favorite movie is Paddington, we will be building a very rudimentary Jam Recipe idea application using React + React Router!🍊🐻

###### Application Specifications:

- At the root URL, we should have a list of Jam Recipe idea `Link`’s. _learn about Link, Route, URL params_
- Add a page for each Jam idea where users can thumbs up or thumbs down a Jam idea. _learn about the match prop, render prop, Redirect component_
- If the user tries to navigate to an invalid URL, we want to display a 404 Error page. _learn about Switch._
- There is a page for users to post recipe ideas! _learn about Prompt, history.push, render prop_
- **If we have time:** Add basic authentication to project - _learn about Higher Order Components, Redirect component + passing state using the Redirect component’s to prop_

### General Idea

We want to build a simple client side React Application (_if time permits, with authentication_) using React Router. We want to be able to post recipe ideas on one page and display all suggestions on the homepage as a link that will take us to a page to up vote or down vote an idea.

### Learning Outcomes:

Learn the fundamental components/ideas behind React Router and successfully build a simple Jam recipe idea application.

1. Add `react-router-dom` to dependencies
2. `import` components

```javascript
import { BrowserRouter } from 'react-router-dom';
```

This would be a good time to talk about what `BrowserRouter` is.

The `BrowserRouter` is the most important component coming out of `react-router-dom`. `BrowserRouter` helps us react to changes in the URL path and manage our location state - it uses the HTML5 History API under the hood. It is important to note that `BrowserRouter` expects a single child rendered inside of it.

```jsx
<BrowserRouter>
  <div>{/* ... */}</div>
</BrowserRouter>
```

The second most important component coming out of `react-router-dom` is the `Route` component. The `Route` component lets us configure what to render when the URL changes.

```jsx
const Home = props => <h1>Home</h1>;
const Add = props => <h1>Add a recipe!</h1>;

<BrowserRouter>
  <div>
    <Route path="/" component={Home} />
    <Route path="/add" component={Add} />
  </div>
</BrowserRouter>;
```

Wait, this isn’t what we want. Why is the Home component rendering when we are on `/add`? This is because `react-router` under the hood is using regular expressions to match our paths to determine which components to render so technically, `/` will match on `/add`. So what do we do to remedy this? We have to tell our `Route` that it should only do an exact match of `/` to display the `Home` component.

```jsx
const Home = props => <h1>Home</h1>;
const Add = props => <h1>Add a recipe!</h1>;

<BrowserRouter>
  <div>
    <Route exact path="/" component={Home} />
    <Route path="/add" component={Add} />
  </div>
</BrowserRouter>;
```

Now having `Route`’s is nice however, as of right now, we can only update the page by using the address bar and by using the back and forward buttons. This isn’t a great UX. We want to add links to our page so the user can navigate around our application without using the address bar. Let’s build a Navigation bar!

```jsx
function Navbar(props) {
  return (
    <ul id="navbar">
      <li>
        <Link to="/">Home</Link>
      </li>
      <li>
        <Link to="/add">Add Recipe Idea</Link>
      </li>
    </ul>
  );
}
```

Super! 🚀 we are now able to navigate to the `/` and `/add` paths. I think it would be a better UX if we were to change the color of the links depending on which path we currently on. Now there are several ways of doing this however, the easiest way is to use `react-router-dom` `NavLink` component. It takes the same `props` as `Link` however it also takes an `activeClassName` property which is the name of the CSS class to apply to the link when the route matches. I've already made one inside of `styles.css` called `.activeLink`! Let's use this!

```jsx
function Navbar(props) {
  return (
    <ul id="navbar">
      <li>
        <NavLink to="/" activeClassName="activeLink">
          Home
        </NavLink>
      </li>
      <li>
        <NavLink to="/add" activeClassName="activeLink">
          Add Recipe Idea
        </NavLink>
      </li>
    </ul>
  );
}
```

This is really nice however, it looks like the Home link is always active. Like we talked about earlier because `/` is matched inside of `/any/path/` we need to explicitly tell the `NavLink` to only apply the `class` when the route matches `exact`ly.

```jsx
function Navbar(props) {
  return (
    <ul id="navbar">
      <li>
        <NavLink exact to="/" activeClassName="activeLink">
          Home
        </NavLink>
      </li>
      <li>
        <NavLink to="/add" activeClassName="activeLink">
          Add Recipe Idea
        </NavLink>
      </li>
    </ul>
  );
}
```

🙌 Great! We now have Links to navigate around our small application. There’s a small flaw in our application right now. What happens if we go to a `Route` path that we haven’t yet configured? Let’s see what happens right now if we try to navigate to a random URL. It looks like the header is there but the body of the application is blank. Let’s improve this UX by displaying a 404 Error page and another `Link` to go back to the homepage.

We’ve hit yet another problem. How to we tell React Router to show the Error page only if the first two Routes we configured are not matched? It would be nice if we had some sort of `switch` like statement that allows us to have a default case in case none of the cases match. We are in luck ✨! `react-router-dom` comes with a `Switch` component that does exactly this. Let’s wrap our `Route` components with this `Switch`.

```jsx
<div className="App">
  <BrowserRouter>
    <div>
      <Navbar />
      <Switch>
        <Route exact path="/" component={Home} />
        <Route path="/add" component={Add} />
        <Route component={Error} />
      </Switch>
    </div>
  </BrowserRouter>
</div>
```

Let’s build a proper `Error` component! Let’s provide a simple 404 Error with the message “Page Not Found”. We should also refer to the current path name somehow to let the user know that the current path is not valid.

```jsx
const Error = props => (
  <div>
    <h1>404 Error: Page not found</h1>
    {/*TODO: Add a short sentence indicating to the user that the current path is invalid*/}
    {/*TODO: Add a Link component to take the user back home*/}
  </div>
);
```

When we pass a component into our `Route` we implicitly get a couple important props passed to our component. Let’s check those out .

```jsx
const Error = props =>
  console.log(props) || (
    <div>
      <h1>404 Error: Page not found</h1>
      {/*TODO: Add a short sentence indicating to the user that the current path is invalid*/}
      {/*TODO: Add a Link component to take the user back home*/}
    </div>
  );
```

If you open up the console, you will notice that we have 3 important new props added to our component! `match`, `location`, and `history`.

`location` provides us information about the current location. This object sounds like it is exactly what we need to accomplish our task. We will discuss `history` and `match` later!

_In our console_
![](&&&SFLOCALFILEPATH&&&invlid-url.png)

It looks like we can access the current URL path using `props.location.pathname`. Lets use this to display the current URL path in our error message.

```jsx
const Error = props => (
  <div>
    <h1>404 Error: Page not found</h1>
    <p>
      <code>{props.location.pathname}</code> is an invalid path
    </p>
    {/*TODO: Add a Link component to take the user back home*/}
  </div>
);
```

Now if you try to navigate to an invalid URL path i.e. a path that isn’t already in our Routes, we should see this -

![](&&&SFLOCALFILEPATH&&&404-error-page.png)

Great! Now let’s finish off the last `TODO` we set for ourselves - Add a `Link` to direct the user back home.

```jsx
const Error = props => (
  <div>
    <h1>404 Error: Page not found</h1>
    <p>
      <code>{props.location.pathname}</code> is an invalid path
    </p>
    <Link to="/">Take me back to safety</Link>
  </div>
);
```

Awesome! 🚀 We now are able to traverse our application and show an error page if the page doesn’t exist. This is great but, our application is pretty boring and static so far. Let’s add some state to our `App` component by refactoring `App` to be a `class` .

```jsx
class App extends React.Component {
  render() {
    return (
      <div className="App">
        <BrowserRouter>
          <div>
            <Navbar />
            <Switch>
              <Route exact path="/" component={Home} />
              <Route path="/add" component={Add} />
              <Route component={Error} />
            </Switch>
          </div>
        </BrowserRouter>
      </div>
    );
  }
}
```

Cool. Now we have some global `state` in our application. Let’s add `recipes` to our state and initialize it with an empty array.

```jsx
class App extends React.Component {
  state = {
    recipes: []
  };
  render() {
    return (
      <div className="App">
        <BrowserRouter>
          <div>
            <Navbar />
            <Switch>
              <Route exact path="/" component={Home} />
              <Route path="/add" component={Add} />
              <Route component={Error} />
            </Switch>
          </div>
        </BrowserRouter>
      </div>
    );
  }
}
```

Let’s create a callback method on `App` that we can pass into our `Add` component which will update this `recipes` state for us when we add a new recipe to our app.

```jsx
class App extends React.Component {
  state = {
    recipes: []
  };

  addRecipe = recipe =>
    this.setState(({ recipes }) => ({ recipes: recipes.concat([recipe]) }));

  render() {
    return (
      <div className="App">
        <BrowserRouter>
          <div>
            <Navbar />
            <Switch>
              <Route exact path="/" component={Home} />
              <Route path="/add" component={Add} />
              <Route component={Error} />
            </Switch>
          </div>
        </BrowserRouter>
      </div>
    );
  }
}
```

Now we want to be able to pass this `addRecipe` method as a callback to the `Add` component as props but, how do we do this? The `Route` component conveniently comes with a `render` prop that allows us to determine what component should be rendered and how this component should be rendered when a path is matched. This gives us some more control over our rendered component.

```jsx
return (
  <div className="App">
    <BrowserRouter>
      <div>
        <Navbar />
        <Switch>
          <Route exact path="/" component={Home} />
          <Route
            path="/add"
            render={props => <Add addRecipe={this.addRecipe} {...props} />}
          />
          <Route component={Error} />
        </Switch>
      </div>
    </BrowserRouter>
  </div>
);
```

Spectacular! 🙌 Now we have a way to update the parent state with a new recipe! Let’s make the `Add` component a little bit more complex and add a `form` that will have a simple text input field and a submit button! We will make use of this `addRecipe` callback method we passed earlier `onSubmit` of the `form`

```jsx
class Add extends React.Component {
  state = {
    field: ''
  };

  handleSubmit = event => {
    event.preventDefault();
    const input = event.target.elements[0];
    const name = input.value.trim();
    const id = getUniqueID();
    const recipe = { id, name, author: '', likes: 0 };

    this.props.addRecipe(recipe);
    input.value = '';
  };

  handleOnChange = event => this.setState({ field: event.target.value });

  isDisabled() {
    return this.state.field.trim() === '';
  }

  render() {
    return (
      <div>
        <h1>Add a recipe!</h1>
        <form onSubmit={this.handleSubmit}>
          <input
            type="text"
            id="input-field"
            value={this.state.field}
            onChange={this.handleOnChange}
          />
          <button disabled={this.isDisabled()}>Add recipe</button>
        </form>
      </div>
    );
  }
}
```

Stellar ⭐️. Now let’s make this an even better UX! If the user adds something to the text field and tries to navigate away, we want to ask the user if they really want to navigate away and lose their awesome jam idea ☹️. Luckily for us, `react-router-dom` gives us a nice component to handle this - the `Prompt` component. Let’s import this from `react-router-dom`

```jsx
class Add extends React.Component {
  state = {
    field: ''
  };

  handleSubmit = event => {
    event.preventDefault();
    const input = event.target.elements[0];
    const name = input.value.trim();
    const id = getUniqueID();
    const recipe = { id, name, author: '', likes: 0 };

    this.props.addRecipe(recipe);
    input.value = '';
  };

  handleOnChange = event => this.setState({ field: event.target.value });

  isDisabled() {
    return this.state.field.trim() === '';
  }

  render() {
    return (
      <div>
        <h1>Add a recipe!</h1>
        <Prompt
          when={this.state.field.trim() !== ''}
          message={'Are you sure you want to leave?'}
        />
        <form onSubmit={this.handleSubmit}>
          <input
            type="text"
            id="input-field"
            value={this.state.field}
            onChange={this.handleOnChange}
          />
          <button disabled={this.isDisabled()}>Add recipe</button>
        </form>
      </div>
    );
  }
}
```

The `Prompt` component accepts two properties - `when` and `message`. Both properties are pretty explanatory - the `when` accepts a boolean value indicating when we should block the user from navigating and the `message` is the message we will show the user when they are prompted.

Now, let’s direct the user to the `Home` route when the user submits an idea. We will later display all Jam idea’s on the front page to choose from! This is where we will talk about the `history` object that is passed implicitly into our components by `Route`. If we `console.log` the `props` argument in our `render` prop and open up the `history` object, you can see several useful functions. The one we are interested in is the `push` method. This allows us to `push` a new route on the the route stack.

Let’s call this `history.push` function at the end of our `handleSubmit` method.

```jsx
class Add extends React.Component {
  state = {
    field: ''
  };

  handleSubmit = event => {
    event.preventDefault();
    const input = event.target.elements[0];
    const name = input.value.trim();
    const id = getUniqueID();
    const recipe = { id, name, author: '', likes: 0 };

    this.props.addRecipe(recipe);
    this.setState({ field: '' });
    this.props.history.push('/');
  };

  handleOnChange = event => this.setState({ field: event.target.value });

  isDisabled() {
    return this.state.field.trim() === '';
  }

  render() {
    return (
      <div>
        <h1>Add a recipe!</h1>
        <Prompt
          when={this.state.field.trim() !== ''}
          message={'Are you sure you want to leave?'}
        />
        <form onSubmit={this.handleSubmit}>
          <input
            type="text"
            id="input-field"
            value={this.state.field}
            onChange={this.handleOnChange}
          />
          <button disabled={this.isDisabled()}>Add recipe</button>
        </form>
      </div>
    );
  }
}
```

Whoops! It looks like this isn’t working completely how we want it to. It looks like the `history.push` call is being called too quickly, before the `this.setState({field: “”})` is able to finish. Many developers don’t know this but `this.setState` accepts a `callback` function as its second argument. This `callback` is called right after React finishes updating the state. Let’s pass an anonymous function that invokes `this.prop.history.push(“/")` .

```jsx
class Add extends React.Component {
  state = {
    field: ''
  };

  handleSubmit = event => {
    event.preventDefault();
    const input = event.target.elements[0];
    const name = input.value.trim();
    const id = getUniqueID();
    const recipe = { id, name, author: '', likes: 0 };

    this.props.addRecipe(recipe);
    this.setState({ field: '' }, () => this.props.history.push('/'));
  };

  handleOnChange = event => this.setState({ field: event.target.value });

  isDisabled() {
    return this.state.field.trim() === '';
  }

  render() {
    return (
      <div>
        <h1>Add a recipe!</h1>
        <Prompt
          when={this.state.field.trim() !== ''}
          message={'Are you sure you want to leave?'}
        />
        <form onSubmit={this.handleSubmit}>
          <input
            type="text"
            id="input-field"
            value={this.state.field}
            onChange={this.handleOnChange}
          />
          <button disabled={this.isDisabled()}>Add recipe</button>
        </form>
      </div>
    );
  }
}
```

Now let’s display all of these awesome recipe ideas on the Homepage for everyone to see! Let’s make a `Link` out of each idea where users can give the idea a thumbs up or (thumbs down ☹️.)

```jsx
const Home = props => (
  <div>
    <h1>The Jam App</h1>
    <ul style={{ listStyleType: 'none' }}>
      {props.ideas.map(idea => (
        <li key={idea.id}>
          {' '}
          <Link to={'/jams/' + idea.id}>{idea.name}</Link>
        </li>
      ))}
    </ul>
  </div>
);
```

Something like this will do. Notice how we are organizing all of our Jam ideas into a path `/jams` and then appending the Jam ID to the end to identify which Jam details page we are on.
Now let’s add a Jam idea using our form! Notice how we have a link for each idea we add to the form on our homepage. If you click on one of them you will notice that we are getting the 404 Error page, which makes sense since we didn’t configure this new `/jams` path as a `Route`. Let’s do this now! We’ve hit a problem: How do we refer to the Jam ID appended to the end of the route? We can’t just make a `Route` for each Jam idea since we won’t know ahead of time the unique identifier. `react-router` allows us to add URL parameters using a special syntax.

```jsx
<Route path="/path/:myParameter" component={MyComponent} />
```

For example, if we were making a Facebook clone, and we wanted to make a profile page for each user, we might have something like

```jsx
<Route path="/profile/:uid" component={Profile} />
```

Where we can refer to the unique identifier appended to the end of the path as `uid`. Let’s add something like this for our application.

```jsx
<Route path="/jams/:id" render={props => <h1>Jam Ideas!</h1>} />
```

Now let’s create a new component for the Jam idea details page. Let’s call this component - `JamIdea`. What we want to do is pass down the `App` component’s `getRecipe` callback function to `JamIdea` so we can grab the recipe from the `App` state. Let’s take a look at this method real quick. Simply put, it takes in an `id` of a recipe and returns the recipe object of that `id` from `state.recipes`. Let’s use this callback function inside of `JamIdea` to get the recipe object from `state.recipes` at `id` matched inside of the URL.

```jsx
function RecipeIdea(props) {
  const recipe = props.getRecipe(id); // wait, how do we get the id in the URL?
}
```

So how to we refer to that `id` in the URL? Again, luckily `react-router` has everything built in for us. Remember earlier we saw an object called `match`? This is exactly where the `id` is stored! 👍

```jsx
function RecipeIdea(props) {
  console.log(props);
  // const recipe = props.getRecipe(id)
}
```

If you do a `console.log(props)` inside of the `JamIdea` component you will see a `match` object! If you open up this object you will see a `path`, `url`, `isExact` and `params` . `path` and `isExact` don’t seem very useful. `url` looks promising but, it isn’t in a nice format for us to consume. If you open up `params` there will be a nice little surprise for us 🍬. We have an object with an `id` prop mapped to our recipe id! Let’s display the `id` as an `h1` tag to make sure this is working as intended.

```jsx
function JamIdea(props) {
  const recipeID = props.match.params.id;

  return <h1>{recipeID}</h1>;
}
```

Yes! This is exactly the `id` we want! Now all we need to do is use the `getRecipe` callback function to get the recipe at `id`.

```jsx
function JamIdea(props) {
  const recipeID = props.match.params.id;
  const recipe = props.getRecipe(recipeID);
  return <div>{JSON.stringfy(recipe)}</div>;
}
```

Hooray! We have the exact object that we wanted from our state. Let’s ignore the `author` property and display the `likes` and `name` attributes of the idea.

```jsx
function JamIdea(props) {
  const recipeID = props.match.params.id;
  const recipe = props.getRecipe(recipeID);

  return (
    <div>
      <h1>{recipe.name}</h1>
      <h2>{recipe.likes}</h2>
    </div>
  );
}
```

Let’s reiterate what we were trying to accomplish with this page! It is a details page for an idea that allows us to up or down vote an idea! Let’s add buttons to accomplish this task. Again we need to pass down a helper method from `App` down to `JamIdea` since we are trying to update the parent state. I’ve made a nice function - `handleVote` to update the recipe idea’s like value!

```jsx
function JamIdea(props) {
  const recipeID = props.match.params.id;
  const recipe = props.getRecipe(recipeID);
  const buttonBehavior = props.handleVote(recipeID);
  return (
    <div>
      <h1>{recipe.name}</h1>
      <h2>{recipe.likes}</h2>
      <button onClick={buttonBehavior('+')}>{'👍'}</button>
      <button onClick={buttonBehavior('-')}>{'👎'}</button>
    </div>
  );
}
```

The `handleVote` is a curried function or simply put, a function with many arguments that is broken down into a series of function that take in a part of the arguments. We are basically binding the `recipeID` to the function returned from `props.handleVote` and binding the behavior (“+” or “-”) to the function returned from `buttonBehavior` . This is a bit more of an advanced topic so if you want to learn more, check this link out -

Now, this is great! One last issue - as you might have noticed, our application crashes if we are trying to access a recipe idea that is not in our state. Let’s make this an even better UX by redirecting the user to the 404 error page and display a relevant error message to the user!
