---
title: Cognitive Services szövegelemzési erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre cognitive Services szövegelemzési erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383434"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services szövegelemzési erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
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
