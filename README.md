# rustsh_infra
rustsh Infra repository

## Домашнее задание № 5 (знакомство с облачной инфраструктурой)

### Подключение к удалённой машине в одну строку
```
ssh -At rustsh@35.210.23.56 ssh 10.132.0.3
```
где ключ `-t` означает подключение псевдотерминала.

### Дополнительное задание: подключение из консоли при помощи команды вида `ssh someinternalhost` 
Для подключения при помощи команды вида `ssh someinternalhost` из локальной консоли рабочего устройства, чтобы подключение выполнялось по алиасу `someinternalhost`, необходимо создать файл `~/.ssh.config` и добавить туда следующий текст:
```
Host bastion
	Hostname 35.210.23.56
	User rustsh
	
Host someinternalhost
	ProxyCommand ssh bastion -W 10.132.0.3:22
	User rustsh
```
где ключ `-W` означает создание туннеля.

### Данные для подключения
bastion_IP = 35.210.23.56
someinternalhost_IP = 10.132.0.3

## Домашнее задание № 6 (деплой тестового приложения)

### Данные для подключения:
testapp_IP = 35.204.17.203

testapp_port = 9292


### Выполнение стартового скрипта
Для запуска скрипта `startup.sh` при создании ВМ необходимо в команду gcloud добавить ключ `--metadata-from-file startup-script=./startup.sh`
Сама команда примет такой вид:
```
gcloud compute instances create reddit-app\
  --boot-disk-size=10GB \
  --image-family ubuntu-1604-lts \
  --image-project=ubuntu-os-cloud \
  --machine-type=g1-small \
  --tags puma-server \
  --restart-on-failure \
  --metadata-from-file startup-script=./startup.sh
```
В результате применения данной команды мы получим инстанс с уже запущенным приложением.

### Добавление правила файервола
Для создания правила файервола из консоли gcloud необходимо выполнить следующую команду:
```
gcloud compute firewall-rules create default-puma-server \
  --allow tcp:9292 \
  --target-tags=puma-server
```