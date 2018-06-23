---
title: Előre elkészített LUIS entitások referencia - Azure |} Microsoft Docs
titleSuffix: Azure
description: Ez a cikk a nyelvi ismertetése (LUIS) szereplő előre elkészített entitások tartalmaz.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 7ce50e4c0be605e1700a2c18533cb087384f524c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316884"
---
# <a name="entities-per-culture"></a>Entitás / kulturális környezet

Nyelvi ismertetése (LUIS) előre elkészített entitásokat biztosít. Ha az alkalmazás egy előre elkészített entitást tartalmaz, LUIS megfelelő entitás előrejelzését a végpont-válasz tartalmazza. Az összes példában utterances is van tüntetve az entitás. Az előre elkészített entitások viselkedésének **nem** módosítható. Ha nincs másként jelezve, előre elkészített entitások érhető el minden LUIS alkalmazás nyelv (kulturális környezetek). Az alábbi táblázat az előre elkészített entitások minden kulturális környezethez tartozó támogatott.

Előre elkészített entitás   |   angol (Egyesült Államok)<br>```En-us```   |   francia (Franciaország)<br>```fr-FR```   |   olasz (Olaszország)<br>```it-IT```   |   spanyol (spanyolországi)<br>```es-ES```   |   kínai<br>```zh-CN```   |   német<br>```de-DE```   |   portugál (brazíliai)<br>```pt-BR```   |   japán (Japán)<br>```ja-JP```   |   koreai (Korea)<br>```ko-kr```   | francia (Kanada)<br>```fr-CA```   |   spanyol (Mexikó)<br>```es-MX```   |   holland (Hollandia)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>Amerikai dollár<br>törtrész (pl.: értékhez)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információkat (pl.: bit/bájt)<br>hossza (pl.: mérő)<br>sebesség (pl.: mérföldes / óra)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Sorszámát](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Tekintse meg a megjegyzéseket az [elavult előre elkészített entitások](luis-reference-prebuilt-deprecated.md)

## <a name="examples-of-prebuilt-entities-in-en-us-culture"></a>Példák az előre elkészített entitások az en-us kulturális környezet
Az alábbi táblázatban példa adatokkal előre elkészített entitásokat és a visszatérési érték.

Előre elkészített entitás   |   Példa utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | Lásd: [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | Lásd: [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
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


## <a name="contribute-to-prebuilt-entity-cultures"></a>Hozzájárul az előre elkészített entitás kulturális környezetek
Az előre elkészített entitások fejlesztett a felismerő szöveges nyílt forráskódú projektben. Adjon [közre](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt kulturális környezet pénznem példákat. 

## <a name="next-steps"></a>További lépések

További tudnivalók a [szám](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), és [pénznem](luis-reference-prebuilt-currency.md) entitásokat. 