# ToolsRunner

A tool runner is a
mechanism which handle tools (like NMAP) and run them according to time
scheduling. A case of customer like “I want NMAP to scan a specific segment
every Friday at 6:00 am oclock”.

![Image description](https://github.com/Doriyaspielman/ToolsRunner-client/blob/master/clock_img.JPG)

When the clock hits the time entered a post request will be sent to a
kotlin server. 
When we get the response back we can see the data received in a table.

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
