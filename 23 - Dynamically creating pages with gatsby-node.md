# React Gatsby

## Dynamically creating pages with gatsby-node

### Gatsby Node APIs

#### createPages Hook

<https://www.gatsbyjs.com/docs/reference/config-files/gatsby-node/>

Gatsby Node APIs allow us to hook into Gatsby and do things...

such as create pages.

We are using **createPages**

Create pages dynamically. This extension point is called only after the initial sourcing and transformation of nodes plus creation of the GraphQL schema are complete so you can query your data in order to create pages.

```javascript
export async function createPages() {
  console.log('creating pages....');
  console.log('creating pages....');
  console.log('creating pages....');
  console.log('creating pages....');
}
```

Note: we use an **async** function

**Console Output**:

![23-createPages.png](./_img/23-createPages.png)

#### createPages Hook (with Params)

```javascript
async function turnPizzasIntoPages(params) {
  // 1. Get a template for this page
  //  2. Query all pizzas
  //  3. Loop over each pizza and create a page for that pizza
}

export async function createPages(params) {
  // create pages dynamically
  // 1. Pizzas
  //  2. Toppings
  //  3. Slicemasters
}
```

#### Create Template (Pizza)

Notice that the file, we locate in a folder called **templates**, starts with a **capital letter**. While this is NOT required... templates are basically **classes** that will be instantiated many times and each **instance** of the **class** will start with a **lowercase letter**. So the casing is really just to signal to the developer the type of code being created much like React Component files start with **caps**.

#### createPages Hook ({graphql, actions})

**action.createPage**:

<https://www.gatsbyjs.com/docs/reference/config-files/actions/#createPage>

```javascript
import path from 'path';

async function turnPizzasIntoPages({ graphql, actions }) {
  // 1. Get a template for this page
  const pizzaTemplate = path.resolve(`./src/templates/Pizza.js`);
  //  2. Query all pizzas
  const { data } = await graphql(`
    query {
      pizzas: allSanityPizza {
        nodes {
          name
          slug {
            current
          }
        }
      }
    }
  `);
  // console.log(data);
  //  3. Loop over each pizza and create a page for that pizza
  // use forEach() because we are just looping... not returning anything
  data.pizzas.nodes.forEach((pizza) => {
    // console.log(`Creating page for ${p.name}`);
    // .createPage() is the actual method while createPages is the Hook into it
    actions.createPage({
      // What is the URL for this new page??
      path: `pizza/${pizza.slug.current}`,
      component: pizzaTemplate,
    });
  });
}

export async function createPages(params) {
  // create pages dynamically
  // 1. Pizzas
  // use await because we are calling async function
  await turnPizzasIntoPages(params);
  //  2. Toppings
  //  3. Slicemasters
}
```

#### Context

Use **React Context** to pass data:

```javascript
  data.pizzas.nodes.forEach((pizza) => {
    // console.log(`Creating page for ${p.name}`);
    // .createPage() is the actual method while createPages is the Hook into it
    actions.createPage({
      // What is the URL for this new page??
      path: `pizza/${pizza.slug.current}`,
      component: pizzaTemplate,
      context: {
        over40dev: 'is awesome!!',
        slug: pizza.slug.current,
      },
    });
  });
```

**React-Dev-Tools**:

Inspect | Search for SinglePizzaPage

![23-context](./_img/23-context.png)


