# RabbitMQ Sender for IFS<sup>®</sup>

My intention was to create an AMQP based RabbitMQ sender which can be deployed in IFS Connector Senders. But after reading about <a href="https://rawcdn.githack.com/rabbitmq/rabbitmq-management/v3.7.7/priv/www/api/index.html"> RabbitMQ Management HTTP API</a>, I decided to implement that using existing IFS HTTP Sender and it worked like a charm :)
<br>
Due to this and my lazyness, creation of a RabbitMQ Sender was put on hold and still I didn't touch the development to be frank.
  Guide to setup this is added below and hope it will be useful!
  *** I just read the documentation once and implemented this by myself. Would like to know any expert comments or alternate implemetations if you have done this.

### Prerequisites
* <a href="https://www.getpostman.com/apps">PostMan</a>
* Administrative privileges for IFS Solution Manager

### Setup

* Open Postman and create a new request
* Go to Authorization, set type as Basic Auth. Provide Username/Password

* Create a new exchange
```
Type: PUT
URL: http://localhost:15672/api/exchanges/%2F/my_queue
Body:
{"type":"direct","auto_delete":false,"durable":true,"internal":false,"arguments":{}}
```
<br>
* Bind queue to exchange
```
Type: POST
URL: http://localhost:15672/api/bindings/%2F/e/my_queue/q/ifs_queue
Body:
{"routing_key": "ifs_queue","arguments": {},}
```
* Login to IFS EE client with Administrator account
* Routing Addresses, Create New
```
Destination Type:  Http
Http Sender:       HTTP_SENDER1
URL:               http://wq-nb-dsj:15672/api/exchanges/%2F/my_queue/publish
Login:             RabbitMQ Username
Password:          RabbitMQ user password
```
<br>
Sample message
```
{
    "properties": {
        "content-type": "application/json"
    },
    "routing_key": "ifs_queue",
    "payload": "Message from IFS",
    "payload_encoding": "string"
}
```

***According to the RabbitMQ doc, HTTP API is not ideal for high performance publishing; the need to create a new TCP connection for each message published can limit message throughput compared to AMQP or other protocols using long-lived connections. Therefore it's not recommend to use in Porduction Systems
  
  
