5. По умолчанию выделено: 1Гб ОЗУ, 2 ядра

6. Можно добавить ресурсов, добавив строки:
'''
  v.memory = 2048
  v.cpus = 4
'''

8. Задать длину журнала можно переменной HISTSIZE. Описано в 595 строке
ignoreboth не записывает в историю команды, начинающиеся с пробела и дублируещиеся команды (ранее сохраненные)

9. Понял так, что нужен при вызове нескольких команд через && и ||. При этом выполняются команды в текущей среде.
Так же можно объявить список переменных.
Описано в строке 197.

10. Можно с помощью скрита:
'''
touch {1..100000}
'''

300000 не получается из-за ограничений данной конструкции.
-bash: /usr/bin/touch: Argument list too long

11. Возвращает 1, если папка /tmp существует.

12. Через команды:
'''
cd /tmp
mkdir new_path_directory
cp /usr/bin/bash bash
export "PATH=/tmp/new_path_directory:$PATH"
'''

13. 
at запускает команды в определенное вермя
batch запускает команды при определенных условиях, к примеру при низкой загрузке.