---
title: Előre összeállított entitások - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az előre összeállított entitások, a Language Understanding (LUIS) részét képező tartalmaz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: bc23f2e5d8304400802c74093d4d78a1fcd8cf22
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219485"
---
# <a name="entities-per-culture"></a>Entitások kultúránként

Language Understanding (LUIS) biztosít az előre összeállított entitások. Ha az alkalmazás tartalmaz egy előre összeállított entitások, LUIS a kapcsolódó entitás előrejelzési a végpont-válasz tartalmazza. Valamennyi példa utterances is az a entitás vannak ellátva. Előre összeállított entitások viselkedését **nem** módosítható. Ha nincs másképp jelölve, előre összeállított entitások érhető el minden, a LUIS alkalmazás területi beállítások (kulturális környezetek). Az alábbi táblázat az előre összeállított entitások minden kulturális környezet által támogatott.

Előre összeállított entitások   |   angol (Egyesült Államok)<br>```En-us```   |   francia (Franciaország)<br>```fr-FR```   |   olasz (Olaszország)<br>```it-IT```   |   spanyol (spanyolországi)<br>```es-ES```   |   kínai<br>```zh-CN```   |   német<br>```de-DE```   |   portugál (brazíliai)<br>```pt-BR```   |   japán (Japán)<br>```ja-JP```   |   koreai (Korea)<br>```ko-kr```   | francia (Kanada)<br>```fr-CA```   |   spanyol (Mexikó)<br>```es-MX```   |   holland (Hollandia)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   |    -   |    -   |    -   |    ✔   |    -   |    -   |    -   |   -   |   -   |   -   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Tekintse meg a megjegyzéseit a [elavult előre összeállított entitások](luis-reference-prebuilt-deprecated.md)

Nem áll rendelkezésre, portugál (brazíliai) – összes tápfolyadékok cseréjét KeyPhrase ```pt-BR```.

## <a name="contribute-to-prebuilt-entity-cultures"></a>Hozzájárul az előre összeállított entitások kulturális környezetek
Az előre összeállított entitások lettek kifejlesztve, a felismerő szöveges nyílt forráskódú projekt. [Közreműködés](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt a kulturális környezet pénznem vonatkozó példákat tartalmaz. 

GeographyV2 és PersonName nem szerepelnek a szöveges felismerő projekt. Az alábbi előre összeállított entitások problémáival kapcsolatban, nyissa meg a [támogatási kérelem](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>További lépések

További információ a [szám](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), és [pénznem](luis-reference-prebuilt-currency.md) entitásokat. 
