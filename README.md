# Homework5
==============================

### 1. Домашнее заданиеНаписать сервис, который будет раз в 30 секунд мониторить лог на предмет наличия ключевого слова. Файл и слово должны задаваться в /etc/sysconfig

1. Cоздаём файл с конфигурацией для сервиса в директории /etc/sysconfig 

        nano /etc/sysconfig/watchlog
        
   _Содержимое:_
   
        # Configuration file for my watchdog service
        # Place it to /etc/sysconfig

        # File and word in that file that we will be monit
        WORD="ALERT"
        LOG=/var/log/watchlog.log
        
 2. Создаем /var/log/watchlog.log и пишем туда строки на своё усмотрение, плюс ключевое слово ‘ALERT’
 
         nano /var/log/watchlog.log
         
 3. Создадим скрипт /opt/watchlog.sh
 
         #!/bin/bash
         WORD=$1
         LOG=$2
         DATE=`date`

         if grep $WORD $LOG &> /dev/null
         then
             logger "$DATE: I found word, Master!"
         else
             exit 0
         fi
         
 4. Создаем юниты для сервиса в /usr/lib/systemd/system/
 
 _wachlog.service_
 
         [Unit]
         Description=My watchlog service

         [Service]
         Type=oneshot
         EnvironmentFile=/etc/sysconfig/watchlog
         ExecStart=/opt/watchlog.sh $WORD $LOG 
 
 _wacthlog.timer_
 
         [Unit]
         Description=Run watchlog script every 30 second

         [Timer]
         # Run every 30 second
         OnUnitActiveSec=30
         Unit=watchlog.service

         [Install]
         WantedBy=multi-user.target
 
 5. Стартуем timer
 
         systemctl start watchlog.timer
         
 6. Проверяем результат
 
         tail -f /var/log/messages
         
         
 ![Screenshot](1.png)
         
         
 
 
