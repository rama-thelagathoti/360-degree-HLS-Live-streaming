# 360-degree-HLS-Live-streaming
nginx, nginx-rtmp and ffmpeg based 360 degree live streaming

## Install ffmpeg


```
 sudo apt install ffmpeg'
 ffmpeg -version /* verify installed version*/
 ffmpeg -decoders /* check supported decoders*/
 
```

## Install nginx
Install dependencies
```
  sudo apt-get install git
  sudo apt-get install build-essential libpcre3 libpcre3-dev libssl-dev
 
```
I am installing 1.16.1 which is the latest version at the time i am using,
cross check latest version and install it
```
 wget http://nginx.org/download/nginx-1.16.1.tar.gz
 tar -xf nginx-1.16.1.tar.gz
 cd nginx-1.16.1/
 
```

## Install nginx-rtmp module


`sudo apt-get install git`
