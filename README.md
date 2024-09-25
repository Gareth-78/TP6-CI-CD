# TP6-CI-CD
## app.js

```
// Load the http module to create an http server.
var http = require('http');
// Configure our HTTP server to respond with Hello World to all requests.
var server = http.createServer(function (request, response) { response.writeHead(200, {"Content-Type": "text/plain"}); response.end("Hello World");
});
// Listen on port 8000, IP defaults to "0.0.0.0"
server.listen(8000);
// Put a friendly message on the terminal
console.log("Server running at http://127.0.0.1:8000/");
```

## package.json

```
{
"name": "hello-world", 
"description": "hello world", 
"version": "0.0.1", 
"private": true, 
"dependencies": {
"express": "3.x" },
"scripts": {"start": "node app.js"} 
}
```

## introci.dockerfile

```
FROM node
WORKDIR /app
ADD package.json /app/ 
RUN npm install
ADD . /app
EXPOSE 8000
ENTRYPOINT ["npm", "start"]
```

## test
![Capture d’écran du 2024-09-25 21-57-27](https://github.com/user-attachments/assets/94e69e01-0833-4b41-901a-5c11b102ccbf)

## Build.sh

```
#!/bin/bash
# The first parameter passed to this script will be used as an image version. 
# If none is passed, latest will be used as a tag.
if [ -z "${1}" ]; then
   version="latest"
else
   version="${1}"
fi

cd introci

docker build -t localhost:5000/containersol/introci:${version} . 

cd ..
```
## test.sh

```
#!/bin/bash
# The first parameter passed to this script will be used as an image version. 
# If none is passed, latest will be used as a tag.
if [ -z "${1}" ]; then
   version="latest"
else
   version="${1}"
fi

docker run -d --name node_app_test -p 8000:8000 -v "$PWD":/app localhost:5000/containersol/introci:${version}

echo "Testing image: localhost:5000/containersol/nodejs_app:${version}"

# Allow the webserver to start up
sleep 1

# Test will be successful if the webpage at the 
# following URL includes the word “success” 
curl -s GET http://localhost:8000 | grep success status=$?

# Clean up the testing container 
docker kill node_app_test && docker rm node_app_test

if [ $status -eq 0 ] ; 
    then echo "Test succeeded"
else
   echo "Test failed"
fi

exit $status
```
## push.sh

```
#!/bin/bash

if [ -z "${1}" ]; then
   version="latest"
else
   version="${1}"
fi

docker push localhost:5000/containersol/introci:"${version}"
```
