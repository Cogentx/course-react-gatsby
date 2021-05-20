# React Gatsby

## Coding our Serverless Function

### Validate Data (required fields)

**File**: 'placeOrder.js'

Make an array of **requiredFields**.

Loop over this array using **for loop** instead of **forEach**

We **stringify**  an object to send it...
...then we **parse** the string to get the object back later.

If you need to **return**, using **.forEach()** would create another scope (inner) that does not allow us to **return** from the outer scope.

In the case where you need to **return** while the **loop** is being process it is better to use a **for loop**.

Which one?

**.forEach** loops all with no valid option to return in the middle.

**for loop** not only allows you to loop over any iterable but it also allows you to interrupt the loop at any time based on a condition you define. You can use **break, throw, or return**.

<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of>

Send back an Error Status Code and Error Message when fields are missing.

![40-http-response-message]()

Use the **Network** tab in the Dev Tools
- click on the POST entry
- click on the RESPONSE  tab under Network next to list

```javascript
// existing code
exports.handler = async (event, context) => {
  const body = JSON.parse(event.body);
  // console.log(body);
  // validate the data coming in is correct
  const requiredFields = ['email', 'name', 'order'];

  for (const field of requiredFields) {
    console.log(`Check that field ${field} is good`);
    if (!body[field]) {
      return {
        statusCode: 400,
        body: JSON.stringify({
          message: `Oops! You are missing the ${field} field`,
        }),
      };
    }
  }
// existing code
```

### Send Email

**File**: 'placeOrder.js'

There are frameworks to help you do this in React but you can also do it with JS Template Strings and no React as shown below.

**Note**: When not using React you need **backticks ( `...` )**

```javascript
  // existing code
const info = await transporter.sendMail({
    from: "Slick's Slices <slick@example.com>",
    to: `${body.name} <${body.email}>, orders@example.com`,
    subject: 'New Order!',
    html: generateOrderEmail({
      order: body.order,
      total: body.total,
    }),
  });
  console.log(info);
  return {
    statusCode: 200,
    body: JSON.stringify({ message: 'Success' }),
  };
  // existing code
```

Creating React-style Components without React

Instead of JSX we put the HTML code inside backticks and use the dynamic syntax of `${...}`

```javascript
function generateOrderEmail({ order, total }) {
  return `
  <div>
  <h2>Your Recent Order for ${total}</h2>
  <p>Please start walking over, we will have your order ready in the next 20 mins.</p>
  <ul>
  ${order.map(
    (o) => `
  <li>${o.name}</li>
  `
  )}
  </ul>
  </div>
  `;
}
```

### placeOrder.js Code

```javascript
const nodemailer = require('nodemailer');

function generateOrderEmail({ order, total }) {
  return `
  <div>
  <h2>Your Recent Order for ${total}</h2>
  <p>Please start walking over, we will have your order ready in the next 20 mins.</p>
  <ul>
  ${order.map(
    (o) => `
  <li>
  <img src="${o.thumbnail}" alt="${o.name}" />
  ${o.size} ${o.name} - ${o.price}
  </li>
  `
  )}
  </ul>
  <p>Your total is <strong>$${total}</strong> due at pickup</p>
  <style>
  ul {
    list-style: none
  }
  </style>
  </div>
  `;
}

// create transport for nodemailer
const transporter = nodemailer.createTransport({
  host: process.env.MAIL_HOST,
  port: 587,
  auth: {
    user: process.env.MAIL_USER,
    pass: process.env.MAIL_PASS,
  },
});

exports.handler = async (event, context) => {
  const body = JSON.parse(event.body);
  // console.log(body);
  // validate the data coming in is correct
  const requiredFields = ['email', 'name', 'order'];

  for (const field of requiredFields) {
    console.log(`Check that field ${field} is good`);
    if (!body[field]) {
      return {
        statusCode: 400,
        body: JSON.stringify({
          message: `Oops! You are missing the ${field} field`,
        }),
      };
    }
  }

  // send the email
  const info = await transporter.sendMail({
    from: "Slick's Slices <slick@example.com>",
    to: `${body.name} <${body.email}>, orders@example.com`,
    subject: 'New Order!',
    html: generateOrderEmail({
      order: body.order,
      total: body.total,
    }),
  });
  console.log(info);
  return {
    statusCode: 200,
    body: JSON.stringify({ message: 'Success' }),
  };
};
```

### usePizza.js Code

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
    console.log(e.body);
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
