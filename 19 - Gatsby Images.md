# Gatsby

## Gatsby Images

### Internet Challenges with Images

- too big
- width / height not right (ratio)
- poor loading perf
- not the right format (lots of formats)
- not compressed

#### Lossless Compression

Makes image as small as possible without loosing any quality

#### Lossy Compression

Makes image as small as possible but does give up some of the quality

### Gatsby Plugins

#### Gatsby-Plugin-Sharp

Exposes several image processing functions built on the Sharp image processing library. This is a low-level helper plugin generally used by other Gatsby plugins. You generally shouldn’t be using this directly but might find it helpful if doing very custom image processing.

It aims to provide excellent out-of-the box settings for processing common web image formats.

**Further Description**

See Gatsby Docs ( <https://www.gatsbyjs.com/plugins/gatsby-plugin-sharp/> )

**Install**

`npm install gasby-plugin-sharp`

How to Use and Options

```javascript
        <Img fluid={pizza.image.asset.fluid} alt={pizza.name}></Img>
        <Img fixed={pizza.image.asset.fixed} alt={pizza.name}></Img>
```

This is related to the GraphQL query...

```javascript
export const query = graphql`
  query PizzaQuery {
    pizzas: allSanityPizza {
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

See Gatsby Docs ( <https://www.gatsbyjs.com/plugins/gatsby-plugin-sharp/> )


#### Image Processing

Takes a lot of processing power; takes time

Can use a service...

- Sanity Image Pipeline
- Cloudinary
- Imgix
