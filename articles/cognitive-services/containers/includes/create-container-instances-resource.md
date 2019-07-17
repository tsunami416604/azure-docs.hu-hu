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
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229321"
---
## <a name="create-an-azure-container-instance-resource"></a>Az Azure Container Instance-erőforrás létrehozásához

1. Nyissa meg a [létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) Container Instances lapot.

2. Az a **alapjai** lapra, adja meg a következő adatokat:

    |Beállítás|Value|
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

4. Az a **speciális** lapra, adja meg a szükséges **környezeti változók** tároló számlázási az ACI-erőforrás beállítások:

    | Kulcs | Value |
    |--|--|
    |`apikey`|Átmásolja a **kulcsok** az erőforrás oldalán. Egy 32 alfanumerikus karakterből álló karakterlánc szóközöket vagy kötőjeleket, nélkül `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Átmásolja a **áttekintése** az erőforrás oldalán.|
    |`eula`|`accept`|

1. Kattintson a **ellenőrzés és létrehozás**
1. Kattintson az ellenőrzés elvégzése után **létrehozás** a létrehozási folyamat befejezéséhez.
1. Az erőforrás sikeres telepítést követően készen áll