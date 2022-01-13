1. Системный вызов chdir:
```
chdir("/tmp")                           = 0
```

2. Файл "/usr/share/misc/magic.mgc":

3. Команда для очистки файла test23.txt, открытого в другой сессии (не смог добиться добавления принзана "(deleted)"):
```
vagrant@vagrant:~$ lsof -p 8162 | grep test23
lsof: WARNING: can't stat() tracefs file system /sys/kernel/debug/tracing
      Output information may be incomplete.
vi      8162 vagrant    4u   REG  253,0    12288 131088 /home/vagrant/.test23.txt.swp
```

# Скрипт очистки:
```
sudo cat /dev/null > /proc/8162/fd/4
```

4. Зомби процессы занимают ресурсы. Они ушли в бесконечное исполнение, но остановки приложения для высвобождения ресурсов не произошло.

5. Увидел много системных и питон вызовов
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libutil.so.1", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libm.so.6", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libexpat.so.1", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libz.so.1", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY) = 3
openat(AT_FDCWD, "/etc/localtime", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8", O_RDONLY|O_NONBLOCK|O_CLOEXEC|O_DIRECTORY) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/encodings/__pycache__/__init__.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/codecs.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/encodings", O_RDONLY|O_NONBLOCK|O_CLOEXEC|O_DIRECTORY) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/encodings/__pycache__/aliases.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/encodings/__pycache__/utf_8.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/encodings/__pycache__/latin_1.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/io.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/abc.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/site.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/os.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/stat.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/_collections_abc.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3
openat(AT_FDCWD, "/usr/lib/python3.8/__pycache__/posixpath.cpython-38.pyc", O_RDONLY|O_CLOEXEC) = 3

6. Системный вызов "/usr/lib/locale/locale-archive"
Part of the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.
```
apt install manpages-dev
man 2 uname
```

7. Еxit status первой команды не нулевой, поэтому результат разный:
command1 && command2 - command2 is executed if, and only if, command1 returns an exit status of zero.
command1; command2 - Команды исполняются последовательно, при этом результаты вызова command1 не влияет на вызов command2.

При использовании set -e разницы в результате использования "&&" и ";" нет, так как при не нулевом ответе происходит выход из скрипта.

#1 Замечание эксперта:
С параметром -e оболочка завершится только при ненулевом коде возврата простой команды. Если ошибочно завершится одна из команд, разделённых &&, то выхода из шелла не произойдёт. Так что, смысл есть.
В man это поведение описано:
The shell does not exit if the command that fails is . . . part of any command executed in a && or || list except the command following the final &&

8. set -euxo pipefail
-e: Программа/скрипт завершается при любом ошибочном испорнении команд, если скрипт выдал ошибку не внутри: while/until/if/AND/OR.
-u: Выдает ошибку в stderr при обращении к необъявленной переменной. Перечитал раз 20, не уверен, что понял правильно.
-x: Вывод результата исполнениях всех команд (или почти всех);
-o: Активирует опцию:
                pipefail - при завершении скрипта вернет статус последней ошибочной команды.

9. Чаще всего встречается статус "S".
что значат дополнительные к основной заглавной буквы статуса процессов - не понял вопрос. Если вопрос в дополнительном символе после буквы со статусом, то это доп. информация о процессе (приоритетность, мультипоточность и др.).