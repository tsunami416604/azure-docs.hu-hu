---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821992"
---
## <a name="create-a-luis-resource"></a>LUIS-erőforrás létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
1. Kattintson a [Létrehozás gombra **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg az összes szükséges beállítást:

    |Beállítás|Value (Díj)|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Földrajzi egység|Válasszon ki egy közeli és elérhető helyet|
    |Díjcsomag|`F0` – a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra
1. A konfigurált `endpoint` és az API-kulcs összegyűjtése:

    |Erőforrás lap a portálon|Beállítás|Value (Díj)|
    |--|--|--|
    |**Áttekintés**|Végpont|Másolja a végpontot. A következőhöz hasonlóan néz ki `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**Kulcsok**|API-kulcs|A két kulcs 1. másolása. Egy 32 alfanumerikus karakterből álló karakterlánc, szóköz vagy kötőjel nélkül, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
