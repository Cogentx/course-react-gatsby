# React Gatsby

## Styling our Order Form

### OrderStyles - create separate file

With **styled components** we can include them in the same file or create a new file and import them in just like any other component.

In the 'styles' folder, create a new file named 'OrderStyles.js' 

Since this is **styled components** and not just CSS - the file does have a '.js' JavaScript extension.

- Set it up as a **grid** with 2-columns then spread the **fieldsets** across the 2-columns
- Grab **fieldset** as a **tag**

```javascript
import styled from 'styled-components';

const OrderStyles = styled.div`
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  fieldset {
    grid-column: span 2;
    max-height: 600px;
    overflow: auto;
    display: grid;
    gap: 1rem;
    align-content: center;
    &.order,
    &.menu {
      grid-column: span 1;
    }
  }
`;

export default OrderStyles;
```

### MenuItemStyles - create separate file

With **styled components** we can include them in the same file or create a new file and import them in just like any other component.

In the 'styles' folder, create a new file named 'MenuItemStyles.js' 

Since this is **styled components** and not just CSS - the file does have a '.js' JavaScript extension.

- Set it up as a **grid** with 2-columns with first column (the image) set to '100px' and the second column to take up the remaining space (1fr)
- Set **grid** to have 2-rows with both rows taking up equal and available space (1fr) each.

Target the image and tell it to take up two rows (special class - .gatsby-image-wrapper)

Target a **button** next to another **button** by using `button + button {...styles...}`

```javascript

```


