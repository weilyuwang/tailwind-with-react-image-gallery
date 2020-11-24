## Tailwind CSS with React


### Init React App

```
npx create-react-app APP_NAME
```

### Install tailwindcss module & its peer-dependencies

```
npm install -D tailwindcss postcss autoprefixer
```

### Init Tailwind & postcss config files

```
npx tailwindcss init -p

- with -p option, postcss.config.js file is created 
```

### Include Tailwind CSS in the styles.css

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```