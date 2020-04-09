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
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876084"
---
## <a name="create-an-computer-vision-resource"></a>Számítógép-látomás-erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson [a **Számítógép-látomás** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) létrehozása erőforrás elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot.
1. Gyűjtse `{ENDPOINT_URI}` össze `{API_KEY}`a konfigurált és a , lásd [a szükséges paraméterek összegyűjtése](../computer-vision-how-to-install-containers.md#gathering-required-parameters) a részletekért.
