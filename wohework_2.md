## Отчёт о порядке выполнения ДЗ по теме "Базовые возможности MongoDB"
1. Виртуальная машина для выполения домашнего задания развернута в Яндекс Cloud в минимальной конфигурации:
![image](https://user-images.githubusercontent.com/87138548/232313093-262cc7e8-1630-4cb0-8ec4-c1af5e7215a7.png)
3. MongoDB успешно установлен на развёрнутой машине:
![image](https://user-images.githubusercontent.com/87138548/232312854-dd98a363-8cbb-471f-a391-6c86c503e719.png)
2. Подключились к MongoDB, переключились на администраторскую БД и создали отдельную роль с правами администратора и чтения-записи в любой БД:
![image](https://user-images.githubusercontent.com/87138548/232314606-b497949b-a4b0-48ac-8bac-054ce001e2de.png)
3. В /etc/mongod.conf включили авторизацию и возможность подключаться к БД из вне с любы хостов:
![image](https://user-images.githubusercontent.com/87138548/232314845-8e0b2166-d1a0-4bb0-a201-7fab1f5611f0.png)
после чего рестартовали сервис
5. Подключились обратно к MongoDB, используя нового пользователя для авторизации:
![image](https://user-images.githubusercontent.com/87138548/232316372-83022621-78aa-47b6-b33c-6d0548193958.png)
6. Создали новую БД titanic и коллекцию passangers, куда будем загружать данные из датасета:
![image](https://user-images.githubusercontent.com/87138548/232316474-7da31404-f5a2-4711-8fbe-699324e1a7ec.png)
7. Залили данные из предварительно загруженного на виртуальную машину датасета titanic.csv:
![image](https://user-images.githubusercontent.com/87138548/232318870-4f7e09af-fb88-4eba-b6f7-14c92e858b55.png)
8. Несколько запросов на выборку данных из коллекции passengers:
  * Посмотрим содержимое одного документа
  * ![image](https://user-images.githubusercontent.com/87138548/232321148-48d6c6c7-cfcc-4562-a773-04c8545206d4.png)
  * Всего на борту было 887 пассажиров
  * ![image](https://user-images.githubusercontent.com/87138548/232320291-525cc7a1-9ce6-446e-86b5-427e26596c22.png)
  * Из них 545 - погибли
  * ![image](https://user-images.githubusercontent.com/87138548/232320519-a0c59c9b-791f-4042-8b13-624a24e702ba.png)
  * Количество выживших пассажиров в возрасте от 18 до 50 лет
  * ![image](https://user-images.githubusercontent.com/87138548/232321566-8cabb5f9-6a73-4717-b6bb-5405692e22f1.png)
  * Всего среди выживших - 109 мужчин и 233 женщины
  * ![image](https://user-images.githubusercontent.com/87138548/232322508-da4f6f83-34b9-448b-89bd-40ebd9f3a26a.png)
9. Обновили несколько записей в коллекции passengers:
  * Усановили, что все, возрастом младше или равным 18 годам - живы
  * ![image](https://user-images.githubusercontent.com/87138548/232324538-b8b3367a-09c0-4f83-a24f-2bd997eed262.png)
  * Установили, что все, возрастом старше 55 лет - плыли первым классом
  * ![image](https://user-images.githubusercontent.com/87138548/232324624-4520c4f1-46b3-4e1c-84ef-0c2157130550.png)
10. Вставили несколько новых документов в коллекцию passengers
  ![image](https://user-images.githubusercontent.com/87138548/232325304-458a7966-2c70-4e15-b1ae-634b18091402.png)
  ![image](https://user-images.githubusercontent.com/87138548/232325431-c45a8870-d847-44af-9902-c46b86f65838.png)
11. Для запроса вида db.passengers.find({$and: [{"Age":{$gt: 18, $lt: 60}}, {"Pclass": 3}, {"Survived": 1}]}) имеем следующий план
  ![image](https://user-images.githubusercontent.com/87138548/232326563-8988a77c-1a04-40e5-aaf8-cb76417dd97b.png)
  * откуда видно что идёт последовательное сканирование всей коллекции
  * При создании индекса по полю "Age"
  * ![image](https://user-images.githubusercontent.com/87138548/232326781-01386327-e92f-4b60-903d-64168901420b.png)
  * план запроса будет содержать два шага: поиск в коллекции по индексу и затем извлечение данных
  * ![image](https://user-images.githubusercontent.com/87138548/232326944-cb24815a-c3af-426f-8815-c068d3318b1b.png)
  * при этом "needTime" у индексного сканирования равен 0
  * ![image](https://user-images.githubusercontent.com/87138548/232327010-40d9b799-a3b0-43d9-b622-c36d0d760644.png)
  * а у операции извлечения данных 613, что несколько меньше, чем про обычном последовательном сканировании колекции
  * ![image](https://user-images.githubusercontent.com/87138548/232327085-cc00ec33-2b2d-4067-b883-e5a2982786cd.png)










