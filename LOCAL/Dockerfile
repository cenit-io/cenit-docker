FROM ruby:2.5.5

# Install dependencies:
#   - build-essential: To ensure certain gems can be compiled
#   - nodejs: Compile assets
RUN set -x; \
  apt update \
  && apt install -y --no-install-recommends \
  openssh-server \
  zlib1g-dev \
  build-essential \
  libssl-dev \
  libreadline-dev \
  libyaml-dev \
  libxml2-dev \
  libxslt1-dev \
  libcurl4-openssl-dev \
  libffi-dev \
  nodejs \
  git \
  imagemagick \
  libmagickwand-dev \
  nginx

# Set an environment variable where the Rails app is installed to inside of Docker image

RUN git clone https://github.com/cenit-io/cenit.git /var/www/cenit
ENV RAILS_ROOT /var/www/cenit

# Set working directory
WORKDIR $RAILS_ROOT

RUN git reset --hard c93de6d7849ef6fe6040523eef6f32b76ce27f05

RUN mkdir -p /var/www/shared/log
RUN mkdir -p /var/www/shared/pids
RUN mkdir -p /var/www/shared/sockets

# Setting env up
ENV RAILS_ENV='production'
ENV RACK_ENV='production'

RUN bundle install --jobs 20 --retry 5 --without development test

RUN gem install foreman

ENV SKIP_MONGO_CLIENT=TRUE

#All copies
COPY mongoid.yml config/mongoid.yml
COPY ../Procfile Procfile

RUN set -x; \
   bundle exec rake assets:precompile

RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx

COPY ../cenit.conf /etc/nginx/sites-enabled/cenit.conf
RUN rm /etc/nginx/sites-enabled/default

EXPOSE 80 3000
