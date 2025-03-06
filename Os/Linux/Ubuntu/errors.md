# Errors manuals
### Ошибки / Errors
1. Ошибки при подключении / Connection errors
 - При подключении по SSH отображается только знак $ / When connecting via SSH, only the $ sign is displayed
   ```bash
   sudo usermod -s /bin/bash <username>
   ```
2. Ошибка «Permission denied»
   - Изменение прав в терминале
      1. Проверяем права доступа к содержимому каталога, набрав «ls -l»
      2. Набираем команду «sudo chmod 755 filename». С ее помощью мы от имени администратора системы даем разрешение на чтение документа себе и любому другому пользователю. Проверяем результат выполнения и убеждаемся, что права доступа изменились нужным образом.
         ```bash
         sudo chmod 755 filename
         ```
