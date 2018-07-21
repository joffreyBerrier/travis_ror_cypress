# Configuration
1. Ruby On Rails 2.4.1
2. Node Js 9.11.2
3. Postgresql 9.4
4. Cypress

### Travis.yml

```yml
language: node_js
node_js:
  - 9.11.2                                                      # choose your version
addons:
  postgresql: "9.4"                                             # choose your version
services:
  - redis-server                                                # redis is not necessary, https://redis.io/
  - postgresql
cache:
  bundler: true                                                 # cache for bundler
  yarn: true                                                    # cache for yarn
  directories:
    - /home/travis/.rvm/                                        # cache for bundler in node environement
    - ~/.cache                                                  # cache travis
before_install:
  - gem install bundler                                         # install bundler
  - bundle install                                              # install dependancies in your gemfile
install:
  - yarn add cypress                                            # install just cypress
before_script:
  - psql -c 'create database travis_ci_test;' -U postgres       # create database test
  - bin/rails server &                                          # launch server rails
  - bundle exec rake assets:precompile                          # compile assets js
  - sleep 10                                                    # pause 10 seconde for Big app
  - RAILS_ENV=test bundle exec rake db:schema:load              # create database with table
  - RAILS_ENV=test bundle exec rake your_seed                   # launch your seed
script:
  - $(yarn bin)/cypress run --record                            # launch your frontend test with Cypress
  - bundle exec rspec spec/controllers spec/models spec/jobs    # launch your backend test

```