---
title: Cognitive Services szövegelemzési erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre cognitive Services szövegelemzési erőforrást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876422"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services szövegelemzési erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **az Erőforrás létrehozása**lehetőséget, majd nyissa meg a **AI + Machine Learning** > **Text Analytics**lehetőséget.
   Vagy nyissa meg a [Szövegelemzés létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)című.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Írjon be egy nevet (2-64 karakter).|
    |Előfizetés|Válassza ki a megfelelő előfizetést.|
    |Hely|Válasszon ki egy közeli helyet.|
    |Tarifacsomag| Adja meg **az S**értéket, a szabványos tarifacsomagot.|
    |Erőforráscsoport|Válasszon ki egy elérhető erőforráscsoportot.|

1. Válassza **a Létrehozás**lehetőséget, és várja meg az erőforrás létrehozását. A böngésző automatikusan átirányítja az újonnan létrehozott erőforráslapra.
1. Gyűjtse össze `endpoint` a konfigurált és egy API-kulcsot:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. Úgy tűnik, hasonló . `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Kulcsok**|API-kulcs|Másolja a két kulcs egyikét. Ez egy 32 karakteres alfanumerikus karakterlánc szóközök `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` és kötőjelek nélkül: <>.|
