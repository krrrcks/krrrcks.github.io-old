---
layout: post
title: "Unicode support for Octopress"
date: 2014-06-10 09:26:22 +0000
comments: true
categories: [Docker, Octopress]
---

Well, it seems Octopress/Jekyll would like to have a locale set
for UTF-8 support. I followed [this (text in German)](http://www.dominik-gaetjens.de/blog/2012/06/09/utf-8-in-octopress/) hint and now my Dockerfile looks
like this:

```
# dockerfile for octopress

FROM ubuntu:14.04
MAINTAINER krrrcks <krrrcks@krrrcks.net>
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update; \
  apt-get -q -y upgrade
RUN /usr/sbin/locale-gen en_US.UTF-8; \
  update-locale LANG=en_US.UTF-8
RUN apt-get -q -y install git curl; \
  apt-get clean
RUN git clone git://github.com/imathis/octopress.git /opt/octopress
RUN curl -L https://get.rvm.io | bash -s stable --ruby
ENV HOME /root
RUN echo "export LC_ALL=en_US.UTF-8" >> /root/.bashrc
RUN echo "export LANG=en_US.UTF-8" >> /root/.bashrc
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

After playing around with Docker and Octopress I put the whole `/opt/octopress` folder on my host machine and then 
restarted the image with the `-v` flag. Therefore I can edit the files on my host machine with my favorite editor
and use the container only for producing the HTML files, for preview and for publishing. 

The `rake preview` is a neat feature because the server always looks for changed files and produces the HTML
files on the fly. That means I can edit the files in my editor and could see the resulting pages in a 
browser nearly the same time.

