# portable_programmer

Устройство представляет из себя портативный программатор (а точнее - его макет, собранный из модулей), который хранит прошивки для записи и считанные данные с программируемых микросхем памяти на SD карте.
Я называю устройство макетом, так как оно не является в полной мере автономным, не имеет схему зарядки и питания от аккумулятора, а также потому что собрано из готовых модулей на соединительной плате, что обуславливает довольно большие размеры конечного устройства.

![view](https://github.com/AndrejChoo/portable_programmer/blob/main/hardware/Board_Up.jpg)

Как видно, устройство собрано на многим известной плате Black pill с STM32F411CEU на борту. Она имеет достаточно большие объёмы Flash и SRAM (что достаточно критично при создании устройств с графическим, даже текстовым интерфейсом), имеет интерфейс SDIO (что очень сильно облегчает работу с чипом при использовании CubeMX и увеличивает скорость чтения/записи SD карты), а также может программироваться через встроенный USB разъём, так как имеет USB-bootloader.

В качестве дисплея использован готовый недорогой модуль разрешением 176х220 точек на базе ili9225, управляемый по SPI. При желании можно переписать код на внешне идентичный дисплей на базе ST7735, только нужно будет добавить библиотеку работы с данным дисплеем.

Разъём MicroSD карты распаян на соединительной плате (см. фото нижней стороны платы).

Графический интерфейс реализован в виде текстового меню. Управление устройства реализовано с помощью пяти кнопок (Up,Down,Left,Right,Set). 

Общение с внешней средой (тоесть с программируемыми чипами) осуществляется с помощью разъёма 7х2 пин, на который выведено питание, SPI4 (mosi,miso,sck), I2C1 (scl, sda) и несколько GPIO. 

На данный момент реализована и проверена работа со: SPI Flash 25хх (по 25хх64 включительно, работу 25хх128 и 25хх256 добавлю в процессе отладки); I2C EEPROM 24хх; Microwire EEPROM 93xx серии.

Для работы с файлами полноценного файлового менеджера устройство не имеет, имена файлов выводятся по очереди в одну строку. Используются файлы только корневого католога флешки. Включена поддержка длинных имён файлов, но не рекомендую использовать слишком длинные имена, так как используется только полторы строки дисплея для отображения имён.
При чтении прошивки в файл можно изменять название файла из 8 символов с помощью кнопок устройства.

Для программирования можно использовать только бинарные файлы (расширение не имеет значения). Если попытаться записать HEX файл, то просто запишется текст файла в виде последовательности байт (букв). Встроенного HEX2BIN конвертера нет, поэтому HEX файлы необходимо сконвертировать заранее.

Подключение внешних микросхем памати к разъёму следующее: SPI Flash DO->miso, DI->mosi, CLK->sck, CS->PB12; I2C EEPROM SCL->scl, SDA->sda; Microwire EEPROM DO->PB1, DI->PB0, CLK->PB2, CS->PA2.

В ближайшее время добавлю поддержку программирования микроконтроллеров avr8.
