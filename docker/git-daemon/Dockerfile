FROM ubuntu:12.10
MAINTAINER Marcin Kulik

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update
RUN apt-get -y install git-daemon-sysvinit

ADD run /usr/local/bin/run

EXPOSE 9418

CMD ["/usr/local/bin/run"]
