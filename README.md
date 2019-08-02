[![dockeri.co](https://dockeri.co/image/alejandro92/cenit)](https://hub.docker.com/r/alejandro92/cenit)
![cenit_io](https://user-images.githubusercontent.com/4213488/40578188-bcbf8a58-60c4-11e8-96d7-19842c348c5e.png)

[Cenit](https://cenit.io) is a 100% open integration Platform (iPaaS) that's modern, powerful, yet hackable to the core, ready to use in the cloud https://cenit.io or on-premises. It is designed to solve unique integrations needs, orchestrate data flows that may involve types of protocols and data formats, and provide API management capabilities. All of which can support wide range of integration use cases. Is particular valuable to embrace a pervasive integration approach.

## How to use this image

For run this image you need to configure the conection with MongoDB and RabbitMQ, if you prefer Docker Compose you can use the following orchestration:

```yaml
version: '3.7'

volumes:
  # We'll define a volume that will store the data from the mongo databases:
  mongodb-data: {}

services:
  cenit:
    environment:
      - ENABLE_RERECAPTCHA=false
      - DB_PORT=mongodb
      - RABBITMQ_BIGWIG_TX_URL=amqp://cenit_rabbit:cenit_rabbit@rabbitmq/cenit_rabbit_vhost
      - UNICORN_WORKERS=5
      - MAXIMUM_UNICORN_CONSUMERS=3
      - DB_PROD='cenit_prod'
    build: .
    ports:
      - "80:80"
    depends_on:
      - mongodb
      - rabbitmq

  rabbitmq:
    image: rabbitmq
    ports:
      - "15672:15672"
      - "5672:5672"
    environment:
      RABBITMQ_DEFAULT_PASS: cenit_rabbit
      RABBITMQ_DEFAULT_USER: cenit_rabbit
      RABBITMQ_DEFAULT_VHOST: cenit_rabbit_vhost
    labels:
        NAME: "rabbitmq-server"

  mongodb:
    image: mongo:3.2
    volumes:
      # We'll mount the 'mongodb-data' volume into the location mongodb stores it's data:
      - mongodb-data:/data/db
```

You can then run using Docker Compose:
```bash
docker-compose up -d
```

