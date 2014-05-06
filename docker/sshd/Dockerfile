FROM gitorious/app
MAINTAINER Marcin Kulik

RUN apt-get -y install openssh-server

RUN mkdir /var/run/sshd

# add empty authorized_keys file for git user
RUN mkdir -p /home/git/.ssh && chown -R git:git /home/git/.ssh && chmod 0700 /home/git/.ssh

# setup /usr/bin/gitorious so it executes bin/gitorious from the app dir
RUN ln -s /srv/gitorious/app/bin/gitorious /usr/bin/gitorious

ADD run /usr/local/bin/run

EXPOSE 22

CMD ["/usr/local/bin/run"]
