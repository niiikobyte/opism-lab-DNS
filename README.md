<!--
Бланк звіту до лабораторної роботи «DNS».
Скопіювати цей файл у власний публічний репозиторій звіту як README.md і заповнити.
Коментарі такого вигляду в GitHub не відображаються; видаляти їх не обов'язково.
Вивід команд вставляти текстом у блоки коду, а не знімками екрана.
Порожні поля не допускаються: якщо щось не вдалося, записати, що саме і чому.
-->

# Лабораторна робота. DNS

| Поле | Значення |
|---|---|
| ПІБ |Нікітюк Нікіта Олексійович |
| Група |ІПЗ-2.01 |
| Ідентифікатор (`sNN`) |snn-201-25 |
| Дата виконання |02.11.2026 |
| Середовище |GitHub Codespaces |
| Версія `dig` | DiG 9.18.39-0ubuntu0.24.04.9-Ubuntu |

## Параметри варіанта

<!-- Переписати з картки stand/cards/sNN.md. -->

| Параметр | Значення |
|---|---|
| `$TTL`, с |75 |
| TTL запису SOA, с |45 |
| Поле `minimum` SOA, с |60 |
| Псевдонім (CNAME на `www`) | |
| Другий хост і його TTL, с | |
| Пріоритет MX | |
| Токен TXT | |
| Ім'я для завдання 5 | |

## Завдання 1. Складові URI

### 1.1. Розбір

<!-- Відсутню складову позначати «—». Порт за замовчуванням записувати з позначкою «(за замовч.)». -->

| № | scheme | userinfo | host | port | path | query | fragment |
|---|---|---|---|---|---|---|---|
| 1 |http |- |www.s25.lab |80 (за замовчуванням) |users/items |lang=81&from=asc |top |
| 2 |http |- |files.s25.lab |80 |items |lang=%D0%BA%D0%B8%D1%97%D0%B2 |- |
| 3 |http |user:pass |@beta.s25.lab |80 (за замовчуванням) |users |- |summary |
| 4 |https |- |www.s25.lab |4443 |users/../items |- |- |
| 5 |mailto |- |admin@s25.lab |- |- |subject=users |- |

| № | Ім'я, що запитується в DNS | Заголовок `Host` | Ціль запиту (request target) |
|---|---|---|---|
| 1 |www.s25.lab |www.s25.lab |users/items?lang=81&from=asc#top |
| 2 |files.s25.lab |files.s25.lab:80 |items/?lang=%D0%BA%D0%B8%D1%97%D0%B2 |
| 3 |user:pass@beta.s25.lab |beta.s25.lab |users#summary |
| 4 |www.s25.lab |www.s25.lab:4443 |users/../items |
| 5 |mailto:admin@s25.lab |- |subject=users |

Декодоване значення параметра URI 2: київ

Чому URI 5 не має складової host, хоча містить символ `@`: це url не є ієрархічною і не є посиланням на сайт, синтаксис не шукає у поссилані хост а передає рядок поштовій програмі. @ это обов'язковим синтаксису електроної пошти, яка є у посиланні.

### 1.2. Перевірка (після завдання 3)

<!-- Для URI 1–3: рядки запиту (починаються з ">") і тіло відповіді сервера. -->

```text

```

Значення заголовка `Authorization` і результат декодування:

```text

```

URI 4: етап, на якому запит не вдається (пошук імені / TCP / TLS), і рядки виводу, що це підтверджують:

```text

```

| Запит | `request URI`, отриманий сервером | `path`, отриманий сервером |
|---|---|---|
| звичайний | | |
| `--path-as-is` | | |

Розбіжності між URI та тим, що отримав сервер, і їх пояснення:

## Завдання 2. Проходження ієрархії вручну

```
@niiikobyte ➜ /workspaces/opism-lab-DNS (master) $ dig @10.53.0.2  ns.s-201-25.lab A +norec

; <<>> DiG 9.18.39-0ubuntu0.24.04.9-Ubuntu <<>> @10.53.0.2 ns.s-201-25.lab A +norec
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5192
;; flags: qr; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 393b07a3ae6a4cc3010000006af16be6be97a088d3207f1f (good)
;; QUESTION SECTION:
;ns.s-201-25.lab.               IN      A

;; AUTHORITY SECTION:
lab.                    172800  IN      NS      ns1.nic.lab.

;; ADDITIONAL SECTION:
ns1.nic.lab.            172800  IN      A       10.53.0.3

;; Query time: 80 msec
;; SERVER: 10.53.0.2#53(10.53.0.2) (UDP)
;; WHEN: Mon Nov 09 06:44:55 UTC 2026
;; MSG SIZE  rcvd: 116
@niiikobyte ➜ /workspaces/opism-lab-DNS (master) $ dig @10.53.0.3  ns.s-201-25.la
b A +norec

; <<>> DiG 9.18.39-0ubuntu0.24.04.9-Ubuntu <<>> @10.53.0.3 ns.s-201-25.lab A +norec
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 48861
;; flags: qr aa; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 5ef105149390a7e0010000006af16c1f0b46583f00e4824e (good)
;; QUESTION SECTION:
;ns.s-201-25.lab.               IN      A

;; AUTHORITY SECTION:
lab.                    60      IN      SOA     ns1.nic.lab. hostmaster.nic.lab. 1793602467 1800 900 604800 60

;; Query time: 80 msec
;; SERVER: 10.53.0.3#53(10.53.0.3) (UDP)
;; WHEN: Mon Nov 09 06:45:51 UTC 2026
;; MSG SIZE  rcvd: 133
@niiikobyte ➜ /workspaces/opism-lab-DNS (master) $ dig @10.53.1.1  ns.s-201-25.la
b A +norec

; <<>> DiG 9.18.39-0ubuntu0.24.04.9-Ubuntu <<>> @10.53.1.1 ns.s-201-25.lab A +norec
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 27760
;; flags: qr; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 8d062fc903360bb7010000006af16c39437cae51af534c7b (good)
;; QUESTION SECTION:
;ns.s-201-25.lab.               IN      A

;; Query time: 4 msec
;; SERVER: 10.53.1.1#53(10.53.1.1) (UDP)
;; WHEN: Mon Nov 09 06:46:17 UTC 2026
;; MSG SIZE  rcvd: 72
```

| Сервер | status | flags | ANSWER | AUTHORITY | ADDITIONAL |
|---|---|---|---|---|---|
| 10.53.0.2 |NOERROR |qr |0 |1 |2 |
| 10.53.0.3 |NXDOMAIN |qr aa |0 |1 |1 |
| 10.53.1.1 |REFUSED |qr |0 |0 |1 |

Вивід `dig ns.s-201-25.lab A +trace`:

```
; <<>> DiG 9.18.39-0ubuntu0.24.04.9-Ubuntu <<>> ns.s-201-25.lab A +trace
;; global options: +cmd
.                       515016  IN      NS      a.root-servers.net.
.                       515016  IN      NS      k.root-servers.net.
.                       515016  IN      NS      d.root-servers.net.
.                       515016  IN      NS      m.root-servers.net.
.                       515016  IN      NS      b.root-servers.net.
.                       515016  IN      NS      h.root-servers.net.
.                       515016  IN      NS      f.root-servers.net.
.                       515016  IN      NS      l.root-servers.net.
.                       515016  IN      NS      i.root-servers.net.
.                       515016  IN      NS      g.root-servers.net.
.                       515016  IN      NS      c.root-servers.net.
.                       515016  IN      NS      e.root-servers.net.
.                       515016  IN      NS      j.root-servers.net.
;; Received 811 bytes from 127.0.0.53#53(127.0.0.53) in 4 ms

.                       86400   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2026110900 1800 900 604800 86400
.                       86400   IN      RRSIG   SOA 8 0 86400 20261122050000 20261109040000 8763 . o86l5Sj+P6aUzJ0E2Mzk30vukS89QXwJYCJuYX12ah2EYoSayeGi/OFj /zTGj+VmbW8lPcTcbhEg+ftpPobRXD3CeH1tLk/kyBqA6jEmToLBiMkl 39VNwSjExKPJoj8LHYOz7QIyquLEpiouq3neMmTlSs02KoTHpu2D/q3w AJSQ4sYUviARpFxLWpNn9NMxWMEq7hOozOi6A5axF1qQhjeVIoHSx5Yz rI/peiiQ2s5Ua1/ztx1aopPMT6LyeoR029awxp0Ve+IEHZtc0C0ull5w dB59wxRzEeUJV6g0ZK+H3UwdWAbIQruFpVfat9JRZxiaF+BNUC42Useu DsIIPQ==
la.                     86400   IN      NSEC    lacaixa. NS DS RRSIG NSEC
la.                     86400   IN      RRSIG   NSEC 8 1 86400 20261122050000 20261109040000 8763 . GknSlpDXRYW7KR9VNMsoiJduO1nfLXMba0t+teAtxNWZj3I7UO0RDKnE ROiYUDNlerCGgPlQ3Ax8slHrAbbPWzfoGRAcg610/NStSpbSkguRd23M +E7yQ3IjauSoy8IGDyWTZ6U/MQaiIIzwl5ZSLAcozel0EM8aP68Kg+PJ /wN2ThuP8JnsomaIASL0VESUBLlvT1PPBNh2Vmm7wmtVIXd/2H/2KpMo +KEHSLia5QgBnsXuaaINipZ1Tqm6R3gL2QmejJzwC0xBaPtDGNXgPdxd nzOyOzhH6ir46QFZJqvW/ztuDQNMBiIh6wib8dDF3ECmkezHVPX0iRVD TcRABg==
.                       86400   IN      NSEC    aaa. NS SOA RRSIG NSEC DNSKEY ZONEMD
.                       86400   IN      RRSIG   NSEC 8 0 86400 20261122050000 20261109040000 8763 . L69jfqvgAXfIKLqOVQsgqd/KqraY8LlIDuSu5umuoA9bFe7Ho+NwI21u OWqHdyRtCLSsD7pX03FzdsC+lTi1IFy02E0rAFh0hxcHSYmkQPrkvmKC IC7FNkxCTEQ0OuZq5aE6EJCzRFhdbfmWsOjBppvo2PtfZ0U4IWG2cF9F aYuKaH3bTmT+0ybvgKrBMnJBCBC7kbnPjDQpTqAjHGHC7MCca82WdOtH RPdovO+DdSF6h+FG7DdMoNVNq1/zFrpqas8E6CDsFxDsusgGrI+ehXc+ 96fc3yGhtgjUMB5nlK2kUHzF6A/XG6okooWQfOGUAoaeFQaiaXJ3A3Nd t3dMlQ==
;; Received 1035 bytes from 198.41.0.4#53(a.root-servers.net) in 9 ms
```

| Запит | TTL запису NS | Прапорець `aa` | Секція, у якій міститься NS |
|---|---|---|---|
| `dig @10.53.0.3 sNN.lab NS +norec` |- |qr aa |- |
| `dig @10.53.1.1 sNN.lab NS +norec` |- |qr |- |

1. Які відповіді є відсиланнями і за якими ознаками це визначено?

2. Для чого у відповіді TLD присутній запис у секції ADDITIONAL і що сталося б, якби його не було?

3. Яка з двох копій запису NS є авторитативною і чому?

## Завдання 3. Заповнення власної зони

Вивід `zone-reload`:

```text

```

<!-- Шість запитів до 10.53.1.1 з +norec, по одному на кожен рядок таблиці картки. -->

| Запис | status | Прапорець `aa` | Значення в ANSWER | TTL | Відповідає картці (так/ні) |
|---|---|---|---|---|---|
| `www` A | | | | | |
| псевдонім CNAME | | | | | |
| `mail` A | | | | | |
| `@` MX | | | | | |
| `@` TXT | | | | | |
| другий хост A | | | | | |

Порівняння відповідей резолвера та власного сервера:

```text

```

| Запит | flags | ANSWER (кількість, типи) | AUTHORITY (кількість, типи) | ADDITIONAL (кількість, типи) | TTL |
|---|---|---|---|---|---|
| псевдонім через resolver-a | | | | | |
| псевдонім через 10.53.1.1 | | | | | |
| MX через resolver-a | | | | | |
| MX через 10.53.1.1 | | | | | |

Пояснення відмінностей:

Повідомлення `zone-check` після тимчасового додавання `@ IN CNAME www`:

```text

```

1. Який TTL має кожен запис зони і звідки це значення взято?

2. Скільки записів містить секція ANSWER у відповіді на запит псевдоніма і чому?

3. Чому запис CNAME неможливо розмістити для імені `@`?

## Завдання 4. TTL і видимість змін

Два запити з інтервалом 5 с:

```text

```

**Прогноз** (записати до вимірювання). Формула часу появи нової адреси через залишок R, найбільша можлива затримка:

Перевірка, що сервер до `zone-reload` повертає стару адресу:

```text

```

Залишок R і час (крок 4), час `zone-reload` (крок 5), перевірка на сервері (крок 6):

```text

```

Очікуваний час появи нової адреси (підставити R у формулу):

Вивід циклу спостереження (перші два рядки, два останні рядки зі старою адресою, рядок з новою адресою):

```text

```

Рядок `server:` з відповіді `curl`:

| Час зміни | Очікуваний час | Спостережений час | Виміряна затримка, с | Прогноз, с |
|---|---|---|---|---|
| | | | | |

1. Чим пояснюється розбіжність між прогнозом і виміряним значенням?

2. Якою була б найбільша можлива затримка для цього запису і за яких умов?

3. Як слід діяти, щоб перенести сервіс на нову адресу, якщо TTL запису становить 86 400 с?

## Завдання 5. Негативне кешування

**Прогноз** (записати до вимірювання). Формула за RFC 2308 і розраховане значення:

Перевірка, що сервер до `zone-reload` повертає `NXDOMAIN`:

```text

```

Запит через resolver-a (крок 3), час `zone-reload` (крок 4), перевірка на сервері (крок 5):

```text

```

Вивід циклу спостереження (перші два рядки, останній рядок з `NXDOMAIN`, перший рядок з `NOERROR`):

```text

```

| Негативну відповідь закешовано | Запис створено | Ім'я резолвиться через resolver-a | Виміряно, с | Прогноз, с |
|---|---|---|---|---|
| | | | | |

<!-- Якщо завдання повторювалося з іншим ім'ям (new2-…), зазначити це й причину. -->

1. Чому тривалість негативного кешування визначається записом SOA, а не записом, який запитували?

2. Чи збігається TTL запису SOA в секції AUTHORITY з TTL запису SOA у файлі зони? Поясніть.

3. Скрипт розгортання перевіряє ім'я сервісу до створення запису, а потім одразу після. Що він побачить і як це виправити?

## Завдання 6. Затримка пошуку імені

**Прогноз** (записати до вимірювання). До яких серверів звернеться resolver-a:

| Ім'я | Перший запит | Повторний запит |
|---|---|---|
| A: нове ім'я у власній зоні | | |
| B: нове ім'я в зоні `lab.` | | |
| C: ім'я в неіснуючому TLD | | |
| D: `www.sNN.lab` після перезапуску резолвера | | |

Вивід вимірювань:

```text

```

| Ім'я | Перший запит, мс | Повторний, мс | Сервери, до яких звернувся резолвер (за журналами) |
|---|---|---|---|
| A | | | |
| B | | | |
| C | | | |
| D | | | |

Значення `time_namelookup` з `curl` після перезапуску резолвера:

Фрагменти журналів запитів root, TLD і власного сервера для рядка D:

```text

```

1. Яку затримку вносить кожен із серверів root, TLD і власний сервер? Покажіть, з яких рядків таблиці це випливає.

2. Чому в рядку A перший запит не проходить через root і TLD, хоча ім'я нове?

3. Вебсторінка завантажує ресурси з п'яти різних доменів. Як це впливає на час першого відкриття сторінки?

## Завдання 7. Інцидент: зупинка власного сервера

**Прогноз** (записати до вимірювання). Що поверне кожен резолвер після зупинки сервера та спливання TTL і скільки часу займе відповідь:

Кешування запису в обох резолверах (крок 2) і час зупинки сервера (крок 3):

```text

```

Запити із заміром часу (крок 5):

```text

```

| Запит | status або тайм-аут | ANSWER, TTL | `real` |
|---|---|---|---|
| resolver-a, перший | | | |
| resolver-a, другий | | | |
| resolver-b | | | |

Час `named-start` і вивід циклу спостереження до першої успішної відповіді (крок 6):

```text

```

Перевірка після завдання (`dig @10.53.1.1 sNN.lab SOA +norec +short`):

```text

```

### Постмортем

1. Що сталося:
2. Вплив на клієнтів resolver-a і resolver-b:
3. Хронологія з виміряним часом:
4. Чому кеш допоміг або не допоміг:
5. Що слід змінити:

1. Який TTL мав запис у відповіді resolver-b і навіщо такий TTL?

2. Порівняйте результат і тривалість двох запитів до resolver-a. Якщо вони різняться, сформулюйте гіпотезу щодо причини; якщо ні, поясніть, що робить резолвер протягом цього часу.

3. Чому після `named-start` ім'я через resolver-a зарезолвилося не одразу?

4. У якій ситуації serve-stale шкодить, а не допомагає? Пов'яжіть відповідь із завданням 4.

## Фінальний файл зони

<!-- Повний вміст stand/students/sNN/zone/sNN.lab.zone після завершення роботи. -->

```text

```

## Висновки

**Що виявилося несподіваним.** Результат, який не збігся з очікуванням, і рядок виводу, де це видно:

**Яке питання залишилося без відповіді.** Що саме не вдалося пояснити; до якого рядка виводу це стосується; що вже перевірено (RFC, документація, додатковий експеримент на стенді) і що з'ясувати не вдалося:

## Декларація використання ШІ

Використовувалися інструменти ШІ: <!-- так / ні -->

<!-- Якщо так, заповнити таблицю: один рядок на кожне звернення, що вплинуло на звіт. Промпт наводити дослівно. -->

| № | Інструмент | Промпт (дослівно) | Що використано у звіті і що змінено самостійно |
|---|---|---|---|
| 1 | | | |
