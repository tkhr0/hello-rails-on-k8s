FROM ruby:2.5.3-stretch

ENV DEBIAN_FRONTEND noninteractive
ENV LANG C.UTF-8

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      apt-file \
      build-essential \
      git \
      zlib1g-dev \
      libssl-dev \
      libreadline-dev \
      libyaml-dev \
      libxml2-dev \
      libxslt-dev \
      vim \
      less

RUN apt-file update \
 && apt-get install -y --no-install-recommends software-properties-common

RUN apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 40976EAF437D05B5 3B4FE6ACC0B21F32 \
 && add-apt-repository 'deb http://archive.ubuntu.com/ubuntu trusty universe' \
 && apt-get update \
 && apt-get install -y --no-install-recommends --allow-downgrades mysql-client-5.6

RUN curl -sL https://deb.nodesource.com/setup_8.x | bash - \
 && apt-get install -y --no-install-recommends nodejs

RUN wget https://github.com/yarnpkg/yarn/releases/download/v1.7.0/yarn_1.7.0_all.deb \
 && dpkg -i yarn_1.7.0_all.deb

RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
 && echo 'UTC=no' > /etc/default/rcS

RUN mkdir -p /root/.ssh

RUN apt-get clean
RUN rm -r /var/lib/apt/lists/*

WORKDIR /myapp

EXPOSE 3000

## add
COPY . .
RUN bundle install
RUN yarn install
CMD bundle exec rails server
