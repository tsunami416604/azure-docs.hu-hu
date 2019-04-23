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
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4f1ce8fd44a501f594f3093789d1ef03e664d018
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008494"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvéhez és régiójához támogatása

Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: vélemények elemzése, kulcsszókeresés, nyelvfelismerés és nevesített entitások felismerése.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API felismeri a legfeljebb 120 különböző nyelvekhez. Nyelv észlelése "parancsfájl" egy nyelv adja vissza. Például a kifejezés a "Javaslatom van egy kutya", a rendszer visszaadja `en` helyett `en-US`. A csak speciális funkcióban kínai, ahol a language detection funkció visszalép `zh_CHS` vagy `zh_CHT` Ha képes meghatározni a parancsfájl a megadott szöveg megadott. Olyan esetekben, ahol egy adott parancsfájl nem lehet azonosítani az egy kínai dokumentumot, hogy vissza fogja küldeni egyszerűen `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Hangulatelemzést, Kulcsszókeresést és nevesített entitások felismerése

Hangulatelemzést, kulcsszókeresést és entitások felismerése támogatott nyelvek listáját a rendszer több szelektív, mivel az elemzők számát finomított további nyelveket nyelvi szabályainak megfelelően.

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

\*\* Megnevezett entitások felismerése és [entitáskapcsolás](how-tos/text-analytics-how-to-entity-linking.md) is ezen a nyelven érhető el.    

## <a name="see-also"></a>Lásd még

[Cognitive Services – dokumentáció lap](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
