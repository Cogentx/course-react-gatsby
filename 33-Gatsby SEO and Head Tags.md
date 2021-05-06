# React Gatsby

## Gatsby SEO and Head Tags

Highest Level we have in our Website is 'Layout.js' - and there is no HTML <body> and <head> tags for meta data and SEO.
- These things are added automatically by Gatsby

So the Question is... how do you stick stuff in the <head> if you need to. For example, a <title> tag for the browser tab.

### React Helmet

React component that allows you to wrap elements such as <title> and it places them inside the 'index.html' <head> section of the website.

This is what is referred to in JS as a **side-effect**. Occurs when you reach outside your component and updates something outside itself.

`import { Helmet } from 'react-helmet';`

Inside JSX of Component use

```javascript
<Helmet>
  <title>Name</title>
</Helmet>
```

The <Helmet> tag can be placed anywhere. If more than one location has a **title** tag, for example, the last read **title** wins and gets placed inside the <head> tag of **index.html** website file.

### SEO - Create Reuseable Component

Setup **defaults** by using **React Helmet** and creating an 'SEO.js' Component which you can then use to wrap code in multiple places. Basically anything you want inserted into the <head> section of your website HTML.

Need to add a **React Helmet Plugin** to get this to work during building and with server-side rendering (important for SEO)

Inside 'gatsby-config.js'

```javascript
import dotenv from 'dotenv';

dotenv.config({ path: '.env' });

export default {
  siteMetadata: {
    title: `Slicks Slices`,
    siteUrl: 'localhost:3333',
    description: 'The Best Pizza',
    twitter: '@slicksSlices',
  },
  plugins: [
    'gatsby-plugin-styled-components',
    'gatsby-plugin-react-helmet',
    {
      // this is the name of the plugin you are adding
      resolve: 'gatsby-source-sanity',
      options: {
        projectId: 'vgus6rh0',
        dataset: 'production',
        watchMode: true,
        token: process.env.SANITY_TOKEN,
      },
    },
  ],
};
```

Inside 'SEO.js'

Using **Helmet** inside a custom SEO component.

Notice the **titleTemplate** is `%s - Slick's Slices`. This will take any **title** provided and append, in this case, ** - Slick's Slices** to the end of the **title**

```javascript
import React from 'react';
import { Helmet } from 'react-helmet';

export default function SEO({ children, location, description, title, image }) {
  return (
    <Helmet titleTemplate={`%s - Slick's Slices`}>
      <html lang="en" />
    </Helmet>
  );
}
```

### SEO Component - html

We can **query** data into a Component that is not a **page** Component by using **Static Query** (`useStaticQuery`)

Inside 'SEO.js'

```javascript
import React from 'react';
import { Helmet } from 'react-helmet';
import { useStaticQuery, graphql } from 'gatsby';

export default function SEO({ children, location, description, title, image }) {
  const { site } = useStaticQuery(graphql`
    query {
      site {
        siteMetadata {
          title
          description
          twitter
        }
      }
    }
  `);
  return (
    <Helmet titleTemplate={`%s - ${site.siteMetadata.title}`}>
      <html lang="en" />
      <title>{title}</title>
      {/* Fav Icons */}
      <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
      <link rel="alternate icon" href="/favicon.ico" />
      {/* Meta Tags */}
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <meta charSet="utf-8" />
      <meta name="description" content={site.siteMetadata.description} />
      {/* Open Graph (og) */}
      {location && <meta property="og:url" content={location.href} />}
      <meta property="og:image" content={image || '/logo.svg'} />
      <meta property="og:title" content={title} key="ogtitle" />
      <meta
        property="og:site_name"
        content={site.siteMetadata.title}
        key="ogsitename"
      />
      <meta property="og:description" content={description} />
      {children}
    </Helmet>
  );
}
```

**Open Graph** is a specification for meta tags for things like Facebook, Twitter, etc. - basically any site that wants to consume your meta tags.

**Note**: we can reference 'logo.svg' using an absolute path because it is located in our project's **static** folder.

### Add SEO Component to Pages

'Pizza.js' Template

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import styled from 'styled-components';
import Img from 'gatsby-image';
import SEO from '../components/SEO';

const PizzaGrid = styled.div`
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
`;

export default function SinglePizzaPage({ data: { pizza } }) {
  return (
    <>
      <SEO title={pizza.name} image={pizza.image?.asset?.fluid?.src} />
      <PizzaGrid>
        <Img fluid={pizza.image.asset.fluid} />
        <div>
          <h2 className="mark">{pizza.name}</h2>
          <ul>
            {pizza.toppings.map((topping) => (
              <li key={topping.id}>{topping.name}</li>
            ))}
          </ul>
        </div>
      </PizzaGrid>
    </>
  );
}

// this needs to be Dynamic  based on the slug passed in via context in gatsby-node.js
// This is a GraphQL Query which has access to all our contexts directly
// use Parens after QUERY to pass in Dynamic variables
// all Dynamic variables being accepted must be TYPED
// the BANG ( ` ! `) on the end means it's absolutely required... meaning you cannot view this page without passing it a slug
// $slug is passed in because we set a CONTEXT variable named SLUG when we call **actions.createPage** from **gatsby-node.js**
export const query = graphql`
  query($slug: String!) {
    pizza: sanityPizza(slug: { current: { eq: $slug } }) {
      name
      id
      image {
        asset {
          fluid(maxWidth: 800) {
            ...GatsbySanityImageFluid
          }
        }
      }
      toppings {
        name
      }
    }
  }
`;
```

'pizzas.js' Page

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import PizzaList from '../components/PizzaList';
import ToppingsFilter from '../components/ToppingsFilter';
import SEO from '../components/SEO';

// where do the PROPS come from (not passed directly) - Gatsby magic!!!
export default function PizzasPage({ data, pageContext }) {
  const pizzas = data.pizzas.nodes;

  return (
    <>
      <SEO
        title={
          pageContext.topping
            ? `Pizzas with ${pageContext.topping}`
            : `All Pizzas`
        }
      />
      <ToppingsFilter activeTopping={pageContext.topping} />
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

'Slicemaster.js' Template

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import Img from 'gatsby-image';
import SEO from '../components/SEO';

export default function SlicemasterPage({ data: { person } }) {
  return (
    <>
      {/* raw image (src) passed into image prop not gatsby-fluid-image */}
      <SEO title={person.name} image={person.image.asset.src} />
      <div className="center">
        <Img fluid={person.image.asset.fluid} />
        <h2>
          <span className="mark">{person.name}</span>
        </h2>
        <p>{person.description}</p>
      </div>
    </>
  );
}

// slug passed in via context
// bang (!) indicates required
export const query = graphql`
  query($slug: String!) {
    person: sanityPerson(slug: { current: { eq: $slug } }) {
      id
      name
      description
      image {
        asset {
          fluid(maxWidth: 1000, maxHeight: 750) {
            ...GatsbySanityImageFluid
          }
        }
      }
    }
  }
`;
```

'slicemasters.js' Page

```javascript
import React from 'react';
import { graphql, Link } from 'gatsby';
import Img from 'gatsby-image';
import styled from 'styled-components';
import Pagination from '../components/Pagination';
import SEO from '../components/SEO';

const SlicemasterGridStyles = styled.div`
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
`;

const SlicemasterStyles = styled.div`
  padding: 2rem;
  a {
    text-decoration: none;
  }
  .gatsby-image-wrapper {
    height: 400px;
  }
  h2 {
    transform: rotate(-2deg);
    text-align: center;
    font-size: 4rem;
    margin-bottom: -2rem;
    position: relative;
    z-index: 2;
  }
  .description {
    background: var(--yellow);
    padding: 1rem;
    margin: 2rem;
    margin-top: -6rem;
    position: relative;
    z-index: 2;
    transform: rotate(1deg);
    text-align: center;
  }
`;

export default function SliceMastersPage({
  data: { slicemasters },
  pageContext,
}) {
  return (
    <>
      <SEO title={`Slicemasters - Page ${pageContext.name || 1}`} />
      <Pagination
        pageSize={parseInt(process.env.GATSBY_PAGE_SIZE)}
        totalCount={slicemasters.totalCount}
        currentPage={pageContext.currentPage || 1}
        skip={pageContext.skip}
        base="/slicemasters"
      />
      <SlicemasterGridStyles>
        {slicemasters.nodes.map((person) => (
          <SlicemasterStyles key={person.id}>
            <Link to={`/slicemaster/${person.slug.current}`}>
              <h2>
                <span className="mark">{person.name}</span>
              </h2>
            </Link>
            <Img fluid={person.image.asset.fluid} />
            <p className="description">{person.description}</p>
          </SlicemasterStyles>
        ))}
      </SlicemasterGridStyles>
    </>
  );
}

export const query = graphql`
  query($skip: Int = 0, $pageSize: Int = 2) {
    slicemasters: allSanityPerson(limit: $pageSize, skip: $skip) {
      totalCount
      nodes {
        id
        name
        description
        slug {
          current
        }
        image {
          asset {
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

'beers.js' Page

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import styled from 'styled-components';
import SEO from '../components/SEO';

const BeerGridStyle = styled.div`
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
`;

const SingleBeerStyles = styled.div`
  border: 1px solid var(--grey);
  padding: 2rem;
  text-align: center;
  img {
    width: 100%;
    height: 200px;
    object-fit: contain;
    display: block;
    display: grid;
    align-items: center;
    font-size: 10px;
  }
  /* target reviews (could add class) */
  span:nth-child(2) {
    margin-left: 10px;
  }
`;

// where do the PROPS come from (not passed directly) - Gatsby magic!!!
export default function BeersPage({ data }) {
  const allBeers = data.beers.nodes;

  return (
    <>
      <SEO title={`Beers! We have ${allBeers.length} in Stock`} />
      <h2>We have {allBeers.length} Beers Available. Dine in Only!</h2>
      <BeerGridStyle>
        {allBeers.map((beer) => {
          const rating = Math.round(beer.rating.average);
          return (
            <SingleBeerStyles key={beer.id}>
              <img src={beer.image} alt={beer.name} />
              <h3>{beer.name}</h3>
              {beer.price}
              <p title={`${rating} out of 5 stars`}>
                {`⭐`.repeat(rating)}
                <span style={{ filter: `grayscale(100%)` }}>
                  {`⭐`.repeat(5 - rating)}
                </span>
                <span>({beer.rating.reviews})</span>
              </p>
            </SingleBeerStyles>
          );
        })}
      </BeerGridStyle>
    </>
  );
}

export const query = graphql`
  query {
    beers: allBeer {
      nodes {
        id
        name
        price
        image
        rating {
          average
          reviews
        }
      }
    }
  }
`;
```

'orders.js' Page

```javascript
import React from 'react';
import SEO from '../components/SEO';

export default function OrderPage() {
  return (
    <>
      <SEO title="Order a Pizza!" />
      <p>Hey! I'm the OrderPage!</p>
    </>
  );
}
```


