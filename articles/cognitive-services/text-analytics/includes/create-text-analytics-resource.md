---
title: Cognitive Services Text Analytics-erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre Cognitive Services Text Analytics erőforrást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779386"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics-erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **Erőforrás létrehozása** lehetőséget, majd lépjen a **Mesterséges intelligencia és gépi tanulás** > **Text Analytics** területre.
   Vagy nyissa meg a következőt: [text Analytics létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Adjon meg egy nevet (2–64 karakter).|
    |Előfizetés|Válassza ki a megfelelő előfizetést.|
    |Hely|Válasszon egy közeli helyet.|
    |Tarifacsomag| Adja meg az **S** betűt, amely a standard tarifacsomag jele.|
    |Erőforráscsoport|Válasszon ki egy elérhető erőforráscsoportot.|

1. Válassza a **Létrehozás** lehetőséget, és várjon, amíg az erőforrás létrejön. A böngésző automatikusan átirányítja az újonnan létrehozott erőforrásoldalra.
1. Gyűjtse össze a konfigurált `endpoint` és egy API-kulcsot:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan jelenik meg: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Kulcsok**|API-kulcs|Másolja a két kulcs egyikét. Ez egy 32 karakterből álló alfanumerikus karakterlánc, szóköz vagy kötőjel nélkül: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
