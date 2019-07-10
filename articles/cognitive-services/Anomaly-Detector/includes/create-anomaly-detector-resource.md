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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717083"
---
## <a name="create-an-anomaly-detector-resource"></a>Hozzon létre egy Anomáliadetektálási detector használatával erőforrást

1. Jelentkezzen be a [Azure Portalon](https://portal.azure.com)
1. Kattintson a [létrehozás **Anomáliadetektálási detector használatával** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) erőforrás
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt nevét (2-64 karakter)|
    |Subscription|Válassza ki a megfelelő előfizetést|
    |Location|Minden olyan közeli és a rendelkezésre álló hely kiválasztása|
    |Tarifacsomag|`F0` – a minimális tarifacsomag|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló|
    |Előzetes verzió megerősítő jelölőnégyzetet (kötelező)|Függetlenül attól, hogy elolvasta a **előzetes** figyelje meg, hogy|

1. Kattintson a **létrehozás** és várja meg a létrehozandó erőforrás. Miután elkészült, lépjen az erőforrás oldalára
1. Adatokat gyűjthet konfigurált `endpoint` és API-kulcs:

    |Portál erőforrás lapján|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpont. Hasonló `https://westus2.api.cognitive.microsoft.com/`|
    |**kulcsok**|API-kulcs|1\. a két kulcs másolása. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



