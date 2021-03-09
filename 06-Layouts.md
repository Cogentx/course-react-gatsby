# React Gatsby

## React Component vs Gatsby Page

### Layouts in Gatsby

Gatsby special layout files can wrap your code.

MUST GO IN GATSBY ROOT DIRECTORY... NOT IN SRC !!!

- gatsby-browser (only works in browser so may have to also put in gatsby-ssr)
  - runs once page has been loaded in the browser
  - allows you to tap into gatsby apis
  - `wrapPageElement` (<https://www.gatsbyjs.com/docs/reference/config-files/gatsby-browser/#wrapPageElement>)
  - this is basically our own plugin
- ![emojis are from Rocket (only on Mac)](../)
- MUST RESTART WHEN THIS FILE MODIFIED 
- (ctrl+c; then `npm start`; refresh page)

```javascript
import React from 'react';
import Layout from './src/components/Layout';

export function wrapPageElement({ element, props }) {
  return <Layout {...props}>{element}</Layout>;
}
```

IMPORTANT!!!

- gatsby-ssr.js (in root of gatsby project)
  - copy everything from gatsby-browser.js






- gatsby-node
- gatsby-config


