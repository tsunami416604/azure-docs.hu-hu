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
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219279"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvi és területi támogatása

Ez a cikk ismerteti, hogy az egyes műveletek milyen nyelveket támogatnak: a hangulat elemzése, a fő kifejezés kibontása, a nyelvfelismerés és a nevesített entitások felismerése.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes felderíteni.  Nyelvfelismerés a nyelv "szkriptjét" adja vissza. Ha például a "Nekem van egy kutyám" kifejezés, akkor a helyett `en` a értéket `en-US`fogja visszaadni. Az egyetlen különleges eset a kínai, ahol a nyelvi észlelési képesség vissza `zh_CHS` fog `zh_CHT` térni, vagy ha meg tudja határozni a parancsfájlt a megadott szöveg alapján. Azokban az esetekben, amikor egy kínai dokumentumhoz nem azonosítható egy adott parancsfájl, a rendszer `zh`egyszerűen visszaadja.

Nem tesszük közzé a funkcióhoz tartozó nyelvek pontos listáját, de számos nyelvet, változatot, dialektust és bizonyos regionális/kulturális nyelvet képes felderíteni. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Hangulatelemzés, Kulcsszókeresés és elnevezett entitások felismerése

Az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez és az entitások felismeréséhez a támogatott nyelvek listája sokkal szelektívebb, mivel az elemzők finomítása a további nyelvek nyelvi szabályainak betartása érdekében történik. Az elnevezett Entity Recognition v2-ben az [entitások](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) teljes készletének támogatása jelenleg a következő nyelvekre korlátozódik: 
* Angol
* Egyszerűsített kínai
* Francia
* Német
* Spanyol

A többi `Person`nyelvhez csak a `Location` és `Organization` a nevesített entitásokat adja vissza a rendszer.

## <a name="language-list-and-status"></a>Nyelvi lista és állapot

A nyelvi támogatást kezdetben előzetes verzióban, az általánosan elérhető (GA) állapotra kell bevezetni, amely egymástól függetlenül, és a Text Analytics szolgáltatás teljes egészében. Lehetséges, hogy a nyelvek előzetes verzióban maradnak, még akkor is, ha a Text Analytics API általánosan elérhetővé vált.

> [!NOTE]
> Az elnevezett Entity Recognition (megnevezett entitások) v3 nyilvános előzetes verziójának részletes nyelvi támogatását lásd: [nevesített entitások típusai](named-entity-types.md).

| Nyelv              | Nyelvkód | Hangulat | Legfontosabb kifejezések | Megnevezett entitások felismerése | Entitás összekapcsolása |       Megjegyzések        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arab                |     `ar`      |           |             |           ✔\*           |                |                    |
| Cseh                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Egyszerűsített kínai    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`szintén elfogadva                   |
| Kínai – hagyományos   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Dán                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Holland                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Angol               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finn               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francia                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Német                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Görög                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Magyar             |     `hu`      |           |             |           ✔\*           |                |                    |
| Olasz               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japán              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Koreai                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norvég (nyelven)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`szintén elfogadva                   |
| Lengyel                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portugál (Portugália) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`szintén elfogadva |
| Portugál (Brazília)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Orosz               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Spanyol               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Svéd               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Török               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*A nyelvi támogatás előzetes verzióban érhető el

\** A (z) [Hangulatelemzés v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) és/vagy [elnevezett Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) nyilvános előzetes verzióban is elérhető.

## <a name="see-also"></a>Lásd még

[Cognitive Services dokumentációs oldal](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
