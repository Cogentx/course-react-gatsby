# React Gatsby

## 37-Calclulating our Order Total

### order.js

Add to the OrderPage form a section containing the order total. This requires us to create a new util function **calculateOrderTotal**.

```javascript
        <fieldset>
          <h3>
            Your Total is {formatMoney(calculateOrderTotal(order, pizzas))}
          </h3>
          <button type="submit">Order Ahead</button>
        </fieldset>
```

Full Code now:

```javascript
import React, { useState } from 'react';
import { graphql } from 'gatsby';
import Img from 'gatsby-image';
import SEO from '../components/SEO';
import calculatePizzaPrice from '../utils/calculatePizzaPrice';
import formatMoney from '../utils/formatMoney';
import OrderStyles from '../styles/OrderStyles';
import MenuItemStyles from '../styles/MenuItemStyles';
import useForm from '../utils/useForm';
import usePizza from '../utils/usePizza';
import PizzaOrder from '../components/PizzaOrder';
import calculateOrderTotal from '../utils/calculateOrderTotal';

export default function OrderPage({ data }) {
  const pizzas = data.pizzas.nodes;
  // could default the object to contain default values
  const { values, updateValue } = useForm({ name: '', email: '' });
  const { order, addToOrder, removeFromOrder } = usePizza({
    pizzas,
    inputs: values,
  });

  // can just default to an empty object
  // const { values, updateValue } = useForm({});

  return (
    <>
      <SEO title="Order a Pizza!" />
      <OrderStyles>
        <fieldset>
          <legend>Your Info</legend>
          <label htmlFor="name">Name</label>
          <input
            type="text"
            id="name"
            name="name"
            value={values.name}
            onChange={updateValue}
          />
          <label htmlFor="email">Email</label>
          <input
            type="email"
            id="email"
            name="email"
            value={values.email}
            onChange={updateValue}
          />
        </fieldset>
        <fieldset className="menu">
          <legend>Menu</legend>
          {pizzas.map((pizza) => (
            <MenuItemStyles key={pizza.id}>
              <Img
                width="50"
                height="50"
                fluid={pizza.image.asset.fluid}
                alt={pizza.name}
              />
              <div>
                <h2>{pizza.name}</h2>
              </div>
              <div>
                {['S', 'M', 'L'].map((size) => (
                  <button
                    type="button"
                    key={size}
                    onClick={() =>
                      addToOrder({
                        id: pizza.id,
                        size,
                      })
                    }
                  >
                    {size} {formatMoney(calculatePizzaPrice(pizza.price, size))}
                  </button>
                ))}
              </div>
            </MenuItemStyles>
          ))}
        </fieldset>
        <fieldset className="order">
          <legend>Order</legend>
          {/* need to pass in remove function instead of getting it from
          custom hook because using the custom hook would create a different state object */}
          <PizzaOrder
            order={order}
            pizzas={pizzas}
            removeFromOrder={removeFromOrder}
          />
        </fieldset>
        <fieldset>
          <h3>
            Your Total is {formatMoney(calculateOrderTotal(order, pizzas))}
          </h3>
          <button type="submit">Order Ahead</button>
        </fieldset>
      </OrderStyles>
    </>
  );
}

export const query = graphql`
  query {
    pizzas: allSanityPizza {
      nodes {
        name
        id
        slug {
          current
        }
        price
        image {
          asset {
            fluid(maxWidth: 100) {
              ...GatsbySanityImageFluid
            }
          }
        }
      }
    }
  }
`;
```


### calculateOrderTotal.js

New util function. Use [].reduce() to calculate the order total.

First find the pizza in the order; add its price to the running total for the order.

```javascript
import calculatePizzaPrice from './calculatePizzaPrice';

export default function calculateOrderTotal(order, pizzas) {
  // Loop over each item in the order
  return order.reduce((runningtotal, singleOrder) => {
    const pizza = pizzas.find((p) => p.id === singleOrder.id);
    return runningtotal + calculatePizzaPrice(pizza.price, singleOrder.size);
  }, 0);
}
```



