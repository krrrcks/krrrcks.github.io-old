---
layout: post
title: "My Dockerfile for setting up Octopress"
date: 2014-05-26 09:24:00 +0000
comments: true
categories: [Docker, Octopress]
---
After my trouble with installing all the dependencies for Octopress I 
came up with the following Dockerfile for Docker. This follows the 
instructions from the Octopress homepage and uses RVM for managing the
ruby dependencies.

```
# dockerfile for octopress

FROM ubuntu:14.04
MAINTAINER krrrcks <krrrcks@krrrcks.net>
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update; \
  apt-get -q -y upgrade
RUN apt-get -q -y install git curl; \
  apt-get clean
RUN git clone git://github.com/imathis/octopress.git /opt/octopress
RUN curl -L https://get.rvm.io | bash -s stable --ruby
ENV HOME /root
RUN echo "source /usr/local/rvm/scripts/rvm" >> /root/.bashrc; 
RUN /bin/bash -l -c "source /usr/local/rvm/scripts/rvm; \
  rvm install 1.9.3; \
  rvm use 1.9.3; \
  rvm rubygems latest; \
  cd /opt/octopress; \
  gem install bundler; \
  bundle install; \
  rake install" 
RUN echo "rvm use 1.9.3" >> /root/.bashrc

WORKDIR /opt/octopress
EXPOSE 4000
CMD ["/bin/bash"] 
```

