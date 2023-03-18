# Домашнее задание по лекции "Командная оболочка Bash: практические навыки"

1. Есть скрипт:

```
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c, d, e будут присвоены? Почему?

c - строка 'a+b' так как a+b конкатенация строк, а не арифметическое сложение
d - строка '1+2',  так как  $ перед переменной в bash воспринимается как строка
e - 3, так как оператор $(()) в bash позволяет выполнять арифметические вычисления

2. На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:

``` while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done 
```

Добавить закрывающую скобку после условия в цикле while и break после записи даты, для завершения выполнения при условии доступности сервиса.

```
while ((1==1))
do
    curl https://localhost:4757
    if (($? != 0))
    then
        date >> curl.log
    else 
        break
    fi
done
```

3. Необходимо написать скрипт, который проверяет доступность трёх IP: 192.168.0.1, 173.194.222.113, 87.250.250.242 по 80 порту и записывает результат в файл log. Проверять доступность необходимо пять раз для каждого узла.

```
#!/bin/bash

ip_addresses=("192.168.0.1" "173.194.222.113" "87.250.250.242")
i_n=5

for ip in "${ip_addresses[@]}"
do
    for ((i=1; i<=i_n; i++))
    do
        if nc -w 2 -zv "$ip" 80 &> /dev/null
        then
            echo "$(date): $ip:80 доступен" >> ip.log
        else
            echo "$(date): $ip:80 не доступен" >> ip.log
        fi
    done
done
```

4. Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен — IP этого узла пишется в файл error, скрипт прерывается.

```
#!/bin/bash


ip_addresses=("192.168.0.1" "173.194.222.113" "87.250.250.242")

while :
do
    for ip in "${ip_addresses[@]}"
    do
        if nc -w 2 -zv "$ip" 80 &> /dev/null
        then
            echo "$(date): $ip:80 доступен" >> ip.log
        else
            echo "$(date): $ip:80 не доступен" >> ip.log
            echo "$ip" >> error.txt
            break
        fi
    done
done
```