---
layout: post
title: "Simplifying Dynamic UIs with React Router"
date: 2019-10-23 20:08:46 -0400
comments: true
categories:
---
Imagine building a React application where the displayed content depends upon some data coming back from an API call. When one of the navigation tabs is clicked at the top of the page, a very specific Component is rendered as the page's content. This seems like an easy problem to solve, right?

For the sake of this example, let's say our React app is to display content about buildings. There are many different types of buildings - homes, shops, skyscrapers, monuments, museums, etc. - obviously, the content for each of them is going to be different. If the API response returns back a property like `buildingType`, we could probably create a switch statement to return the correct component dynamically:

```javascript
getComponent(buildingType) {
  switch(buildingType) {
    case "museum": return Museum;
    case "skyscraper": return Skyscraper;
    ...
    default: return DefaultBuilding;
  }
}

render() {
  const BuildingComponent = this.getComponent(this.state.buildingType);
  return <BuildingComponent />;
}
```

This solution is okay if we're creating a really simple React application. But for an more complicated UI, this isn't going to cut it. Maybe we need to set props on `Museum` and a totally different set of props on `Skyscraper`. Now what? Maybe you could convert each case into an object like so:

```javascript
case "museum": return {
  comp: Museum,
  props: {
    this.state.building.museumType,
    this.state.building.museumRanking
  }
}

...

render() {
  const building = this.getComponent(this.state.buildingType);
  const BuildingComponent = building.comp;
  const buildingProps = building.props;
  return <BuildingComponent {...buildingProps} />;
}
```

Now just imagine having to do this for 10 types of buildings. That switch statement would become hundreds of lines in mere seconds! This may be the simplest solution, but it's certainly ugly!

Enter React Router. React Router shows and hides content based on, you guessed it, routes. Usually, React Router is used for single page applications. You can show routes like /new, /edit, or /delete without actually having the entire application refresh. Instead, it simply shows the `New` component when the /new route is triggered.

Using React Router, we can simplify our code by using easy to understand `Routes` to dictate what Component and props we need.

First, we need a `Router`. There are many to choose from. `BrowserRouter` is what you'd expect - it updates the browser's URL bar with a new route. It looks as if you've actually navigated to a new page, even though the app does not actually reload from scratch. If you're building a React UI inside of another framework that already has routes, you obviously don't want to actually update the browser's URL bar. In that case, pick the `MemoryRouter`. Inside the `Router`, declare your routes like so:

```javascript
render() {
  return (
    <MemoryRouter>
      <Route exact path="/house" component={House} />
      <Route exact path="/museum" render = { (routeProps) => <Museum {...routeProps} {...this.getMuseumProps()} />} />
    </MemoryRouter>
  )
}
```

I've provided two examples of how to declare a component in a `Route`. The first way is pretty straight forward. The `House` component does not need any props passed to it, so we can just use the component prop in `Route` and pass it the component's name. The route for /museum is a little different. In this case, we use the render prop. You always need to pass down information about the current route state - that's what `routeProps` is. But then you can append any other props that component might require. In this case, you can make small, manageable functions to return the props needed for specific components.

But wait. How do we actually switch routes? `Link` is the obvious component to use that comes built into React Router. You can look at [their documentation](https://reacttraining.com/react-router/web/guides/primary-components/navigation-or-route-changers) for more information on that. In this example, we have some navigation tabs that calls an onClick method defined in the same component as our `Router`. How do we get the onClick method to change the route?

Step 1: Update the export default line-

```javascript
import { withRouter } from 'react-router-dom';

class MainBuilding extends React.Component {
  ...
}
export default withRouter(MainBuilding);
```

Step 2: Change the route in the onClick method-

```javascript
class MainBuilding extends React.Component {
  ...
  onTabClick(newBuildingType) {
    const newRoutePath = `/${newBuildingType}`;
    this.props.history.push(newRoutePath);
    this.setState({buildingType: newBuildingType});
  }
}
```

When we push the new route to history and update our state, our `render()` method is called and matches the new route. So, if the `newBuildingType` is "house", then the component matching the route for `/house` will be displayed. Yes, you might have a lot of routes. But you can refactor this into still yet more manageable chunks, unlike a switch statement.

I hope this example helps you think about how to incorporate React Router into you app to better organize serving up specific components.
