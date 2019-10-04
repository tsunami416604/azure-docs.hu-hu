---
title: Cognitive Services Text Analytics erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre Cognitive Services Text Analytics erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377422"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **erőforrás létrehozása**lehetőséget, majd lépjen az **AI + Machine learning** > **text Analyticsra**.
   Vagy nyissa meg a következőt: [text Analytics létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Adjon meg egy nevet (2-64 karakter).|
    |Subscription|Válassza ki a megfelelő előfizetést.|
    |Location|Válasszon egy közeli helyet.|
    |Tarifacsomag| Adja meg az **S**, a standard díjszabási szintet.|
    |Resource group|Válasszon ki egy rendelkezésre álló erőforráscsoportot.|

1. Válassza a **Létrehozás**lehetőséget, és várja meg az erőforrás létrehozását. A böngésző automatikusan átirányítja az újonnan létrehozott erőforrás-oldalra.
1. Gyűjtse össze a `endpoint` konfigurált és egy API-kulcsot:

    |Erőforrás lap a portálon|Beállítás|Value|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`következőhöz hasonlóan jelenik meg:.|
    |**Kulcsok**|API-kulcs|Másolja a két kulcs egyikét. Ez egy 32 karakterből álló alfanumerikus karakterlánc, szóköz vagy kötőjel nélkül: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
