# React Gatsby

## React Component vs Gatsby Page

Pages go in the 'Pages' folder... all other pages (including naming) is optional.

Pages look like React Components but the main difference is that they are not Reuseable; store in src/Pages; start with a Small Letter

React Components are Reuseable (such as Nav); are often stored in the src/Components folder; start with a Capital Letter

## Gatsby Routing

Gatsby routing is done using the {Link} from 'gatsby'

- it uses HTML5 'push state' that changes the browser URL without doing a **full page refresh** the way an Anchor (a) tag does by default
- instead of
- `a href="/"`
- we use
- `Link to="/"`

This engages HTML5 push state; does not do a full page refresh; hence is much faster.

### Programatically Changing Page

Difference between **Declarative** and **Imperative**

The LINK tag is **Declarative** because we are declaring in a HTML syntax what we want to happen

In **Imperative**, you write the code to determine what happens when, for example, a Form is submitted.

## Gatsby Navigation

Use built-in Gatsby **navigate** to go to a difference page
`import { Link, navigate } from 'gatsby';`

Add **`{replace: true}`** to put page into browser history if you want to use back button.

This is almost always for Apps NOT Websites... Links are better for Websites because you can right-click and open in new tab for example.
- generally don't use buttons for navigation on a website.



