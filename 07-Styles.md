# React Gatsby

## React Component vs Gatsby Page

### Styles in Gatsby

you could just add a Link that to a stylesheet and it would work but... 
...that goes against the notion that Gatsby needs to know about everything in your project
...this includes Styles, Images, etc.

This allows Gatsby to figure out the 'Critical CSS' 
... the CSS needed to for the page to be rendered and includes all that CSS before any of the HTML in the rendered Page.
... this ensures there is no Flash of Unstyled Content

### How to Include CSS in Gatsby

There are actually many different ways to include CSS in Gatsby

- include CSS file in 'Styles' folder then import it into 'gatsby-browser.js' and it will just work!
- CSS Modules (... not Wes' recommended way; read more in Gatsby Docs)
- Styled Components (... Wes' choice)
 - scoped CSS inside React Applications
 - scope CSS and make reuseable React Components
 - don't have to use all features

### Create Some Global

CSS - some parts - go grab it; some we'll discuss

Global Styles - some things you want to be consistent across your entire website
- colors
- fonts
- text size
- max-size on images
- all browsers have default settings
  - use 'normalize.css' to set all styles so it doesn't matter what browser being used; we are starting with the same base settings.
  - ex. no border; no margins
  - 'normalize.css' installed as npm package so can just be imported
- Global styles SETUP as JS - NOT - CSS file.
  - GlobalStyles.js
  - Just copy from downloads because not that interesting
  - if copying from GitHub; change to RAW view before copying.
  - anything you want to be global just put it in this file.


### Gatsby - Import ANYTHING

Normally, inside vanilla JS you can only IMPORT JavaScript files
- in Gatsby you can import CSS or SVG or any type of image or other file type and Gatsby will handle it properly
- custom properties; used with VAR keyword; applied to `:root`  which makes it available anywhere in application/website
- 

This loads image as base64 text string; small; but when scaled can seem blurly while full image is loading; for extra effect we can optionally add **pixelated**

```css
  .gatsby-image-wrapper img[src*=base64\\,] {
    image-rendering: -moz-crisp-edges;
    image-rendering: pixelated;
  }
```

This is to prevent images from overflowing there parent container

```css
  img {
    max-width: 100%;
  }
```

Gatsby puts random identifier on image... say 'bg' and if you every change 'bg', Gatsby will know and it will put a different random identifier on it; so it's a different file name. so people never have to do a hard refresh because it won't be CACHED.




