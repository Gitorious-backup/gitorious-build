FROM ubuntu:14.04
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
                       libreadline6 libicu-dev imagemagick nodejs sudo \
                       mysql-client

# create dir for the app and docker's files
RUN mkdir /srv/gitorious && chown git:git /srv/gitorious

# clone app repository into /srv/gitorious/app
RUN su git -c "git clone git://gitorious.org/gitorious/mainline.git /srv/gitorious/app"

WORKDIR /srv/gitorious/app

# checkout specific commit
RUN su git -c "git fetch && git checkout -b deploy b97c197"

# install bundler
RUN gem install bundler --no-rdoc --no-ri

# install tools required for compiling rugged gem's native exts
RUN apt-get -y install cmake pkg-config

# checkout source code and bundle
RUN su git -c "bundle install --deployment --without postgres"

# build assets
RUN su git -c "git submodule update --recursive --init && bundle exec rake assets:precompile"

# put revision (git sha) and version (tag + sha) into public/
RUN su git -c "git rev-parse HEAD >public/REVISION && git describe --tags HEAD >public/VERSION"

# symlink uploads
RUN ln -ns /srv/gitorious/data/uploads /srv/gitorious/app/public/system && chown -h git:git /srv/gitorious/app/public/system

# copy internal configs into the image
ADD config /srv/gitorious/app/config/internal

ADD run /usr/local/bin/run

EXPOSE 3000
VOLUME ["/srv/gitorious/app/log"]
VOLUME ["/srv/gitorious/config"]

ENTRYPOINT ["/usr/local/bin/run"]
