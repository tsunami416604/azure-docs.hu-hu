---
title: LUIS-tároló üzembe helyezése az Azure Container-példányokon
titleSuffix: Azure Cognitive Services
description: Telepítse a LUIS-tárolót egy Azure Container-példányra, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879206"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>A language understanding (LUIS) tároló üzembe helyezése az Azure Container-példányokba

Ismerje meg, hogyan [LUIS](luis-container-howto.md) telepítheti a Cognitive Services LUIS-tárolóját az Azure [Container-példányokra.](https://docs.microsoft.com/azure/container-instances/) Ez az eljárás egy anomáliadetektor-erőforrás létrehozását mutatja be. Ezután megbeszéljük a kapcsolódó tárolókép húzását. Végül kiemeljük a két vezénylési lehetőségének gyakorlását egy böngészőből. A tárolók használatával a fejlesztők figyelmét elterelheti az infrastruktúra kezeléséről az alkalmazásfejlesztésre való összpontosításra.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

A LUIS-tárolóhoz olyan `.gz` modellfájl szükséges, amely futásidőben van behúzva. A tárolónak képesnek kell lennie a modellfájl elérésére a tárolópéldánykötet-csatlakoztatáson keresztül. Az Azure-fájlmegosztás létrehozásáról a [Fájlmegosztás létrehozása](../../storage/files/storage-how-to-create-file-share.md)című témakörben talál további információt. Vegye figyelembe az Azure Storage-fiók nevét, kulcsát és fájlmegosztási nevét, mivel később szüksége lesz rájuk.

### <a name="export-and-upload-packaged-luis-app"></a>Csomagolt LUIS-alkalmazás exportálása és feltöltése

Ahhoz, hogy a LUIS-modellt (csomagolt alkalmazást) feltölthesse az Azure-fájlmegosztásba, <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">először <span class="docon docon-navigate-external x-hidden-focus"> </span>exportálnia kell azt a LUIS portálról. </a> Az Azure Portalon keresse meg a tárfiók-erőforrás **Áttekintés lapját,** és válassza a **Fájlmegosztások**lehetőséget. Jelölje ki a nemrég létrehozott fájlmegosztási nevet, majd kattintson a **Feltöltés gombra.**

> [!div class="mx-imgBorder"]
> ![Feltöltés fájlmegosztásba](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Töltse fel a LUIS-modellfájlt.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
