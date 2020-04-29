---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875117"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
1. Kattintson [az **anomália-detektor** erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0`– a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|
    |Előnézet megerősítése jelölőnégyzet (kötelező)|Azt jelzi, hogy elolvasta-e az **előnézeti** értesítést|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra
1. Összegyűjtése `endpoint` KONFIGURÁLT és API-kulcs:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan néz ki`https://westus2.api.cognitive.microsoft.com/`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. másolása. Egy 32 alfanumerikus karakterből álló karakterlánc, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóköz vagy kötőjel nélkül.|



