---
title: LUIS-tároló üzembe helyezése az Azure Container instances szolgáltatásban
titleSuffix: Azure Cognitive Services
description: Telepítse a LUIS-tárolót egy Azure Container instanceba, és tesztelje egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689446"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>A Language Understanding (LUIS) tároló üzembe helyezése az Azure Container instances szolgáltatásban

Megtudhatja, hogyan helyezheti üzembe a Cognitive Services [Luis](luis-container-howto.md) -tárolót az Azure [Container instances](https://docs.microsoft.com/azure/container-instances/)szolgáltatásban. Ez az eljárás egy anomália-detektor erőforrás létrehozását mutatja be. Ezután megbeszéljük a társított tároló-rendszerkép kihúzását. Végezetül kiemeljük a két böngészőből való összehangolás lehetőségét. A tárolók használatával az alkalmazások fejlesztésére összpontosíthat a fejlesztők figyelmét arra, hogy az infrastruktúra kezelése helyett.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

A LUIS-tárolóhoz szükség van egy `.gz` Model fájlra, amely futásidőben van behúzva. A tárolónak képesnek kell lennie arra, hogy elérje a modell fájlját a Container példányból származó mennyiségi csatlakoztatáson keresztül. Az Azure-fájlmegosztás létrehozásával kapcsolatos információkért lásd: [fájlmegosztás létrehozása](../../storage/files/storage-how-to-create-file-share.md). Jegyezze fel az Azure Storage-fiók nevét, kulcsát és a fájlmegosztás nevét, mert később szüksége lesz rájuk.

### <a name="export-and-upload-packaged-luis-app"></a>Csomagolt LUIS-alkalmazás exportálása és feltöltése

A LUIS-modell (csomagolt alkalmazás) Azure-fájlmegosztásba való feltöltéséhez <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">először <span class="docon docon-navigate-external x-hidden-focus"> </span>exportálnia kell azt a Luis-portálról </a>. A Azure Portal navigáljon a Storage-fiók erőforrás **Áttekintés** lapjára, és válassza a **fájlmegosztás**lehetőséget. Válassza ki a közelmúltban létrehozott fájlmegosztás nevét, majd kattintson a **feltöltés** gombra.

> [!div class="mx-imgBorder"]
> ![feltöltés a fájlmegosztás](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Töltse fel a LUIS Model fájlt.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
