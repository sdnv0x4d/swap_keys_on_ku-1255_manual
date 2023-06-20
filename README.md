
# Как свапнуть клавиши Fn/Ctrl на клавиатуре Lenovo Thinkpad KU-1255

Я пользуюсь ноутбуками ThinkPad на протяжении 3-х лет и мне не нравится расположение клавиш Fn/Ctrl (да, это не тру), их можно поменять местами через настройки BIOS.  
  
Но внешняя USB-клавиатура Thinkpad этого не позволяет, как свапнуть клавиши?  
Под катом подробно все расскажу.

----------

Кратко: нужно вручную поправить прошивку и залить ее на клавиатуру. Мне подсказывали, что сменить клавиши поможет хоткей Fn+Shift+Esc, но у меня это не работало.  
Решение было взято из этого [Issue](https://github.com/lentinj/tp-compact-keyboard/issues/32#issuecomment-687659110)  
  
Что нам понадобится?

-   ПК с Windows
    
-   HEX-редактор (у меня Notepad++)
    

Почти все действия я выполняю в Mobaxterm потому что zsh и мне так удобнее.

> Нет гарантий того, что ваш девайс останется в рабочем состоянии.

1.  Скачиваем прошивку для самой клавиатуры.  
    [https://download.lenovo.com/pccbbs/options/tp_compact_usb_kb_with_trackpoint_fw.exe](https://download.lenovo.com/pccbbs/options/tp_compact_usb_kb_with_trackpoint_fw.exe)
    

2.  Смотрим хеш-сумму скачанной прошивки и сравниваем ее с оригинальной. Если вывод пустой, то все нормально.
    

```
certutil.exe -hashfile tp_compact_usb_kb_with_trackpoint_fw.exe SHA256 | awk '{ if (NR == 2) print $1 }' > hash_downloaded && echo 7116a3819ee094857d21e4671cb6cf953d582372126f0f6728f6b2421eda7bd4 > hash_src && diff hash_downloaded hash_src
```

> Запускайте команду находясь в одной директории с файлом прошивки или указывайте абсолютный путь к ней
> 
> Вы можете использовать удобные вам GUI-инструменты или cmd/powershell

![Как это выглядело у меня](https://habrastorage.org/getpro/habr/upload_files/ec0/ecb/90f/ec0ecb90f889983573ec371399abed2e.png "Как это выглядело у меня")

3.  Открываем скачанный `.exe` файл прошивки через HEX-редактор, переходим к адресу 0x74004, меняем ba на f5 (Это заменяет Левый Ctrl на Fn)
    

> В Notepad++: Меню Плагины, HEX-Editor, View in HEX. Хоткей для перехода Ctrl+G и снять флаг "...to Line"

![Заменяем Левый Ctrl на Fn](https://habrastorage.org/getpro/habr/upload_files/bc2/a30/f6c/bc2a30f6c9a6bc981ea22431bfbe6d12.png "Заменяем Левый Ctrl на Fn")

4.  Затем переходим к адресу 0x740BA, меняем f5 на ba (Это заменяет Fn на Левый Ctrl)
    

![Заменяем Fn на Левый Ctrl](https://habrastorage.org/getpro/habr/upload_files/aec/afd/aaf/aecafdaaf70af02fed90033936ee000d.jpg "Заменяем Fn на Левый Ctrl")

5.  Сохраняем файл прошивки, проверяем хеш-сумму, которая должна получиться в итоге. Если вывод пустой, то мы все сделали правильно.
    

```
certutil.exe -hashfile tp_compact_usb_kb_with_trackpoint_fw.exe SHA256 | awk '{ if (NR == 2) print $1 }' >> hash_downloaded && echo 123143092dab578550c87a62526b07a6c5f06c047f2455be87971aa51577e300 >> hash_src && diff hash_downloaded hash_src
```

6.  Подключаем клавиатуру, запускаем exe-шник с прошивкой, жмем Start. Видим сообщение об успешном апдейте и переподключаем клавиатуру к ПК.
    

![](https://habrastorage.org/getpro/habr/upload_files/3b0/cd9/41f/3b0cd941f4866b393033e148af08eda7.jpg)

> Дайте права на исполнение программе, если вы скачивали ее через консоль

7.  Радуемся свапнутым клавишам. Т.к. мы заливали прошивку именно в саму клавиатуру, то этот мод будет работать на любой ОС и на любом устройстве, сам я пользуюсь этой клавой на Debian.
    

P.S. Для больших любителей хардварного DIY - один канадский леново-форумчанин запилил [модификацию дорожек на самой клавиатуре](https://forums.lenovo.com/t5/Displays-Options-and-Accessories/External-Thinkpad-Keyboard-KU-1255-switch-Fn-Ctrl-on-non-thinkpad-PC/m-p/2192639?page=3#3954879).
