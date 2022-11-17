FROM debian:buster

RUN apt-get upgrade -y && apt-get update -y && apt-get install nginx -y

COPY ./conf/default.conf /etc/nginx/sites-available/default/.
COPY ./tools/my.html /template/my.html
COPY ./tools/my.html /etc/nginx/sites-available/.

CMD ["nginx", "-g", "daemon off;"]