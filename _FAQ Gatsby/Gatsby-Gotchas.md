# Gatsby Gotchas

## Gatsby-config.

### secrets

Do not put secrets in code or config files.  

Even if your site does not surface the config file, you do not want secrets in GIT or any version control system.

USE environment files such as ( .env ) for node

In the .env file use a name in caps with no spaces and set that equal to your token (or other secret) without using any quotes.

```env
SANITY_TOKEN=skZF7Iblahblahblah
```
