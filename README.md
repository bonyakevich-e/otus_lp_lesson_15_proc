### OTUS Linux Professional Lesson #15 | Subject: Управление процессами

#### ЦЕЛЬ: Работать с процессами

#### ЗАДАНИЕ: Написать свою реализацию ps ax используя анализ /proc

Вывод `ps ax` выглядит следующим образом:

![image](https://github.com/bonyakevich-e/otus_lp_lesson_15_proc/assets/114911797/0c1ee8d2-2f90-4229-ac9d-f91c8a6e4549)

То есть нам нужно с псевдофайловой системы /proc вытащить PID, TTY, статус, процессорное время и команду запуска процессов.

1. `PID`. Номер PID процесса в /proc это числовые имена каталогов
2. `TTY`. Утилита ps вытаскивает имя tty с файла /proc/<pid>/stat, значение tty_nr (7е значение). Здесь указано десятичное число (тип dev_t), которое ps преобразовывает в имя устройства tty. Делает он это с помощью макроса `linux/kdev_t.h`. Если быть точнее, то макрос преобразовывает число dev_t в номер major,minor устройства.
Это преобразование можно сделать вручную. В документации сказано:
```
tty_nr %d   (7) The controlling terminal of the process.  (The minor device number is contained in the combination of bits 31 to 20 and 7 to 0; the major device number is in bits 15 to 8.)
```
Возьмем, например, значение dev_t - 34823. Чтобы узнать номер устройства нужно перевести это число в двоичный 32х битный вид и воспользоваться документацией выше:

![image](https://github.com/bonyakevich-e/otus_lp_lesson_15_proc/assets/114911797/f64baa9a-570b-4cd9-90a7-472a2f2f7c0f)

Комбинация из желтых цветов это minor номер устройства = 7

Комбинация из зеленых цветов это major номер устройства = 136

То есть номер устройства (136,7). Дальше с помощью `ls -l /dev/pts/ /dev/tty*` мы можем посмотреть какое имя терминала соответствует этому номеру устройства (в нашем случае это /dev/pts/7)
