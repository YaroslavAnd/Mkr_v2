# МКР. Apache Kafka request-reply та послідовності Колатца

## Що реалізовано

Реалізовано два окремі Java-сервіси:

- `producer` — надсилає запит у Kafka-топік `demo-requests`, чекає відповідь у `demo-responses` і друкує `avgSteps`.
- `consumer` — слухає `demo-requests`, обчислює середню кількість кроків Колатца на проміжку `start..finish` і надсилає відповідь у `demo-responses`.

Зв'язок відбувається через Kafka за шаблоном request-reply. Для зв'язування запиту та відповіді використовується header `correlation-id`.

## Структура

```text
.
├── README.md
├── checklist.md
├── docker-compose.yml
├── producer
│   ├── Dockerfile
│   ├── pom.xml
│   └── src/main/java/com/example/ProducerApp.java
└── consumer
    ├── Dockerfile
    ├── pom.xml
    └── src/main/java/com/example/ConsumerApp.java
```

## Варіант запуску через Docker Compose

```bash
docker compose up --build
```

Перевірка логів:

```bash
docker logs kafka-producer
docker logs kafka-consumer
```

Зупинка:

```bash
docker compose down -v
```

## Варіант запуску командами з умови

### 1. Створити мережу

```bash
docker network create kafka-net
```

### 2. Запустити Kafka

```bash
docker run -d --name kafka --network kafka-net -p 9092:9092 \
-e KAFKA_NODE_ID=1 \
-e KAFKA_PROCESS_ROLES=broker,controller \
-e KAFKA_CONTROLLER_QUORUM_VOTERS=1@kafka:9093 \
-e KAFKA_CONTROLLER_LISTENER_NAMES=CONTROLLER \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:29092,CONTROLLER://0.0.0.0:9093,PLAINTEXT_HOST://0.0.0.0:9092 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092 \
-e KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT \
-e KAFKA_INTER_BROKER_LISTENER_NAME=PLAINTEXT \
-e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
-e KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR=1 \
-e KAFKA_TRANSACTION_STATE_LOG_MIN_ISR=1 \
-e KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS=0 \
-e KAFKA_AUTO_CREATE_TOPICS_ENABLE=true \
-e CLUSTER_ID=MkU3OEVBNTcwNTJENDM2Qk \
-v kafka-data:/var/lib/kafka/data \
confluentinc/cp-kafka:7.7.1
```

### 3. Зібрати образи

```bash
docker build -t kafka-demo-consumer -f consumer/Dockerfile consumer
docker build -t kafka-demo-producer -f producer/Dockerfile producer
```

### 4. Запустити сервіси

```bash
docker run -d --name kafka-consumer --network kafka-net -e BOOTSTRAP_SERVERS=kafka:29092 kafka-demo-consumer
docker run -d --name kafka-producer --network kafka-net -e BOOTSTRAP_SERVERS=kafka:29092 kafka-demo-producer
```

### 5. Перевірити логи

```bash
docker logs kafka-consumer
docker logs kafka-producer
```

Очікуваний результат у Producer:

```text
-> Запит надіслано: start=10 finish=100 id=...
<- Отримано відповідь: avgSteps=...
Готово. Контейнер живе.
```

Очікуваний результат у Consumer:

```text
Чекаю запитів у 'demo-requests'.
<- Отримано запит: start=10 finish=100
-> Надіслано відповідь: avgSteps=...
```

## Завантаження на GitHub

```bash
git init
git branch -M main
git add .
git commit -m "Add MKR Kafka request reply Collatz"
git remote add origin https://github.com/YOUR_LOGIN/mkr-kafka-collatz.git
git push -u origin main
```
