# spbiac_test

## Структура плейбука
```
.
├── ansible.cfg <--- настройки ansible
├── artifacts <--- результаты работы
│   ├── jenkins
│   │   └── jenkinsPassword <--- сгенерированный пароль для авторизации
│   └── ssh
│       └── app1.pub <--- ssh pub key от сервера приложений
├── group_vars
│   └── all 
├── hosts.txt <--- список хостов
├── initial.yml <--- плейбук для настройки ансибл пользователя на хостах вместо рута
├── main.yml <--- основной плейбук
├── README.md
├── requirements.yml <--- зависимости
└── roles
    ├── add_user <--- роль для создания пользователя
    │   ├── defaults
    │   │   └── main.yml
    │   └── tasks
    │       └── main.yml
    ├── cron_backup <--- роль создаёт crontab на сервере app для бекапа конфига nginx с web (rsync)
    │   ├── defaults
    │   │   └── main.yml
    │   └── tasks
    │       └── main.yml
    ├── gen_ssh_key <--- роль для генерации ssh ключа на хосте приложения (для беспарольного rsync'a)
    │   ├── defaults
    │   │   └── main.yml
    │   └── tasks
    │       └── main.yml
    ├── initial_user <--- роль создаёт ansible пользователя
    │   ├── defaults
    │   │   └── main.yml
    │   └── tasks
    │       └── main.yml
    ├── jenkins_on_tomcat <--- роль по установке tomcat и jenkins
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   │   ├── apache-tomcat-9.0.76.tar.gz
    │   │   └── jenkins.war
    │   ├── tasks
    │   │   ├── apache_tomcat.yml
    │   │   ├── jenkins.yml
    │   │   └── main.yml
    │   └── templates
    │       └── tomcat.service.j2
    └── nginx <--- роль по установке nginx
        ├── defaults
        │   └── main.yml
        ├── handlers
        │   └── main.yml
        ├── tasks
        │   └── main.yml
        └── templates
            └── jenkins_proxy.j2
```
## Использование

1) Установить зависимости
```
$ ansible-galaxy install -r requirements.yml
```

2) Задать ip адреса хостов в hosts.txt

3) (опционально) Задать в плейбуке initial.yml пользователя и пароль для серверов. Плейбук выполнит ssh-copy-id текущего пользователя и создаст нового пользователя на серверах, и также задаст беспарольное подключение для него (по умолчанию superuser).
```
$ ansible-playbook initial.yml
```
Или можно настроить подключение самостоятельно и указать пользователя для работы в group_vars/all

4) Запустить основной плейбук main.yml
```
$ ansible-playbook main.yml
```
После работы плейбука можно будет получить доступ к jenkins через web:
```
http://ip_адрес_веба/jenkins
```
Или напрямую:
```
http://ip_адрес_приложения:8080/jenkins
```

- Логин admin, сгенерированный пароль находится в папке проекта artifacts/jenkins/jenkinsPassword
- Бекапы происходят на сервере app. Папка по умолчанию /home/backup_collector/bak
