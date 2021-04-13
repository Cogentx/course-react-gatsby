# React Gatsby

## Styling our Pizza Grid with CSS subgrid

### CSS Sub-Grid

**CSS Sub-Grid** instructs **Children** to use style of **Grandparent Grid** not **Parent Grid**.

- display type is grid
- grid-template-columns set to repeat()
- repeat() uses auto-fill and minmax()
- minmax() uses **pixels** and **free space unit (fr)**


```css
PizzaGridStyles {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 4rem;
  grid-auto-rows: auto auto 500px;
}

PizzaStyles {
  display: grid;
  /* CSS browser backup for not supported */
  @supports not (grid-template-rows: subgrid) {
    --rows: auto auto 1fr;
  }
  /* take your row sizing not from parent but from grandparent */
  /* CSS variables use -- inside var() if first don't exist (--rows variable) use second (subgrid) */
  grid-template-rows: var(--rows, subgrid);
  /* each of the Pizzas need to span 3 rows in the CSS Grid */
  grid-row: span 3;
  /* override the grid gap */
  gap: 1rem;
  /* take the margin off h2 and p */
  h2,
  p {
    margin: 0;
  }
```

with React Styled Components:

```javascript
import React from 'react';
import { Link } from 'gatsby';
import Img from 'gatsby-image';
import styled from 'styled-components';

const PizzaGridStyles = styled.div`
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 4rem;
  grid-auto-rows: auto auto 500px;
`;

const PizzaStyles = styled.div`
  display: grid;
  /* CSS browser backup for not supported */
  @supports not (grid-template-rows: subgrid) {
    --rows: auto auto 1fr;
  }
  /* take your row sizing not from parent but from grandparent */
  /* CSS variables use -- inside var() if first don't exist (--rows variable) use second (subgrid) */
  grid-template-rows: var(--rows, subgrid);
  /* each of the Pizzas need to span 3 rows in the CSS Grid */
  grid-row: span 3;
  /* override the grid gap */
  gap: 1rem;
  /* take the margin off h2 and p */
  h2,
  p {
    margin: 0;
  }
`;

function SinglePizza({ pizza }) {
  return (
    <PizzaStyles>
      <Link to={`/pizza/${pizza.slug.current}`}>
        <h2>
          <span className="mark">{pizza.name}</span>
        </h2>
      </Link>
      <p>{pizza.toppings.map((t) => t.name).join(', ')}</p>
      <Img fluid={pizza.image.asset.fluid} alt={pizza.name} />
      {/* <Img fixed={pizza.image.asset.fixed} alt={pizza.name}></Img> */}
    </PizzaStyles>
  );
}

export default function PizzaList({ pizzas }) {
  return (
    <PizzaGridStyles>
      {pizzas.map((p) => (
        <SinglePizza key={p.id} pizza={p} />
      ))}
    </PizzaGridStyles>
  );
}
```


