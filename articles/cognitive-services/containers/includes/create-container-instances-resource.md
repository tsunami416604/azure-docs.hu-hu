---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre Azure Container instance-erőforrásokat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876427"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure Container instance-erőforrás létrehozása

1. Lépjen a Container Instances [Létrehozás](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) lapjára.

2. Az **alapvető beállítások** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza ki a rendelkezésre álló erőforráscsoportot, vagy hozzon létre egy `cognitive-services`újat, például:.|
    |Tárolónév|Adjon meg egy nevet, `cognitive-container-instance`például:. A névnek alsó sapkában kell lennie.|
    |Hely|Válasszon régiót az üzembe helyezéshez.|
    |Lemezkép típusa|Ha a tároló rendszerképét olyan tároló-beállításjegyzékben tárolja, amely nem igényel hitelesítő `Public`adatokat, válassza a lehetőséget. Ha a tároló rendszerképének eléréséhez hitelesítő adatokra van szükség, válassza a lehetőséget `Private`. További részletekért tekintse meg a [Container-Tárházak és-lemezképek](../../cognitive-services-container-support.md#container-repositories-and-images) című témakört, amelyből megtudhatja, hogy a tároló képe `Public` vagy `Private` sem (nyilvános előzetes verzió). |
    |Rendszerkép neve|Adja meg a Cognitive Services tároló helyét. A hely a `docker pull` parancs argumentumként használt helye. Tekintse meg a [tároló adattárait és lemezképeit](../../cognitive-services-container-support.md#container-repositories-and-images) a rendelkezésre álló képnevekhez és a hozzájuk tartozó adattárhoz.<br><br>A rendszerkép nevének teljesen minősítettnek kell lennie három rész megadásával. Először a Container Registry, majd a tárház, végül a rendszerkép neve: `<container-registry>/<repository>/<image-name>`.<br><br>Íme egy példa, `mcr.microsoft.com/azure-cognitive-services/keyphrase` amely a Microsoft Container Registry kulcsszókeresés rendszerképét jelöli az Azure Cognitive Services adattár alatt. Egy másik példa a `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , amely a tároló előzetes tárolójának beállításjegyzékének Microsoft-tárházában a szöveges kép beszédét jelképezi. |
    |Operációs rendszer típusa|`Linux`|
    |Méret|Az adott kognitív szolgáltatási tárolóra vonatkozó javasolt javaslatok méretének módosítása:<br>2 CPU-mag<br>4 GB

3. A **hálózatkezelés** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Portok|Állítsa be a TCP- `5000`portot a következőre:. A tároló elérhetővé tétele a 5000-as porton.|

4. A **speciális** lapon adja meg a szükséges **környezeti változókat** az Azure Container instance-erőforrás tároló számlázási beállításainál:

    | Kulcs | Érték |
    |--|--|
    |`apikey`|Az erőforrás **kulcsok** lapjáról másolt. Egy 32 alfanumerikus karakterből álló karakterlánc, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`szóköz vagy kötőjel nélkül.|
    |`billing`|Az erőforrás **Áttekintés** lapjáról lett másolva.|
    |`eula`|`accept`|

5. Kattintson **a felülvizsgálat és létrehozás** gombra.
6. Az ellenőrzés után kattintson a **Létrehozás** gombra a létrehozási folyamat befejezéséhez.
7. Az erőforrás sikeres üzembe helyezése után készen áll
