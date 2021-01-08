# streamflix

## Project setup
1. Run `npm install` at the project directory to download all dependencies.
2. Create `Key.js` at `src` folder. The content should be a single exported variable called API_KEY, containing the API key for The Movie Database. The contents of the file should look like this (replace `yourtmdbapikeyhere` with the actual API key that is provided):
```
export const API_KEY = "yourtmdbapikeyhere"
```

## Compiles and hot-reloads for development
```
npm run serve
```
Run this command for development mode (changes are seen immediately after saving).

## Compiles and minifies for production
```
npm run build
```
Run this command for production mode (minifies files). Keep in mind that this is a single page application, but it uses `vue-router`, so make a redirect rule when hosting the build files (make sure the url always redirect to the index page, otherwise it may lead to a 404 error)

## Lints and fixes files
```
npm run lint
```

## Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
