#Steps to enable HLS live streaming

## Install ffmpeg
```
 sudo apt install ffmpeg'
 ffmpeg -version /* verify installed version*/
 ffmpeg -decoders /* check supported decoders*/
 
```
## Install nginx-rtmp module and nginx server
Install dependencies
```
  sudo apt-get install git
  sudo apt-get install build-essential libpcre3 libpcre3-dev libssl-dev
  wget http://zlib.net/zlib-1.2.11.tar.gz
  tar -zxf zlib-1.2.11.tar.gz
  cd zlib-1.2.11/
  ./configure
  make
  sudo make install
 
```
Install nginx-rtmp module

I am installing 1.16.1 which is the latest version at the time i am using,
cross check latest version and install it
```
 git clone https://github.com/sergey-dryabzhinsky/nginx-rtmp-module.git
 wget http://nginx.org/download/nginx-1.16.1.tar.gz
 tar -xf nginx-1.16.1.tar.gz
 cd nginx-1.16.1/
 ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module
 make -j 4
 sudo make install
 
```
## configure nginx
```
  whereis nginx /* path where nginx server is installed*/
  nano /usr/local/nginx/conf/nginx.conf
  /*if nano is not installed use gedit*/
  /*if there are any permission issue run following command also*/
  sudo chmod -R 777 /usr/local/nginx/conf/nginx.conf
  nano /usr/local/nginx/conf/nginx.conf
 
```
sample configuration file is as follows, copy following contents to /usr/local/nginx/conf/nginx.conf
```
worker_processes  auto;
events {
    worker_connections  1024;
}

# RTMP configuration
rtmp {
    server {
        listen 1935; # Listen on standard RTMP port
        chunk_size 4000;

        application live {
            live on;
            # Turn on HLS
            hls on;
            hls_path /mnt/hls/;
            hls_fragment 3;
            hls_playlist_length 60;
            # disable consuming the stream from nginx as rtmp
            deny play all;
	           #prevent auto delete from /mnt/hls after streaming is finished
            hls_cleanup off;		
        }
	    }
}

http {
    sendfile off;
    tcp_nopush on;
    #aio on;
    directio 512;
    default_type application/octet-stream;

    server {
        listen 8080;

        location / {
            # Disable cache
            add_header 'Cache-Control' 'no-cache';

            # CORS setup
            add_header 'Access-Control-Allow-Origin' '*' always;
            add_header 'Access-Control-Expose-Headers' 'Content-Length';

            # allow CORS preflight requests
            if ($request_method = 'OPTIONS') {
                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Max-Age' 1728000;
                add_header 'Content-Type' 'text/plain charset=UTF-8';
                add_header 'Content-Length' 0;
                return 204;
            }

            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /mnt/;
        }
    }
}
```
make sure  logs folder is writable, where nginx debug logs created
```
chmod -R 777 /usr/local/nginx/logs/*
chmod -R 777 /usr/local/nginx/logs/
``` 
## start nginx
this starts nginx as foreground daemon
`sudo /usr/local/nginx/sbin/nginx -g 'daemon off;'`
 or you can start nginx as background daemon
`sudo /usr/local/nginx/sbin/nginx -t`

Now setup is ready for streaming.

## stream your contents using ffmpeg 
```
sudo ffmpeg -i sample.mp4 -vcodec libx264 -vprofile baseline -g 30 -acodec aac -f flv rtmp://localhost/live/stream1
```
stream1 must be unique name and should not use for another stream at the same time. 'live' is the context under rtmp drective mentioned in nginx.conf file.
Here sample.mp4 is prerecorded file streamed to rtmp port. nginx server will stream from rtmp to client.

## test your stream
option 1:

test using VLC player 'play from network stream' option. if /mnt/hls is default location of HLS used in nginx.conf file then URL which has to be used for testing is "http://localhost:8080/hls/stream1.m3u8" or  "http://you IP address:8080/hls/stream1.m3u8"

option 2:

Use any HLS player available over the internet
for example http://player.streamingtvguides.com/



