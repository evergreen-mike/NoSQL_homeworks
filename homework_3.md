1. Созданы 3 виртуальные машины под сервер конфигурации из 3-ёх реплика-сетов, на которые установили mongodb
  * ![image](https://user-images.githubusercontent.com/87138548/233784601-50a7d911-1b47-481c-98f2-c810505963c5.png)
  * на каждой машине создали необходимые директории 
  `sudo mkdir /home/mongo && sudo mkdir /home/mongo/dbc && sudo chmod 777 /home/mongo/dbc`
  * и проинициализировали экземпляры с ключами --configsvr и --replSet
  `mongod --configsvr --dbpath /home/mongo/dbc --replSet RScfg --fork --logpath /home/mongo/dbc/dbc.log --pidfilepath /home/mongo/dbc/dbc.pid --port 27017 --bind_ip_all`
  * на одном из сервреов объединяем все три инстанса в один реплика-сет
  `rs.initiate({"_id" : "RScfg", configsvr: true, members : [{"_id" : 0, priority : 3, host : "10.129.0.30:27017"},{"_id" : 1, host : "10.129.0.26:27017"},{"_id" : 2, host : "10.129.0.3:27017"}]});`
2. 

