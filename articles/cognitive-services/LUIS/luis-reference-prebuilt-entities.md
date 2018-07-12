---
title: A LUIS előre összeállított entitások referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk az előre összeállított entitások, a Language Understanding (LUIS) részét képező tartalmaz.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/11/2018
ms.author: v-geberr
ms.openlocfilehash: 680acb5ed808750312da46b6bf4ebf3afca1bea5
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969976"
---
# <a name="entities-per-culture"></a>Entitások kultúránként

Language Understanding (LUIS) biztosít az előre összeállított entitások. Ha az alkalmazás tartalmaz egy előre összeállított entitások, LUIS a kapcsolódó entitás előrejelzési a végpont-válasz tartalmazza. Valamennyi példa utterances is az a entitás vannak ellátva. Előre összeállított entitások viselkedését **nem** módosítható. Ha nincs másképp jelölve, előre összeállított entitások érhető el minden, a LUIS alkalmazás területi beállítások (kulturális környezetek). Az alábbi táblázat az előre összeállított entitások minden kulturális környezet által támogatott.

Előre összeállított entitások   |   angol (Egyesült Államok)<br>```En-us```   |   francia (Franciaország)<br>```fr-FR```   |   olasz (Olaszország)<br>```it-IT```   |   spanyol (spanyolországi)<br>```es-ES```   |   kínai<br>```zh-CN```   |   német<br>```de-DE```   |   portugál (brazíliai)<br>```pt-BR```   |   japán (Japán)<br>```ja-JP```   |   koreai (Korea)<br>```ko-kr```   | francia (Kanada)<br>```fr-CA```   |   spanyol (Mexikó)<br>```es-MX```   |   holland (Hollandia)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Tekintse meg a megjegyzéseit a [elavult előre összeállított entitások](luis-reference-prebuilt-deprecated.md)

Nem áll rendelkezésre, portugál (brazíliai) – az összes alárendelt kulturális környezetek KeyPhrase ```pt-BR```.

<!--
## Examples of prebuilt entities in en-us culture
The following table lists prebuilt entities with example data and the return values.

Prebuilt entity   |   Example utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Hozzájárul az előre összeállított entitások kulturális környezetek
Az előre összeállított entitások lettek kifejlesztve, a felismerő szöveges nyílt forráskódú projekt. Adjon [közreműködés](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt a kulturális környezet pénznem vonatkozó példákat tartalmaz. 

## <a name="next-steps"></a>További lépések

További információ a [szám](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), és [pénznem](luis-reference-prebuilt-currency.md) entitásokat. 