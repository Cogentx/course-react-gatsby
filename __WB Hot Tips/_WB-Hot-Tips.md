# Wes Bos - Hot Tips

## Objects

### Object.Values with Array.Filter(BOOLEAN)

Return an array of values that exist (no falsy object value)

```javascript
prepare: ({ title, media, ...toppings }) => {
      // 1. filter undefined toppings out
      const tops = Object.values(toppings).filter(Boolean);
      // 2. return the preview object for the pizza
      return {
        title,
        media,
        subtitle: tops.join(', '),
      };
    },
```

![WB-Hot Tips](./../_img/wb-obj-values.png)

### Gatsby-node

Q. Why not just pass the entire Pizza Object via Context and avoid the Query on the SinglePizzaPage?

A. That is perfectly valid... however... because all the SinglePizzaPages work the same... it is sometimes easier to have the Query directly in this code in case modifications or even just a quick check of something in the query is needed. Another benefit is that as soon as you make a change to the Query in SinglePizzaPage, it is automatically updated in the React-Dev-Tools. Another related advantage is that if you wrote your entire Query in Gatsby-Node you would have to kill the process each time you made a Query Update.
... again - not a hard and fast rule - personal preference
