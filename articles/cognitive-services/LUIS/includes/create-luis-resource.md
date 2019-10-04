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
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717167"
---
## <a name="create-a-luis-resource"></a>A LUIS-erőforrás létrehozása

1. Jelentkezzen be a [Azure Portalon](https://portal.azure.com)
1. Kattintson a [létrehozása **hangfelismerés**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt nevét (2-64 karakter)|
    |Subscription|Válassza ki a megfelelő előfizetést|
    |Location|Minden olyan közeli és a rendelkezésre álló hely kiválasztása|
    |Tarifacsomag|`F0` – a minimális tarifacsomag|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló|

1. Kattintson a **létrehozás** és várja meg a létrehozandó erőforrás. Miután elkészült, lépjen az erőforrás oldalára
1. Adatokat gyűjthet konfigurált `endpoint` és API-kulcs:

    |Portál erőforrás lapján|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpont. Hasonló `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**kulcsok**|API-kulcs|1\. a két kulcs másolása. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
