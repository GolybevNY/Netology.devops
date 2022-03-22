Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"

Работа c HTTP через телнет.

Подключитесь утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80

отправьте HTTP запрос

GET /questions HTTP/1.0

HOST: stackoverflow.com

[press enter]

[press enter]

В ответе укажите полученный HTTP код, что он означает?

Ответ:

HTTP/1.1 301 Moved Permanently
cache-control: no-cache, no-store, must-revalidate
location: https://stackoverflow.com/questions
x-request-guid: 94e99cd0-1d4c-4530-92f5-08949e3aa608
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Accept-Ranges: bytes
Date: Tue, 22 Mar 2022 16:05:11 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-fra19177-FRA
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1647965112.586235,VS0,VE92
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=8c9c8446-84ba-9558-d0a0-666c510a7181; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly

Код состояния HTTP 301 или Moved Permanently (с англ. — «Перемещено навсегда») — стандартный код ответа HTTP, получаемый в ответ от сервера в ситуации, когда запрошенный ресурс был на постоянной основе перемещён в новое месторасположение, и указывающий на то, что текущие ссылки, использующие данный URL, должны быть обновлены. Адрес нового месторасположения ресурса указывается в поле Location получаемого в ответ заголовка пакета протокола HTTP.


Повторите задание 1 в браузере, используя консоль разработчика F12.

откройте вкладку Network

отправьте запрос http://stackoverflow.com

найдите первый ответ HTTP сервера, откройте вкладку Headers

укажите в ответе полученный HTTP код.

![2022-03-22_19-17-15](https://user-images.githubusercontent.com/95014681/159526087-e6493ae6-97fe-4adc-af8e-6b48443db1ab.png)


проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?

приложите скриншот консоли браузера в ответ.

![2022-03-22_19-18-18](https://user-images.githubusercontent.com/95014681/159525639-8cfb1f28-899b-419d-ac86-88550cc632b9.png)


Какой IP адрес у вас в интернете?
Ответ:

95.52.188.180 

Какому провайдеру принадлежит ваш IP адрес? 

inetnum:        95.52.188.0 - 95.52.189.255

netname:        RU-AVANGARD-DSL

descr:          PJSC "Rostelecom" North-West Region

descr:          14A, Sinopskaya emb., 191167, Saint-Petersburg, Russia

Какой автономной системе AS? Воспользуйтесь утилитой whois
Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS?
Воспользуйтесь утилитой traceroute

Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?

Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig

Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig

В качестве ответов на вопросы можно приложите лог выполнения команд в консоли или скриншот полученных результатов.





