# circleci-apache-conf
[![CircleCI](https://circleci.com/gh/leymannx/circleci-resolve-host.svg?style=svg)](https://circleci.com/gh/leymannx/circleci-resolve-host)

https://discuss.circleci.com/t/apache-conf-could-not-resolve-host/19994

So from one day to the next my Behat tests suddenly started failing as curl couldn't resolve my custom host anymore. I've set up a very [basic repo](https://github.com/leymannx/circleci-resolve-host) containing just an Apache conf and the config.yml to demonstrate that. In the end I simply `$ curl example.localhost` but it always returns `cURL error 6: Could not resolve host: example.localhost`. Whereas `$ curl localhost` just works fine.

This is my example.conf
```
Listen 8080

<VirtualHost *:8080>
  DocumentRoot /home/circleci/circleci-resolve-host
  ServerName example.localhost
</VirtualHost>
```

And this is my config.yml:
```
version: 2

jobs:
  build:
    branches:
      only:
        - master
    docker:
      - image: circleci/php:7.1-apache-jessie-node-browsers

    working_directory: ~/circleci-resolve-host

    steps:
      - checkout

      - run:
          name: Install some basic tools
          command: |
            sudo apt-get update -y
            sudo apt-get upgrade -y
            sudo apt-get install -y nano

      - run:
          name: Configure & start Apache
          command: |
            sudo cp ~/circleci-resolve-host/.circleci/example.conf /etc/apache2/sites-available/example.conf
            sudo a2ensite example
            sudo service apache2 start
            
      - run:
          name: Run tests
          command: |
            curl example.localhost
```

Is this a bug? Or what am I missing?
