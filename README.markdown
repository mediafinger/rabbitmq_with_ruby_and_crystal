# RabbitMQ Example Architecture

Setup RabbitMQ with at least one Ruby/Hutch publisher and at least one Crystal consumer.

Find at least one general purpose setup with the right amount of guarantees and defined workflows for:

* routing errors
* consumer issues
* worker issues
* retry handling
* dead message handling


## Ruby app with Hutch

Does the whole basic setup that will be used by all other consumers and publishers.

https://github.com/gocardless/hutch

* Use as many ENV as possible.
* Export the JSON/YAML RabbitMQ Config afterward.

Compare _$HOME/www/2018/rabbitmq_spike/hutch/_  
and _$HOME/www/2018/rabbitmq_soa/demo_app_with_hutch/_


## Crystal app with amqp-client

This app must follow the same basic setup! (Can it use a shared config?)

https://github.com/cloudamqp/amqp-client.cr

Could be an example for a fast worker that uses protobufs to define and collect a dataset.

https://github.com/jeromegn/protobuf.cr

**Idea:** receives "URN updated" event and fetches data of this element or  
      receives "sync type" event and fetches data of all elements of type


## Exponential (or step-wise) retries

* Requeue failed messages to a delay-exchange,
* give them a TTL according to the number of retries,
* create an expiring queue for each TTL and send the message there,
* define the normal exchange as dead-letter-exchange of the delay-exchange,
* ensure to use the original routing key, when dead-lettering / retrying the message.

https://www.brianstorti.com/rabbitmq-exponential-backoff/

Distinguish between messages with indefinite retries  
and messages to reject after a few attempts / a max duration.

https://github.com/alphasights/sneakers_handlers/blob/master/lib/sneakers_handlers/exponential_backoff_handler.rb


## Dockerize it?!

Installing Ruby, Crystal, PostgreSQL and RabbitMQ on a machine to develop locally and run things faster will feel great.

But for a simple setup of apps in different languages and repeatability on the CI, dockerize the setup.

Add a Dockerfile for the Ruby app:  
https://cloudonaut.io/dockerizing-ruby-on-rails/

Add A Dockerfile for the Crystal app:  
https://dev.to/franciscello/a-crystal-story-a-container-that-sails-1d37

Checkout the _$HOME/www/2018/rmq/_ repo for an example of an Alpine RabbitMQ Docker setup.

Use docker-compose to make it play nicely with the other services:
https://www.digitalocean.com/community/tutorials/containerizing-a-ruby-on-rails-application-for-development-with-docker-compose

Use Docker multi-stage builds:  
https://blog.bitsrc.io/a-guide-to-docker-multi-stage-builds-206e8f31aeb8



## Write End-2-End tests

A good README is great, but also add tests to demonstrate how the setup works.

Run the tests through a GitHub Action.

## Queue monitoring

When still feeling bored, throw Prometheus and Grafana into the mix to display RabbitMQs monitoring dashboard.
