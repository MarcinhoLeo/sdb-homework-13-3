# Домашнее задание к занятию 13.3 "Защита сети" - Леонардо Гунба

### Задание 1.

Проведите разведку системы и определите, какие сетевые службы запущены на "защищаемой" системе:

**sudo nmap -sA < ip-адрес >**

**sudo nmap -sT < ip-адрес >**

**sudo nmap -sS < ip-адрес >**

**sudo nmap -sV < ip-адрес >**

(По желанию можете поэкспериментировать с опциями: https://nmap.org/man/ru/man-briefoptions.html )


*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*


Эти сработали - MSSQL, PostgreSQL, VNC Scan 5800-5820

**sudo nmap -sT < ip-адрес >**

**sudo nmap -sS < ip-адрес >**

**sudo nmap -sV < ip-адрес >**

```
02/17/2023-22:22:18.801265  [**] [1:2002911:6] ET SCAN Potential VNC Scan 5900-5920 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.211.55.43:43922 -> 10.211.55.44:5903
02/17/2023-22:22:18.805211  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.211.55.43:35980 -> 10.211.55.44:5815
02/17/2023-22:22:18.807588  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.211.55.43:43098 -> 10.211.55.44:1433
02/17/2023-22:22:18.808191  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.211.55.43:37642 -> 10.211.55.44:5432
02/17/2023-22:22:18.807587  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.211.55.43:43098 -> 10.211.55.44:1433
02/17/2023-22:22:18.808191  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.211.55.43:37642 -> 10.211.55.44:5432
```


### Задание 2.

Проведите атаку на подбор пароля для службы SSH:

**hydra -L users.txt -P pass.txt < ip-адрес > ssh**

1. Настройка **hydra**: 
 
 - создайте два файла: **users.txt** и **pass.txt**;
 - в каждой строчке первого файла должны быть имена пользователей, второго - пароли (в нашем случае это могут быть случайные строки, но ради эксперимента можете добавить имя и пароль существующего пользователя).

Дополнительная информация по **hydra**: https://kali.tools/?p=1847

2. Включение защиты SSH для Fail2Ban:

-  Открыть файл /etc/fail2ban/jail.conf;
-  Найти секцию **ssh**;
-  Установить **enabled**  в **true**.

Дополнительная информация по **Fail2Ban**:https://putty.org.ru/articles/fail2ban-ssh.html

поменять в конфиге /etc/fail2ban/jail.conf параметр "maxretry = 2"

```
root@ubuntu-linux-22-04-desktop:~# tail -f /var/log/fail2ban.log 
2023-02-17 20:46:54,313 fail2ban.jail           [24942]: INFO    Jail 'sshd' uses pyinotify {}
2023-02-17 20:46:54,317 fail2ban.jail           [24942]: INFO    Initiated 'pyinotify' backend
2023-02-17 20:46:54,318 fail2ban.filter         [24942]: INFO      maxLines: 1
2023-02-17 20:46:54,329 fail2ban.filter         [24942]: INFO      maxRetry: 2
2023-02-17 20:46:54,329 fail2ban.filter         [24942]: INFO      findtime: 600
2023-02-17 20:46:54,329 fail2ban.actions        [24942]: INFO      banTime: 600
2023-02-17 20:46:54,329 fail2ban.filter         [24942]: INFO      encoding: UTF-8
2023-02-17 20:46:54,329 fail2ban.filter         [24942]: INFO    Added logfile: '/var/log/auth.log' (pos = 16385, hash = 2f4fdbd25b02b44d93420e8b984c1728c8547611)
2023-02-17 20:46:54,330 fail2ban.jail           [24942]: INFO    Jail 'sshd' started
2023-02-17 20:47:11,784 fail2ban.filter         [24942]: INFO    [sshd] Found 10.211.55.43 - 2023-02-17 20:47:11
2023-02-17 20:51:34,307 fail2ban.filter         [24942]: INFO    [sshd] Found 10.211.55.43 - 2023-02-17 20:51:34
2023-02-17 20:51:34,596 fail2ban.filter         [24942]: INFO    [sshd] Found 10.211.55.43 - 2023-02-17 20:51:34
2023-02-17 20:51:34,611 fail2ban.filter         [24942]: INFO    [sshd] Found 10.211.55.43 - 2023-02-17 20:51:34
2023-02-17 20:51:34,612 fail2ban.actions        [24942]: NOTICE  [sshd] Ban 10.211.55.43
```

*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban*