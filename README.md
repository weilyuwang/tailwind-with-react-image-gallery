## Tailwind CSS (2.0) with React

#### Reference: 
- Using Tailwind CSS with Create React App [https://daveceddia.com/tailwind-create-react-app]
- Tailwind CSS v2.0 | Setup & Install React with Tailwind Css [https://www.youtube.com/watch?v=mmFAW6t5G7Q&t=708s]

### Init React App

```
npx create-react-app APP_NAME
```

### Install Tailwind, its peer-dependencies and Tools

```
npm install -D tailwindcss postcss postcss-cli autoprefixer npm-run-all onchange
```
- Use `onchange` to watch for changes & rebuild   
- Use `npm-run-all` to run the file watcher and CRA in parallel.    


### Set up npm build command

Config `package.json` so that `npm start` command will run both the Tailwind build and the CRA server.

Update the scripts section of package.json to look like this:

```
"scripts": {
    "start": "npm-run-all start:tailwind -l -p start:p:*",
    "start:tailwind": "postcss ./src/assets/css/tailwind.css -o ./src/assets/css/tailwind.output.css",
    "start:p:watch-tailwind": "onchange 'tailwind.config.js' 'src/**/*.css' -e './src/assets/css/tailwind.output.css' -- npm run start:tailwind",
    "start:p:react": "react-scripts start",
    
    "build": "run-s build:** -l", 
    "build:a:tailwind": "NODE_ENV=production npm run start:tailwind",
    "build:b:react": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
},
```

`start:tailwind` runs the `postcss-cli` tool and generates the CSS that we can import into our app.

`start:p:watch-tailwind` will re-build the Tailwind output file when the tailwind.config.js file or any CSS files under the src directory are changed. It’s explicitly ignoring the tailwind.output.css file so that we don’t get an infinite loop of rebuilds.

`start` script does a few things:
- first, a complete (re)build of Tailwind CSS file
- then, in parallel (-p), watch for CSS changes and start the CRA dev server (it runs all `start:p:*` scripts in parallel)

`build:a:tailwind` runs `start:tailwind` with NODE_ENV set to production 
`build` runs all `build**` scripts sequentially (run-s)

#### NPM supports pre- and post- scripts

Scripts that start with the word “pre” are special to NPM (and Yarn). They automatically run before the named script.

We’re effectively saying “build Tailwind before starting up dev mode” and “build Tailwind before building the React app for production”.


### Set up the Tailwind source CSS file (src/assets/css/tailwind.css)

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```
The Tailwind build will replace these @tailwind directives with Tailwind’s generated CSS and write it out to `src/assets/css/tailwind.output.css`.



### Import the Generated CSS File

Inside index.js file:

```
import "./assets/css/tailwind.output.css";
```


### Init Tailwind & postcss config files

```
npx tailwindcss init -p
```
- this will generate a `tailwind.config.js` file as well as a `postcss.config.js` in the root of the project.     



### Purge Unused CSS Classes (For Production)

PurgeCSS can leave the unused classes out of the final build, as long as you tell it where to look.

`Tailwind 1.4.0 added built-in support for PurgeCSS`, so we only need to add a little configuration to turn it on, and then our CSS build output will be WAY smaller.

Inside `tailwind.config.js` file, update the `purge` array:

```
module.exports = {
  purge: [
    'src/**/*.js',
    'src/**/*.jsx',
    'src/**/*.ts',
    'src/**/*.tsx',
    'public/**/*.html',
  ],
  theme: {
    extend: {},
  },
  variants: {},
  plugins: [],
}
```

For this simple React application, purgecss reduces ~290KB css file size: `1.94 KB (-293.71 KB)  build/static/css/main.ab16f177.chunk.css`


### Start App (Local Dev)

```
npm start
```

### Live 

- Deployed with Firebase Hosting: https://react-tailwind-image-gallery.web.app/
