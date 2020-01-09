---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrásokat.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 18f4edf5cc63a448779423cc1b302130b4b80724
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692567"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container instance-erőforrás létrehozása

1. Lépjen a Container Instances [Létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) lapjára.

2. Az **alapvető beállítások** lapon adja meg a következő adatokat:

    |Beállítás|Value (Díj)|
    |--|--|
    |Előfizetés|Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza ki a rendelkezésre álló erőforráscsoportot, vagy hozzon létre egy újat, például `cognitive-services`.|
    |Tárolónév|Adjon meg egy nevet, például `cognitive-container-instance`. A névnek alsó sapkában kell lennie.|
    |Földrajzi egység|Válasszon régiót az üzembe helyezéshez.|
    |Lemezkép típusa|Ha a tároló rendszerképét olyan tároló-beállításjegyzékben tárolja, amely nem igényel hitelesítő adatokat, válassza a `Public`lehetőséget. Ha a tároló rendszerképének eléréséhez hitelesítő adatokra van szükség, válassza a `Private`lehetőséget. További részletekért tekintse meg a [Container-Tárházak és-lemezképek](../../cognitive-services-container-support.md#container-repositories-and-images) című témakört, amelyből megtudhatja, hogy `Public`-e vagy sem `Private` ("nyilvános előnézet"). |
    |Rendszerkép neve|Adja meg a Cognitive Services tároló helyét. A hely a `docker pull` parancs argumentumként használt helye. Tekintse meg a [tároló adattárait és lemezképeit](../../cognitive-services-container-support.md#container-repositories-and-images) a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz.<br><br>A rendszerkép nevének teljesen minősítettnek kell lennie három rész megadásával. Először a Container Registry, majd a tárház, végül a rendszerkép neve: `<container-registry>/<repository>/<image-name>`.<br><br>Íme egy példa, `mcr.microsoft.com/azure-cognitive-services/keyphrase` az Kulcsszókeresés rendszerképet képviseli a Microsoft Container Registry az Azure Cognitive Services adattárában. Egy másik példa az, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`, amely a tároló előzetes tárolójának beállításjegyzékének Microsoft adattárában a szöveges kép beszédét jelképezi. |
    |Operációs rendszer típusa|`Linux`|
    |Méret|Az adott kognitív szolgáltatási tárolóra vonatkozó javasolt javaslatok méretének módosítása:<br>2 CPU-mag<br>4 GB

3. A **hálózatkezelés** lapon adja meg a következő adatokat:

    |Beállítás|Value (Díj)|
    |--|--|
    |Portok|Állítsa be `5000`a TCP-portot. A tároló elérhetővé tétele a 5000-as porton.|

4. A **speciális** lapon adja meg a szükséges **környezeti változókat** az Azure Container instance-erőforrás tároló számlázási beállításainál:

    | Jelmagyarázat | Value (Díj) |
    |--|--|
    |`apikey`|Az erőforrás **kulcsok** lapjáról másolt. Egy 32 alfanumerikus karakterből álló karakterlánc, szóköz vagy kötőjel nélkül, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Az erőforrás **Áttekintés** lapjáról lett másolva.|
    |`eula`|`accept`|

5. Kattintson **a felülvizsgálat és létrehozás** gombra.
6. Az ellenőrzés után kattintson a **Létrehozás** gombra a létrehozási folyamat befejezéséhez.
7. Az erőforrás sikeres üzembe helyezése után készen áll
