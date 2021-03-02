# Gatsby Course

## Gatsby Pages

Everything must go through Gatsby.

### Public folder

- this is where the build of Gatsby goes
- this is what gets deployed
- don't modify anything in this folder
- if you run into trouble with Gatsby; just delete this folder; Gatsby will rebuild it (same as Gatsby Clean)

### Static folder

- put files that you simply want to be served up
- like its a regular static webpage
- can put favicon in here
- generally don't put anything in this folder including images and fonts
- don't want to import anything into Gatsby via static path which is what would happen if you referenced this folder
- IMPORTANT: Everything must go through Gatsby, meaning that all of our JS, CSS, Images, everything MUST be imported by Gatsby so Gatsby can know all about your site and optimize the build for it


### src folder

- 'pages' is special in Gatsby; this is where most of Gatsby will live
  - 'pages' can be static or dynamic
  - setup 'index.js' and export react component to display when this page shows up
- the rest of the folders, such a 'components' are optional
- this layout is common
  - assets
  - components
  - pages
  - styles
  - templates (similar to pages by resusable )
  - utils (functions like formatting money)

### Package.json Scripts

IMPORTANT: develop with `npm start` from Gatsby folder (not main; not sanity)

### PAGES

IMPORTANT: when creating our 'Pages' Files we do not start with CAPS as we would with React Components because these are one-off files and NOT reusable React Components.

Create all the pages with copy; paste; rename;

In the next lesson we talk about going from page to page the Gatsby Way!

Page Naming

- Name the file with no Cap start
- Name the component with Name followed by Page such as HomePage
- 


Public - don't put stuff in here

Static - files to serve directly like favicon. Generally don't put anything in here
- REMEMBER:  Everything must GO THRU GATSBY

SRC
- all code goes in source but none of the folders are Gatsby specific except PAGES - this is where we put our Gatsby pages
- everything else we can call what we want and organize how we want
- examples
- assets (fonts and images)
- components (react compnents)
- styles (styles; notice .gitkeep allows git to keep empty folders)
- templates
- utils

Gatsby Pages

Pages in Gatsby can be dynamically generated or they can be done on file system routes

WILL CHANGE IN FUTURE... but for now **import react from 'react'; ** must go at the top of each Gatsby page

PACKAGE.JSON SCRIPTS
![04-scripts](./_img/04%20-%20scripts%20at%202.42.06%20PM.png)

"develop" puts us into a development live reload mode. We kick this off with "start"

`npm start`

pops up localhost URL as well as a "graphql"





