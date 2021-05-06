# React Gatsby

## Creating the Order page with Custom Hooks

### OrderPage - order.js

Use a <form> to get information about the order to send by email using a serverless function.

Start inside <form> using <fieldset> tags to group related form-elements.

```html
      <form action="">
        <fieldset>
          
        </fieldset>
      </form>
```

Inside 'order.js' - the OrderPage:

**WARNING**: Inside React JSX, inside a <form> element you cannot place a **value** on an <input> without having a plan for when that value changes.  This is the error that occurs in the console.

**Warning**: Failed prop type: You provided a `value` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultValue`. Otherwise, set either `onChange` or `readOnly`.
    in input (at order.js:14)

```javascript
import React, { useState } from 'react';
import SEO from '../components/SEO';

export default function OrderPage() {
  const [name, setName] = useState('');

  return (
    <>
      <SEO title="Order a Pizza!" />
      <form>
        <fieldset>
          <legend>Your Info</legend>
          <label htmlFor="name">Name</label>
          <input type="text" name="name" value={name} />
          <label htmlFor="email">Email</label>
          <input type="email" name="email" />
        </fieldset>
      </form>
    </>
  );
}
```

To fix this you can add an **onChange** handler and the React Hook **useState**

```javascript
import React, { useState } from 'react';
import SEO from '../components/SEO';

export default function OrderPage() {
  const [name, setName] = useState('');

  return (
    <>
      <SEO title="Order a Pizza!" />
      <form>
        <fieldset>
          <legend>Your Info</legend>
          <label htmlFor="name">Name</label>
          <input
            type="text"
            name="name"
            value={name}
            onChange={(e) => {
              setName(e.target.value);
            }}
          />
          <label htmlFor="email">Email</label>
          <input type="email" name="email" />
        </fieldset>
      </form>
    </>
  );
}
```

This is okay until you start having many different inputs. Having to create **new state** and an **updater** function for each will get out of control quickly.

In React, there are also many **Form** helper libraries including **Formik** that can help with this.

OR

### Create Custom Hook - useForm

We could just build our own **Form Custom Hook**. Create a file 'useForm.js' in the 'utils' folder.

Inside 'useForm.js', create and export a function that contains **useState** and inside function. Return an object from the outer function that includes the **state values** and the **updateValue** function we created.

Since the **state values** is an object, we must **spread** its existing key/value pairs into the object being passed to the **useState** along with the new value.

```javascript
import { useState } from 'react';

export default function useForm(defaults) {
  const [values, setValues] = useState(defaults);

  function updateValue(e) {
    // check if input is meant to be a 'number';
    // - if so, it will come in as a String
    // - and will need to be converted back to Number

    // destructure the `e.target` <input> to get 'name' and 'value'
    let { value } = e.target;
    if (e.target.type === 'number') {
      value = parseInt(value);
    }

    setValues({
      // copy existing state 'value' into it
      ...values,
      // update the new value that changed
      [e.target.name]: value
    });
  }

  // this now resembles the useState Hook except that it is customized to Forms
  // - we are passing back the state 'values' and a new 'update state' function
  return { values, updateValue };
}
```

This approach takes the **name** value of the **input** and **dynamically** makes that the **Object Key** by enclosing in square brackets `[] ` , inside the object being created, and setting its **value** to by the **input value**.


### React Dev Tools - Observe Changes to State

On a page running React, **inspect** the element you want to observe **state** on and choose **React Dev Tools - Components** tab (which will now be set to the 'inspected' element).

Open the **hooks** section of the **Detail** panel in the React Dev Tools. Notice the **state** change as you update the fields on the form.


### OrderPage - Menu Section

Because we have a **Page** we can use either **Dynamic** or **Static Query**.

```javascript
import React, { useState } from 'react';
import { graphql } from 'gatsby';
import Img from 'gatsby-image';
import SEO from '../components/SEO';
import useForm from '../utils/useForm';
import calculatePizzaPrice from '../utils/calculatePizzaPrice';
import formatMoney from '../utils/formatMoney';

export default function OrderPage({ data }) {
  // could default the object to contain default values
  const { values, updateValue } = useForm({ name: '', email: '' });
  // can just default to an empty object
  // const { values, updateValue } = useForm({});
  const pizzas = data.pizzas.nodes;

  return (
    <>
      <SEO title="Order a Pizza!" />
      <form>
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
        <fieldset>
          <legend>Menu</legend>
          {pizzas.map((pizza) => (
            <div key={pizza.id}>
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
                  <button type="button">
                    {size} {formatMoney(calculatePizzaPrice(pizza.price, size))}
                  </button>
                ))}
              </div>
            </div>
          ))}
        </fieldset>
        <fieldset>
          <legend>Order</legend>
        </fieldset>
      </form>
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

### Create Util - calculatePizzaPrice

```javascript
const sizes = {
  S: 0.75,
  M: 1,
  L: 1.25,
};

export default function calculatePizzaPrice(cents, size) {
  return cents * sizes[size];
}
```

### Create Util - formatMoney

```javascript
const formatter = Intl.NumberFormat('en-CA', {
  style: 'currency',
  currency: 'CAD',
});

export default function formatMoney(cents) {
  return formatter.format(cents / 100);
}
```




