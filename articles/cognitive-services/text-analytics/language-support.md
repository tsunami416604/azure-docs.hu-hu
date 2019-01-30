---
title: Nyelvi támogatás – szövegelemzési API
titleSuffix: Azure Cognitive Services
description: 'A szövegelemzési API által támogatott természetes nyelvek listáját. Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: vélemények elemzése, kulcsszókeresés, nyelvfelismerés és entitások felismerése.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: ec1858256c942fd50818a214de426fd00c3c2c88
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216931"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvéhez és régiójához támogatása

Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: hangulatelemzést, kulcsszókeresést és nyelvfelismerést.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API felismeri a legfeljebb 120 különböző nyelvekhez. Nyelv észlelése "parancsfájl" egy nyelv adja vissza. Például a kifejezés a "Javaslatom van egy kutya", a rendszer visszaadja `en` helyett `en-US`. A csak speciális funkcióban kínai, ahol a language detection funkció visszalép `zh_CHS` vagy `zh_CHT` Ha képes meghatározni a parancsfájl a megadott szöveg megadott. Olyan esetekben, ahol egy adott parancsfájl nem lehet azonosítani az egy kínai dokumentumot, hogy vissza fogja küldeni egyszerűen `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Hangulatelemzést, Kulcsszókeresést és entitások felismerése

Hangulatelemzést, kulcsszókeresést és entitások felismerése támogatott nyelvek listáját a rendszer több szelektív, mivel az elemzők számát finomított további nyelveket nyelvi szabályainak megfelelően.

## <a name="language-list-and-status"></a>Nyelvek listája és állapota

Nyelvi támogatás kezdetben integráljuk általánosan elérhető (GA) állapotra, egymástól, és a teljes Text Analytics szolgáltatás előzetes verzióban elérhető jelennek meg. Továbbra is általánosan elérhető a szövegelemzési API átmenetek során még előzetes verzióként elérhető nyelvek lehetőség.

| Nyelv    | Nyelvkód | Hangulat | Kulcskifejezések | Entitások felismerése |   Megjegyzések  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| dán      | `da`          | ✔ \*     | ✔           |             |     |
| holland       | `nl`          | ✔ \*     | ✔          |             |     |
| Angol     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| finn     | `fi`          | ✔ \*     | ✔           |             |     |
| francia      | `fr`          | ✔        | ✔           |             |     |
| német      | `de`          | ✔ \*     | ✔           |            |     |
| görög       | `el`          | ✔ \*     |             |            |     |
| olasz     | `it`          | ✔ \*     | ✔           |             |     |
| japán    | `ja`          |          | ✔           |            |     |
| koreai      | `ko`          |          | ✔           |            |     |
| Norvég (bokmal) | `no`          | ✔ \*     |  ✔          |             |     |
| lengyel      | `pl`          | ✔ \*     |  ✔          |             |     |
| portugál (általános) | `pt-PT`| ✔        |  ✔          |       |`pt` Emellett elfogadva|
| portugál (brazíliai)   | `pt-BR`|          |  ✔   |         |     |
| orosz     | `ru`          | ✔ \*     | ✔           |             |     |
| spanyol     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| svéd     | `sv`          | ✔ \*     | ✔           |             |     |
| török     | `tr`          | ✔ \*     |             |             |  |

\* azt jelzi, hogy a nyelvi támogatás az előzetes verzió

\*\* Entitások kinyeréséhez spanyol csak érhető el a [(2.1-es – előzetes verzió)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Lásd még

[Cognitive Services – dokumentáció lap](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
