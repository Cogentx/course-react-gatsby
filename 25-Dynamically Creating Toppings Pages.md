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

Goal: Highlight Active Topping.

Using **pageContext**

When we **createPage** we pass a **context** Object. This becomes the **pageContext** which is then accessable inside the Page Component.

#### Pass Prop to ToppingFilter Component from PizzaPage

From PizzaPage ('pizzas.js' in 'page' folder):

```javascript
export default function PizzasPage({ data, pageContext }) {
  const pizzas = data.pizzas.nodes;

  return (
    <>
      <ToppingsFilter activeTopping={pageContext.topping} />
      <PizzaList pizzas={pizzas} />
    </>
  );
}
```

#### Receive Prop in ToppingFilter Component

Destructure the incoming **Props** to include **pageContext** from which we can obtain data we passed in **ToppingsFilter** Component ('ToppingFilter.js' in the 'components' folder)

```javascript
export default function ToppingsFilter({ activeTopping }) {
  // existing code
}
```

### Refactor ToppingFilter CSS for 'active' Class

#### Refactor to properly have 'active' Class work

Need to include the **` & `** before the **` .active `** in the CSS to make the dynamic class inclusion work!!!

```javascript
const ToppingsStyles = styled.div`
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  margin-bottom: 3rem;
  a {
    display: grid;
    grid-template-columns: auto 1fr;
    align-items: center;
    gap: 0 1rem;
    padding: 5px;
    background: var(--grey);
    border-radius: 2px;
    .count {
      background: white;
      padding: 2px 5px;
    }
    // add &
    &.active {
      background: var(--yellow);
    }
  }
`;
```

#### Refactor to replace 'active' Class with Aria-Current Attribute

The active page in Gastby automatically has the **aria-current** attribute set to **page** which means we can target this attribute in our CSS. Again, must include the **` & `** since dynamic (**attr** not always present). With this approach we do not need to apply the **active class**.

```javascript
const ToppingsStyles = styled.div`
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  margin-bottom: 3rem;
  a {
    display: grid;
    grid-template-columns: auto 1fr;
    align-items: center;
    gap: 0 1rem;
    padding: 5px;
    background: var(--grey);
    border-radius: 2px;
    .count {
      background: white;
      padding: 2px 5px;
    }
    // Must include the & since dynamic (attr not always present)
    &[aria-current='page'] {
      background: var(--yellow);
    }
    /* &.active {
      background: var(--yellow);
    } */
  }
`;
```

### Modify ToppingsFilter to add All Toppings Link

Include a link to ALL pizzas and a count. Get the count from the length of pizzas array.

```javascript
      <Link to="/pizzas">
        <span className="name">All</span>
        <span className="count">{pizzas.nodes.length} </span>
      </Link>
```

The full code for ToppingsFilter now looks like this:

```javascript
import React from 'react';
import { useStaticQuery, graphql, Link } from 'gatsby';
import styled from 'styled-components';

const ToppingsStyles = styled.div`
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  margin-bottom: 3rem;
  a {
    display: grid;
    grid-template-columns: auto 1fr;
    align-items: center;
    gap: 0 1rem;
    padding: 5px;
    background: var(--grey);
    border-radius: 2px;
    .count {
      background: white;
      padding: 2px 5px;
    }
    &[aria-current='page'] {
      background: var(--yellow);
    }
    /* &.active {
      background: var(--yellow);
    } */
  }
`;

function countPizzasInTopping(pizzas) {
  // return pizzas with counts
  const counts = pizzas
    .map((p) => p.toppings)
    .flat()
    .reduce((acc, topping) => {
      // check if it is existing topping, otherwise create new entry and include count
      acc[`${topping.id}`] = acc[`${topping.id}`] || {
        id: topping.id,
        name: topping.name,
        count: 0,
      };
      // increment
      acc[`${topping.id}`].count += 1;
      return acc;
    }, {});

  return Object.values(counts).sort((a, b) => b.count - a.count);
}

export default function ToppingsFilter({ activeTopping }) {
  // Get a list of all the toppings
  const { pizzas } = useStaticQuery(graphql`
    query {
      pizzas: allSanityPizza {
        nodes {
          toppings {
            name
            id
          }
        }
      }
    }
  `);

  // Get a list of all the Pizzas with their toppings
  // log vars showing names of variables with accompaning output
  // console.log({ toppings, pizzas });

  // Count how many pizzas have each topping
  const toppingsWithCount = countPizzasInTopping(pizzas.nodes);

  // Loop over the list of toppings and display the topping and the count of pizzas in that topping
  // Link it up...
  return (
    <ToppingsStyles>
      <Link to="/pizzas">
        <span className="name">All</span>
        <span className="count">{pizzas.nodes.length} </span>
      </Link>
      {toppingsWithCount.map((topping) => (
        <Link
          key={topping.id}
          to={`/topping/${topping.name}`}
          className={topping.name === activeTopping ? 'active' : ''}
        >
          <span className="name">{topping.name}</span>
          <span className="count">{topping.count} </span>
        </Link>
      ))}
    </ToppingsStyles>
  );
}
```
