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
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 529568cf4f667e72b0b4fb7ba83d0b85c4a90341
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499998"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvi és területi támogatása

Ez a cikk ismerteti, hogy az egyes műveletek milyen nyelveket támogatnak: a hangulat elemzése, a fő kifejezés kibontása, a nyelvfelismerés és a nevesített entitások felismerése.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes felderíteni.  Nyelvfelismerés a nyelv "szkriptjét" adja vissza. Ha például a "Nekem van egy kutyám" kifejezésre, akkor `en-US`helyett `en` fog visszaadni. Az egyetlen különleges eset a kínai, ahol a nyelvfelismerés funkció visszaadja `zh_CHS` vagy `zh_CHT`, ha a megadott szöveg alapján meghatározhatja a parancsfájlt. Olyan helyzetekben, amikor egy kínai dokumentumhoz nem azonosítható egy adott parancsfájl, egyszerűen csak `zh`fog visszatérni.

Nem tesszük közzé a funkcióhoz tartozó nyelvek pontos listáját, de számos nyelvet, változatot, dialektust és bizonyos regionális/kulturális nyelvet képes felderíteni. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Hangulatelemzés, Kulcsszókeresés és elnevezett entitások felismerése

Az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez és az entitások felismeréséhez a támogatott nyelvek listája sokkal szelektívebb, mivel az elemzők finomítása a további nyelvek nyelvi szabályainak betartása érdekében történik. Az elnevezett Entity Recognition v2-ben az [entitások](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition-v2) teljes készletének támogatása jelenleg a következő nyelvekre korlátozódik: 
* Angol
* Egyszerűsített kínai
* francia
* német
* spanyol

A többi nyelvhez csak a `Person`, a `Location` és az `Organization` entitást adja vissza a rendszer.

## <a name="language-list-and-status"></a>Nyelvi lista és állapot

A nyelvi támogatást kezdetben előzetes verzióban, az általánosan elérhető (GA) állapotra kell bevezetni, amely egymástól függetlenül, és a Text Analytics szolgáltatás teljes egészében. Lehetséges, hogy a nyelvek előzetes verzióban maradnak, még akkor is, ha a Text Analytics API általánosan elérhetővé vált.

> [!NOTE]
> Az elnevezett Entity Recognition (megnevezett entitások) v3 nyilvános előzetes verziójának részletes nyelvi támogatását lásd: [nevesített entitások típusai](named-entity-types.md).

| Nyelv              | Nyelvkód | Hangulat | Kulcskifejezések | Megnevezett entitások felismerése | Entitás összekapcsolása |       Megjegyzések        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| arab                |     `ar`      |           |             |           ✔ \*           |                |                    |
| cseh                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Egyszerűsített kínai    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                |                    |
| Kínai – hagyományos   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| dán                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| holland                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Angol               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| finn               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| francia                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| német                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| görög                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| magyar             |     `hu`      |           |             |           ✔ \*           |                |                    |
| olasz               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| japán              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| koreai                |     `ko`      |           |      ✔      |           ✔ \*           |                |                    |
| Norvég (nyelven)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| lengyel                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| portugál (általános) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` is elfogadva |
| portugál (brazíliai)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| orosz               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| spanyol               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| svéd               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| török               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

a \* nyelvi támogatása előzetes verzióban érhető el

a \** a [Hangulatelemzés v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview) és/vagy a [named Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview) nyilvános előzetes verzióban is elérhető.

## <a name="see-also"></a>Lásd még

[Cognitive Services dokumentációs oldal](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
