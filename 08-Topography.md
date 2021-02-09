# React Gatsby

## React Component vs Gatsby Page

### Topography (CSS Styles) in Gatsby

we import the path of the font file '.woff ' into the variable **font** which allows us to use that variable elsewhere... as seen below, for example: `src: url(${font});`

```JavaScript
import font from '../assets/fonts/frenchfries.woff';

const Typography = createGlobalStyle`
  @font-face {
    font-family: FrenchFries;
    src: url(${font});
  }
```

Notes:

![example showing vid err](../Course-React-Gatsby/_img/08%20at%202.24.03%20PM.png)

Gatsby then takes the **font** out of our **assets** folder and places it where it knows about it in the **static** folder.

### Adding Global Special Classes

We can add Global Classes or Tags with specific styles.

These tags or classes can then be used anywhere in the site or app for common style, for example: **mark** tag and class as  well as **tilt** class for effect.

```css
mark, .mark {
    background: var(--yellow);
    padding: 0 2px 2px 2px;
    margin: 0;
    display: inline;
    line-height: 1;
  }

  .center {
    text-align: center;
  }

  .tilt {
    transform: rotate(-2deg);
  }
```
