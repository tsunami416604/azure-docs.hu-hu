---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677575"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

1. Jelentkezzen be <a href="https://portal.azure.com" target="_blank">a <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portalba</a>.
1. Válassza az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">anomália <span class="docon docon-navigate-external x-hidden-focus"></span> -detektor erőforrás létrehozása</a> lehetőséget.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0` – 10 hívás/másodperc, 20000 tranzakció havonta. <br> Vagy<br> `S0` -80 hívás/másodperc|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra
1. Összegyűjtése konfigurált `endpoint` és API-kulcs:

    |Kulcsok és végpont lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan néz ki ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. másolása. Egy 32 alfanumerikus karakterből álló karakterlánc, szóköz vagy kötőjel nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



