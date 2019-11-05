---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523030"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
1. Kattintson [az **anomália-detektor** erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Földrajzi egység|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0` – a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|
    |Előnézet megerősítése jelölőnégyzet (kötelező)|Azt jelzi, hogy elolvasta-e az **előnézeti** értesítést|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra
1. A konfigurált `endpoint` és az API-kulcs összegyűjtése:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan néz ki `https://westus2.api.cognitive.microsoft.com/`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. másolása. Egy 32 alfanumerikus karakterből álló karakterlánc, szóköz vagy kötőjel nélkül, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



