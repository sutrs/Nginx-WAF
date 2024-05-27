FROM ubuntu:latest
RUN apt-get update && \
	apt-get install -y wget tar apt-utils autoconf automake build-essential git libcurl4-openssl-dev libgeoip-dev liblmdb-dev libpcre3 libpcre3-dev libtool libxml2 libxml2-dev libssl-dev libyajl-dev pkgconf zlib1g-dev openssl libnginx-mod-http-headers-more-filter vim && \
	openssl req -x509 -nodes -days 365 -subj "/C=CA/ST=QC/O=Company Inc/CN=example.com" -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/private/nginx-selfsigned.crt && \
#	openssl x509 -in /etc/ssl/private/nginx-selfsigned.crt -out /etc/ssl/private/nginx-selfsigned.pem && \
	git clone --depth 1 -b v3/master --single-branch https://github.com/SpiderLabs/ModSecurity && \
	cd ModSecurity && \
	git submodule init && \
	git submodule update && \
	./build.sh && \
	./configure && \
	make && \
	make install && \
	cd / && \
	git clone --depth 1 https://github.com/SpiderLabs/ModSecurity-nginx.git && \
	git clone https://github.com/sutrs/headers-more-nginx-module.git && \
	wget http://nginx.org/download/nginx-1.25.1.tar.gz && \
	tar zxvf nginx-1.25.1.tar.gz && \
	cd nginx-1.25.1 && \
	./configure  --user=root --group=root --with-debug --with-ipv6 --with-http_ssl_module  --with-compat --add-module=/ModSecurity-nginx --without-http_access_module --without-http_auth_basic_module --without-http_autoindex_module --without-http_empty_gif_module --without-http_fastcgi_module --without-http_referer_module --without-http_memcached_module --without-http_scgi_module --without-http_split_clients_module --without-http_ssi_module --without-http_uwsgi_module --add-module=../headers-more-nginx-module && \
	make && \
	make install && \
	cd / && \
	git clone https://github.com/coreruleset/coreruleset.git /usr/src/owasp-modsecurity-crs && \
	cp -R /usr/src/owasp-modsecurity-crs/rules/ /usr/local/nginx/conf/  && \
	mv /usr/local/nginx/conf/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example  /usr/local/nginx/conf/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf && \
	mv /usr/local/nginx/conf/rules/RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example  /usr/local/nginx/conf/rules/RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf && \
	apt-get remove -y --purge apt-utils autoconf automake build-essential git pkgconf && \
	apt-get autoremove -y
COPY nginx.conf /usr/local/nginx/conf/nginx.conf
COPY kuzzle.conf /usr/local/nginx/conf/kuzzle.conf
COPY modsec_includes.conf /usr/local/nginx/conf/modsec_includes.conf
COPY modsecurity.conf /usr/local/nginx/conf/modsecurity.conf
COPY crs-setup.conf /usr/local/nginx/conf/rules/crs-setup.conf
#COPY build /usr/local/nginx/html/
#COPY ssl/certs/nginx-selfsigned.key /etc/ssl/private/
#COPY ssl/certs/nginx-selfsigned.crt /etc/ssl/private/
#COPY ssl/kuzzle-certs/localhost.key /etc/ssl/private/
#COPY ssl/kuzzle-certs/localhost.crt /etc/ssl/private/
CMD ./usr/local/nginx/sbin/nginx -g 'daemon off;'
EXPOSE 80
EXPOSE 443
