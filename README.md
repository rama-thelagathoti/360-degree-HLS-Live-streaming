# 360-degree-HLS-Live-streaming
nginx, nginx-rtmp and ffmpeg based 360 degree live streaming

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

`sudo apt-get install git`
