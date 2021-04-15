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

#### Add rating Effect inside JSX:

```javascript
              <p>
                {`⭐`.repeat(rating)}
                <span style={{ filter: `grayscale(100%)` }}>
                  {`⭐`.repeat(5 - rating)}
                </span>
              </p>
```

#### Inside List within React JSX

```javascript
export default function BeersPage({ data }) {
  const allBeers = data.beers.nodes;

  return (
    <>
      <h2>We have {allBeers.length} Beers Available. Dine in Only!</h2>
      <BeerGridStyle>
        {allBeers.map((beer) => {
          const rating = Math.round(beer.rating.average);
          return (
            <SingleBeerStyles key={beer.id}>
              <img src={beer.image} alt={beer.name} />
              <h3>{beer.name}</h3>
              {beer.price}
              <p>
                {`⭐`.repeat(rating)}
                <span style={{ filter: `grayscale(100%)` }}>
                  {`⭐`.repeat(5 - rating)}
                </span>
              </p>
            </SingleBeerStyles>
          );
        })}
      </BeerGridStyle>
    </>
  );
}
```

#### Page Code including Styled Component

Continuing with **BeersPage** ('beers.js') in the **pages** folder.

```javascript
import React from 'react';
import { graphql } from 'gatsby';
import styled from 'styled-components';

const BeerGridStyle = styled.div`
  display: grid;
  gap: 2rem;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
`;

const SingleBeerStyles = styled.div`
  border: 10px solid var(--grey);
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
