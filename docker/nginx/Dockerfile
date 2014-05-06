FROM ubuntu:14.04
MAINTAINER Marcin Kulik

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update

RUN apt-get -y install nginx ruby

RUN useradd -m -d /home/git -u 5000 -U git

ADD nginx.conf /etc/nginx/nginx.conf
ADD gitorious.conf.erb /etc/nginx/sites-available/default.erb
ADD gen-nginx-conf /usr/local/bin/gen-nginx-conf
ADD run /usr/local/bin/run

EXPOSE 80
VOLUME ["/var/log/nginx"]

CMD ["/usr/local/bin/run"]
