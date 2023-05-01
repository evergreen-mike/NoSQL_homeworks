## Отчёт о порядке выполнения ДЗ по теме "Clickhouse"
1. Создали одиночную ВМ и установили на неё инстанс Clickhouse
  * ![image](https://user-images.githubusercontent.com/87138548/235459947-aed78c95-9096-4ce6-a914-00f0f9c6e90e.png)
2. Создали БД geo_data и структуру таблицы под тестовые загружаемые данные, как указано в датасете (https://clickhouse.com/docs/en/getting-started/example-datasets/cell-towers),
затем импортировали данные в созданную таблицу geo_data.cell_towers из файла cell_towers.csv с помощью команды 
`cat cell_towers.csv | clickhouse-client --password --query "INSERT INTO geo_data.cell_towers FORMAT CSVWithNames"`:
  * ![image](https://user-images.githubusercontent.com/87138548/235461815-d2a43151-ecea-4592-9133-994a3da576e2.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235462046-436ca8d4-c444-4125-a5a0-cc0e3a341f79.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235464181-54c7cd6f-e1c6-41d2-96f7-462758960632.png)
  * импорт более 43 млн. записей занял около 5 минут
3. Выполнили несколько запросов:
  * ![image](https://user-images.githubusercontent.com/87138548/235464981-52c1c705-5c76-4001-a9c4-b456f2e3765e.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235465283-5d730389-3024-488b-9f72-e6bc80d005c2.png)
  * скорость обработки (простой агрегации) всех 43 миллионов строк занимает менее секунды времени
4. Создали ещё одну БД nypd и структуру таблицы nypd.NYPD_Complaint под новые данные (https://clickhouse.com/docs/en/getting-started/example-datasets/nypd_complaint_data#run-queries),
наполнили её данными из файла NYPD_data.tsv
  * ![image](https://user-images.githubusercontent.com/87138548/235466143-5a468534-55e6-46ad-9bbb-cbcd0b0eb8c6.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235466358-e51f9225-138e-42b7-a478-ddb04bbe83c6.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235468991-14039e70-3c64-445f-b8e5-7cabcadbf30f.png)
  * в новой табличке небольшое количество строк, поэтому данные импортировались мгновенно
5. Выполнили несколько запросов:
  * ![image](https://user-images.githubusercontent.com/87138548/235469983-e0b7ffa7-7ae1-45c6-b58f-b711ae229959.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235470091-aa500985-20d4-42a2-a7e5-658082e3a9a2.png)
6. Развернули 3 машины для шардирования и установили на них clickhouse:
  * ![image](https://user-images.githubusercontent.com/87138548/235488031-78f157eb-4a9e-4b25-9c5e-58be20fc04d5.png)
  * в файл `/etc/clickhouse-server/config.xml` добавлены настройки шардированного кластера "my_test_cluster"
  * ![image](https://user-images.githubusercontent.com/87138548/235498331-310f309f-11e6-4578-91a3-fab12a3cb427.png)
  * установли паарметр listen_host, чтобы можно было подключаться с других хостов
  * `<listen_host>::</listen_host>`
  * создали на  каждом шарде БД geo_data и таблицу geo_data.cell_towers по аналогии с п.2.
7. Создали таблицу geo_data.cell_towers_all с движком Distributed на каждом шарде, для выполнения распределенных запросов и загрузили данные из файла cell_towers.csv
на первый шард:
  * ![image](https://user-images.githubusercontent.com/87138548/235489995-f542c49f-1099-472b-bba2-09aac2c57814.png)
8. Выполнили запросы к табличке geo_data.cell_towers_all из п.3 на разных шардах (на 2ом и 3ем):
  * ![image](https://user-images.githubusercontent.com/87138548/235498830-921057b5-b2cd-434c-9547-d77f8e1859b7.png)
  * ![image](https://user-images.githubusercontent.com/87138548/235499011-ac81aa15-dc8e-4777-8f5b-9d4f435d881a.png)
 В первом случае запрос на шардированной таблице выполнился немного медленее - 0.043 против 0.032 на нешардированной таблице,
 во втором случае запрос на шардированной таблице выполнился значительно быстрее - 0.078 против 0.251 секунды.



