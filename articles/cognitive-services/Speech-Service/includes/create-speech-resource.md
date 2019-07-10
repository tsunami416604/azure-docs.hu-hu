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
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711823"
---
## <a name="create-a-speech-resource"></a>Beszéd erőforrás létrehozása

1. Jelentkezzen be a [Azure Portalon](https://portal.azure.com)
1. Kattintson a [létrehozás **Speech** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) erőforrás
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
    |**Áttekintés**|Végpont|Másolja a végpont. Hasonló `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**kulcsok**|API-kulcs|1\. a két kulcs másolása. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
