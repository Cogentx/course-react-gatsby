# React Gatsby

## Single Slicemaster Pages

### Create Single Slicemaster Pages

#### In Gatsby-node.js file

Add code to existing 'turnSlicemastersIntoPages()' function to create a new page in Gatsby for each 'Slicemaster'.

```javascript
async function turnSlicemastersIntoPages({ graphql, actions }) {
  // 1. Query all slicemasters
  const { data } = await graphql(`
    query {
      slicemasters: allSanityPerson {
        totalCount
        nodes {
          id
          name
          description
          slug {
            current
          }
        }
      }
    }
  `);
  // NEW CODE - Begins
  // 2. Turn each slicemaster into their own page
  data.slicemasters.nodes.forEach((slicemaster) => {
    console.log(slicemaster);
    actions.createPage({
      component: resolve('./src/templates/Slicemaster.js'),
      path: `/slicemaster/${slicemaster.slug.current}`,
      context: {
        name: slicemaster.name,
        slug: slicemaster.slug.current,
      },
    });
  });
  // NEW CODE - Ends

  // 3. Figure out how many pages there are based on how many slicemasters there are, and how many per page
  // Everything from .ENV, even numbers, come in as Strings so we need to convert to number
  const pageSize = parseInt(process.env.GATSBY_PAGE_SIZE);
  const pageCount = Math.ceil(data.slicemasters.totalCount / pageSize);

  // 4. Loop from 1 to n and create the pages for them
  Array.from({ length: pageCount }).forEach((_, i) => {
    actions.createPage({
      path: `/slicemasters/${i + 1}`,
      component: path.resolve(`./src/pages/slicemasters.js`),
      // This data is passed to the template when we create it
      context: {
        skip: i * pageSize,
        currentPage: i + 1,
        pageSize,
      },
    });
  });
}
```

#### New Template - Slicemaster.js - GraphQL Query

Create new Template in the 'Templates' folder named 'Slicemaster.js'. Code similiar to how we did the Pizza Template.

```javascript
import React from 'react';
import { graphql } from 'gatsby';

export default function SlicemasterPage({ data }) {
  console.log('data', data);
  // console.log('Person', person);
  return <h1>SlicemasterPage</h1>;
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

#### New Template - Slicemaster.js - Component with JSX and Query Result

Basic JSX with existing CSS classes (so no styled components needed here). Notice also the use the Gatsby's Img tag (which has to be imported manually from 'gatsby-image)

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import Img from 'gatsby-image';

export default function SlicemasterPage({ data: { person } }) {
  return (
    <div className="center">
      <Img fluid={person.image.asset.fluid} />
      <h2>
        <span className="mark">{person.name}</span>
      </h2>
      <p>{person.description}</p>
    </div>
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

