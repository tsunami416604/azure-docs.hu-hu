---
title: Megnevezett entitások felismeréséhez támogatott kategóriák
titleSuffix: Azure Cognitive Services
description: A Text Analytics API támogatott entitások kategóriáinak megismerése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709544"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Támogatott entitások kategóriái a Text Analytics API v3-ban

Ebből a cikkből megtudhatja, hogy mely entitás-kategóriákat lehet visszaadni, [névvel ellátott entitás-felismeréssel](how-tos/text-analytics-how-to-entity-linking.md) . Az Egypéldányos v 3.1 előzetes verziója is elérhető, amely magában foglalja a személyes ( `PII` ) és az állapotadatok ( `PHI` ) adatainak észlelését. Emellett a **Health (állapot** ) lapra kattintva megtekintheti a támogatott kategóriák listáját a Text Analytics for Health szolgáltatásban.

## <a name="entity-categories"></a>Entitások kategóriái

#### <a name="general"></a>[Általános](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Egészségügy](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Következő lépések

* [Elnevezett entitások felismerésének használata a Text Analyticsban](how-tos/text-analytics-how-to-entity-linking.md)
