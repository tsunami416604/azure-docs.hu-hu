---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878365"
---
## <a name="create-an-face-resource"></a>Face erőforrás létrehozása

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [az **Arcerőforrás** létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) gombra.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot
1. A `endpoint` konfigurált és egy API-kulcs összegyűjtése:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. Úgy néz ki, hasonló`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. Ez egy 32 alfanumerikus karakterből álló karakterlánc `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóközök és kötőjelek nélkül.|
