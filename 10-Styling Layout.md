# React Gatsby

## React Component vs Gatsby Page

### C-WB-Gatsby-10-Styling Layout

Using Styled Components, style the Layout and modify Nav for data-attribute styling (very cool!!!).

Created as Styled Components which allow for 'scoped-styles' in React turning styles into Components

### Modify Styled Component for Nav for Data Attribute

```javascript
const NavStyles = styled.nav`
  margin-bottom: 3rem;
  .logo {
    transform: translateY(-25%);
  }
  ul {
    margin: 0;
    padding: 0;
    text-align: center;
    list-style: none;
    display: grid;
    grid-template-columns: 1fr 1fr auto 1fr 1fr;
    grid-gap: 2rem;
    align-items: center;
    margin-top: -6rem;
  }
  li {
    --rotate: -2deg;
    transform: rotate(var(--rotate));
    order: 1;
    &:nth-child(1) {
      --rotate: 1deg;
    }
    &:nth-child(2) {
      --rotate: -2.5deg;
    }
    &:nth-child(4) {
      --rotate: 2.5deg;
    }
    &:hover {
      --rotate: 3deg;
    }
  }
  a {
    font-size: 3rem;
    text-decoration: none;
    &:hover {
      color: var(--red);
    }
    &[aria-current='page'] {
      color: var(--red);
    }
  }
`;
```

### Create Styled Component for Layout

```javascript
const SiteBorderStyles = styled.div`
// see CSS below for included styles
`;
```
create styled component variable

Convention - end with 'Styles' so you know it is Styled-Component

```css
{
  max-width: 1000px;
  /* margin top is set to 12rem then repeated below */
  margin: 12rem auto 4rem auto;
  /* because  not all browsers support clamp yet */
  margin-top: clamp(2rem, 10vw, 12rem);
  background: white url(${stripes});
  background-size: 1500px;
  padding: 5px;
  /* clamp is new to css; not part of gatsby or styled-components */
  padding: clamp(5px, 1vw, 25px);
  box-shadow: 0 0 5px 3px rgba(0, 0, 0, 0.044);
  border: 5px solid white;
  @media (max-width: 1100px) {
    margin-left: 1.5rem;
    margin-right: 1.5rem;
  }
```

**Note:**
Make your major componets a style; then just use selectors to style things inside the main items.
