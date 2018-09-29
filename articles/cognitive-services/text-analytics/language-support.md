---
title: Nyelvi támogatás – szövegelemzési API
titleSuffix: Azure Cognitive Services
description: 'A szövegelemzési API által támogatott természetes nyelvek listáját. Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: hangulatelemzést, kulcsszókeresést és nyelvfelismerést.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 09/25/2018
ms.author: ashmaka
ms.openlocfilehash: e9f466ac6bce98a6a9f2d79a443c9602ca40bb26
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435477"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>A Text Analytics API nyelvéhez és régiójához támogatása

Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: hangulatelemzést, kulcsszókeresést és nyelvfelismerést.

## <a name="language-detection"></a>Nyelvfelismerés

A Text Analytics API felismeri a legfeljebb 120 különböző nyelvekhez. Nyelv észlelése "parancsfájl" egy nyelv adja vissza. Például a kifejezés a "Javaslatom van egy kutya", a rendszer visszaadja `en` helyett `en-US`. A csak speciális funkcióban kínai, ahol a language detection funkció visszalép `zh_CHS` vagy `zh_CHT` Ha képes meghatározni a parancsfájl a megadott szöveg megadott. Olyan esetekben, ahol egy adott parancsfájl nem lehet azonosítani az egy kínai dokumentumot, hogy vissza fogja küldeni egyszerűen `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Hangulatelemzést, Kulcsszókeresést és Entitáskapcsolás

A hangulatelemzést, kulcsszókeresést és entitáskapcsolás támogatott nyelvek listája a következő több szelektív, mivel az elemzők számát finomított további nyelveket nyelvi szabályainak megfelelően.

## <a name="language-list-and-status"></a>Nyelvek listája és állapota

Nyelvi támogatás kezdetben integráljuk általánosan elérhető (GA) állapotra, egymástól, és a teljes Text Analytics szolgáltatás előzetes verzióban elérhető jelennek meg. Továbbra is általánosan elérhető a szövegelemzési API átmenetek során még előzetes verzióként elérhető nyelvek lehetőség.

| Nyelv    | Nyelvkód | Hangulat | Kulcskifejezések | Entitáskapcsolás |   Megjegyzések  |
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
| spanyol     | `es`          | ✔        | ✔           |     |     |
| svéd     | `sv`          | ✔ \*     | ✔           |             |     |
| török     | `tr`          | ✔ \*     |             |             |     |

\* azt jelzi, hogy a nyelvi támogatás az előzetes verzió

## <a name="see-also"></a>Lásd még

[Cognitive Services – dokumentáció lap](https://docs.microsoft.com/azure/cognitive-services/)   
[A Cognitive Services termékoldala](https://azure.microsoft.com/services/cognitive-services/)
