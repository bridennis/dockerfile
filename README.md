# dockerfile

The lightweight (compressed less than 200MB) docker images designed for PHP web development.

These images based on Alpine linux and include an Nginx as an HTTP server, PHP, Xdebug and Composer.


### How to build image

For example version 8.1:
```
cd anpx/8.1/
docker build -t bridennis/anpx:8.1 .
```

### How to push image to dockerhub

```
docker push bridennis/anpx:8.1
```