---
title: Cognitive Services Text Analytics erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre a Cognitive Services Text Analytics erőforrás.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877447"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **erőforrás létrehozása**, majd lépjen **mesterséges Intelligencia és Machine Learning** > **Szövegelemzés**.
   Vagy nyissa meg [létrehozása Szövegelemzés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Adja meg a szükséges beállításokat:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Adjon meg egy nevet (2-64 karakter)|
    |Subscription|Válassza ki a megfelelő előfizetést|
    |Location|Válasszon ki egy közeli helyet|
    |Tarifacsomag| Adja meg **S**, a standard díjcsomag|
    |Resource group|Válasszon ki egy rendelkezésre álló|

1. Válassza ki **létrehozás** és várja meg a létrehozandó erőforrás. A böngésző automatikusan átirányítja az újonnan létrehozott erőforrás-oldalon.
1. A beállított gyűjtése `endpoint` és API-kulcs:

    |Portál erőforrás lapján|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpont. Hasonlóan jelenik meg `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**kulcsok**|API-kulcs|Másolja ki az egyik két kulcsot. Egy 32 karakterből álló alfanumerikus karakterláncnak nem szóközökkel vagy kötőjelekkel együtt: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
