---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrásokat.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051174"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container instance-erőforrás létrehozása

1. Lépjen a Container Instances [Létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) lapjára.

2. Az **alapvető beállítások** lapon adja meg a következő adatokat:

    |Beállítás|Value|
    |--|--|
    |Subscription|Válassza ki előfizetését.|
    |Resource group|Válassza ki a rendelkezésre álló erőforráscsoportot, vagy hozzon létre egy `cognitive-services`újat, például:.|
    |Tároló neve|Adjon meg egy nevet, `cognitive-container-instance`például:. A névnek alsó sapkában kell lennie.|
    |Location|Válasszon régiót az üzembe helyezéshez.|
    |Lemezkép típusa|Ha a tároló rendszerképét olyan tároló-beállításjegyzékben tárolja, amely nem igényel hitelesítő `Public`adatokat, válassza a lehetőséget. Ha a tároló rendszerképének eléréséhez hitelesítő adatokra van szükség, válassza a lehetőséget `Private`. További részletekért tekintse meg a [Container-Tárházak és-lemezképek](../../cognitive-services-container-support.md#container-repositories-and-images) című témakört `Private` , amelyből megtudhatja, hogy a tároló képe `Public` vagy sem (nyilvános előzetes verzió). |
    |Lemezkép neve|Adja meg a Cognitive Services tároló helyét. A hely a `docker pull` parancs argumentumként használt helye. Tekintse meg a [tároló adattárait és lemezképeit](../../cognitive-services-container-support.md#container-repositories-and-images) a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz.<br><br>A rendszerkép nevének teljesen minősítettnek kell lennie három rész megadásával. Először a Container Registry, majd a tárház, végül a rendszerkép neve: `<container-registry>/<repository>/<image-name>`.<br><br>Íme egy példa, `mcr.microsoft.com/azure-cognitive-services/keyphrase` amely a Microsoft Container Registry kulcsszókeresés rendszerképét jelöli az Azure Cognitive Services adattár alatt. Egy másik példa a `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , amely a tároló előzetes tárolójának beállításjegyzékének Microsoft-tárházában a szöveges kép beszédét jelképezi. |
    |Operációs rendszer típusa|`Linux`|
    |Size|Az adott kognitív szolgáltatási tárolóra vonatkozó javasolt javaslatok méretének módosítása:<br>2 CPU-mag<br>4 GB

3. A **hálózatkezelés** lapon adja meg a következő adatokat:

    |Beállítás|Value|
    |--|--|
    |Portok|Állítsa be a TCP- `5000`portot a következőre:. A tároló elérhetővé tétele a 5000-as porton.|

4. A **speciális** lapon adja meg a szükséges **környezeti változókat** az Azure Container instance-erőforrás tároló számlázási beállításainál:

    | Kulcs | Value |
    |--|--|
    |`apikey`|Az erőforrás **kulcsok** lapjáról másolt. Egy 32 alfanumerikus karakterből álló karakterlánc, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóköz vagy kötőjel nélkül.|
    |`billing`|Az erőforrás **Áttekintés** lapjáról lett másolva.|
    |`eula`|`accept`|

1. Kattintson **a felülvizsgálat és létrehozás** gombra.
1. Az ellenőrzés után kattintson a **Létrehozás** gombra a létrehozási folyamat befejezéséhez.
1. Az erőforrás sikeres üzembe helyezése után készen áll