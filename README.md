# Работа с API

    Для работы с запросами по API понадобится аутентификация через Bearer Token.

    ## Аутентификация

    Для получения токена нужно сделать запрос методом POST с логином и паролем вашего старшего пользователя в роли "брокер" на адрес: `https://api.p2p.business/api/auth/login` и в параметрах body / form-data передать логин и пароль:

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/auth/login' \
    --form 'email="LOGIN"' \
    --form 'password="PASSWORD"'
    ```

    Затем скопировать токен который вы будете подставлять в headers для всех последуюших запросов. Токен бессрочный, по желанию вы можете самостоятельно его сменить вернувшись на шаг назад и заново обратится к `https://api.p2p.business/api/auth/login`

    ## Тестовый Запрос

    Что бы протестировать авторизацию в API отправте запрос с вашим токеном в заголовке по адресу `https://api.p2p.business/api`, если в ответ прийдёт json со списком роутов, значит вы успешно прошли авторизацию.

    ```bash
    curl --location --request POST 'https://api.p2p.business/api' \
    --header 'Authorization: Bearer ВАШ_ТОКЕН' \
    --header 'Content-Type: application/json'
    ```

    ## Получение списка типов карт

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/card-types' \
    --header 'Authorization: Bearer ВАШ_ТОКЕН' \
    --header 'Content-Type: application/json'
    ```

    ## Получение списка доступных стран

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/countries' \
    --header 'Authorization: Bearer ВАШ_ТОКЕН' \
    --header 'Content-Type: application/json'
    ```

    ## Получение списка банков по стране

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/banks/ru' \
    --header 'Authorization: Bearer ВАШ_ТОКЕН' \
    --header 'Content-Type: application/json'
    ```

    ## Запрос на подбор карты

    В этом запросе будет предложена карта по заданым критериям, поля `country` и `amount` обязательные, остальные опционально.
    
    ⚠️ Важно! На это этапе идёт только подбор карты, что бы взять эту карту в работу обязательно отправтье запрос, что вы её забираете, в противном случае транзакции будут потеряны.

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/get-card' \
    --header 'Authorization: Bearer 1|Tx3dKVLZScFZcjk0WfdjmddDaI7ypNDRAl0HWDQT' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "country": "ru",
        "amount": "1000",
        "bank": 2,
        "type": "visa",
        "slavic": true,
        "excluded_banks": 1,
        "exeption": 1
    }'
    ```

    ## Забрать карту в работу

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/get-card/ID_КАРТЫ' \
    --header 'Authorization: Bearer ВАШ_ТОКЕН' \
    --header 'Content-Type: application/json'
    ```

    в ответ вы получите либо положительный статус:

    ```json
    {
        "status": true
    }
    ```

    либо ошибку с текстом

    ```json
    {
        "status": false,
        "message": "This card is already in use"
    }
    ```

    ## В работе 🚧 
    ¡ запросы не будут отрабатывать !

    ## Создание транзакции

    Создаём транзакцию по карте

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/create-transaction/ID_КАРТЫ' \
    --header 'Authorization: Bearer 1|Tx3dKVLZScFZcjk0WfdjmddDaI7ypNDRAl0HWDQT' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "amount": "1000",
        "bill": "https://yourdomain.com/bill2934849.jpg",
    }'
    ```

    в ответ вы получите либо положительный статус:

    ```json
    {
        "status": true,
        "id": 35890
    }
    ```

    либо ошибку с текстом

    ```json
    {
        "status": false,
        "message": "Card limit has exceeded"
    }
    ```


    ## Получение статуса по транзакции

    Можно получать статус по конкретной транзакции 

    ```bash
    curl --location --request POST 'https://api.p2p.business/api/transaction/ID_ТРАНЗАКЦИИ' \
    --header 'Authorization: Bearer 1|Tx3dKVLZScFZcjk0WfdjmddDaI7ypNDRAl0HWDQT' \
    --header 'Content-Type: application/json'
    ```

    ответ:

    ```json
    {
        "status": true,
        "id": 35890,
        "transaction_status": "success",
    }
    ```

    Альтернативно: можно обсудить вариант получение списком

    ```json
    [
        {
            "id": 35890,
            "transaction_status": "success",
        },
        {
            "id": 35891,
            "transaction_status": "canceled",
        },
        {
            "id": 35892,
            "transaction_status": "waiting",
        },
    ]
    ```
