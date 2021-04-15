# React Gatsby

## Sourcing Data from an external API

### Fetch Data from External API (sample data)

We can source data from any available API. For dev and testing we can use a sample API such as the ones available from <https://sampleapis.com> 

### Where to put Fetch Code in Gatsby

Put the **Fetch Code** in the 'gatsby-node.js' file before we **createPage** using another **Gatsby Hook** called **sourceNodes**.

<https://www.gatsbyjs.com/docs/reference/config-files/gatsby-node/#sourceNodes>
Extension point to tell plugins to source nodes. This API is called during the Gatsby bootstrap sequence. Source plugins use this hook to create nodes. This API is called exactly once per plugin (and once for your site’s gatsby-config.js file). If you define this hook in gatsby-node.js it will be called exactly once after all of your source plugins have finished creating nodes.

Note: typically, in JavaScript we would use the **Fetch API** but that is a **browser API** and this is actually a **Node** file (likely coming to Node in the future). As of now, to use **Fetch** in **Node** file you must **import** a package ( `import fetch from 'isomorphic-fetch;` )

```javascript
import path from 'path';
import fetch from 'isomorphic-fetch';

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
      context: {
        slug: pizza.slug.current,
      },
    });
  });
}

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

  // 3. createPage for that topping
  data.toppings.nodes.forEach((topping) => {
    actions.createPage({
      path: `topping/${topping.name}`,
      component: toppingsTemplate,
      context: {
        topping: topping.name,
        toppingRegex: `/${topping.name}/i`,
      },
    });
  });
  // 4. Pass topping data to Pizza.js
}

async function fetchBeersAndTurnIntoNodes({
  actions,
  createNodeId,
  createContentDigest,
}) {
  // 1. Fetch list of beers
  const res = await fetch('https://api.sampleapis.com/beers/ale');
  const beers = await res.json();
  // 2. Loop over each beer (any loop such as forEach or...)
  for (const beer of beers) {
    const nodeMeta = {
      id: createNodeId(`beer-${beer.name}`),
      parent: null,
      children: [],
      internal: {
        type: 'Beer',
        mediaType: 'application/json',
        contentDigest: createContentDigest(beer),
      },
    };
    // 3. Create a Node for each beer
    // pass ONE object... spread values into it
    // refactor: should be spread as {...beer} not {beer}
    actions.createNode({ ...beer, ...nodeMeta });
  }
}

// Gatsby API Function (Hook) to Source External Data from API
// Docs: https://www.gatsbyjs.com/docs/reference/config-files/gatsby-node/#sourceNodes
export async function sourceNodes(params) {
  // fetch list of beers and source them into our Gatsby API
  await Promise.all([fetchBeersAndTurnIntoNodes(params)]);
}

// Gatsby API Function (Hook) to Create Pages
export async function createPages(params) {
  // create pages dynamically
  // Pizzas and Toppings
  // Since both can be run **concurrently** and both are **JavaScript Promise-based** we can `await Promise.all([...])` and pass an array of **Promises**.
  // Wait for all promises to be resolved before finishing this function (i.e. go web page)
  await Promise.all([
    turnPizzasIntoPages(params),
    turnToppingsIntoPages(params),
  ]);

  //  3. Slicemasters
}
```

Stop and Re-Start Gatsby.

Now, in the GraphQL endpoint (view in Graph-i-QL | graphiql). Gatsby will create two query entries for you:

- first, with the name you used... in our case 'Beer' (internal: {type: 'Beer'} in above code)
- second, with the prefix 'all' as in 'allBeer'

![26-graphiql-beer](./_img/26-graphiql-beer.png)
