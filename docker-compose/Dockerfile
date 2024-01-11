FROM debian:stable

WORKDIR /root
COPY ./configure /root/ngx_configure
RUN apt update && apt upgrade -y &&\
    apt install -y wget make gcc perl &&\
    wget github.com/PCRE2Project/pcre2/releases/download/pcre2-10.42/pcre2-10.42.tar.gz &&\
    wget https://www.zlib.net/fossils/zlib-1.2.13.tar.gz &&\
    wget https://www.openssl.org/source/openssl-1.1.1v.tar.gz &&\
    wget https://github.com/vkholodkov/nginx-upload-module/archive/refs/tags/2.3.0.tar.gz &&\
    wget https://nginx.org/download/nginx-1.24.0.tar.gz &&\
    tar -zxf pcre2-10.42.tar.gz &&\
    cd pcre2-10.42 &&\
    ./configure &&\
    make &&\
    make install &&\
    cd .. &&\
    tar -zxf zlib-1.2.13.tar.gz &&\
    cd zlib-1.2.13 &&\
    ./configure &&\
    make &&\
    make install &&\
    cd .. &&\
    tar -zxf openssl-1.1.1v.tar.gz &&\
    cd openssl-1.1.1v &&\
    ./config &&\
    make &&\
    make install &&\
    cd .. &&\
    tar -zxf 2.3.0.tar.gz &&\
    tar -zxf nginx-1.24.0.tar.gz &&\
    cd nginx-1.24.0 &&\
    grep -v '^#' ../ngx_configure | tr '\n' ' ' | xargs ./configure &&\
    make &&\
    mkdir -p /usr/local/nginx/sbin &&\
    mkdir -p /usr/lib/nginx/modules &&\
    mkdir -p /var/run/nginx &&\
    mkdir -p /var/log/nginx &&\
    mkdir -p /var/cache/nginx &&\
    useradd nginx &&\
    usermod -a -G nginx nginx &&\
    chown -R nginx:nginx /var/log/nginx &&\
    chown -R nginx:nginx /var/cache/nginx &&\
    chown -R nginx:nginx /var/run/nginx &&\
    make install &&\
    sed -i 's/listen       80;/listen       8080;/g' /etc/nginx/nginx.conf &&\
    ln -s /usr/local/nginx/sbin/nginx /usr/local/sbin/ &&\
    apt purge --autoremove -y wget make gcc perl &&\
    rm -rf /var/lib/apt/lists/* &&\
    rm -rf /root/* 

USER nginx
EXPOSE 8080
CMD [ "nginx", "-g", "daemon off;" ]
