# React Gatsby

## React Component vs Gatsby Page

### C-WB-Gatsby-09-Styling the Nav and Logo

Using Styled Components, style the Nav and Logo.

Created as 'scoped-styles'


### Create Styled Component

create styled component variable

Convention - end with 'Styles' so you know it is Styled-Component

```javascript
const NavStyles = styled.nav`
  background: green;
  a {
    background: red;
  }
`;
```

```css
{
/* rem is based on html base font */
grid-gap: 2rem; 
/*  clamp - min-size, scale-size-as-browser-scales, max-size*/
font-size: clamp(1px, 0.65vw, 8px); 
/* everything else based on 'em' which relates to font-size of selected element (the clamped font-size above in this case... cool way to control size) */
  width: 30em;
  height: 30em;
}
```

**Note:**
Make your major componets a style; then just use selectors to style things inside the main items.





