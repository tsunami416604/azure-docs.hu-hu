---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422362"
---
## <a name="create-a-speech-resource"></a>Beszédfelismerési erőforrás létrehozása

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [a **Beszédfelismerés** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) ierőforrás elemre.
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot
1. A `endpoint` konfigurált és egy API-kulcs összegyűjtése:

    |Erőforrás lap a portálon|Beállítás|Érték|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. Úgy néz ki, hasonló`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. Ez egy 32 alfanumerikus karakterből álló karakterlánc `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóközök és kötőjelek nélkül.|
