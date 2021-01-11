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
Run this command for production mode (minifies files). Keep in mind that this is a single page application, but it uses `vue-router`, so make a redirect rule when hosting the build files (make sure the url always redirect to the index page, otherwise it may lead to a 404 error). To preview the build version locally, these steps are recommended:
1. Install serve (using `npm install -g serve`). Skip this step if serve is already installed globally.
2. Build the static html files (using `npm run build`)
3. Run `serve -s dist` at the project directory. (`-s` means single page application mode, and `dist` is the directory of the production build)

## Lints and fixes files
```
npm run lint
```

## Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
