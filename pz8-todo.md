# pz8-todo

## Домашнее задание

1. HOWTO по логгированию на официальном [сайте](https://docs.python.org/3/howto/logging.html).
2. [Cтатья](https://github.com/LukeTonin/python-logging-a-practical-guide) с более подробным описанием того, что делали на пз.
3. [Отличная](https://habr.com/en/company/flant/blog/536698/) статья с примерами работы интерактивного rebase. Для особо пытливых еще один хороший [ресурс](https://www.git-tower.com/learn/git/first-aid-kit) с видеозаписями, где рассказывается, как бы что поправить?
4. [poetry](https://python-poetry.org/docs/)
5. [Ознакомиться](https://ru.wikipedia.org/wiki/.ini) с форматом ini-файлов.

**ДДЗ:**

1. Уточнение:  при использовании флага --edit должен открываться редактор (заданный в конфиге). В случае если, после редактирования тудушки пользователь сломает правильный формат, то ему должно вывестись соответствующее сообщение о том, что формат данных неверный. Для самых больших красавчиков при повторной попытке редактировать тудушку пользователю должно предлагаться открыть последнюю сохраненную версию или версию где он ошибся с форматом.

2. Уточнение: дата должна быть у каждой записи в тудушку. В случае если пользователь задаст некорректную дату необходимо выводить соответствующее сообщение. Для самых-самых в качестве ввода от пользователя необходимо уметь принимать дату в нечетком виде, например: вчера, сегодня, после завтра, вчера в 9 утра и др.

3. В консоль должны выводиться только логи, сообщающие об ошибке. Формат сообщения - просто текст.

   В файл должны записываться как ошибки, так и все дейтсвия пользователя - создал, удалил, изменил тудушку итд. Формат сообщения: время, уровень, модуль, номер строки, сообщение

   Также необходимо организовать ротацию логов по времени - каждые сутки должен быть новый файл с логами. Хранить нужно логи за последнюю неделю. (Подсказка: для реализации ротации в модуле logging уже предусмотрен соответствующий хэндлер). 

2. Реализовать установку тудушки с помощью  poetry.

## Рассматриваемые вопросы:

1. логирование
1. формат  ini
4. интерактивный rebase

## Ход работы:

1. Даже если что-то делаете в графическом интерфейсе: запускаете пайлинт, тесты, покрытие, гит, все что угодно. Это нужно уметь делать в консоли. Основные моменты прописывать в ридми. Вы никогда не знаете, как и в каком окружении кто-то другой будет запускать. Поэтому единственный универсальный способ - запуск через консоль.

1. Логгирование

   **Отличие от принтов:**

   * конфигурирование, фильтрация, больше информации (функция, где сработал лог, строка кода, содержание стека и др.)

   * разные уровни, вкл/выкл

	**Настройка логгирования**
	
	* Непосредственно в коде. Больше контроля, но чтобы настроить нужно править непосредственно код.
	* В конфиграционном файле в форматах: ini, json, yaml. Меньше контроля, нежели при настройке в коде, однако не нужно править непосредственно код, можно изменять в процессе функционирования программы (функция logging.config.listen). 

Перейдем к настройке логгера и посмотрим на основные отличия от принтов на практике.

Самый базовый логгер будет выглядеть следующим образом. Объект логгера и объект обработчика.

```python
def main():
    logger = logging.getLogger('todo_logger')
    handler = logging.StreamHandler(stream=sys.stdout)
    logger.addHandler(handler)
    logger.warning("Journal started")
```

Принцип работы следующий. Мы создали логгер с названием todo_logger. Далее мы сообщаем о необходиомсти вывода сообщения с помощью вызова  `logger.warning`. После вызова метода логгер создает сообщение, называемое LogRecord. Одна сам по себе логгер не может доставить сообщение. Для этого и нужен handler. Хэндлеры - объекты, которые берут сообщение, созданное логгером и доставляют его куда нужно - консоль, файл, удаленный сервер и тд.

**Сделаем коммит о наших успехах**. Ошибка в коммит сообщении? Да блин!

Для решения подобных проблем есть интерактивный git rebase. Супер мощный инструмент для того чтобы поправить все что угодно в гите.

Посмотрим историю коммитов:

```bash
git log
```

Нам нужен родительский коммит к коммиту, который мы хотим отредактировать.

```bash
git rebase -i <commit-id>
```

Откроется окно. Ищем нужный нам коммит, но ничего менять не нужно. Мы должны вместо pick написать reword - указать, что хотим сделать. После этого сохраняем файл. Откроется редактор, где уже необходимо изменить текст коммита. Успех, сообщение отредактировано.

**Вернемся к логгированию**. Есть несколько уровней логгирования. В примере выше использовался уровень warning. Помимо этого есть: DEBUG, INFO, WARNING, ERROR, CRITICAL.  В целом названия уровней логгирования говорят сами за себя, но подробнее узнаете в процессе изучения домашнего задания.

По умолчанию уровень логгирования - WARNING, то есть если в примере выше сделать logging.info("текст") ничего не будет выведено. А все, что выше по уровню, чем warning - будет выведено. Проверим на CRITICAL. 

Можно самому установить уровень логгирования с помощью метода setLevel.

```python
logger.setLevel(logging.DEBUG)
```

В процессе написания логгинга могут быть полезны вспомогательные функции.

Например, так мы можем посмотреть текущий уровень логгирования.

```python
print(f"Уровень логгирования: {logger.getEffectiveLevel()}")
```

Откатимся чуть-чуть назад. На самом деле необязательно создавать "свой" логгер. В модуле logging имеется логгер по умолчанию, нак называемый root logger.

```python
logging.warning("Journal started")
```

Что тут произойдет? Какие видим отличия?

Как уже было сказано, необязательно создавать свои хэндлеры и свой логгер. Если не найдено хэндлера, то будет вызван дефолтныйы хэндлер, а также сообщение лога будет отформатировано по умолчанию.

**Совет дня:** Забудьте о принтах. Не нужно пугаться логгирования и думать, что вот когда программа станет побольше тогда и подумаю о логгировании, а пока буду пользоваться принтами.  Также легко, как пользоваться print, можно сразу начинать пользоваться logging.

А что насчет форматирования? Мы увидели, что вывод дефолтного логгера и нашего отличаются.

Сообщения логов можно форматировать удобным для вас образом. Для многих случаев важно время появления сообщения. 

Есть еще одна важная сущность в логгировании - **форматеры** (formatter).

```python
logger = logging.getLogger('todo_logger')

handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)

logger.setLevel(logging.DEBUG)
logger.critical("Journal started")
```

Проверим, что изменилось.

**Закомитим изменения.** Закомитил тестовый файл, который не нужен в репе... да б! Что можно сделать? Как обычно посутпаете в таком случае?

Воспользуемся еще одной возможностью интерактивного rebase. 

Удалим по ошибке закомиченный файл. 

```bash
git status
```

Добавим нужные файлы

```bash
git add src/main.py 
```

Сделаем почти обычный коммит

```bash
git commit --fixup <id коммита с ошибками>
```

После этого выполним  посмотрим в чем особенность сделанного коммита

```bash
git log
```

Переходим к интерактивному rebase и снова нужен коммит, предшествующий плохому.

```bash
git rebase -i <commit-id> --autosquash
```

Откроется редактор, но более делать ничего не нужно. Просто сохраняем и все. 

**Снова к логгированию**

С увеличением программы какие-то логи определенного уровня хочется писать в файл, а какие-то в консоль. Да и в целом не хочется каждый раз править код,, можно легко ошибиться, когда логгеров станет больше двух. 

Выше было сказано про разные форматы файлов для конфигурирования логов. Сегодня познакомимся с еще одним форматом, для конфигурирования - ini-файлы.

```ini
[loggers]
keys=root,todo

[handlers]
keys=consoleHandler,fileHandler

[formatters]
keys=consoleFormatter,fileFormatter

[handler_consoleHandler]
class=logging.StreamHandler
formatter=consoleFormatter
args=()

[handler_fileHandler]
class=logging.FileHandler
formatter=fileFormatter
args=("logs/project.log","a", "utf-8")

[formatter_fileFormatter]
class=logging.Formatter
format=%(asctime)s - [%(module)s:%(levelname)s] - %(message)s
datefmt=%d-%m-%y %H:%M:%S

[formatter_consoleFormatter]
class=logging.Formatter
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s

[logger_todo]
level=DEBUG
handlers=fileHandler
qualname=todo

[logger_root]
level=DEBUG
handlers=consoleHandler
```

И код в main.py:

```python
logging.config.fileConfig("logging.ini")
logger = logging.getLogger("todo")
logger.debug("test")
```

Попробуем сделать одинаковые хэндлеры для разных логгеров. Сделаем обоим consoleHandler.

Увидим дублирование записей. Почему так? На самом деле логгеры устроены иерархически. Более подробно об этом в статьях дома. А сейчас важно запомнить, что все логгеры иерархически наследуются от root. Таким образом все сообщения, приходящие в дочерние логгеры, в нашем случае в логгер - todo, также передаются хэндлеру  root.

Чтобы избежать этого эффекта необходимо передать параметр

```ini
[logger_todo]
level=DEBUG
handlers=consoleHandler
propagate=0
qualname=todo
```

Чтобы отключить логгер:

```python
logger.disabled = True
```

