# React Gatsby

## Querying and Displaying Pagination

### My Initial Attempt without WB

Working on the **SliceMastersPage** ('slicemasters.js') in the **pages** folder.

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import Img from 'gatsby-image';
import styled from 'styled-components';

const SlicemasterGridStyles = styled.div`
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  margin-bottom: 10px;
`;

export default function SliceMastersPage({ data }) {
  const slicemasters = data.slicemasters.nodes;

  return (
    <>
      {slicemasters.map((person, idx) => (
        <SlicemasterGridStyles key={idx}>
          <Img fluid={person.image.asset.fluid} />
          <h3>{person.name}</h3>
        </SlicemasterGridStyles>
      ))}
    </>
  );
}

export const query = graphql`
  query {
    slicemasters: allSanityPerson {
      nodes {
        name
        image {
          asset {
            fluid(maxWidth: 200) {
              ...GatsbySanityImageFluid
            }
          }
        }
      }
    }
  }
`;
```

### Coding with WB

Continuing with **SliceMastersPage** ('slicemasters.js') in the **pages** folder.

```javascript
import React from 'react';
import { graphql, Link } from 'gatsby';
import Img from 'gatsby-image';
import styled from 'styled-components';

const SlicemasterGridStyles = styled.div`
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
`;

const SlicemasterStyles = styled.div`
  border: 1px solid var(--grey);
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

export default function SliceMastersPage({ data: { slicemasters } }) {
  return (
    <>
      <h2>There are {slicemasters.totalCount} Slice Masters here!</h2>
      <SlicemasterGridStyles>
        {slicemasters.nodes.map((person) => (
          <SlicemasterStyles key={person.id}>
            <Link to={person.slug.current}>
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
