# React Gatsby

## Querying, Displaying and Styling the Beers Page

### My Initial Attempt without WB

Working on the **BeersPage** ('beers.js') in the **pages** folder.

Styling not done and display of image must be refactored... it shows the image as well as the alt because it's not available and then it is.

```javascript
import React from 'react';
import { graphql } from 'gatsby';

// where do the PROPS come from (not passed directly) - Gatsby magic!!!
export default function BeersPage({ data, pageContext }) {
  const allBeers = data.beers.nodes;

  return (
    <div>
      {allBeers.map(
        ({ beer }, idx) => (
          <div key={idx}>
            <img src={beer.image} alt="beer" />
            <h2>
              <span className="mark">{beer.name}</span>
              <span>{beer.price}</span>
            </h2>
          </div>
        )
        // console.log('processing beer: ', beer.name);
      )}
    </div>
  );
}

export const query = graphql`
  query BeersQuery {
    beers: allBeer {
      nodes {
        beer {
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
  }
`;
```

### Coding with WB

