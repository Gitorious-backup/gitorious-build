FROM ubuntu:12.10
MAINTAINER Marcin Kulik

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update
RUN apt-get -y install postfix

ADD run /usr/local/bin/run

EXPOSE 25

CMD ["/usr/local/bin/run"]
