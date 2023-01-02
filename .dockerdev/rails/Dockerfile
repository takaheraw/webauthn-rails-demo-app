# syntax=docker/dockerfile:1

ARG RUBY_VERSION=2.7.2

FROM ruby:${RUBY_VERSION}

ARG DEBIAN_FRONTEND=noninteractive
ARG NODE_VER=16.15.0
ARG BUNDLER_VERSION=2.3.24
ENV GEM_HOME=/usr/local/bundle
ENV BUNDLE_PATH=$GEM_HOME
ENV BUNDLE_APP_CONFIG=$BUNDLE_PATH
ENV BUNDLE_BIN=/usr/local/bin
ENV BUNDLE_JOBS=4
ENV BUNDLE_RETRY=3
ENV LANG=C.UTF-8
ENV APP_ROOT=/app
ENV RAILS_ENV=development
ENV BIND="0.0.0.0"

COPY .dockerdev/rails/Aptfile /tmp/Aptfile

RUN ARCH= && \
    dpkgArch="$(dpkg --print-architecture)" && \
  case "${dpkgArch##*-}" in \
    amd64) ARCH='x64';; \
    ppc64el) ARCH='ppc64le';; \
    s390x) ARCH='s390x';; \
    arm64) ARCH='arm64';; \
    armhf) ARCH='armv7l';; \
    i386) ARCH='x86';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac && \
  apt-get update && \
  apt-get install -yq --no-install-recommends \
    build-essential \
    ca-certificates \
    curl \
    default-mysql-client \
    git \
    tini \
    wget \
    $(cat /tmp/Aptfile | xargs) && \
  cd ~ && \
	wget -q https://nodejs.org/download/release/v$NODE_VER/node-v$NODE_VER-linux-$ARCH.tar.gz && \
	tar xf node-v$NODE_VER-linux-$ARCH.tar.gz && \
	rm node-v$NODE_VER-linux-$ARCH.tar.gz  && \
  mv node-v$NODE_VER-linux-$ARCH /opt/node && \
  apt-get clean && \
  rm -rf /var/cache/apt/archives/* && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
  truncate -s 0 /var/log/*log

ENV PATH="${PATH}:${APP_ROOT}/bin:/opt/node/bin"

WORKDIR ${APP_ROOT}

RUN npm install -g npm@latest && \
	npm install -g yarn && \
  gem update --system  --no-document && \
  rm -f /usr/local/bin/ruby/gems/*/specifications/default/bundler-*.gemspec && \
  gem install bundler -v ${BUNDLER_VERSION} --no-document

COPY Gemfile Gemfile.lock ${APP_ROOT}/

RUN bundle config set --local path 'vendor/bundle' \
  && bundle install

ENTRYPOINT ["/usr/bin/tini", "--"]
EXPOSE 3000
