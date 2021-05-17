# React Gatsby

## Custom Hook for our Order Form

### usePizza.js

This is the custom hook. Return from the hook all the things that are needed to use the hook.

```javascript
import { useState } from 'react';

export default function usePizza({ pizzas, inputs }) {
  // 1. create state to hold order
  const [order, setOrder] = useState([]);
  // 2. Make a function to add things to order
  function addToOrder(orderedPizza) {
    setOrder([...order, orderedPizza]);
  }
  // 3. Make a function to remove things from order
  function removeFromOrder(index) {
    setOrder([
      // everything before index
      ...order.slice(0, index),
      // everything after index
      ...order.slice(index + 1),
    ]);
  }
  // 4. TODO: send this data to serverless function when user checks out

  // return from Custom Hook all the things that are needed to use the Hook
  return {
    order,
    addToOrder,
    removeFromOrder,
  };
}
```

### PizzaOrder.js

Using the custom hook to create functionality for this component. Notice the use of two util functions. 

```javascript
import formatMoney from '../utils/formatMoney';
import calculatePizzaPrice from '../utils/calculatePizzaPrice';
```

```javascript
import React from 'react';
import Img from 'gatsby-image';
import MenuItemStyles from '../styles/MenuItemStyles';
import formatMoney from '../utils/formatMoney';
import calculatePizzaPrice from '../utils/calculatePizzaPrice';

export default function PizzaOrder({ order, pizzas, removeFromOrder }) {
  return (
    <>
      {order.map((singleOrder, index) => {
        const pizza = pizzas.find((p) => p.id === singleOrder.id);
        return (
          <MenuItemStyles key={singleOrder.id}>
            <Img fluid={pizza.image.asset.fluid} />
            <h2>{pizza.name}</h2>
            <p>
              {formatMoney(calculatePizzaPrice(pizza.price, singleOrder.size))}
              <button
                type="button"
                className="remove"
                title={`Remove ${singleOrder.size} ${pizza.name} from Order`}
                onClick={() => removeFromOrder(index)}
              >
                &times;
              </button>
            </p>
          </MenuItemStyles>
        );
      })}
    </>
  );
}
```

### Order.js

Uses the PizzaOrder component which takes the custom hook

**Note**: need to pass in remove function instead of getting it from custom hook because using the custom hook would create a different state object

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

### MenuItemStyles.js

Refactor the **remove** class to change `position: absolute` (Element positioned absolutely on the web page) to `position: relative` (Element positioned relative to its siblings)

```css
  .remove {
    background: none;
    color: var(--red);
    font-size: 3rem;
    position: relative;
    top: 0;
    right: 0;
    box-shadow: none;
    line-height: 1rem;
  }
```





