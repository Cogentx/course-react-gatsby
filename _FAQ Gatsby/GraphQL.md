# Gatsby and GraphQL

## GraphiQL (graph-i-ql)

Access Docs tab to see how to see how to query... some examples

```markdown
query {
  sanityPizza(name: {regex: "/veggie/i"}) {
    name
    toppings {
      name
      vegetarian
    }
  }
}
# query {
#   sanityPizza(id: {eq: "-c5ddf24e-2e75-5894-bf50-bc8cf5f2d04b"}) {
#     name
#   }
# }

# query MyQuery {
#   allSanityPizza {
#     nodes {
#       name
#       id
#       price
#       slug {
#         current
#       }
#       toppings {
#         name
#       }
#     }
#   }
#   allSanityPerson {
#     nodes {
#       name
#     }
#   }
# }
```