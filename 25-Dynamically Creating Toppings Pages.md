# React Gatsby

## Dynamically Creating Toppings Pages

### What Not To Do?

Since **turnPizzasIntoPages** and **turnToppingsIntoPages** are NOT related, this code would cause **turnToppingsIntoPages** to NOT be called UNTIL **turnPizzasIntoPages** COMPLETES.  This is not what we want to do since they can be run **concurrently**.

```javascript
export async function createPages(params) {
  // create pages dynamically
  // 1. Pizzas
  // use await because we are calling async function
  await turnPizzasIntoPages(params);
  await turnToppingsIntoPages(params);
  //  2. Toppings
}
```

Since both can be run **concurrently** and both are **JavaScript Promise-based** we can `await Promise.all([...])` and pass an array of **Promises**.

```javascript
export async function createPages(params) {
  // Wait for all promises to be resolved before finishing this function (i.e. go web page)
  await Promise.all([
    turnPizzasIntoPages(params),
    turnToppingsIntoPages(params),
  ]);
}
```

### Get Toppings Template

Since we already have a **PizzaPage** ('pizza.js' in 'pages' folder) we can use this to show a subset of the pizzas by making the query on that page dynamic. So we set our **Toppings Template** like so:

```javascript
  // 1. Get the template
  const toppingsTemplate = path.resolve(`./src/pages/pizzas.js`);
```

### Query all the toppings

```javascript
async function turnToppingsIntoPages({ graphql, actions }) {
  // 1. Get the template
  const toppingsTemplate = path.resolve(`./src/pages/pizzas.js`);
  // 2. Query all the toppings
  const { data } = await graphql(`
    query {
      toppings: allSanityTopping {
        nodes {
          id
          name
        }
      }
    }
  `);
}
```

### createPage for Toppings

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import PizzaList from '../components/PizzaList';
import ToppingsFilter from '../components/ToppingsFilter';

// where do the PROPS come from (not passed directly) - Gatsby magic!!!
export default function PizzasPage({ data }) {
  const pizzas = data.pizzas.nodes;

  return (
    <>
      <ToppingsFilter />
      <PizzaList pizzas={pizzas} />
    </>
  );
}

export const query = graphql`
  # query PizzaQuery($topping: [String]) {
  query PizzaQuery($toppingRegex: String) {
    pizzas: allSanityPizza(
      # filter: { toppings: { elemMatch: { name: { in: $topping } } } }
      filter: { toppings: { elemMatch: { name: { regex: $toppingRegex } } } }
    ) {
      nodes {
        name
        id
        slug {
          current
        }
        toppings {
          id
          name
        }
        image {
          asset {
            fixed(width: 200, height: 200) {
              ...GatsbySanityImageFixed
            }
            fluid(maxWidth: 400) {
              ...GatsbySanityImageFluid
            }
          }
        }
      }
    }
  }
`;
```

Note: same function signature

```javascript
async function turnPizzasIntoPages({ graphql, actions }) {}

async function turnToppingsIntoPages({ graphql, actions }) {}
```

### Modify PizzaPage (pizzas.js) for Dynamic Query

Modify the **graphql query** to take Dynamic Query Parameter.

Note: **GraphQL** is NOT **JavaScript** so the different rules apply such as **you cannot do string interpolation inside GraphQL Query** as you can inside JavaScript and even inside JSX using the curly brace syntax.

Inside **pizzas.js**:

- modify query to take an parameter with required type of String Array ( [String] )
- if using **regex** then the parameter will be of type String (NOT [String])
- modify pizzas (allSanityPizza) query to take parameter of **filter**
- inside the filter, since **toppings** is an array, use **elemMatch** (Gatsby GraphQL way... not standardized across all GraphQL implementations)
- inside the **elemMatch** use **in** or **regex**. Both must be set **context** in file calling **createPage**.

```javascript

```

### Modify ToppingsFilter to take Prop

#### Pass Prop to ToppingFilter Component from PizzaPage



#### Pass Prop to ToppingFilter Component from PizzaPage
