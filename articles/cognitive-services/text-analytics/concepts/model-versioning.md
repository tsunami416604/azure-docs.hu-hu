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
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: c9c598d385768e604d9b42e3ea055629e6ae2866
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108549"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>A modell verziószámozása a Text Analytics API

A Text Analytics API 3. verziója lehetővé teszi, hogy kiválassza a modell azon verzióját, amelyet az adatain használni fog. A választható `model-version` paraméter használatával kiválaszthatja a modell verzióját az API-kérelmekben. Példa: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Ha ez a paraméter nincs megadva, az API alapértelmezés szerint a legújabb stabil verziót fogja megadni. 

## <a name="available-versions"></a>Elérhető verziók

Az alábbi táblázat segítségével megkeresheti, hogy az egyes végpontok milyen modell-verziókat támogatnak.


| Végpont                        | Támogatott verziók                       | legújabb verzió |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Ezen modellek frissítéseiről a Újdonságok című útmutatóban talál [részleteket.](../whats-new.md)

## <a name="text-analytics-for-health"></a>Text Analytics állapota

Az [állapot](../how-tos/text-analytics-for-health.md) -tároló Text Analytics a fenti API-végpontok helyett a modell eltérő verzióját használja.  Vegye figyelembe, hogy a tárolók rendszerképében csak egy modell verziója érhető el.

| Végpont                        | Tároló képcímkéje                     | Modell verziója |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview`vagy legújabb | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## <a name="next-steps"></a>Következő lépések

* [Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
