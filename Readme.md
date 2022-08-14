## ATOMAN 
### Приложение автотранспортного отдела Экспресс доставки

Приложение предназначено для учета состояния автомобилей отдела автотранспортного отдела (далее АТО) Экспресс доставки. 
Основные выполняемые задачи:
1. Закрепление автомобиля за водителем на момент передачи в эксплуатацию  с фиксацией информации в виде акта передачи.
2. Фиксация состояния автомобиля при передаче от механика водителю и обратно, от механика в сервисный центр и обратно, от водителя водителю.
3. Фиксация повреждения автомобиля на момент передачи в виде инцидентов с фотографиями и местами повреждения.
4. Просмотр хронологии повреждений и перемещения автомобиля между эксплуатирующими лицами.

Общая схема взаимодействия сервисов представлена ниже.
![Схема взаимеодейсвия сервисов Автотранспортного отдела](https://user-images.githubusercontent.com/60660331/184500954-84e04c79-8a9b-4d44-ac4b-bc89bfa6684a.png)

## Сервисы и элементы системы
### 1. Шлюз 1С
Основная задача сервиса - синхронизация данных о сотрудниках и автомобилях с БД 1С Предприятие. 
Серивис отслеживает изменения в данных по автомобилям и сотрудникам, публикуя информацию в **Шину сообщений**, из которой подписанные сервисы: **Сервис пользователей** и **Сервис хронологии по автомобилям** получают информацию, обновляя данные. Шлюз 1С так же подписывается на событие о факте передачи автомобиля от одного сотрудника другому и обновляет информацию в БД 1С Предприятие, а также формирует требуемые документы.

### 2. Пользователи, полномочия
Основная задача сервиса - хранение кэшированных данных об идентификаторах, ролях и также основной информации: ФИО, должность и т.п. полученной из 1С, а также предоставление этой информации при запросе от **API GateWay** на чтение.

### 3. Хронология автомобилей
Основные задачи сервиса: 
- хранение и выдача по запросу фактов передачи автомобилей
- хранение и выдача фактов осмотра автомобилей
- хранение и выдача информации об инцидентах при осмотре автомобиля

Всю новую информацию сервис получает из **Шины сообщений**, а так же отвечает на запросы от **API GateWay** на чтение хронологии по автомобилям.

### 4.Хранилище файлов к документам
Основная задача сервиса - хранение файлов документов (фотографий), которые пользователи прикладывают во время создания акта передачи или осмотра автомобиля. Файлы приходят в сервис через **Шину сообщений** и возвращаются по запросу от **API GateWay** на чтение.

### 5.Сервис авторизации JWT
Сервис получает от пользовательского приложения штрих-код с кодом пользователя или пару логин + пароль. Если пользователь известен - сервис делает запрос в сервис **Пользователей и полномочий** для получения информации о ролях пользователя в системе и пакует полученный ответ в JWT-токен, который сохраняет пользовательское приложение.

### 6.API GateWay
Основные задачи сервиса:
- маршрутизация трафика от приложения к нужным сервисам в зависимости от запроса
- агреггирование информации при запросе информации об инцидентах и актах передачи автомобилей

### Основные сценарии

1. [Создание нового пользователя](https://github.com/Flipmachin1001/ATOMAN/blob/4c3b932ed38f56b909cfe3a0433ce57d5f521d5d/%D0%A1%D1%86%D0%B5%D0%BD%D0%B0%D1%80%D0%B8%D0%B8/%D0%92%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8F%20%D0%B2%20%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D1%83.md)
2. [Вход в систему](https://github.com/Flipmachin1001/ATOMAN/blob/a287fcfc6eb49a8f208a8e19b63164d526f6a72a/%D0%A1%D1%86%D0%B5%D0%BD%D0%B0%D1%80%D0%B8%D0%B8/%D0%92%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8F%20%D0%B2%20%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D1%83.md))
3. [Осмотр автомобиля](https://github.com/Flipmachin1001/ATOMAN/blob/a287fcfc6eb49a8f208a8e19b63164d526f6a72a/%D0%A1%D1%86%D0%B5%D0%BD%D0%B0%D1%80%D0%B8%D0%B8/%D0%9E%D1%81%D0%BC%D0%BE%D1%82%D1%80%20%D0%B0%D0%B2%D1%82%D0%BE%D0%BC%D0%BE%D0%B1%D0%B8%D0%BB%D1%8F.md)
4. [Просмотр статистики по автомобилю](https://github.com/Flipmachin1001/ATOMAN/blob/01583602ce6f1dcce9e36b74dbb4b644ef9b46a4/%D0%A1%D1%86%D0%B5%D0%BD%D0%B0%D1%80%D0%B8%D0%B8/%D0%9F%D1%80%D0%BE%D1%81%D0%BC%D0%BE%D1%82%D1%80%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20%D0%BF%D0%BE%20%D0%B0%D0%B2%D1%82%D0%BE%D0%BC%D0%BE%D0%B1%D0%B8%D0%BB%D1%8E.md)

### Схемы и контракты данных
Для удобства просмотра можно использовать [Swagger Editor](https://editor.swagger.io/). 
Для просмотра необходимо в редактор вставить содержимое yaml файла.
1.[Шлюз 1С](https://github.com/Flipmachin1001/ATOMAN/blob/df67877bb38a561232939f3a3d486c2d765071d3/%D0%9A%D0%BE%D0%BD%D1%82%D1%80%D0%B0%D0%BA%D1%82%D1%8B/Gateway_1C.yaml)

2.[Авторизация](https://github.com/Flipmachin1001/ATOMAN/blob/df67877bb38a561232939f3a3d486c2d765071d3/%D0%9A%D0%BE%D0%BD%D1%82%D1%80%D0%B0%D0%BA%D1%82%D1%8B/Auth.yaml)

3.[Пользователи](https://github.com/Flipmachin1001/ATOMAN/blob/df67877bb38a561232939f3a3d486c2d765071d3/%D0%9A%D0%BE%D0%BD%D1%82%D1%80%D0%B0%D0%BA%D1%82%D1%8B/Users.yaml)

4.[Хранилище файлов](https://github.com/Flipmachin1001/ATOMAN/blob/df67877bb38a561232939f3a3d486c2d765071d3/%D0%9A%D0%BE%D0%BD%D1%82%D1%80%D0%B0%D0%BA%D1%82%D1%8B/Storage.yaml)

5.[Хронология автомобилей](https://github.com/Flipmachin1001/ATOMAN/blob/df67877bb38a561232939f3a3d486c2d765071d3/%D0%9A%D0%BE%D0%BD%D1%82%D1%80%D0%B0%D0%BA%D1%82%D1%8B/CarSharing.yaml)
