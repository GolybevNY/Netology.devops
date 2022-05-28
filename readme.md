Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"


1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.

Выполнено:

![Bitwarden](https://user-images.githubusercontent.com/95014681/167627939-dd9b6429-ad96-485d-8d48-a86baf2d03aa.png)


2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.

Выполнено:

![Two Step Login](https://user-images.githubusercontent.com/95014681/167628814-2fea4f1c-4140-4047-9648-b74031c4b4b6.png)


3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.

Выполнено:
Установим apache2:


$ sudo apt install apache

Сгенерируем самоподписанный сертификат:


![SelfSignCRT](https://user-images.githubusercontent.com/95014681/167644143-5b053f79-7904-4c30-ac7d-c90d67027cf5.png)


Настроим сайт по умолчанию (Apache) для работы по HTTPS, создадим файл /etc/apache2/sites-available/your_domain_or_ip.conf и внесем следующую когфигурацию:


![Apache site-conf](https://user-images.githubusercontent.com/95014681/168880297-8be4dd8d-ca7c-4ae2-a8af-9108f6bbd3b9.png)

Затем активируем конфигурацию сайта по умолчанию с https:


sudo a2ensite your_domain_or_ip.conf

sudo systemctl reload apache2


Выполним проброс портов из гостевой системы, добавим в vagrantfile:

config.vm.network "forwarded_port", guest: 443, host: 33443


Проверим:

![https_selfsigned](https://user-images.githubusercontent.com/95014681/168879061-d0b5a682-ccb7-47fe-9d73-ac887e719bee.png)


4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).


Выполнено:

![Testssl_avito_1](https://user-images.githubusercontent.com/95014681/168484959-4e828a53-7819-4e50-b45d-c6ebdf030181.png)

![Testssl_avito_2](https://user-images.githubusercontent.com/95014681/168484967-519d0409-94e4-4fe4-ae1a-ac537a959502.png)

![Testssl_avito_3](https://user-images.githubusercontent.com/95014681/168484973-4f78bc49-77c1-4fae-b4a7-86dffcc7acdf.png)


5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.

Сгенерируем новый приватный ключ для vm1:

![VM1_SSH-keygen](https://user-images.githubusercontent.com/95014681/170829235-3c9c0c20-eefc-4d19-a098-78d35fbe3dc2.png)

Скопируем свой публичный ключ на другой сервер vm2 и подключимся к серверу vm2 по ssh:

![VM1_SSH_pub_copy](https://user-images.githubusercontent.com/95014681/170829322-7ff04695-d100-4a24-a5e0-d61c7b65cef8.png)


6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.

Выполнено:

![SSH-Config](https://user-images.githubusercontent.com/95014681/170829862-76b82c48-f527-4e18-b9be-fbe8c9007a37.png)


7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.

Выполнено:

![tcpdump](https://user-images.githubusercontent.com/95014681/170832025-c1202d9a-f7fa-452e-9304-a81f299b167e.png)


![Wireshark](https://user-images.githubusercontent.com/95014681/170831953-68cbc709-6dbb-47b3-8144-680644719d5c.png)


Задание для самостоятельной отработки (необязательно к выполнению)
8*. Просканируйте хост scanme.nmap.org. Какие сервисы запущены?

9*. Установите и настройте фаервол ufw на web-сервер из задания 3. Откройте доступ снаружи только к портам 22,80,443







