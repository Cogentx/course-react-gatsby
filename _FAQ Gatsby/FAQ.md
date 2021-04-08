# Gatsby Q & A

## Q & A

Q: My data was showing perfectly fine in Gatsby a few days ago and now it isn't.

A: Many common issues such as stale data or corrupt data can be fixed by running gatsby clean in the terminal - make sure you are in the Gatsby folder in the terminal. Gatsby uses an aggressive cache that can corrupt at times so you can try to fix it by cleaning the cache out

Q: I'm getting a npm ERR! Cannot read property 'matches' of undefined message when installing the gatsby-cli globally.

A: Node 15.x isn't yet supported and will cause that error, it's better to stick with Node 14.x for the course at the moment.

Q: I'm getting an error when starting Gatsby that says it's not finding a gatsby-source-sanity/src/gatsby-node.ts file.

A: This error appears if you have a typo in your options that you pass to the gatsby-source-sanity plugin in the gatsby-config.js file. You can compare your code against the stepped solution for that file. Check that you have the same capitilization of the options (projectId, dataset, watchMode) as it is a common cause of the issue.

Q: I'm getting a import dotenv from 'dotenv'; SyntaxError: Unexpected identifier error when starting the Gatsby server.

A: Please use the provided npm scripts to start the Gatsby server (such as npm run develop or yarn develop). Don't use the global gatsby develop command as Gatsby doesn't support using ESM imports yet. The provided npm scripts uses an esm npm package to add support for the newer import syntax.

Q: I'm getting a Error: GraphQL API not deployed error when starting the Gatsby server.

A: You need to make sure you deployed the Sanity dataset to the GraphQL API by running the sanity graphql deploy production command in your terminal before it can be used by Gatsby. Make sure that you are in the Sanity folder in your terminal when you run that command.

Q: I made a change to my Sanity content and it's not showing in Gatsby in real-time?

A: Please make sure you have the 2 options watchMode and token configured in the gatsby-source-sanity plugin. If the changes you are making to the content is unpublished and you'd like to preview that in real-time you would need to add the overlayDrafts option to your Gatsby plugin. You can read more about this in the gatsby-source-sanity readme.

Q: I made a change to my Sanity schema and it's not showing in Gatsby?

A: Changes to the schema in your local Sanity studio will not automatically change the Sanity GraphQL API, you'll have to deploy those changes by using sanity graphql deploy production. Make sure that you are in the Sanity folder in your terminal when you run that command.


