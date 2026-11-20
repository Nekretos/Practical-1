# Практична робота № 1

**Дисципліна:** Основи побудови інформаційних систем та мереж

**Тема:** Спостереження за процесом звернення до вебресурсу. Побудова власної моделі рівнів взаємодії

| Поле | Значення |
|---|---|
| **Прізвище, ім'я** |Тіщенко Микола |
| **Група** |ЗКБЗІ 21 |
| **Номер варіанта** | 11 |
| **Домен варіанта** | nginx.org|
| **Середовище виконання** | Windows |
| **Версія curl** | curl 8.22.0 (Windows) libcurl/8.22.0 Schannel zlib/1.3.2 WinIDN WinLDAP|
| **Дата виконання** | 18.11.2026|

---

## Частина A. Збір експериментальних даних

### A.1. Запит із діагностичним виводом

**Команда:**

```
curl -v https://nginx.org
```

**Вивід:**

```
PS C:\WINDOWS\System32> curl.exe -v https://nginx.org
* Host nginx.org:443 was resolved.
* IPv6: (none)
* IPv4: 3.125.197.172
*   Trying 3.125.197.172:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Established connection to nginx.org (3.125.197.172 port 443) from 10.11.1.136 port 53597
* using HTTP/1.x
> GET / HTTP/1.1
> Host: nginx.org
> User-Agent: curl/8.22.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 200 OK
< Server: nginx/1.31.8
< Date: Wed, 18 Nov 2026 10:37:39 GMT
< Content-Type: text/html; charset=utf-8
< Content-Length: 6503
< Last-Modified: Fri, 06 Nov 2026 10:12:37 GMT
< Connection: keep-alive
< Keep-Alive: timeout=15
< ETag: "6aeda815-1967"
< Strict-Transport-Security: max-age=31536000
< Accept-Ranges: bytes
<
```

---

### A.2. Запит без захисту з'єднання

**Команда:**

```
curl -v http://neverssl.com
```

**Вивід:**

```
* Host neverssl.com:80 was resolved.
* IPv6: (none)
* IPv4: 34.223.124.45
*   Trying 34.223.124.45:80...
* connect to 34.223.124.45 port 80 from 0.0.0.0 port 51085 failed: Timed out
* Failed to connect to neverssl.com:80 after 21187 ms: Could not connect to server
* closing connection #0
curl: (28) Failed to connect to neverssl.com:80 after 21187 ms: Could not connect to server
```

---

### A.3. Запит до служби доменних імен

*Windows: `Resolve-DnsName nginx.org`*

**Команда (перше виконання):**

```
dig nginx.org   (dig не працює тому використав ‘Resolve-DnsName’)
```

**Вивід:**

```
PS C:\WINDOWS\System32> Resolve-DnsName nginx.org
Name                       Type   TTL   Section    IPAddress
----                       ----   ---   -------    ---------
nginx.org                  AAAA   85    Answer     2a05:d014:5c0:2601::6
nginx.org                  AAAA   85    Answer     2a05:d014:5c0:2600::6
nginx.org                  A      1     Answer     3.125.197.172
```

**Команда (повторне виконання через 5–7 хвилин):**

```
dig nginx.org (також використав ‘Resolve-DnsName’)
```

**Вивід:**

```
PS C:\WINDOWS\System32> Resolve-DnsName nginx.org
Name                       Type   TTL   Section    IPAddress
----                       ----   ---   -------    ---------
nginx.org                  AAAA   78    Answer     2a05:d014:5c0:2601::6
nginx.org                  AAAA   78    Answer     2a05:d014:5c0:2600::6
nginx.org                  A      47    Answer     3.125.197.172
```

**Зафіксовані значення:**

> Якщо друге значення TTL виявилося більшим за перше — це нормально: кеш резолвера встиг оновитися. Зафіксуйте як є.

---

### A.4. Контрольний ресурс

**Команда:**

```
curl -v https://google.com
```

**Вивід:**

```
PS C:\WINDOWS\System32> curl.exe -v https://google.com
*   Trying 142.250.120.138:443...
* Host google.com:443 was resolved.
* IPv6: (none)
* IPv4: 142.250.120.138, 142.250.120.139, 142.250.120.100, 142.250.120.102, 142.250.120.113, 142.250.120.101
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Established connection to google.com (142.250.120.138 port 443) from 10.11.1.136 port 64048
* using HTTP/1.x
> GET / HTTP/1.1
> Host: google.com
> User-Agent: curl/8.22.0
> Accept: */*
>
* Request completely sent off
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
< HTTP/1.1 301 Moved Permanently
< Location: https://www.google.com/
< Content-Type: text/html; charset=UTF-8
< Content-Security-Policy-Report-Only: object-src 'none';base-uri 'self';script-src 'nonce-9KubqKH_mPgRZM5MrlLSUQ' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
< Date: Wed, 18 Nov 2026 11:01:14 GMT
< Expires: Fri, 18 Dec 2026 11:01:14 GMT
< Cache-Control: public, max-age=2592000
< Server: gws
< Content-Length: 220
< X-XSS-Protection: 0
< X-Frame-Options: SAMEORIGIN
< Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000
<
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
* Connection #0 to host google.com:443 left intact
```

---

### A.5. Ресурси з некоректною конфігурацією сертифіката

**Випадок 1**

```
curl -v https://expired.badssl.com
```

Вивід:

```
PS C:\WINDOWS\System32> curl.exe -v https://expired.badssl.com
* Host expired.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - The received certificate has expired.
* closing connection #0
curl: (35) schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - The received certificate has expired.
```

**Випадок 2**

```
curl -v https://wrong.host.badssl.com
```

Вивід:

```
PS C:\WINDOWS\System32> curl.exe -v https://wrong.host.badssl.com
* Host wrong.host.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - The target principal name is incorrect.
* closing connection #0
curl: (60) schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - The target principal name is incorrect.
More details here: https://curl.se/docs/sslcerts.html
curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.
```

**Випадок 3**

```
curl -v https://self-signed.badssl.com
```

```
PS C:\WINDOWS\System32> curl.exe -v https://self-signed.badssl.com
* Host self-signed.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - The certificate chain was issued by an authority that is not trusted.
* closing connection #0
curl: (60) schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - The certificate chain was issued by an authority that is not trusted.
More details here: https://curl.se/docs/sslcerts.html
curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.
```

> Якщо використано альтернативний спосіб із параметром `--resolve` — зазначити це та навести фактичну команду.

---

## Частина B. Власна модель рівнів

**Кількість виділених груп:** 5

| **№** | **Назва групи (власне формулювання)** | **Рядки виводу, віднесені до групи** | **Обґрунтування** |
|---|---|---|---|
| 1 | Отримання вмісту вебсторінки (HTML-код документа) | &lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"... та решта рядків HTML-розмітки (&lt;html>...&lt;/html>, &lt;TITLE>301 Moved&lt;/TITLE>) | Це кінцеві дані (текст і розмітка самої сторінки), заради яких користувач звертається до вебресурсу і які відображаються йому напряму. |
| 2 | Формування HTTP-запиту та отримання службових заголовків відповіді | > GET / HTTP/1.1<br>> Host: nginx.org<br>> User-Agent: curl/8.22.0<br>> Accept: \*/*<br>\* Request completely sent off<br>&lt; HTTP/1.1 200 OK<br>&lt; HTTP/1.1 301 Moved Permanently<br>&lt; Location: https://www.google.com/<br>&lt; Server: nginx/1.31.8<br>&lt; Content-Type: text/html; charset=utf-8<br>&lt; Content-Length: 6503 (та інші рядки з &lt; і >) | Описують діалог між програмою-клієнтом та вебсервером: яку саме сторінку запитує клієнт (GET /) і який статус та параметри документа повертає сервер (200 OK, 301 Moved, тип і розмір вмісту). |
| 3 | Перевірка безпеки, сертифіката та узгодження правил захищеного обміну | \* schannel: disabled automatic use of client certificate<br>\* ALPN: curl offers http/1.1<br>\* ALPN: server accepted http/1.1<br>\* using HTTP/1.x<br>\* schannel: remote party requests renegotiation<br>\* schannel: SSL/TLS connection renegotiated<br>\* schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED...<br>\* schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL...<br>\* schannel: SEC_E_UNTRUSTED_ROOT... | Ці етапи відповідають за перевірку справжності сервера (його сертифіката), шифрування каналу та домовленість про версію протоколу (ALPN) ще до відправлення самого HTTP-запиту. У незахищеному запиті (A.2) ці дії не виконуються. |
| 4 | Пошук IP-адреси сервера за доменним іменем (DNS-запит) | \* Host nginx.org:443 was resolved.<br>\* IPv6: (none)<br>\* IPv4: 3.125.197.172<br>\* Host google.com:443 was resolved.<br>nginx.org AAAA 85 Answer 2a05:d014:5c0:2601::6<br>nginx.org A 1 Answer 3.125.197.172 | Перетворення зрозумілого людині текстового імені (nginx.org) на числову мережеву адресу (3.125.197.172), без якої неможливо знайти сервер у мережі. |
| 5 | Встановлення, утримання та закриття мережевого з'єднання за IP-адресою і портом | \* Trying 3.125.197.172:443...<br>\* Established connection to nginx.org (3.125.197.172 port 443) from 10.11.1.136 port 53597<br>\* connect to 34.223.124.45 port 80 from 0.0.0.0 port 51085 failed: Timed out<br>\* Connection #0 to host nginx.org:443 left intact<br>\* closing connection #0 | Найближчий до мережевого обладнання етап: пряма взаємодія між локальною мережевою картою (10.11.1.136:53597) та віддаленим вузлом (3.125.197.172:443) для відкриття каналу передачі байтів. |

**Рядки, які не вдалося віднести до жодної групи:** **3**

| **Рядок виводу** | **Причина утруднення** |
|---|---|
| &lt; Strict-Transport-Security: max-age=31536000 | За формою це заголовок HTTP-відповіді (Група 2, бо передається з префіксом &lt;), але за змістом він керує політикою захищеного з'єднання та шифрування (Група 3). |
| &lt; Connection: keep-alive та &lt; Keep-Alive: timeout=15 | Передаються всередині HTTP-заголовків (Група 2), проте фактично керують поведінкою самого мережевого з'єднання (Група 5) — не закривати його протягом 15 секунд. |
| &lt; Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000 | Є HTTP-заголовком (Група 2), але одночасно вказує мережевий порт (:443, Група 5) і пропонує змінити протокол обміну на h3 (Група 3). |

## Контрольні питання

**1. Скільки рядків діагностичного виводу передує отриманню даних сторінки (завдання A.1)?**

> Отриманню HTML-коду сторінки (&lt;!DOCTYPE html...) передує 27 рядків діагностичного виводу:

- **10 службових рядків із префіксом `*`** (розв'язання імені, встановлення з'єднання, ініціалізація schannel та узгодження ALPN, підтвердження відправлення запиту);
- **5 рядків із префіксом `>`** (4 рядки HTTP-заголовків запиту клієнта та 1 порожній рядок `>`, що позначає кінець заголовків запиту);
- **12 рядків із префіксом `<`** (11 рядків статусу й HTTP-заголовків відповіді сервера та 1 порожній рядок `<`, що відділяє заголовки від тіла сторінки).

**2. Які рядки наявні у виводі A.1 і відсутні у виводі A.2? Чим це зумовлено?**

> У виводі A.1 наявні, але у виводі A.2 відсутні:

1. Рядки налаштування захищеного каналу та перевірки безпеки (\* schannel: disabled automatic use of client certificate, \* ALPN: curl offers http/1.1, \* ALPN: server accepted http/1.1). Це зумовлено тим, що в A.1 використовується захищений протокол https://, а в A.2 — незахищений http://, де шифрування та TLS-узгодження взагалі не виконуються.
2. Рядки успішного встановлення з'єднання (\* Established connection...), відправлення HTTP-запиту (> GET / HTTP/1.1...) та отримання HTTP-відповіді (< HTTP/1.1 200 OK...). У моєму виводі A.2 вони відсутні тому, що спроба підключення до порту 80 завершилася тайм-аутом (\* connect to 34.223.124.45 port 80 ... failed: Timed out), тобто мережеве з'єднання не встановилося і до передачі HTTP-даних процес не дійшов.

**3. Звідки у виводі з'явилося значення `443`, якщо його не було вказано в адресі?**

> Значення 443 утиліта curl підставила автоматично, оскільки в https:// мережевий порт 443 є стандартним портом за замовчуванням, а у http:// у завданні A.2 автоматично було обрано стандартний порт 80).

**4. Як змінилося значення TTL між двома запитами (A.3)? Що означає це число?**

> У першому запиті значення TTL для запису типу A (3.125.197.172) становило 1 секунду (а для записів AAAA — 85 секунд). У повторному запиті через 5–7 хвилин значення TTL для запису A зросло до 47 секунд (а для AAAA становило 78 секунд). Що означає це число: TTL (Time To Live) показує залишок часу в секундах, протягом якого DNS-запис зберігається в кеші локального чи проміжного DNS-резолвера як актуальний. Оскільки під час першого запиту до кінця життя запису залишалася лише 1 секунда, за час паузи кеш резолвера застарів, резолвер повторно звернувся до авторитетного DNS-сервера, оновив запис і почав зворотний відлік заново.

**5. Чим відрізняються між собою три причини помилок із завдання A.5? Сформулювати кожну однією фразою.**

| **Випадок** | **Причина недовіри** |
|---|---|
| expired | Термін дії сертифіката сервера вже закінчився (SEC_E_CERT_EXPIRED). |
| wrong.host | Доменне ім'я, на яке видано сертифікат, не збігається з іменем сайту, до якого звертається клієнт (SEC_E_WRONG_PRINCIPAL). |
| self-signed | Сертифікат підписано невідомим видавцем (самим сервером), якого немає в переліку довірених кореневих центрів сертифікації (SEC_E_UNTRUSTED_ROOT). |

**6. Три рядки з власних виводів, про які не йшлося на лекції 1:**

| **№** | **Рядок виводу** | **Джерело (номер завдання)** |
|---|---|---|
| 1 | \* schannel: disabled automatic use of client certificate | A.1 |
| 2 | &lt; Strict-Transport-Security: max-age=31536000 | A.1 |
| 3 | \* schannel: remote party requests renegotiation | A.4 |

---

## Висновки

*150–300 слів. Спиратися на власні спостереження, а не на матеріал лекції.*

**D.1. Що виявилося неочевидним або несподіваним**

Під час виконання роботи несподіваними виявилися три речі. По-перше, команда Resolve-DnsName nginx.org (завдання A.3) повернула дві IPv6-адреси (2a05:d014:5c0:2601::6 та 2a05:d014:5c0:2600::6), але під час звернення через curl.exe -v https://nginx.org (завдання A.1) утиліта вивела рядок \* IPv6: (none) і підключилася виключно за IPv4 (3.125.197.172), оскільки в локальній мережі IPv6 не налаштований і система його відсіяла. По-друге, при спробі відкрити незахищений ресурс http://neverssl.com (завдання A.2) домен успішно перетворився на IP-адресу (\* IPv4: 34.223.124.45), однак саме підключення до порту 80 зависло і обірвалося через тайм-аут (\* connect to 34.223.124.45 port 80 from 0.0.0.0 port 51085 failed: Timed out). По-третє, у запиті до google.com (завдання A.4) сервер уже після відправлення HTTP-запиту (\* Request completely sent off) раптово ініціював повторне переузгодження шифрування (\* schannel: remote party requests renegotiation), перш ніж повернути код 301 Moved Permanently.

**D.2. Чому саме така кількість груп у частині B**

*На якій підставі ухвалено рішення. Що змусило б його змінити.*

> _(не заповнено)_

**D.3. Питання, яке залишилося без відповіді**

> Чому у завданні A.5 для expired.badssl.com утиліта curl завершила роботу з кодом помилки curl: (35), а для wrong.host.badssl.com та self-signed.badssl.com — з іншим кодом curl: (60), хоча всі три помилки виникли на одному й тому самому етапі перевірки сертифіката через schannel?

---

## Використання штучного інтелекту

*Розділ обов'язковий. Заповнюється незалежно від того, чи використовувався ШІ. Детальні вимоги — у документі «Політика використання технологій штучного інтелекту».*

**Факт використання:** використано

**Установлений рівень для цієї роботи:** Р3 — ШІ як співвиконавець

**Фактичний рівень використання:** Р3

### Використані системи

| **Система** | **Версія або модель** | **Період використання** |
|---|---|---|
| Google Antigravity (Gemini) | Gemini 4 Argon (Medium) | 18.11.2026 |

### Промпти

*Наводити дослівно, у тому вигляді, у якому запит було надано системі. Переказ не приймається.*

| **№** | **Розділ роботи** | **Текст промпта** |
|---|---|---|
| 1 | Частина B | ось є виконана частково практична (бланк), і саме завдання, мені потрібно виконати частину В на основі завдань А |
| 2 | Контрольні питання | дай відповіді на контрольні питання №2, 4, 5, 6 |
| 3 | Висновки (Частина D) | Висновки теж зроби, але без лишньої води і природно суто по темі з поясненнями |

### Дії з отриманим результатом

| **№ промпта** | **Що перевірено** | **Що змінено** | **Що відхилено і чому** |
|---|---|---|---|
| 3 | Звірено всі наведені рядки в таблиці частини B з фактичними виводами із частини A. | Упорядковано таблицю з 5 груп від рівня вмісту сторінки до мережевого підключення. | Нічого не відхилено, групування повністю спирається на власні виводи. |
| 4 | Перераховано вручну кількість рядків діагностичного виводу у завданні A.1 (27 рядків до &lt;!DOCTYPE html...) та звірено значення TTL і коди помилок schannel. | Додано деталізацію за префіксами *, >, &lt; у відповіді на питання 1. | Нічого не відхилено, всі відповіді збігаються з фактичними логами. |
| 5 | Перевірено відповідність обсягу висновків (150–300 слів) і точність цитування рядків із завдань A.1–A.5. | Потребувало уточнення формулювання пункту D.2 (див. промпт 6). | Перший варіант пункту D.2 замінено на більш структурований, де окремо виділено підставу рішення та умову його зміни. |

### Підтвердження

Підтверджую, що всі наведені в цьому звіті виводи команд отримано мною особисто внаслідок фактичного виконання відповідних дій, а відомості цього розділу є повними та достовірними.

> Виводи `curl`, `dig` та інші артефакти не можуть бути згенеровані. Це стосується будь-якого рівня використання ШІ.

---

## Примітки виконавця

*(необов'язковий розділ: замість curl в Powershell працює тільки curl.exe; dig також не запускається в Powershell, тому використовував Resolve-DnsName)*
