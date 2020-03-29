---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure-tárolópéldány-erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 18f4edf5cc63a448779423cc1b302130b4b80724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75692567"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container Instance-erőforrás létrehozása

1. Nyissa meg a Create page for Container Instances [(Létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) lap a tárolópéldányokhoz) lapot.

2. Az **Alapok** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza ki a rendelkezésre álló erőforráscsoportot, vagy hozzon létre egy újat, például `cognitive-services`.|
    |Tárolónév|Írjon be `cognitive-container-instance`egy nevet, például . A névnek alsó bbbetűs betűkkel kell lennie.|
    |Hely|Válassza ki a központi telepítéshez kívánt régiót.|
    |Lemezkép típusa|Ha a tárolórendszerkép egy tárolóbeállításjegyzékben van tárolva, `Public`amelyhez nincs szükség hitelesítő adatokra, válassza a lehetőséget. Ha a tárolórendszerkép eléréséhez `Private`hitelesítő adatok szükségesek, válassza a lehetőséget. A [tárolótárolók és -képek](../../cognitive-services-container-support.md#container-repositories-and-images) című dokumentumból megtudhatja, hogy a tárolórendszerkép-e `Public` vagy sem `Private` ("Nyilvános előzetes verzió"). |
    |Rendszerkép neve|Adja meg a Cognitive Services tároló helyét. A hely az, amit a `docker pull` parancs argumentumaként használnak. Tekintse meg a [tároló tárolók és a rendszerképek](../../cognitive-services-container-support.md#container-repositories-and-images) a rendelkezésre álló rendszerképek nevét és a hozzájuk tartozó tárház.<br><br>A kép nevének három részből kell állnia. Először is, a tároló rendszerleíró adatbázis, majd `<container-registry>/<repository>/<image-name>`a tárház, végül a kép neve: .<br><br>Íme egy példa, azt jelenti, `mcr.microsoft.com/azure-cognitive-services/keyphrase` hogy a kulcskifejezés kinyerése képa a Microsoft Container Registry az Azure Cognitive Services tárház alatt. Egy másik `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` példa, amely a beszédfelismerési rendszer képet a Microsoft-tárházban a Container Preview tároló beállításjegyzékben. |
    |Operációs rendszer típusa|`Linux`|
    |Méret|Módosítsa a méretet az adott Cognitive Service-tároló javasolt javaslataira:<br>2 CPU-mag<br>4 GB

3. A **Hálózat** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Portok|Állítsa a TCP-portot `5000`. Kiteszi a konténert az 5000-es porton.|

4. A **Speciális** lapon adja meg az Azure Container Instance erőforrás tárolószámlázási beállításaihoz szükséges **környezeti változókat:**

    | Kulcs | Érték |
    |--|--|
    |`apikey`|Az erőforrás **Kulcsok** lapjáról másolva. Ez egy 32 alfanumerikus karakterből álló karakterlánc `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóközök és kötőjelek nélkül.|
    |`billing`|Az erőforrás **Áttekintés** lapjáról másolva.|
    |`eula`|`accept`|

5. Kattintson **a Véleményezés és létrehozás gombra**
6. Az érvényesítési fázis után kattintson a **Létrehozás** gombra a létrehozási folyamat befejezéséhez.
7. Az erőforrás sikeres üzembe helyezése után készen áll
