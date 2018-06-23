---
title: Támogatott nyelvek szöveg Analytics API - Azure kognitív szolgáltatások |} Microsoft Docs
description: Általánosan elérhető listája és preview nyelvi támogatása a szöveg Analytics API-műveleteket. Véleményeket elemzés, a kulcs kifejezés kinyerési és a nyelvi észlelési vonatkozik.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349407"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>A szöveg Analytics API támogatott nyelvek

Ez a cikk ismerteti az egyes műveletek támogatott nyelvek: céggel kapcsolatos véleményeket elemzés, a kulcs kifejezés kivonása és a nyelvi észlelése.

## <a name="language-detection"></a>Nyelvfelismerés

A szöveg Analytics API legfeljebb 120 különböző nyelveken képes észlelni. Nyelvi észlelés a "script" nyelvének adja vissza. Például az a kifejezés "Van egy kutya" ad vissza `en` helyett `en-US`. A csak különleges esetben kínai, ahol a nyelvi észlelési funkció visszaadható `zh_CHS` vagy `zh_CHT` Ha megállapíthatja, hogy a parancsfájl a megadott szöveg megadott. Ha a parancsfájl nem azonosíthatók, kínai dokumentum helyzetekben, ad vissza egyszerűen `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Véleményeket elemzés, kulcs kifejezés kivonása és entitás csatolása

Véleményeket elemzés, kulcs kifejezés kivonása és entitás csatolása támogatott nyelvek listáját esetén több szelektív, mivel a lekérdezések kifinomultabb további nyelvek nyelvi szabályainak megfelelően.

## <a name="language-list-and-status"></a>Nyelvek listája és állapota

Nyelvi támogatás kezdetben eldöntése Preview diplomaosztás általánosan elérhető (GA) állapotát, és a teljes szöveges Analytics szolgáltatás egymástól függetlenül. Akkor lehet szöveg Analytics API tér át általánosan elérhető, noha az előzetesben maradjon nyelvekhez.

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
| Norvég (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| lengyel      | `pl`          | ✔ \*     |  ✔          |             |     |
| portugál (általános) | `pt-PT`| ✔        |  ✔          |       |`pt` is elfogadva|
| portugál (brazíliai)   | `pt-BR`|          |  ✔   |         |     |
| orosz     | `ru`          | ✔ \*     | ✔           |             |     |
| spanyol     | `es`          | ✔        | ✔           |     |     |
| svéd     | `sv`          | ✔ \*     | ✔           |             |     |
| török     | `tr`          | ✔ \*     |             |             |     |

\* azt jelzi, Preview nyelvi támogatás

## <a name="see-also"></a>Lásd még

[Kognitív szolgáltatások dokumentációja lap](https://docs.microsoft.com/azure/cognitive-services/)   
[Kognitív Services termék oldalát](https://azure.microsoft.com/services/cognitive-services/)
