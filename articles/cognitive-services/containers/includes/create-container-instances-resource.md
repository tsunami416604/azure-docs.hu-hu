---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure container instance-erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711405"
---
## <a name="create-an-azure-container-instance-resource"></a>Az Azure Container Instance-erőforrás létrehozásához

1. Nyissa meg a [létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) Container Instances lapot.

2. Az a **alapjai** lapra, adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Subscription|Válassza ki előfizetését.|
    |Resource group|Válassza ki az elérhető erőforráscsoportot vagy hozzon létre például egy új `cognitive-services`.|
    |Tárolónév|Írjon be egy nevet, például `cognitive-container-instance`. A név alacsonyabb nagybetűs kell lennie.|
    |Location|Válassza ki a régiót, üzembe helyezéshez.|
    |Lemezkép típusa|`Public`|
    |Rendszerkép neve|Adja meg a Cognitive Services-tároló helye. A hely lehet, használja ugyanazt a `docker pull` parancshoz, tekintse meg a [tároló adattárak és rendszerképek](../../cognitive-services-container-support.md#container-repositories-and-images) elérhető rendszerkép nevét és a megfelelő tárház.|
    |Operációs rendszer típusa|`Linux`|
    |Size|A javasolt javaslatokat az adott a Cognitive Services-tároló méretének módosítása:<br>2 Processzormagok<br>4 GB

3. Az a **hálózatkezelés** lapra, adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Portok|A TCP-port beállítása `5000`. Tesz elérhetővé a tárolót 5000-es porton.|

4. Az a **speciális** lapra, adja meg a szükséges **környezeti változók** tároló [beállítások számlázási](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) az ACI erőforrás:

    | Kulcs | Érték |
    |--|--|
    |`apikey`|Átmásolja a **kulcsok** a Text Analytics erőforrás lapján. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Átmásolja a **áttekintése** a Text Analytics erőforrás lapján. Például: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Kattintson a **ellenőrzés és létrehozás**
1. Kattintson az ellenőrzés elvégzése után **létrehozás** a létrehozási folyamat befejezéséhez.
1. Az erőforrás sikeres telepítést követően készen áll