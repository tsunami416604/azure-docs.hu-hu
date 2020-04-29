---
title: Cognitive Services Text Analytics erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre Cognitive Services Text Analytics erőforrást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876422"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **erőforrás létrehozása**lehetőséget, majd lépjen az **AI + Machine learning** > **text Analyticsra**.
   Vagy nyissa meg a következőt: [text Analytics létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Adjon meg egy nevet (2-64 karakter).|
    |Előfizetés|Válassza ki a megfelelő előfizetést.|
    |Hely|Válasszon egy közeli helyet.|
    |Tarifacsomag| Adja meg az **S**, a standard díjszabási szintet.|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot.|

1. Válassza a **Létrehozás**lehetőséget, és várja meg az erőforrás létrehozását. A böngésző automatikusan átirányítja az újonnan létrehozott erőforrás-oldalra.
1. Gyűjtse össze a `endpoint` konfigurált és egy API-kulcsot:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`következőhöz hasonlóan jelenik meg:.|
    |**Kulcsok**|API-kulcs|Másolja a két kulcs egyikét. Ez egy 32 karakterből álló alfanumerikus karakterlánc, szóköz vagy kötőjel nélkül: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
