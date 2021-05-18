# React Gatsby

## Modifying our Custom Hook to send the order data

### Add More State to Custom Hook (usePizza.js)

Start by declaring state for error, loading and message and then return it so its available to hook

```javascript
  const [error, setError] = useState();
  const [loading, setLoading] = useState(false);
  const [message, setMessage] = useState('');
  // existing code
    return {
    order,
    addToOrder,
    removeFromOrder,
    error,
    loading,
    message,
  };
```

### Refactor OrderPage (order.js) to Add the New State

In 'order.js' add additional props from **usePizza** custom hook.

Before

```javascript
 const { order, addToOrder, removeFromOrder } = usePizza({
    pizzas,
    inputs: values,
  });
```

After

```javascript
  const {
    order,
    addToOrder,
    removeFromOrder,
    error,
    loading,
    message,
  } = usePizza({
    pizzas,
    inputs: values,
  });
```

### OrderStyles - correction

The styled component should be styled.form``; in this case since we want the outputed element to be a form.

```javascript
import styled from 'styled-components';

const OrderStyles = styled.form`
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

### Create Util - attachNamesAndPrices

```javascript
import calculatePizzaPrice from './calculatePizzaPrice';
import formatMoney from './formatMoney';

export default function attachNamesAndPrices(order, pizzas) {
  return order.map((o) => {
    const pizza = pizzas.find((p) => p.id === o.id);
    console.log(pizza);
    return {
      ...o,
      name: pizza.name,
      thumbnail: pizza.image.asset.fluid.src,
      price: formatMoney(calculatePizzaPrice(pizza.price, o.size)),
    };
  });
}
```

### Loading State (usePizza.js)

Add a **disabled** property to the **Order Ahead** button. Also change the button text based on **loading** prop.

Use the **fetch** function.

Fetch is in 2 stages...

First you get the **res** (response) then get the response **body**.

```javascript
import { useState, useContext } from 'react';
import OrderContext from '../components/OrderContext';
import formatMoney from './formatMoney';
import calculateOrderTotal from './calculateOrderTotal';
import attachNamesAndPrices from './attachNamesAndPrices';

export default function usePizza({ pizzas, values }) {
  // 1. create state to hold order
  // we get rid of this next line because we moved useState up to the provider
  // const [order, setOrder] = useState([]);
  // Now we access both our state and our updater function (setOrder) via context
  const [order, setOrder] = useContext(OrderContext);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);
  const [message, setMessage] = useState('');

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
  async function submitOrder(e) {
    e.preventDefault();
    setLoading(true);
    setError(null);
    setMessage('Go Eat Pizza!');
    // gather all the data
    const body = {
      order: attachNamesAndPrices(order, pizzas),
      total: formatMoney(calculateOrderTotal(order, pizzas)),
      name: values.name,
      email: values.email,
    };
    console.log(body);
    // 4. TODO: send this data to serverless function when user checks out
    const res = await fetch(
      `${process.env.GATSBY_SERVERLESS_BASE}/placeOrder`,
      {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(body),
      }
    );
    const text = JSON.parse(await res.text());

    // check if everything worked (between 400 and 599 - bad)
    if (res.status >= 400 && res.status < 600) {
      setLoading(false);
      setError(text.message);
    } else {
      // it worked
      setLoading(false);
      setMessage('Success! Come on down for your pizza');
    }
  }

  // return from Custom Hook all the things that are needed to use the Hook
  return {
    order,
    addToOrder,
    removeFromOrder,
    error,
    loading,
    message,
    submitOrder,
  };
}
```
