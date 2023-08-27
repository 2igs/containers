# 2igs' Container Images

## Get an image
Pull prebuilt image from [Docker Hub Registry](https://hub.docker.com/r/2igs/)

```
docker pull 2igs/{app}:{tag}
```

You can build the image yourself also.

```
git clone https://github.com/2igs/images.git
cd 2igs/{app}
docker build -t 2igs/{app} .
```
