---
layout: post
title: "Модуль 5 MalDev Academy на русском"
---

**Модуль 5 MalDev Academy на русском**

Антивирусы и другие утилиты используют несколько тактик и техник для обнаружения вирусов, в данном модуле мы рассмотрим такие тактики и техники и даже научимся обходить часть из них. 
Сразу скажи данный модуль мне понравился, однако я решил добавить примеры и немного расширить сказанное автором курса. 

![]({{ site.baseurl }}/images/m36.jpg)

***Сигнатуры и статический анализ***

`Сигнатура` — это количество байт или строк в вредоносе, которое однозначно идентифицирует его. Еще могут быть указаны имена переменных и импортируемые функции. Во время сканирования антивирус или другая утилита пытается сопоставить программу со списком известных правил. Эти правила составляют базу знаний антивирусного по. 
Например, если шеллкод начинается с  `FC 48 83 E4 F0 E8 C0 00 00 00 41 51 41 50 52 51`, то это указывает на сигнатуру msfvenom.

`Обнаружение хеша` —  Это очень простой метод, и самый быстрый и легкий способ, которым антивирус может обнаружить вредоносное ПО. Проверка осуществляется путем простого сохранения хешей (например, MD5, SHA256) известного вредоносного ПО в базе данных. Хеш файла вредоносного ПО будет сравниваться с базой данных, на положительный результат. Уклонение от обнаружения по хешу чрезвычайно просто, хотя, скорее всего, этого недостаточно само по себе. Изменив хотя бы 1 байт в файле, хеш файла изменится для любого алгоритма хеширования, и, следовательно, файл будет иметь хеш файла, который, скорее всего, будет уникальным.

Пример:

Представьте, что у вас есть вредоносный файл evil.exe.  
Антивирус сохранил его SHA256 хеш в своей базе данных.  Когда вы запускаете evil.exe, антивирус вычисляет его SHA256 хеш и сравнивает с базой данных. Если хеши совпадают, антивирус блокирует запуск.

Как обойти:

Достаточно изменить всего один байт в файле evil.exe (например, изменить один символ в комментарии или добавить лишний пробел).  После этого SHA256 хеш файла полностью изменится.  Теперь, даже если файл делает абсолютно то же самое, антивирус не сможет обнаружить его по хешу, потому что хеш в базе данных и хеш измененного файла не совпадают.  Именно поэтому изменение даже одного байта эффективно "ломает" обнаружение по хешу.

***Эврестический анализ***

Поскольку методы обнаружения по сигнатурам легко обойти, внеся незначительные изменения во вредоносный файл, было введено эвристическое обнаружение для выявления подозрительных характеристик, которые могут быть обнаружены в неизвестных, новых и измененных версиях существующего вредоносного ПО. В зависимости от решения безопасности, эвристические модели могут состоять из одного или обоих следующих компонентов:

 - Статический эвристический анализ:

Он включает в себя декомпиляцию подозрительной программы и сравнение фрагментов кода с известным вредоносным ПО, которое уже известно и находится в эвристической базе данных. Если определенный процент исходного кода совпадает с чем-либо в эвристической базе данных, программа помечается.   

Пример: Представьте себе вредоносную программу, которая шифрует файлы пользователя. Статический анализ может выявить, что 80% ее кода совпадает с кодом известного шифровальщика. Даже если имя файла и некоторые другие детали изменены, высокий процент совпадения заставит антивирус пометить файл как подозрительный.
 
 - Динамический эвристический анализ:

Программа помещается в виртуальную среду или песочницу, которая затем анализируется решением безопасности на предмет любого подозрительного поведения.

Пример: Антивирус запускает подозрительный файл в изолированной среде. Он наблюдает за тем, что программа пытается подключиться к неизвестному серверу в интернете, пытается изменить системные файлы или получить доступ к конфиденциальным данным. Даже если код программы не похож на известные вредоносные программы, подозрительное поведение заставит антивирус сгенерировать предупреждение.

![]({{ site.baseurl }}/images/m37.jpg)

***Поведенческий анализ***

После запуска вредоносного ПО, антивирус продолжают отслеживать подозрительное поведение запущенного процесса. Оно будет искать подозрительные индикаторы, такие как загрузка DLL, вызов определенных API Windows и подключение к интернету. Как только подозрительное поведение обнаружено, антивирус проводит сканирование памяти запущенного процесса. Если процесс признан вредоносным, он завершается.

Некоторые действия могут привести к немедленному завершению процесса без сканирования памяти. 
Например, если вредоносное ПО выполняет внедрение процесса в notepad.exe и подключается к интернету, это, скорее всего, приведет к немедленному завершению процесса из-за высокой вероятности вредоносной активности.

Лучший способ избежать обнаружения на основе поведения — сделать поведение процесса максимально безопасным (например, избегать порождения дочернего процесса cmd.exe). Кроме того, сканирование памяти можно обойти с помощью шифрования памяти. Это более сложная тема, которая будет рассмотрена в будущих модулях.

Пример:

Представьте себе, что вы — антивирусная программа. Вы наблюдаете за процессом malware.exe.

 - Запуск: Пользователь запускает malware.exe.

 - Подозрительное поведение: malware.exe пытается загрузить DLL под названием evil.dll.  Это первый подозрительный признак.  Большинство обычных программ не загружают DLL со случайными именами.

 - Дальнейшие действия: malware.exe начинает вызывать API Windows, которые обычно используются для получения доступа к конфиденциальным данным системы.  Это второй подозрительный признак. - 

 - Подключение к интернету:  malware.exe пытается установить соединение с IP-адресом, который известен как сервер управления вредоносным ПО. Это третий и самый серьёзный признак.

 - Реакция:  Из-за совокупности этих подозрительных действий (загрузка подозрительной DLL, вызовы чувствительных API, подключение к известному вредоносному серверу), антивирусная программа считает malware.exe вредоносным и немедленно завершает его работу.  В данном случае, из-за очевидности вредоносных действий, сканирование памяти может быть даже не выполнено - процесс сразу же блокируется.

***API хукинг***

API hooking  — это техника, используемая решениями по безопасности, в основном EDR (Endpoint Detection and Response), для мониторинга выполнения процессов или кода в реальном времени на предмет вредоносного поведения. API hooking работает путем перехвата часто используемых API и последующего анализа параметров этих API в реальном времени. Это мощный способ обнаружения, поскольку он позволяет решению по безопасности видеть содержимое, передаваемое API, после его расшифровки или деобфускации. Это обнаружение считается комбинацией обнаружения в реальном времени и на основе поведения.

Приведенная ниже диаграмма показывает высокий уровень API hooking.
![]({{ site.baseurl }}/images/m38.jpg)

Существует несколько способов обхода API hooks, таких как DLL unhooking (отключение перехвата DLL) и прямые системные вызовы (syscalls). Эти темы будут рассмотрены в будущих модулях.

Практический пример:

Представьте себе, что вы — EDR, следящий за процессом malware.exe.  malware.exe пытается украсть пароли, хранящиеся в браузере.  Для этого ему нужно получить доступ к памяти, где хранятся эти пароли.  Браузер, как и большинство программ, использует для этого API Windows, например, ReadProcessMemory.

Обычный вызов API:  malware.exe вызывает ReadProcessMemory, передавая ему адрес памяти, где хранятся пароли, и другие необходимые параметры.

API Hooking:  EDR установил хук (перехватчик) на API ReadProcessMemory.  Когда malware.exe вызывает этот API, управление передается не напрямую Windows, а сначала EDR.

Анализ: EDR анализирует параметры, переданные ReadProcessMemory.  Он видит, что malware.exe пытается получить доступ к области памяти, где хранятся пароли браузера.  Это подозрительное поведение.

Реакция: EDR может заблокировать вызов ReadProcessMemory, предотвратив кражу паролей.  Он также может зарегистрировать это событие для дальнейшего анализа и классифицировать malware.exe как вредоносное ПО.

Визуализация (упрощенно):
```
[malware.exe] --> [API Hook (EDR)] --> [ReadProcessMemory (Windows)]
                                     ^
                                     |
                                     [Анализ параметров]
                                     |
                                     [Решение: Заблокировать/Разрешить]
```

***Чекинг IAT***

Одним из компонентов, который обсуждался в структуре PE (Portable Executable), является таблица адресов импорта (Import Address Table или IAT).  Вкратце, IAT содержит имена функций, которые используются в PE во время выполнения. Она также содержит библиотеки (DLL), которые экспортируют эти функции. Эта информация ценна для решения по безопасности, поскольку оно знает, какие WinAPI использует исполняемый файл.   

Например, программа-вымогатель используется для шифрования файлов и, следовательно, скорее всего, будет использовать криптографические функции и функции управления файлами. Когда решение по безопасности видит IAT, содержащую функции этих типов, такие как `CreateFileA/W`, `SetFilePointer`, `Read/WriteFile`, `CryptCreateHash`, `CryptHashData`, `CryptGetHashParam`, программа либо помечается, либо подвергается дополнительному изучению. Изображение ниже показывает использование инструмента dumpbin.exe для проверки IAT двоичного файла.

![]({{ site.baseurl }}/images/m39.jpg)

Одним из решений, позволяющих избежать сканирования IAT, является использование хэширования API, которое будет обсуждаться в будущих модулях.

Практический пример:

Представьте себе, что вы — антивирусная программа, анализирующая файл ransomware.exe. Вы хотите определить, является ли этот файл вредоносным. Один из способов — изучить его IAT.

 - Анализ IAT: Вы открываете ransomware.exe с помощью такого инструмента, как dumpbin.exe, и изучаете его IAT.

 - Подозрительные функции: Вы замечаете, что IAT содержит следующие функции:

1. `CreateFileA/W`: Используется для создания или открытия файлов.
2. `SetFilePointer`: Используется для перемещения указателя внутри файла.
3. `Read/WriteFile`: Используются для чтения и записи данных в файлы.
4. `CryptCreateHash`, `CryptHashData`, `CryptGetHashParam`: Криптографические функции, используемые для создания хэшей.
   
 - Интерпретация: Наличие этих функций в IAT наводит на мысль, что программа может выполнять операции с файлами и использовать криптографию.  Это подозрительно, особенно в сочетании.  Ведь именно такие функции нужны для шифрования файлов, что характерно для программ-вымогателей.

 - Реакция:  На основании анализа IAT, антивирусная программа может пометить ransomware.exe как потенциально вредоносный и предпринять дальнейшие действия, например, запустить его в песочнице для более детального анализа или заблокировать его выполнение.

Почему анализ IAT эффективен:

Видно намерения: IAT показывает, какие функции программа собирается использовать. Это даёт представление о её намерениях. Если программа импортирует криптографические функции и функции для работы с файлами, это может указывать на то, что она планирует шифровать файлы.
Относительно просто: Анализ IAT — относительно простой и быстрый способ получить информацию о программе.
Как вредоносное ПО может попытаться обойти анализ IAT (кратко):

API Hashing: Вместо хранения имён функций в IAT, вредоносное ПО может хранить их хэши. Это затрудняет анализ, поскольку антивирусная программа не видит имён функций напрямую. Хэши нужно будет "развернуть" (что может быть сложно или невозможно без ключа), чтобы понять, какие API используются. Именно поэтому в переводе упоминается, что API Hashing будет рассмотрено отдельно.
Динамическая загрузка: Вредоносное ПО может загружать необходимые DLL и функции динамически во время выполнения, а не указывать их в IAT. В таком случае, IAT не будет содержать подозрительных функций. Это усложняет обнаружение, поскольку антивирусная программа не видит этих функций при статическом анализе файла.

***Ручной анализ***

Несмотря на обход всех вышеупомянутых механизмов обнаружения, команды защиты (blue team) и аналитики вредоносного ПО по-прежнему могут вручную анализировать вредоносное ПО. Защитник, хорошо разбирающийся в реверс-инжиниринге вредоносного ПО, скорее всего, сможет обнаружить вредоносное ПО. Кроме того, решения по безопасности часто отправляют копию подозрительных файлов в облако для дальнейшего анализа.

Разработчики вредоносного ПО могут применять методы противодействия реверс-инжинирингу, чтобы усложнить процесс обратной разработки. Некоторые методы включают обнаружение отладчика и обнаружение виртуализированной среды, которые обсуждаются в будущих модулях.

Пример:

Представим ситуацию, когда реверс-инженеру попался стилер, защищенный VMProtect. Задача — идентифицировать вредоносные функции и понять, как именно стилер собирает информацию.

Последовательность действий:

 - Идентификация защиты:
Первым делом запускаем DIE, чтобы определить, чем именно защищен стилер. DIE покажет, что используется VMProtect. 

Теперь загружаем стилер в IDA Pro.

Так как VMProtect скрывает реальный код, заменяя его на виртуальный. В IDA нужно найти места, где происходит переход на виртуализированный код. Обычно это можно определить по вызовам специальных функций VMProtect.

IDA может помочь с девиртуализацией, но это сложный и трудоемкий процесс, он требует много часов работы, есть так же различные утилиты, которые могут помочь со снятием защиты. 

После девиртуализации изучаем таблицу импорта (IAT). Скорее всего, там будут присутствовать API, связанные с работой с файлами (например, CreateFile, ReadFile, WriteFile), сетью (например, socket, connect, send, recv) и криптографией (если стилер шифрует украденные данные).

 - Динамический анализ (x64dbg):
Загружаем стилер в x64dbg.

Точки остановки: Ставим точки остановки на подозрительные API, например работа с сетью или внедерние шеллкода

Запускаем стилер и наблюдаем за его поведением. Когда программа достигает точки остановки, мы можем увидеть, какие параметры передаются API, и понять, какие действия выполняет стилер.

С помощью x64dbg можно перехватить сетевой трафик, чтобы увидеть, какие данные стилер отправляет на сервер злоумышленника.



***Домашнее задание***

![]({{ site.baseurl }}/images/m40.jpg)

Задание:


 - Найдите на GitHub репозиторий с исходным кодом стиллера.

 - Скомпилируйте исходный код стиллера, чтобы получить исполняемый файл.
 - Запуск стиллера на виртуальной машине:

Установите виртуальную машину с операционной системой Windows (например, VirtualBox, VMware).

Сделайте снимок виртуальной машины перед запуском стиллера, чтобы иметь возможность откатиться в случае возникновения проблем.

Запустите стиллер на виртуальной машине, чтобы убедиться в его работоспособности. 

 - Изучение стиллер с помощью IDA Pro

Изучите основной функционал стиллера с помощью IDA Pro:
1. Найдите и проанализируйте основные функции стиллера (например, функции для кражи паролей, данных из браузеров, файлов).
2. Идентифицируйте и изучите API-функции Windows, которые использует стиллер (например, функции для доступа к реестру, файловой системе, сетевым подключениям).
3. Попробуйте понять, как стиллер обходит защиту и скрывает свою активность.

Если дз не получится не переживайте, со временем сможете вернутся к нему и решить. 
А пока основное задание подписаться на мой канал. @l33trfm0x
