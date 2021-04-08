# Sanity FAQ

Q. For Array, how to display if key1 exists and key2 is true in Sanity?

A. Pass keys separately joined in rest parameter

then split the rest parameter object based on value type

then compare the length of each value; one filtered to be string and one filtered to be boolean true - use second filter to only keep values that are true - .filter(Boolean)

if lengths of resulting arrays are the same then apply the desired true condition result

```javascript
prepare: ({ title, media, veg, ...toppings }) => {
      // 1. filter undefined toppings out
      const tops = Object.values(toppings).filter(
        (top) => typeof top === 'string'
      );
      const isVeg = Object.values(toppings)
        .filter((top) => typeof top === 'boolean')
        .filter(Boolean);
      // 2. return the preview object for the pizza
      return {
        title: `${title} ${isVeg.length === tops.length ? ' 🌱' : ''}`,
        media,
        subtitle: tops.join(', '),
      };
    },
```

