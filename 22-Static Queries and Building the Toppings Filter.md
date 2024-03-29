# React Gatsby

## Static Queries and Building the Toppings Filter

### Static Queries

Static Queries allow you to query data from outside a Page (as opposed to Dynamic which is at the Page-level only)

Static Queries do not take variables.

#### Static Query from ToppingsFilter Component

Run with React Hook.

Write the query in **GraphiQL**

#### Count Pizzas (WB Code modified by Me)

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
    .active {
      background: var(--yellow);
    }
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

export default function ToppingsFilter() {
  // Get a list of all the toppings
  const { toppings, pizzas } = useStaticQuery(graphql`
    query {
      toppings: allSanityTopping {
        nodes {
          name
          id
          vegetarian
        }
      }
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
      {toppingsWithCount.map((topping) => (
        <Link key={topping.id} to={`/topping/${topping.name}`}>
          {topping.name} <span className="count">{topping.count} </span>
        </Link>
      ))}
    </ToppingsStyles>
  );
}
```

#### Count Pizzas (My Original Code)

```javascript
// Count how many pizzas have each topping
  const count = toppings.nodes.reduce((theCount, topping) => {

    const pizzasWithTopping = pizzas.nodes.filter((p) =>
      p.toppings.map((t) => t.name).includes(topping.name)
    );
    // console.log('pizzasWithToppings: ', pizzasWithTopping);
    if (pizzasWithTopping.length > 0) {
      theCount[`${topping.name}`] = theCount[`${topping.name}`] || {
        name: topping.name,
        count: 0,
      };
      // console.log('theCount1: ', theCount);
      theCount[`${topping.name}`].count++;
      // console.log('theCount2: ', theCount);
    }
    return theCount;
  }, {});
  console.table(count);
```

### Re-factored ToppingsFilter Query

We don't actually need to query the **toppings** here because we are getting the list of **toppings** from mapping over all the **pizzas.toppings**.

New **ToppingsFilter** code with **toppings** removed from the query:

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
    .active {
      background: var(--yellow);
    }
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

export default function ToppingsFilter() {
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
      {toppingsWithCount.map((topping) => (
        <Link key={topping.id} to={`/topping/${topping.name}`}>
          {topping.name} <span className="count">{topping.count} </span>
        </Link>
      ))}
    </ToppingsStyles>
  );
}
```
