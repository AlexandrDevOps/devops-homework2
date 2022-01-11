# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"

## Обязательная задача 1

Есть скрипт:
```bash
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная  | Значение | Обоснование |
| ------------- | ------------- | ------------- |
| `c`  | a+b  | переменная задана в виде текста |
| `d`  | 1+2  | знак $ позволяет получить значение переменной, но не распространяется на операцию сложения поэтому присваивается такое значение |
| `e`  | 3    | знак $ с помощью скобок распространяется на все выражение и позволяет получить значения переменных и выполнить сложение |


## Обязательная задача 2
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
```bash
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```
в примере нехватает одной скобки в выражении "( ( 1==1)", ещё необходимо дополнить таймаут проверки и выход из цикла:  
```bash
 while (( 1 == 1 ))  
 do  
    curl https://localhost:4757  
    if (($? != 0))  
    then  
      date >> curl.log  
    else exit  
    fi  
    sleep 10  
 done
```
Необходимо написать скрипт, который проверяет доступность трёх IP: `192.168.0.1`, `173.194.222.113`, `87.250.250.242` по `80` порту и записывает результат в файл `log`. Проверять доступность необходимо пять раз для каждого узла.

### Ваш скрипт:
```bash
    hosts=(192.168.0.1 173.194.222.113 87.250.250.24)  
    timeout=10  
    for i in {1..5}  
    do  
      date >>hosts.log  
        for h in ${hosts[@]}  
        do  
	  curl -Is --connect-timeout $timeout $h:80 >/dev/null  
          echo "    check" $h status=$? >>hosts.log  
        done  
    done 
```

## Обязательная задача 3
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

### Ваш скрипт:
```bash
    hosts=(192.168.0.1 173.194.222.113 87.250.250.24)
    timeout=10
    res=0
    while (($res == 0))
    do
        for h in ${hosts[@]}
        do
	  curl -Is --connect-timeout $timeout $h:80 >/dev/null
	  res=$?
	  if (($res != 0))
	  then
	    echo "    ERROR on " $h status=$res >>hosts2.log
	  fi
        done
    done
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: \[04-script-01-bash\] сломал хук.

### Ваш скрипт:
```bash
???
```
