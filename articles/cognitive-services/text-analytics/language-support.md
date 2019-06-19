---
title: Nyelvi támogatás – szövegelemzési API
titleSuffix: Azure Cognitive Services
description: 'A szövegelemzési API által támogatott természetes nyelvek listáját. Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: vélemények elemzése, kulcsszókeresés, nyelvfelismerés és entitások felismerése.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 704a1193eb47f9346900c6c8a003122c30c8ab44
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203969"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvéhez és régiójához támogatása

Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: vélemények elemzése, kulcsszókeresés, nyelvfelismerés és nevesített entitások felismerése.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API számos különféle nyelveken, variantní hodnoty, dialektusok és néhány területi kulturális nyelv észlelését.  Nyelv észlelése "parancsfájl" egy nyelv adja vissza. Például a kifejezés a "Javaslatom van egy kutya", a rendszer visszaadja `en` helyett `en-US`. A csak speciális funkcióban kínai, ahol a language detection funkció visszalép `zh_CHS` vagy `zh_CHT` Ha képes meghatározni a parancsfájl a megadott szöveg megadott. Olyan esetekben, ahol egy adott parancsfájl nem lehet azonosítani az egy kínai dokumentumot, hogy vissza fogja küldeni egyszerűen `zh`.

Nem tesszük közzé ezt a szolgáltatást a nyelvek pontos listáját, de képes észlelni a számos különféle nyelveken, variantní hodnoty, dialektusok és néhány területi kulturális nyelv. 

Ha a tartalom kevésbé gyakran használt nyelven, megpróbálhatja nyelvfelismerés megtekintheti, ha egy kódot ad vissza. A válasz nem észlelhető nyelvek `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Hangulatelemzést, Kulcsszókeresést és nevesített entitások felismerése

Hangulatelemzést, kulcsszókeresést és entitások felismerése támogatott nyelvek listáját a rendszer több szelektív, mivel az elemzők számát finomított további nyelveket nyelvi szabályainak megfelelően. A teljes körű támogatása [entitástípusok](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) jelenleg csak az alábbi nyelveken: 
* Angol
* Egyszerűsített kínai
* francia
* német
* spanyol

Csak a `Person`, `Location` és `Organization` nevesített entitásokhoz a többi nyelvet adja vissza.

## <a name="language-list-and-status"></a>Nyelvek listája és állapota

Nyelvi támogatás kezdetben integráljuk általánosan elérhető (GA) állapotra, egymástól, és a teljes Text Analytics szolgáltatás előzetes verzióban elérhető jelennek meg. Továbbra is általánosan elérhető a szövegelemzési API átmenetek során még előzetes verzióként elérhető nyelvek lehetőség.

| Nyelv    | Nyelvkód | Hangulat | Kulcskifejezések | Megnevezett entitások felismerése |   Megjegyzések  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| arab      | `ar`          |           |             | ✔ \*                     | |
| cseh       | `cs`          |           |             | ✔ \*                     | |
| Egyszerűsített kínai | `zh-CN`|           |             | ✔ \*        |    |
| dán      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| holland       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Angol     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| finn     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| francia      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| német      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| görög       | `el`          | ✔ \*     |             |            |     |
| magyar   | `hu`          |           |             |  ✔ \*          |     | 
| olasz     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| japán    | `ja`          |          | ✔           |  ✔ \*          |     |
| koreai      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norvég (bokmal) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| lengyel      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| portugál (általános) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` Emellett elfogadva|
| portugál (brazíliai)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| orosz     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| spanyol     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| svéd     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| török     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Nyelvi támogatás az előzetes verzióban

\*\* [Megnevezett entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) és [entitáskapcsolás](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) is ezen a nyelven érhető el.    

## <a name="see-also"></a>Lásd még

[Cognitive Services – dokumentáció lap](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
