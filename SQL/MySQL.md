## MariaDB

Если появилась необходимость посмотреть какие запросы к БД выполняет программа, можно включить логирование запросов, не залезая в исходный код самой программы.

Рассмотрим два способа сохранения логов в файл и таблицу базы:
- Установите глобальную переменную способа сохранения логов в режим 'TABLE'
  
  ```shell
  SET GLOBAL log_output = 'TABLE';
  Execute SET GLOBAL general_log = 'ON';
  ```
  По умолчанию устанавливается таблица mysql.general_log
- Для сохранения в файлы компьютера (сервера) установите 'FILE', она была установлена по умолчанию

  ```shell
  SET GLOBAL log_output = "FILE"; the default.
  SET GLOBAL general_log_file = "/path/to/your/logfile.log";
  SET GLOBAL general_log = 'ON';
  ```

Чтобы посмотреть какой изначально установлен путь сохранения файла, введите:
```shell
SELECT @@general_log_file
```
