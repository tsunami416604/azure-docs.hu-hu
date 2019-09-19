---
title: Nyelvi támogatás – Text Analytics API
titleSuffix: Azure Cognitive Services
description: 'A Text Analytics API által támogatott természetes nyelvek listája. Ez a cikk azt ismerteti, hogy az egyes műveletek milyen nyelveket támogatnak: a hangulat elemzése, a kulcsfontosságú kifejezés kinyerése, a nyelvfelismerés és az entitások felismerése.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 953699793d81485e3828b9fb46de8523d2b7674e
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130007"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvi és területi támogatása

Ez a cikk ismerteti, hogy az egyes műveletek milyen nyelveket támogatnak: a hangulat elemzése, a fő kifejezés kibontása, a nyelvfelismerés és a nevesített entitások felismerése.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes felderíteni.  Nyelvfelismerés a nyelv "szkriptjét" adja vissza. Ha például a "Nekem van egy kutyám" kifejezés, akkor a `en` `en-US`helyett a értéket fogja visszaadni. Az egyetlen különleges eset a kínai, ahol a nyelvi észlelési képesség vissza `zh_CHS` fog `zh_CHT` térni, vagy ha meg tudja határozni a parancsfájlt a megadott szöveg alapján. Azokban az esetekben, amikor egy kínai dokumentumhoz nem azonosítható egy adott parancsfájl, a rendszer `zh`egyszerűen visszaadja.

Nem tesszük közzé a funkcióhoz tartozó nyelvek pontos listáját, de számos nyelvet, változatot, dialektust és bizonyos regionális/kulturális nyelvet képes felderíteni. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető `unknown`nyelvek válasza a következő:.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Hangulatelemzés, Kulcsszókeresés és elnevezett entitások felismerése

Az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez és az entitások felismeréséhez a támogatott nyelvek listája sokkal szelektívebb, mivel az elemzők finomítása a további nyelvek nyelvi szabályainak betartása érdekében történik. Az entitások teljes készletének [](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) támogatása jelenleg a következő nyelvekre korlátozódik: 
* Angol
* Egyszerűsített kínai
* francia
* német
* spanyol

A többi `Person`nyelvhez `Organization` csak a `Location` és a nevesített entitásokat adja vissza a rendszer.

## <a name="language-list-and-status"></a>Nyelvi lista és állapot

A nyelvi támogatást kezdetben előzetes verzióban, az általánosan elérhető (GA) állapotra kell bevezetni, amely egymástól függetlenül, és a Text Analytics szolgáltatás teljes egészében. Lehetséges, hogy a nyelvek előzetes verzióban maradnak, még akkor is, ha a Text Analytics API általánosan elérhetővé vált.

| Nyelv    | Nyelvkód | Hangulat | Kulcskifejezések | Megnevezett entitások felismerése |   Megjegyzések  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| arab      | `ar`          |           |             | ✔ \*                     | |
| cseh       | `cs`          |           |             | ✔ \*                     | |
| Egyszerűsített kínai | `zh-hans`| ✔ \***     |             | ✔         |    |
| Kínai – hagyományos | `zh-hant`| ✔ \***     |             |          |    |
| dán      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| holland       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Angol     | `en`          | ✔ \***       | ✔           |  ✔ \*\*     |      |
| finn     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| francia      | `fr`          | ✔ \***       | ✔           |  ✔            |     |
| német      | `de`          | ✔ \*     | ✔           |  ✔           |     |
| görög       | `el`          | ✔ \*     |             |            |     |
| magyar   | `hu`          |           |             |  ✔ \*          |     | 
| olasz     | `it`          | ✔ \***     | ✔           |  ✔ \*           |     |
| japán    | `ja`          | ✔ \***         | ✔           |  ✔ \*          |     |
| koreai      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norvég (nyelven) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| lengyel      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| portugál (általános) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt`szintén elfogadva|
| portugál (brazíliai)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| orosz     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| spanyol     | `es`          | ✔        | ✔           |   ✔ \*\*      |     | 
| svéd     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| török     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\*A nyelvi támogatás előzetes verzióban érhető el

\*\*A [nevesített entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) és az entitások összekapcsolása egyaránt elérhető ehhez a nyelvhez. [](how-tos/text-analytics-how-to-entity-linking.md#entity-linking)  

\** * Elérhető [Hangulatelemzés v3 nyilvános előzetes](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview) verzióban

## <a name="see-also"></a>Lásd még

[Cognitive Services dokumentációs oldal](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
