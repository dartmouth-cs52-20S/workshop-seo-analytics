# CS52 Workshops:  SEO and Analytics

![](https://media.giphy.com/media/l46Cy1rHbQ92uuLXa/source.gif)

Mixpanel is a Dashboard that provides you with various insights on how your website or application is being used.
In this workshop we'll be using the `react-ab-test` library to  create and run an A/B Test on a new starterpack route.

## Overview

We're going to set up and run and A/B Test on our starterpack and  use Mixpanel to see the results. This will allow you to see how many times a user clicks "Yes" based on the positioning of the text (above or below the "Yes" and "No" buttons)

## Setup

### First, we need something to analyze

Go to your starterpack and create/go to an `analytics` branch
```git
git checkout -b analytics
```

Once you're in the new branch, add the `@marvelapp/react-ab-test` library to your project using `yarn`
```bash
yarn add @marvelapp/react-ab-test
```
* `react-ab-test` will allow us to define an experiment, the variants, and interact with mixpanel!

### Let's Set up Mixpanel

- Go to https://mixpanel.com/ and create an account. When they ask for your company name on the second sign-up window, make one up and say you're a company of 1.

- Create a project: ![](https://i.imgur.com/1gDwmsD.png) You can name your project whatever you like.

- Under settings, go to "Set up Mixpanel" and copy the HTML snippet: ![](https://i.imgur.com/s0pvntu.png) Paste this snippet into your index.html file in the bottom of the file's `<head>`.
    * Behind the black box is a mixpanel token. We're living life on the edge and using it in the front end (the starterpack has no back end). 
    * Normally you want to hide this token somewhere in the back end, similar to what we did for the blog API.

- While you're here, *turn off adblock*. Mixpanel and your adblocker are not friends, so Mixpanel won't be able to receive any data sent if an adblocker is active.

## Step by Step

### Set up the starterpack

1. If you haven't made the `analytics` branch. Do that now (`git checkout -b analytics` in the command line).

2. Let's set up an `ABTester` component in `abtest.js`.
```bash
# In your starterpack folder
cd src/components && touch abtest.js
```

3. In your `app.js` file, add a new route to an `ABTester` component. We haven't made it yet, but that's where we'll set up and run our test. Don't forget to put it in the `Nav` too.

<details>
 <summary> Need help? These are your updated components </summary>
 
 ```javascript
// keep previous imports
import ABTester from './abtest';

// Welcome and About components are unchanged

const Nav = (props) => {
  return (
    <nav>
      <ul>
        <li><NavLink to="/" exact>Home</NavLink></li>
        <li><NavLink to="/about">About</NavLink></li>
        <li><NavLink to="/abtest">A/B Test!</NavLink></li>
      </ul>
    </nav>
  );
};

const App = (props) => {
  return (
    <Router>
      <div>
        <Nav />
        <Switch>
          <Route exact path="/" component={Welcome} />
          <Route path="/about" component={About} />
          <Route path="/abtest" component={ABTester} />
          <Route component={FallBack} />
        </Switch>
      </div>
    </Router>
  );
};

export default App;

 ```
</details>

### Creating an A/B Test

Let's create an A/B Test! Our test is going to see if people click yes or no based on the whether the question is above or below the buttons.

4. Let's start working in `abtest.js`. When you open the file, import the standard stuff: React and Component (from react), Link (from react-router-dom), and your `style.scss`. In addition to the usual, import `emitter` and `experimentDebugger` from `@marvelapp/react-ab-test`. We'll be importing more from `react-ab-test`, so stay tuned.

5. Let's add an `experimentDebugger`. The `experimentDebugger` will allow us to switch between variants when we debug the test. To do this, Add the following above the class declaration:
```javascript
experimentDebugger.enable();
```
6. The next thing we need to do is define our test. We'll use the `emitter` for that. Under `experimentDebugger.enable`, set up your test:

```javascript
emitter.defineVariants('buttonExperiment', ['text-above', 'text-below'], [50, 50]);
```
* The first parameter is the experiment name, the second parameter is an array of the variants that you'll test, and the third is the weights of how frequent each parameter will show up. The third parameter must add up to 100.
    * You can have more than two variants, but remember that the more variants, the tougher the analysis.
    * When selecting your weights for testing a brand new component, it's usually a best practice to start out by weighting the variants evenly. This will ensure you get a handful of trials for all variants.

<details><summary>At this point, your <code>abtest.js</code> should look like this: </summary>

```javascript
import React, { Component } from 'react';
import { Link } from 'react-router-dom';
import { emitter, experimentDebugger, } from '@marvelapp/react-ab-test';

experimentDebugger.enable();
emitter.defineVariants('buttonExperiment', ['text-above', 'text-below'], [50, 50]);

class ABTester extends Component {

  render() {
    return ();
  }
}

export default ABTester;
```
</details>

This doesn't do much, so let's add the experiment!

### Adding `<Experiment>` and `<Variant>`

The nice thing about `react-ab-test` is its use of React Components to define and experiment and view the variants. Let's start working in our `render()` function to build the experiment.

7. Before adding the new components, add `Experiment` and `Variant` to the list of imports for `react-ab-test`. The updated import statement should look like this:

```javascript
import {
  emitter, experimentDebugger, Experiment, Variant,
} from '@marvelapp/react-ab-test';
```

We're going to work inside the `render()` function's return statement for now.

8. Let's wrap everything inside our return statement in a `<div>` which we'll use for styling later. Inside that, we'll add the `<Experiment>` component. We'll set the `name` prop equal to the experiment name (`buttonExperiment`).

9. Each direct child of the `<Experiment>` tag must be a `<Variant>`. We'll add two of those, one for each variant in our test. Add the `name` prop and set each one equal to a variant's name. After this step, your return statement should look like this:
<details><summary>Here's what we just added</summary>

```html
  <div>
    <Experiment name="buttonExperiment">
      <Variant name="text-above">
      </Variant>
      <Variant name="text-below">
      </Variant>
    </Experiment>
  </div>
```

</details>

Now that we have the experiment skeleton set up, let's add something to it!

### Populating the variants

10. We'll provide you with a component for the yes/no buttons for your test. Put this in `abtest.js` outside of the class component:
```javascript
const TestButtons = (props) => {
  return (
    <Link to="/">
      <div>
        <button onClick={props.onClick} type="submit"><h2>Yes</h2></button>
        <button type="submit"><h2>No</h2></button>
      </div>
    </Link>
  );
};
```

* The "No" button isn't linked to `props.onClick` like the "Yes" button because you only care about when the user clicks "Yes". We'll set up the test to track whenever a user goes on the page, so clicking "No" in our test has the same effect as no response.

11. Inside each variant, add the following:
```html
<h1>Continue</h1>
<TestButtons onClick={this.onButtonClick} />
```
12. Let's also make a quick `onButtonClick()` function in your `ABTester` component:
```javascript
  onButtonClick = (event) => {
    console.log('Yes!');
  }
```
We'll come back to this and link it up with the experiment later in the tutorial.

Start up your starterpack (`yarn start`) and go to your A/B Test route. You should see a little box on the bottom center of the window that says "1 Active Experiment." If you click on the box, this should appear:

![debugger](https://i.imgur.com/m0U8j96.png)
(You might have "text-below" checked, that is also okay. The checked selection is the active variant)

* This little panel is the `experimentDebugger` we enabled earlier. It will allow you to switch between the different variants without having to directly access the site's data.
    * Even though the panel says that it will be hidden on production builds the You'll want to delete this before deploying your website, you'll want to delete the line that enabled the debugger as a safety precaution.

Each variant should show the same thing when loaded:

![](https://i.imgur.com/uVjxqR3.png)

Strange? Nope--the two variants have the exact same code so they should appear the same. Let's fix that:

13. As its name implies, change the `text-below` variant by placing the header text below the buttons.
```html
<TestButtons onClick={this.onButtonClick} />
<h1>Continue</h1>
```
Your render function is complete! 

<details><summary>Here's the entire render function</summary>

```html
  render() {
    return (
      <div className="">
        <Experiment name="buttonExperiment">
          <Variant name="text-above">
            <h1>Continue?</h1>
            <TestButtons onClick={this.onButtonClick} />
          </Variant>
          <Variant name="text-below">
            <TestButtons onClick={this.onButtonClick} />
            <h1>Continue?</h1>
          </Variant>
        </Experiment>
      </div>
    );
  }
```

</details>

<details><summary>Here's some CSS to style your site</summary>

```css
body, html {
  font-family: "Helvetica Nueue", sans-serif;
  height: 100%;
}

.active {
  color: black;
}

button{
  background-color: rgba($color: #53cbf1, $alpha: 1.0);
  padding: 10px;
  border-radius: 10px;
  border-style: solid;
  width: 75px;
  margin: 5px;
}

```
</details>

### Integrating your test with Mixpanel

Now it's time to send your test results to Mixpanel! With `react-ab-test`, it's super easy to set up.

14. In the `onButtonClick()` function, use the `emitter` to send a win with the following line:
    ```javascript
    emitter.emitWin('buttonExperiment');
    ```
    * A "win" is equivalent to saying that the target object was selected. In our case, the "Yes" button is a win because the user chose to continue.
    * How does our program know which variant is used? That comes with the next step

15. Add `mixpanelHelper` to your list of imports from `@marvelapp/react-ab-test`. Above the `experimentDebugger.enable()` line, add the following:
    ```javascript
    mixpanelHelper.enable();
    ```
    * `mixpanelHelper` handles all the Mixpanel-related functionality for our experiment. Next time you load your site you will likely see warnings that `componentWillMount()` has been renamed, and that's ok. It's just `mixpanelHelper` at work.
    * Some important terminology:
        * When a test is loaded, you "play" the experiment, and an __"Experiment Play"__ event is sent to mixpanel
        * When a win is emitted, `mixpanelHelper()` sends the pertinent information to Mixpanel. It's sent as an __"Experiment Win"__ event. 

Your webpage should look similar to one of these screenshots based on the variant:


|text-above|text-below|
|:---:|:---:|
|![](https://i.imgur.com/J3ovUc4.png)|![](https://i.imgur.com/40m1V2y.png)|

16. Don't want to use the experiment debugger to change variants? You can also use Chrome Devtools! Under the "Application" window go to "Local Storage" > Your local host. It should have a "PUTSHELL-[your experiment name]" key, and the corresponding value should be the displayed variant: 
![](https://i.imgur.com/sxkk9L1.png)
You could change the value to the other variant's name, but don't change it to something random!

Now you're ready to test and analyze!

### "Running" your test and seeing your results

18. Double-check that your ad-blocker is disabled. Mixpanel won't be able to connect to your page if the ad-blocker is on.

19. "Run" your test a few times by going to your A/B Test route, clicking on either the "Yes" or "No" button. To re-run your test, either close the webpage or refresh it. Simply going back to the route will not count as a new "Play".

20. After running your test and switching variants a couple times, let's go back to mixpanel. On the top click on "Analysis" and open up the "Insights" page. You should see a line graph with some data!

![](https://i.imgur.com/0HzmiH8.png)

This is cool, but it isn't very useful. We see how many times the experiment was played and won, but it's not variant-specific.

21. Let's break our data down! Add a breakdown and in the search bar type "Variant". You should see a variant option show up, and click on that.

22. The variants are separated, but the lines still look confusing. A bar graph might be a better way to show our results. To the top right of the graph, Change "Line" to "Bar". Now you can easily see how many times each experiment was played and won based on the variant! 

Your breakdowns and graph should look similar to this (with different numbers):

![](https://i.imgur.com/Kuf0jAT.png)

### All done!

What you should have at this point:

* [ ] Starterpack with a new route for your A/B Test
* [ ] The A/B Test with two variants: The question "Continue?" above the yes/no buttons in one variant and below the buttons in another.
* [ ] Mixpanel should be able to track whenever someone "plays" the experiment or there's a "win"

## Summary / What you Learned

* [x] How to use `react-ab-test` to set up an A/B test in a React App
* [x] How to collect and view data collected by Mixpanel

## Reflection

1. What are the most important factors to increasing your ranking on SERPs? 
2. What are the benefits of running an A/B Test?

## Resources

* https://github.com/marvelapp/react-ab-test
* https://www.javascriptjanuary.com/blog/ab-testing-in-react
* https://databox.com/seo-statistics
* https://databox.com/how-to-increase-referral-traffic
