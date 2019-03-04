FROM ruby:2.6.1-slim-stretch

# 基本的なパッケージのインストール
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      build-essential \
      curl \
      gnupg gnupg1 gnupg2 \
      imagemagick \
      default-libmysqlclient-dev \
      vim \
      less

# Node.js と Yarn をインストールして不要なファイルを autoremove / clean で削除
RUN curl -sSL https://deb.nodesource.com/setup_10.x | bash - \
 && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
 && echo deb https://dl.yarnpkg.com/debian/ stable main | tee /etc/apt/sources.list.d/yarn.list \
 && apt-get update \
 && apt-get install -y --no-install-recommends nodejs yarn \
 && apt-get clean

WORKDIR /myapp

COPY Gemfile  .
COPY Gemfile.lock .
RUN bundle install

COPY package.json .
COPY yarn.lock .
RUN yarn install

COPY . .
CMD bundle exec rails server
