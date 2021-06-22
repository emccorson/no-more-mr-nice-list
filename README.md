# no-more-mr-nice-list

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

### Other stuff
I needed to format the facebook messages properly using this command:
```
cat message_1.json | jq . | iconv -f utf8 -t latin1 > m1.json
```
