# Styling Tips

## CSS and Styled-Components and Emmet

### Styling with Emmet shortcuts

|Abr|Result|
|:---|:---|
|ml1.5r|margin-left: 1.5rem;|
|mr1.5r|margin-right: 1.5rem;|
|bgs15p|background-size: 15%;|
|bgs1500px|background-size: 1500px;|

### Styling by data-attribute

```css
  a {
    font-size: 3rem;
    text-decoration: none;
    &:hover {
      color: red;
    }
    &[aria-current="page"] {
      
    }
  }
```


