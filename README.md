# dockerfile

The lightweight (compressed less than 200MB) docker images designed for PHP web development.

These images based on Alpine linux and include an Nginx, PHP, Xdebug and Composer.


### How to build image

For example version 8.3:
```
cd anpx/8.3/
docker build -t bridennis/anpx:8.3 .
```

### How to push image to dockerhub

```
docker push bridennis/anpx:8.3
```