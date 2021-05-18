# React Gatsby

## 38-Move Order State to React Context with a custom Provider

### Persisting State on a Page with React Context

#### Gatsby-browser && Gatsby-ssr | wrapRootElement

Using **React Context**.

**Context**  allows you to store data at a high level and access it at much lower levels without having to pass state down the component tree manually.

Apply the same changes to both files

```javascript
import React from 'react';
import Layout from './src/components/Layout';
import { OrderProvider } from './src/components/OrderContext';

export function wrapPageElement({ element }) {
  return <Layout>{element}</Layout>;
}

export function wrapRootElement({ element }) {
  return <OrderProvider>{element}</OrderProvider>;
}
```

#### OrderContext.js (create component)

```javascript
import React, { useState } from 'react';

// create order context
const OrderContext = React.createContext(null);

export function OrderProvider({ children }) {
  // we need to stick state in here
  const [order, setOrder] = useState([]);
  return (
    <OrderContext.Provider value={[order, setOrder]}>
      {children}
    </OrderContext.Provider>
  );
}

export default OrderContext;
```

#### usePizza Custom Hook (refactor)

```javascript
import { useState, useContext } from 'react';
import OrderContext from '../components/OrderContext';

export default function usePizza({ pizzas, inputs }) {
  // 1. create state to hold order
  // we get rid of this next line because we moved useState up to the provider
  // const [order, setOrder] = useState([]);
  // Now we access both our state and our updater function (setOrder) via context
  const [order, setOrder] = useContext(OrderContext);

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








#### PizzaOrder.js (refactor)

Notice how we create the unique id required by React

```javascript
<MenuItemStyles key={`${singleOrder.id}-${index}`}>
```

Full Code:

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
          <MenuItemStyles key={`${singleOrder.id}-${index}`}>
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
