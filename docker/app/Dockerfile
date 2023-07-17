# We're currently using Ruby 2.5.1
FROM ruby:2.6.3


# In a single step - install the dependences (apk is Alpine Linux's 
# equivalent to apt) and then remove the cached files (to keep the image 
# as small as we can)
RUN apk add --update bash build-base nodejs mariadb-dev tzdata && \
  rm -rf /var/cache/apk/*

# Make a directory to hold our app and set the working directory to it
RUN mkdir /app
WORKDIR /app

# Do the gem installation stuff first, in a separate step, because they
# Don't change as often as the code (so we can cache them in a separate 
# Docker image layer)
COPY Gemfile Gemfile.lock ./
RUN bundle install --binstubs

# Now copy the current folder to the WORKDIR and drop root privileges
COPY . .
RUN chown -R nobody:nogroup /app

# Compile all assets
RUN bundle exec rake assets:precompile

# Inform Docker what port our container listens on at runtime
EXPOSE 3000

# Set a default command to start the Rails server (can override this for workers, etc.)
CMD ["bundle", "exec", "rails", "server", "-p", "3000"]