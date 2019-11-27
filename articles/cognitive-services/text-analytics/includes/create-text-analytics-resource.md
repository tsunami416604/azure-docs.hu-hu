---
title: Cognitive Services Text Analytics erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre Cognitive Services Text Analytics erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383434"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics erőforrás létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Válassza az **erőforrás létrehozása**lehetőséget, majd lépjen a következőre: **AI + Machine learning** > **text Analytics**.
   Vagy nyissa meg a következőt: [text Analytics létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Adjon meg egy nevet (2-64 karakter).|
    |Előfizetést|Válassza ki a megfelelő előfizetést.|
    |Hely|Válasszon egy közeli helyet.|
    |Tarifacsomag| Adja meg az **S**, a standard díjszabási szintet.|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot.|

1. Válassza a **Létrehozás**lehetőséget, és várja meg az erőforrás létrehozását. A böngésző automatikusan átirányítja az újonnan létrehozott erőforrás-oldalra.
1. A konfigurált `endpoint` és az API-kulcs összegyűjtése:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`hoz hasonlóan jelenik meg.|
    |**Kulcsok**|API-kulcs|Másolja a két kulcs egyikét. Ez egy 32 karakterből álló alfanumerikus karakterlánc, szóköz vagy kötőjel nélkül: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
