---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025237"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
1. Kattintson [az **anomália-detektor** erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0` – a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra
1. Összegyűjtése konfigurált `endpoint` és API-kulcs:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan néz ki `https://westus2.api.cognitive.microsoft.com/`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. másolása. Egy 32 alfanumerikus karakterből álló karakterlánc, szóköz vagy kötőjel nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



