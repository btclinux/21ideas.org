---
title: "Что такое SegWit"
h1: "Что представляет собой обновление Segregated Witness"
cover: /img/segwit/01.png
description: "Обновление Segregated Witness SegWit в 2017 году изменило структуру данных транзакции в Биткоине. Эта статья поделится подробностями этого обновления."
url: segwit
date: 2024-02-10
bookFlatSection: false
weight: 61
---

Обновление Segregated Witness (_SegWit_) в 2017 году изменило структуру данных транзакции в Биткоине.

Основной причиной обновления было исправление **пластичности транзакций** (я объясню это чуть позже). Другим значительным изменением стало **увеличение размера блока**.

{{< hint btc>}}
Перевод статьи [Грега Уокера](https://twitter.com/in3rsha), [опубликованной](https://learnmeabitcoin.com/beginners/guide/segwit) на сайте learnmeabitcoin.

[Поддержать проект](/contribute/).
{{</hint >}}

## В чем заключалось основное изменение?

### Legacy-транзакция

В традиционной (Legacy) транзакции Биткоина код разблокировки (и подписи) располагаются _следом_ за каждым [входом](/protocol/utxo), поэтому код разблокировки распределен по всем данным транзакции.

Затем на основе **всех данных транзакции** создается TXID (идентификатор транзакции):

{{% image "/img/segwit/02-ru.png" /%}}

### SegWit-транзакция

В SegWit-транзакции весь код разблокировки (и подписи) переносятся в конец данных транзакции.

Затем TXID создается из всех данных транзакции, **за исключением кода разблокировки**:

{{% image "/img/segwit/03-ru.png" /%}}

В итоге, на TXID в **SegWit-транзакции** влияют только *результаты* транзакции (движение биткоинов), но не код, необходимый для *валидации* транзакции (т.е. подписи, необходимые для разблокировки биткоинов для расходования).

{{% image "/img/segwit/04-ru.png" /%}}

Таким образом, по сути, мы отделили "валидирующую" часть (код разблокировки) от остальной части транзакции.

{{% hint info %}}
Если мы назовем этот код валидации данными *свидетеля* (как это сделал бы криптограф), то можно сказать, что мы "*отделили свидетеля*". "Segregated Witness" переводится с английского как "отделённый свидетель". 
{{% /hint %}}

## Преимущества SegWit

### 1. Исправление пластичности транзакций

В Биткоине под **пластичностью транзакций** понимается тот факт, что **ID транзакции может быть изменен путем изменения подписей**:

{{% image "/img/segwit/05-ru.png" %}}
_Подпись может быть изменена путем инвертирования [значения s](https://learnmeabitcoin.com/technical/keys/signature/#legacy-step-6). Подпись остается действительной, и транзакция имеет тот же результат, но TXID будет другим._
{{% /image %}}

Это означает, что при отправке Legacy-транзакции в сеть любой узел имеет возможность изменить TXID, прежде чем передавать его дальше:

{{% image "/img/segwit/06-ru.png" %}}
_В конце концов ваша транзакция попадет в блокчейн под другим TXID, чем вы ожидали, что будет доставлять неудобства._
{{% /image %}}

Однако если подписи больше не являются частью TXID, то впоследствии кто-то другой не сможет изменить TXID вашей транзакции:

{{% image "/img/segwit/07-ru.png" /%}}

Другими словами, SegWit делает идентификаторы транзакций *достоверными*.

### 2. Увеличение вместимости блока

Из-за того, что код разблокировки был перенесен в _новое_ поле witness в данных транзакции, способ расчета размера блока также изменился.

Ранее размер транзакций измерялся в [байтах](/transaction-size), а предельный размер блока составлял 1 000 000 байт (1 МБ):

{{% image "/img/segwit/08-ru.png" /%}}

С появлением SegWit транзакции больше не измеряются в байтах. Вместо этого транзакции и блоки получили новую метрику под названием [вес](/transaction-size):

{{% image "/img/segwit/09-ru.png" /%}}

- Максимальный размер блока составляет `4 000 000` единиц веса.
    - Один *обычный* байт в транзакции равен `4` единицам веса.
    - Байт *witness*-данных в транзакции равен `1` единице веса.

То есть, ограничение размера блока умножается на 4, чтобы получить новое *ограничение веса блока*.

Каждый байт в транзакции умножается на 4, чтобы получить [вес транзакции](/transaction-size). Однако вы умножаете каждый байт witness-данных на 1, что, по сути, дает вам 75-процентную скидку на размещение "кода разблокировки" в блоке.

Таким образом, можно сказать, что код разблокировки занимает _четверть_ занимаемого ранее места, что означает, что теперь в блоке больше места для данных транзакций.

## Увеличило ли обновление SegWit размер блока?

Да, размер каждого блока теперь может превышать 1 000 000 байт (1 МБ).

Хотя ограничение на размер блока не было увеличено на определенное количество _байт_, снижение веса кода разблокировки означает, что блоки могут превысить прежний лимит в 1 МБ.

{{% hint info %}}
**То, что размер блока изменился с 1 000 000 байт до 4 000 000 единиц веса, не означает, что SegWit увеличил абсолютное размера блока до 4 МБ.**

Это связано с тем, что типичный блок не будет заполнен исключительно witness-данными (1 единица веса за байт). Вместо этого транзакции содержат комбинацию обычных данных (4 весовые единицы) и witness-данных (1 единица веса). Поэтому "увеличение размера блока" зависит от состава транзакций в блоке.
{{% /hint %}}

### Насколько увеличился размер блока благодаря SegWit?

Обновление SegWit увеличило максимальный размер *типичных* блоков примерно до **1,8 МБ**.

Типичный блок транзакций состоит примерно на **60% из кода разблокировки** [^1]. Таким образом, если мы вычислим _вес_ блока размером 1 МБ, состоящего из "типичных" данных транзакций, мы получим:

```
400 000 байт * 4 = 1 600 000 единиц веса
600 000 байт * 1 =   600,000 единиц веса

Общий вес        = 2 200 000 единиц веса
```

Теперь, если блок может иметь максимальный вес в 4 000 000 единиц, мы можем определить, насколько это увеличивает его размер:

```
4,000,000 / 2,200,000 = 1.81
```

Так что можно сказать, что это эффективное увеличение размера блока до **1,8 МБ**.

## Почему изменения были реализованы именно так?

Если вы хотите исправить пластичность транзакций и увеличить ёмкость блока, то, наверняка, есть более _простой_ способ этого добиться, верно? Зачем реструктурировать данные о транзакциях и создавать новую метрику под названием "вес блока"?

Хороший вопрос. И вы правы — добиться изменений можно было гораздо проще. Например, можно было бы сделать вот так:

{{% image "/img/segwit/10-ru.png" /%}}

Однако если вы сделаете это, транзакции и блоки станут "недействительными" в соответствии с текущими правилами.

Это означает, что узлы сети будут отвергать эти новые транзакции и блоки, поскольку они не соответствуют их правилам о том, как должны "выглядеть" транзакции и блоки.

{{% image "/img/segwit/11-ru.png" %}}
_Например, одно из правил гласит, что каждый блок должен быть размером не более 1 МБ._
{{% /image %}}

Поэтому, если вы захотите внести эти изменения, **вам придется заставить всех в сети обновить свое программное обеспечение** (и, очевидно, согласиться с изменениями).

В противном случае вы получите сеть, в которой будут созданы два разных блокчейна: обновленные узлы будут строить блокчейн по новым правилам, а старые узлы продолжат строить блокчейн по старым правилам.

{{% image "/img/segwit/12-ru.png" /%}}

Это явление называется хардфорком. В принципе, это может сработать, но очень рискованно и создаст проблемы для тех, кто не обновит программное обеспечение.

### Как при обновлении SegWit удалось избежать хардфорка?

Вместо того чтобы сделать SegWit хардфорком, он был реализован как софтфорк.

При обновлении SegWit **транзакции и блоки *все еще следуют* текущим правилам сети Биткоин**, поэтому все узлы по-прежнему воспринимают SegWit-блоки как действительные. "Старые" узлы будут принимать эти "новые" блоки и добавлять их в свою копию блокчейна.

{{% image "/img/segwit/13-ru.png" /%}}

Поэтому старые узлы продолжают взаимодействовать с новыми узлами, *несмотря на то, что они (старые узлы) не обновились*.

{{% image "/img/segwit/14-ru.png" %}}
_Вследствие софтфорка все узлы синхронизируются с единой версией блокчейна._
{{% /image %}}

Недостатком для "старых" узлов является то, что они не смогут воспользоваться новыми возможностями SegWit до тех пор, пока не обновятся. Однако они могут продолжать совершать транзакции "старого образца" и **поддерживать блокчейн**.

Итак, обновление SegWit может показаться "хакерским" способом устранения пластичности транзакций и увеличения ёмкости блоков, но такой подход позволяет избежать проблемы, связанной с попыткой заставить всех перейти на новое программное обеспечение (или в противном случае остаться не у дел).

## Когда был активирован SegWit?

Segwit был активирован 24 августа 2017 года в 01:57:37 по UTC, на высоте блока [481824](https://learnmeabitcoin.com/explorer/block/0000000000000000001c8018d9cb3b742ef25114f27563e3fc4a1902167f9893).

Именно тогда узлы начали применять новые правила консенсуса, предусмотренные обновлением SegWit, для всех новых блоков и транзакций.

## Как вступил в действие SegWit?

Обновление Segregated Witness вступило в силу, когда **95%** майнеров заявили о готовности его поддержать.

Майнеры могут сигнализировать о готовности, используя определенный [номер версии](https://learnmeabitcoin.com/technical/block/version/) в блоках, которые они добывают.

{{% image "/img/segwit/15-ru.png" %}}
_Поле версии является частью [заголовка блока](https://learnmeabitcoin.com/technical/block/#header)._
{{% /image %}}

Таким образом, когда 95% блоков имели этот номер версии, SegWit был запланирован к активации:

{{% image "/img/segwit/16-ru.png" %}}
_Порог 95% рассчитывается в течение периода между корректировками сложности ([целевого значения](https://21ideas.org/difficulty/)). Если 95-процентный порог достигнут, софтфорк активируется в начале _следующего_ периода (который составляет 2016 блоков, или примерно 2 недели)._
{{% /image %}}

### Было ли ограничение по времени для активации?

Да, было задано окно активации:

|**Старт:**|15 ноября 2016 г., 00:00|
|---|---|
|**Тайм-аут:**| **15 ноября 2017 г., 00:00**|

Если бы к полуночи 15 ноября 2017 года достаточное количество майнеров не заявило о готовности к обновлению Segregated Witness, предложение было бы отменено.

### График активации

В таблице показано количество блоков, сигнализирующих о SegWit, за каждый период между корректировками сложности, предшествующий активации:

|Начало|Период|Сигнализирующие блоки|Процент|
|---|---|---|---|
|18.11.2016, 08:30:15|[439488](https://learnmeabitcoin.com/explorer/439488#blockchain) до [441503](https://learnmeabitcoin.com/explorer/441503#blockchain)|451/2016|22.37%|
|02.12.2016, 02:46:26|[441504](https://learnmeabitcoin.com/explorer/441504#blockchain) до [443519](https://learnmeabitcoin.com/explorer/443519#blockchain)|487/2016|24.16%|
|15.12.2016, 01:28:33|[443520](https://learnmeabitcoin.com/explorer/443520#blockchain) до [445535](https://learnmeabitcoin.com/explorer/445535#blockchain)|520/2016|25.79%|
|28.12.2016, 17:40:55|[445536](https://learnmeabitcoin.com/explorer/445536#blockchain) до [447551](https://learnmeabitcoin.com/explorer/447551#blockchain)|521/2016|25.84%|
|10.01.2017, 22:40:52|[447552](https://learnmeabitcoin.com/explorer/447552#blockchain) до [449567](https://learnmeabitcoin.com/explorer/449567#blockchain)|489/2016|24.26%|
|22.01.2017, 22:52:52|[449568](https://learnmeabitcoin.com/explorer/449568#blockchain) до [451583](https://learnmeabitcoin.com/explorer/451583#blockchain)|468/2016|23.21%|
|04.02.2017, 23:38:49|[451584](https://learnmeabitcoin.com/explorer/451584#blockchain) до [453599](https://learnmeabitcoin.com/explorer/453599#blockchain)|485/2016|24.06%|
|18.02.2017, 09:38:26|[453600](https://learnmeabitcoin.com/explorer/453600#blockchain) до [455615](https://learnmeabitcoin.com/explorer/455615#blockchain)|537/2016|26.64%|
|03.03.2017, 19:04:46|[455616](https://learnmeabitcoin.com/explorer/455616#blockchain) до [457631](https://learnmeabitcoin.com/explorer/457631#blockchain)|532/2016|26.39%|
|17.03.2017, 08:36:15|[457632](https://learnmeabitcoin.com/explorer/457632#blockchain) до [459647](https://learnmeabitcoin.com/explorer/459647#blockchain)|582/2016|28.87%|
|30.03.2017, 16:39:08|[459648](https://learnmeabitcoin.com/explorer/459648#blockchain) до [461663](https://learnmeabitcoin.com/explorer/461663#blockchain)|614/2016|30.46%|
|13.04.2017, 02:59:50|[461664](https://learnmeabitcoin.com/explorer/461664#blockchain) до [463679](https://learnmeabitcoin.com/explorer/463679#blockchain)|671/2016|33.28%|
|27.04.2017, 02:20:01|[463680](https://learnmeabitcoin.com/explorer/463680#blockchain) до [465695](https://learnmeabitcoin.com/explorer/465695#blockchain)|698/2016|34.62%|
|10.05.2017, 03:40:48|[465696](https://learnmeabitcoin.com/explorer/465696#blockchain) до [467711](https://learnmeabitcoin.com/explorer/467711#blockchain)|663/2016|32.89%|
|23.05.2017, 07:29:52|[467712](https://learnmeabitcoin.com/explorer/467712#blockchain) до [469727](https://learnmeabitcoin.com/explorer/469727#blockchain)|622/2016|30.85%|
|04.06.2017, 14:35:07|[469728](https://learnmeabitcoin.com/explorer/469728#blockchain) до [471743](https://learnmeabitcoin.com/explorer/471743#blockchain)|642/2016|31.85%|
|17.06.2017, 23:18:53|[471744](https://learnmeabitcoin.com/explorer/471744#blockchain) до [473759](https://learnmeabitcoin.com/explorer/473759#blockchain)|825/2016|40.92%|
|02.07.2017, 00:47:17|[473760](https://learnmeabitcoin.com/explorer/473760#blockchain) до [475775](https://learnmeabitcoin.com/explorer/475775#blockchain)|917/2016|45.49%|
|14.07.2017, 08:45:42|[475776](https://learnmeabitcoin.com/explorer/475776#blockchain) до [477791](https://learnmeabitcoin.com/explorer/477791#blockchain)|1440/2016|71.43%|
|27.07.2017, 11:03:54|[477792](https://learnmeabitcoin.com/explorer/477792#blockchain) до [479807](https://learnmeabitcoin.com/explorer/479807#blockchain)|2016/2016|100.00%|

Как видите, порог в 95% майнеров, сигнализирующих о готовности к SegWit, был превышен в период между блоками [477792](https://learnmeabitcoin.com/explorer/block/00000000000000000016ba7786309176445b838b36a16bd1ef3c3e3020473206) и [479807](https://learnmeabitcoin.com/explorer/block/00000000000000000053e2d10bd703ad5b7787614965711d6170b69b133aa366).

Таким образом, обновление SegWit было активировано спустя 2016 блоков (примерно через 2 недели) на блоке [481824](https://learnmeabitcoin.com/explorer/block/0000000000000000001c8018d9cb3b742ef25114f27563e3fc4a1902167f9893):

|Начало|Период|Примечание|
|---|---|---|
|09.08.2017, 12:36:50|[479808](https://learnmeabitcoin.com/explorer/479808#blockchain) до [481823](https://learnmeabitcoin.com/explorer/481823#blockchain)|SegWit зафиксирован|
|24.08.2017, 01:57:37|[481824](https://learnmeabitcoin.com/explorer/481824#blockchain) и далее|SegWit активирован|

{{% hint info %}}
- Период с 439488 до 441503 был первым сигнальным периодом после начала окна активации.
- 100% блоков между 477792 и 479807 сигнализировали о переходе на SegWit. Вы можете посмотреть блоки и их сигналы здесь: [segwit-signals-version-bits.txt](https://learnmeabitcoin.com/beginners/guide/segwit/segwit-signals-version-bits.txt) (второй бит справа сигнализирует о поддержке SegWit).
- Существует промежуток в один период между корректировками сложности, когда софтфорк "зафиксирован", прежде чем он будет активирован.
{{% /hint %}}

### Почему решение об активации принимали _майнеры_?

Если вы хотите, чтобы софтфорк был успешным, вам нужно, чтобы большинство майнеров добывали блоки "нового" типа.

Это необходимо для того, чтобы блокчейн с "новыми" блоками опередил любой блокчейн, который может быть создан на основе "старых" блоков (от необновленных майнинг-узлов, которые всё ещё добывают блоки "старого образца").

В результате "новый" блокчейн будет строиться быстрее, чем любой блокчейн, создаваемый из "старых" блоков, поэтому все узлы, естественно, примут одну и ту же самую длинную цепочку:

{{% image "/img/segwit/17-ru.png" %}}
_Большинство майнинговых мощностей позволяет всем участникам сети работать с одной цепочкой._
{{% /image %}}

Таким образом, наличие подавляющего большинства майнеров обеспечивает плавное обновление, поскольку гарантирует, что вся сеть придет к одной версии блокчейна.

{{% hint info %}}
Не обязательно, чтобы майнеры были наиболее компетентной группой для принятия решений при софтфорке; скорее, они _необходимы_ для обеспечения плавного обновления для всей сети.
{{% /hint %}}

## Что произойдет, если я не запущу обновление SegWit?

Если вы используете старую версию узла (например, [Bitcoin Core v0.13.0](https://github.com/bitcoin/bitcoin/blob/master/doc/release-notes/release-notes-0.13.0.md) или ниже), все узлы SegWit, к которым вы подключены, будут удалять все witness-данные из транзакций, прежде чем отправить их вам.

{{% image "/img/segwit/18-ru.png" /%}}

Это означает следующее:

- Вы будете получать те же транзакции, что и все остальные.
- Если вы получите транзакцию SegWit, вы увидите _движение_ биткоинов, но не увидите никаких данных _кода разблокировки_.

Так что, по сути, ваш узел получит "облегченную" версию SegWit-транзакций.

## Как обновиться?

- **Bitcoin Core** – Убедитесь, что вы используете версию [0.13.1](https://github.com/bitcoin/bitcoin/blob/master/doc/release-notes/release-notes-0.13.1.md) или выше.
- **Другие кошельки** – Почти все современные кошельки на сегодняшний день поддерживают транзакции SegWit.

SegWit существует уже так давно, что вряд ли вы столкнетесь с программным обеспечением, которое его не поддерживает (если только оно не очень старое).

## Источники

- [BIP 141 (Уровень консенсуса)](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki)
- [BIP 144 (Взаимодействие между узлами)](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki)
- [Bitcoin.org – Преимущества Segregated Witness](https://bitcoincore.org/en/2016/01/26/segwit-benefits/)

### Спасибо

- [Питеру Вюлле](https://github.com/sipa) – за объяснение [структуры данных SegWit-транзакций](https://bitcoin.stackexchange.com/questions/49097/what-does-a-segregated-witness-transaction-look-like) (помимо всего прочего).
- [Грегори Максвеллу](https://github.com/gmaxwell) и [Люку Дашеру](https://github.com/luke-jr) – за объяснение параметра [веса блока](https://www.reddit.com/r/Bitcoin/comments/5e7a8n/what_will_be_the_block_size_limit_if_segwit/).

### Дополнительные материалы

- [Объяснение Segregated Witness](https://www.youtube.com/watch?v=DzBAG2Jp4bg) – Хорошее объяснение SegWit в видеоформате с полезными визуализациями изменений в структуре данных транзакций.

[^1]: Я получил эту цифру в 60%, пробежавшись по файлам [blk.dat](https://learnmeabitcoin.com/technical/block/blkdat/), сложив данные `scriptSig` для всех транзакций в блоке, и сравнив их с общим размером блока. Я не проводил исчерпывающих тестов, но 60% кажется справедливым средним значением. Например, вот результат для [blk00700.dat](https://learnmeabitcoin.com/beginners/guide/segwit/blk00700_scriptsig.txt).

### Поддержите переводчика

Поддержать переводчика можно, отправив немного сат в сети Лайтнинг:

{{% image "/img/btclinux-ln-qr.jpg" %}}
_LNURL1DP68GURN8GHJ7MRW9E6XJURN9UH8WETVDSKKKMN0WAHZ7MRWW4EXCUP0X9UX2VENXDJN2CTRXSUN2VE3XGCRQPNAPC6_
{{% /image %}}