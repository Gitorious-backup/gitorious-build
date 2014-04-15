FROM ubuntu:12.10
MAINTAINER Marcin Kulik

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update

ENV RAILS_ENV production

# add git user
RUN useradd -m -d /home/git git

# disable startup of services during image creation
# RUN echo exit 101 > /usr/sbin/policy-rc.d && chmod +x /usr/sbin/policy-rc.d

# install required packages
RUN apt-get -y install ruby ruby-dev rake git

# create dir for the app and docker's files
RUN mkdir /srv/gitorious && chown git:git /srv/gitorious

# checkout and build the app in /srv/gitorious/app
RUN sudo -u git git clone git://gitorious.org/gitorious/mainline.git /srv/gitorious/app

# install bundler
RUN gem install bundler --no-rdoc --no-ri

# checkout source code and bundle
RUN su git -c "cd /srv/gitorious/app && git checkout -q v3.0.1"

RUN apt-get -y install build-essential libmysqlclient-dev libxml2-dev \
                       libxslt1-dev libreadline6 libicu-dev imagemagick

RUN su git -c "cd /srv/gitorious/app && bundle install --deployment --without development test postgres"

WORKDIR /srv/gitorious/app

# symlink external (exposed) config files
RUN ln -s /srv/gitorious/config/database.yml /srv/gitorious/app/config/; \
    ln -s /srv/gitorious/config/gitorious.yml /srv/gitorious/app/config/; \
    ln -s /srv/gitorious/config/smtp.yml /srv/gitorious/app/config/; \
    ln -s /srv/gitorious/config/authentication.yml /srv/gitorious/app/config/

RUN su git -c "git fetch -q && git checkout a07426e && bundle"

RUN apt-get -y install nodejs

RUN su git -c "git submodule update --recursive --init && bundle exec rake assets:precompile"

# copy configs and scripts into the image
ADD config/gitorious.overrides.yml /srv/gitorious/app/config/gitorious.overrides.yml
ADD config/resque.yml /srv/gitorious/app/config/resque.yml
ADD config/memcache.yml /srv/gitorious/app/config/memcache.yml
ADD config/thinking_sphinx.yml /srv/gitorious/app/config/thinking_sphinx.yml
ADD config/unicorn.rb /srv/gitorious/app/config/unicorn.rb
ADD templates /srv/gitorious/templates
ADD run /usr/local/bin/run

EXPOSE 3000
VOLUME ["/srv/gitorious/app/log"]
VOLUME ["/srv/gitorious/app/tmp"]
VOLUME ["/srv/gitorious/config"]

ENTRYPOINT ["/usr/local/bin/run"]
