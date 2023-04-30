## Отчёт о порядке выполнения ДЗ по теме "Couchbase"
1. Созданы 8 виртуальных машин (так как для тестового аккаунта Яндекс это максимальная квота на ВМ без доп запросов), на которые установили инстансы Couchbase:
  * ![image](https://user-images.githubusercontent.com/87138548/235359429-6d28d7a0-7f94-4c22-9338-1a35a9f2848c.png)
2. Зашли в веб-форму для дальнейшей настройки кластера:
  * ![image](https://user-images.githubusercontent.com/87138548/235359102-a779cb3a-da6b-4833-acaa-aa142e470283.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235360147-64e407a0-35a2-4957-9115-1b299ecfe836.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235362166-a209b559-4552-4d86-87b6-334ba1528b56.png)
3. Создали свой бакет в кластере, указав что данные должны будут копироваться ещё на 3 реплики
  * ![image](https://user-images.githubusercontent.com/87138548/235364637-eeb9ffc4-79df-4503-bb5c-e7bb26c84957.png)
  * И заполинили его данными из файлика с пассажирами Титаника, с помощью консольной утилиты cbimport
  * `export PATH=$PATH:/opt/couchbase/bin`
  * `cbimport csv -c couchbase://localhost -u admin -p 111111 -b titanic -d file://titanic.csv -g passanger::#MONO_INCR#`
  * ![image](https://user-images.githubusercontent.com/87138548/235364150-3159b2aa-795a-403b-9eb1-c73b83345462.png)
  * Данные успешно загрузились и можно просмотреть их по батчам, а так же подкорректировать при активном флаге field editing
  * ![image](https://user-images.githubusercontent.com/87138548/235365102-4716a66e-4a39-44f3-a200-89aa48135a99.png)
  * для генерации id в команде cbimport в ключе -g можно было указать #UUID#, чтобы сгенерировать уникальные идентификаторы
4. Создали простой индекс по полю "Age"
  * ![image](https://user-images.githubusercontent.com/87138548/235366165-fd3f27f7-ce9c-4edd-9d3c-0eea93c58aa8.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235366197-ebc7ab0b-d087-44d1-a35c-0828d51bec85.png)
  * и выполнили запрос, прсмотрев всех пассажиров, старше 31 года (всего 399 записей)
  * ![image](https://user-images.githubusercontent.com/87138548/235366954-aec1210d-f59b-4f3e-8740-c56d895a3b07.png)
5. При выключенной 5ой ноде данных, тот же самый запрос вернул только 3 записи
  * ![image](https://user-images.githubusercontent.com/87138548/235367477-c4a4a461-7fd2-4c6a-b67e-e1d3b81e1a17.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235367494-0f362318-a92c-4b9c-b103-111f5a5f7999.png)
6. После того, как запустили Failover и он успешно завершился, запрос отдал все 399 записей
  * ![image](https://user-images.githubusercontent.com/87138548/235367666-a3750f9b-ffb1-4cd8-9bff-487d44712394.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235367696-822e4c1f-99e4-4d35-93c9-8e84c617181f.png)
7. После обратного включения 5ой ноды, она стала доступна для восстановления
  * ![image](https://user-images.githubusercontent.com/87138548/235368296-360a906c-7eb3-47b7-a6c8-d689f90f9c6d.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235368346-409ba617-8aa1-4225-bb10-659688ff1293.png)
8. При выключении сразу двух нод (6ой и 8ой), Failover завершился неудачно и данные оказались недоступны
  * ![image](https://user-images.githubusercontent.com/87138548/235368518-74a12b11-65ba-4a80-97e6-fea3d76ee9d4.png)
9. Поcле того, как ноды были возвращены, в настройках бакета был изменён параметр количества реплик для бакета, после чего - при выключении 6ой и 8ой нод,
Failover успешно завершился, а бакет вновь стал доступен для просмотра 
  * ![image](https://user-images.githubusercontent.com/87138548/235368980-4bb37fb3-72d5-4c89-936d-5b7304bb6a9d.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235369151-9d26da16-8494-4288-93ad-7e4693f98f6b.png)
  * однако запрос вернул 375 записей, против 399 ранее (п.4). Всего же осталось 828 записей из 887.
  * ![image](https://user-images.githubusercontent.com/87138548/235369729-de63a1e9-1949-46f6-9926-a3a446b4c630.png)
Таким образом уменьшение количества реплик для бакета может привести к утрате какой-то части данных, при выходе из строя нескольких нод и запуска Failover на них,
при большом количестве реплик для бакета Failover не завершится и данные не потеряются в процессе, однако будут недоступны до возвращения нод в строй.








