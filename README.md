### How to install localstack to practice:

- docker pull localstack/localstack

- docker run --rm -it -p 4566:4566 -p 4571:4571 localstack/localstack

Note: Keep in mind that you will need another instance of the terminal.

Or you can use docker-compose for more fleixibility:

```bash
services:
  localstack:
    image: localstack/localstack
    container_name: localstack_main
    ports:
      - "4566:4566" # puerto principal
      - "4571:4571" # puerto legacy
    environment:
      - SERVICES=s3,dynamodb,lambda # servicios que quieres levantar
      - DEBUG=1
      - DATA_DIR=/tmp/localstack/data
    volumes:
      - "./localstack-data:/tmp/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
``
