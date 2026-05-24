# Checklist МКР

- [x] Є два окремі сервіси: Producer і Consumer.
- [x] Є окремий `Dockerfile` для Producer.
- [x] Є окремий `Dockerfile` для Consumer.
- [x] Використовується Apache Kafka.
- [x] Producer створює топіки `demo-requests` і `demo-responses`, якщо їх немає.
- [x] Consumer створює ті самі топіки, якщо стартує першим.
- [x] Producer генерує `correlation-id`.
- [x] Producer підписується на `demo-responses` до надсилання запиту.
- [x] Producer надсилає повідомлення формату `start,finish`.
- [x] Consumer читає `start,finish` з `demo-requests`.
- [x] Consumer обчислює середню кількість кроків Колатца.
- [x] Consumer надсилає відповідь у `demo-responses`.
- [x] У відповіді використовується той самий `correlation-id`.
- [x] Producer чекає саме свою відповідь.
- [x] Producer після отримання відповіді залишається запущеним.
- [x] Consumer працює у нескінченному циклі.
- [x] README містить команди запуску.
- [x] Реальні файли коду лежать прямо в репозиторії, а не в zip-архіві.
