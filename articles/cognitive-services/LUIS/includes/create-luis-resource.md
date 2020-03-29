---
title: LUIS-erőforrás létrehozása
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465933"
---
## <a name="create-a-luis-resource"></a>LUIS-erőforrás létrehozása

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [a **Nyelvismertetés létrehozása gombra.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot.
1. Gyűjtse `endpoint` össze a konfigurált és egy API-kulcsot, lásd [a szükséges paraméterek összegyűjtése.](#gathering-required-parameters)

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
