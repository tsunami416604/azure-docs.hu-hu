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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875117"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomáliadetektor-erőforrás létrehozása

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [ **az Anomália-detektor** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) erőforrás létrehozása elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|
    |Az előnézet megerősítése jelölőnégyzet (kötelező)|Elolvasta-e az **előzetes értesítést,** vagy sem|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot
1. A `endpoint` konfigurált és egy API-kulcs összegyűjtése:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. Úgy néz ki, hasonló`https://westus2.api.cognitive.microsoft.com/`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. Ez egy 32 alfanumerikus karakterből álló karakterlánc `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóközök és kötőjelek nélkül.|



