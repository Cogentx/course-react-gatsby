# React Gatsby

## 29-Paginating Data in Gatsby

Since Gatsby is **pre-built** we need to know how many pages we have at **build-time** instead of **run-time**

Put in **environment variable** in **.env** file. Variables could also be put in a JavaScript **settings** file if they do not contain secret info like tokens.

In order to surface a variable from **.env** inside Gatsby the variable must be prefixed in the file with **GATSBY_**

```bash
GATSBY_PAGE_SIZE=4
```
