---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499101"
---
## <a name="create-an-computer-vision-resource"></a>Computer Vision erőforrás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson [a **Computer Vision** erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Földrajzi egység|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0` – a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra.
1. A konfigurált `{ENDPOINT_URI}` és `{API_KEY}`összegyűjtése a részletekért tekintse meg a [szükséges paraméterek összegyűjtését](../computer-vision-how-to-install-containers.md#gathering-required-parameters) ismertető témakört.
