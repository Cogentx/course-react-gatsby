# React Gatsby

## Querying and Displaying Pagination

### My Initial Attempt without WB

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

