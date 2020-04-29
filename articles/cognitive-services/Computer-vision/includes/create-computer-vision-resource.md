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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876084"
---
## <a name="create-an-computer-vision-resource"></a>Computer Vision erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson [a **Computer Vision** erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0`– a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra.
1. A begyűjtés `{ENDPOINT_URI}` és `{API_KEY}`a további részletekért lásd a [szükséges paraméterek összegyűjtése](../computer-vision-how-to-install-containers.md#gathering-required-parameters) című témakört.
