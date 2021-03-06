# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | выйдет сообщение с ошибкой, так как складываются 2 переменные разных типов int и str  |
| Как получить для переменной `c` значение 12?  | привести 'a' к типу str: c = str(a) + b  |
| Как получить для переменной `c` значение 3?  | привести 'b' к типу int: c = a + int(b)  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
import os

bash_command = ["cd c:/Users/KOT/devops-netology", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\KOT\AppData\Local\Programs\Python\Python39\python.exe C:/Users/KOT/script.py

README.md
TestFile1
TestFile3

Process finished with exit code 0
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
import os
import sys

cmd = os.getcwd()

if len(sys.argv)>=2:
    cmd = sys.argv[1]
bash_command = ["cd "+cmd, "git status 2>&1"]

print('\033[31m')
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('fatal') != -1:
        print('\033[31m Каталог \033[1m '+cmd+'\033[0m\033[31m не является GIT репозиторием\033[0m')
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified: ', '')
        prepare_result = prepare_result.replace(' ', '')
        print(cmd+':'+prepare_result)
print('\033[0m')
```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\KOT\AppData\Local\Programs\Python\Python39\python.exe C:/Users/KOT/script3.py

Каталог  C:\Users\KOT не является GIT репозиторием

Process finished with exit code 0
```
```
C:\Users\KOT\AppData\Local\Programs\Python\Python39\python.exe C:/Users/KOT/devops-netology/script3.py

C:\Users\KOT\devops-netology:README.md
C:\Users\KOT\devops-netology:TestFile1
C:\Users\KOT\devops-netology:TestFile3

Process finished with exit code 0
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
import socket as s
import time as t
import datetime as dt

# основные переменные
i = 1
serv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0

print('---------------------------------------- Скрипт запущен ----------------------------------------------------------')
print('Проверяемые сервисы:', serv)
print('------------------------------------------------------------------------------------------------------------------')

while 1==1 :
  for host in serv:
    ip = s.gethostbyname(host)
    if ip != serv[host]:
      if i==1 and init !=1:
        print(str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+serv[host]+' '+ip)
      serv[host] = ip
  i++1
  if i >= 3 :
    break
  t.sleep(2)
```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\KOT\AppData\Local\Programs\Python\Python39\python.exe C:/Users/KOT/script2.py

---------------------------------------- Скрипт запущен ----------------------------------------------------------
Проверяемые сервисы: {'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
------------------------------------------------------------------------------------------------------------------
2022-01-16 23:54:52 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 108.177.14.194
2022-01-16 23:54:52 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 216.58.209.197
2022-01-16 23:54:52 [ERROR] google.com IP mistmatch: 0.0.0.0 142.251.1.139
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 108.177.14.194 64.233.165.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 64.233.165.194 108.177.14.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 108.177.14.194 173.194.222.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 173.194.222.194 142.251.1.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 142.251.1.194 64.233.165.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 64.233.165.194 108.177.14.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 108.177.14.194 173.194.222.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 173.194.222.194 142.251.1.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 142.251.1.194 64.233.165.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 64.233.165.194 108.177.14.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 108.177.14.194 173.194.222.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 173.194.222.194 142.251.1.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 142.251.1.194 64.233.165.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 64.233.165.194 108.177.14.194
2022-01-16 23:54:55 [ERROR] drive.google.com IP mistmatch: 108.177.14.194 173.194.222.194

```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```
