```
FROM centos:7
RUN rm -f /etc/yum.repos.d/*
COPY CentOS-Base.repo /etc/yum.repos.d/
ADD nginx-1.28.0.tar.gz /mnt
WORKDIR /mnt/nginx-1.28.0
RUN yum install -y gcc make pcre-devel openssl-devel
RUN sed -i 's/CFLAGS="$CFLAGS -g"/#CFLAGS="$CFLAGS -g"/g' auto/cc/gcc
RUN ./configure --with-http_ssl_module --with-http_stub_status_module
RUN make
RUN make install
EXPOSE 80
VOLUME ["/usr/local/nginx/html"]
CMD ["/usr/local/nginx/sbin/nginx", "-g", "daemon off;"]
```
