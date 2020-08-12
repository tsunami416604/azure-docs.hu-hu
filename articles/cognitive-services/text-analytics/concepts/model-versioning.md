---
title: A modell verziójának meghatározása a Text Analytics v3-ban
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan határozhatja meg az adatain használt Text Analytics API modellt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 7d008dc0f7ed90397ac07e12320d861b107f0f46
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120932"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>A modell verziószámozása a Text Analytics API

A Text Analytics API 3. verziója lehetővé teszi, hogy kiválassza a modell azon verzióját, amelyet az adatain használni fog. A választható `model-version` paraméter használatával kiválaszthatja a modell verzióját az API-kérelmekben. Például: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Ha ez a paraméter nincs megadva, az API alapértelmezés szerint a legújabb stabil verziót fogja megadni. 

## <a name="available-versions"></a>Elérhető verziók

Az alábbi táblázat segítségével megkeresheti, hogy az egyes végpontok milyen modell-verziókat támogatnak.


| Végpont                        | Támogatott verziók                                     | legújabb verzió |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Ezen modellek frissítéseiről a Újdonságok című útmutatóban talál [részleteket.](../whats-new.md)

## <a name="text-analytics-for-health"></a>Egészségügyi Text Analytics

Az [állapot](../how-tos/text-analytics-for-health.md) -tároló Text Analytics a fenti API-végpontok helyett a modell eltérő verzióját használja.  Vegye figyelembe, hogy a tárolók rendszerképében csak egy modell verziója érhető el.

| Végpont                        | Tároló képcímkéje                     | Modell verziója |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.013150001-amd64-preview`vagy legújabb | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-07-24`  |




## <a name="next-steps"></a>További lépések

* [A Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
