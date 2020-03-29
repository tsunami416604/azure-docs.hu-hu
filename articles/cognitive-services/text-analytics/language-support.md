---
title: Nyelvi támogatás – Text Analytics API
titleSuffix: Azure Cognitive Services
description: 'A Text Analytics API által támogatott természetes nyelvek listája. Ez a cikk ismerteti, hogy mely nyelvek támogatottak az egyes műveletekhez: hangulatelemzés, kulcskifejezések kinyerése, nyelvfelismerés és entitásfelismerés.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219279"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvi és régiótámogatása

Ez a cikk ismerteti, hogy mely nyelvek támogatottak az egyes műveletekhez: hangulatelemzés, kulcskifejezések kinyerése, nyelvfelismerés és elnevezett entitásfelismerés.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes észlelni.  A nyelvfelismerés egy nyelv "parancsfájlját" adja vissza. Például, részére a mondat " nekem van `en` egy `en-US`kutya" ez akarat visszatér helyett . Az egyetlen különleges eset a kínai, ahol `zh_CHS` `zh_CHT` a nyelvfelismerési képesség visszatér, vagy ha meg tudja határozni a szkriptet a megadott szöveg alapján. Olyan helyzetekben, amikor egy adott szkript nem azonosítható egy `zh`kínai dokumentumhoz, egyszerűen vissza fog térni.

Nem tesszük közzé a szolgáltatás hoz a nyelvek pontos listáját, de számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes észlelni. 

Ha a tartalmat ritkábban használt nyelven fejezi konditálják, megpróbálhatja a Nyelvfelismerés t, és ellenőrizheti, hogy az visszaad-e egy kódot. A nem észlelhető nyelvekre `unknown`a válasz a.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Hangulatelemzés, kulcskifejezés-kinyerés és elnevezett entitásfelismerés

A hangulatelemzés, a kulcskifejezések kinyerése és az entitásfelismerés esetében a támogatott nyelvek listája szelektívebb, mivel az analizátorok finomítva vannak a további nyelvek nyelvi szabályainak megfelelően. A Named Entity Recognition v2 csoportban az [entitástípusok](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) teljes készletének támogatása jelenleg a következő nyelvekre korlátozódik: 
* Angol
* Kínai-egyszerűsített
* Francia
* Német
* Spanyol

A `Person`többi `Location` `Organization` nyelvhez csak a és a megnevezett entitások lesznek visszaadva.

## <a name="language-list-and-status"></a>Nyelvi lista és állapot

A nyelvi támogatás kezdetben előzetes verzióban jelenik meg, és az általánosan elérhető (GA) státuszba kerül, egymástól és a Text Analytics szolgáltatás egészétől függetlenül. A nyelvek előzetes verzióban is megtekinthetők maradhatnak, még akkor is, ha a Text Analytics API átvált az általánosan elérhetővé.

> [!NOTE]
> A névvel ellátott entitásfelismerés(NER) 3-as verzió nyilvános előzetes verziójának részletes nyelvi támogatásáról a [Named entity types](named-entity-types.md)című témakörben van.

| Nyelv              | Nyelvkód | Hangulat | Kulcsfontosságú kifejezések | Megnevezett entitások felismerése | Entitáscsatolás |       Megjegyzések        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arab                |     `ar`      |           |             |           ✔\*           |                |                    |
| Cseh                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Kínai-egyszerűsített    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`is elfogadott                   |
| Kínai-hagyományos   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
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
| Norvég (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`is elfogadott                   |
| Lengyel                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portugál (Portugália) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`is elfogadott |
| Portugál (Brazília)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Orosz               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Spanyol               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Svéd               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Török               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*A nyelvi támogatás előzetes verzióban érhető el

\** Is elérhető a [hangulat elemzés v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) és / vagy [named entity elismerés v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) nyilvános előnézetek.

## <a name="see-also"></a>Lásd még

[Cognitive Services dokumentációja lap](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
