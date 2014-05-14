FROM ubuntu:12.10
MAINTAINER Marcin Kulik

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update

ENV RAILS_ENV production

# add git user
RUN useradd -m -d /home/git -u 5000 -U git

# disable startup of services during image creation
# RUN echo exit 101 > /usr/sbin/policy-rc.d && chmod +x /usr/sbin/policy-rc.d

# install required packages
RUN apt-get -y install ruby ruby-dev rake git build-essential rsync \
                       libmysqlclient-dev libxml2-dev libxslt1-dev \
                       libreadline6 libicu-dev imagemagick nodejs sudo

# create dir for the app and docker's files
RUN mkdir /srv/gitorious && chown git:git /srv/gitorious

# clone app repository into /srv/gitorious/app
RUN su git -c "git clone --depth 10 git://gitorious.org/gitorious/mainline.git /srv/gitorious/app"

WORKDIR /srv/gitorious/app

# checkout specific commit
RUN su git -c "git checkout -b deploy 61bb2b0"

# install bundler
RUN gem install bundler --no-rdoc --no-ri

# checkout source code and bundle
RUN su git -c "bundle install --deployment --without postgres"

# symlink external (exposed) config files
RUN ln -s /srv/gitorious/config/database.yml /srv/gitorious/app/config/; \
    ln -s /srv/gitorious/config/gitorious.yml /srv/gitorious/app/config/; \
    ln -s /srv/gitorious/config/smtp.yml /srv/gitorious/app/config/; \
    ln -s /srv/gitorious/config/authentication.yml /srv/gitorious/app/config/

# build assets
RUN su git -c "git submodule update --recursive --init && bundle exec rake assets:precompile"

# put revision (git sha) into public/REVISION
RUN su git -c "git rev-parse HEAD >public/REVISION"

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
VOLUME ["/srv/gitorious/config"]

ENTRYPOINT ["/usr/local/bin/run"]
