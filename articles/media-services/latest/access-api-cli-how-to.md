---
title: A Azure Media Services API elérése – Azure CLI | Microsoft Docs
description: A Azure Media Services API eléréséhez kövesse ennek a cikknek a lépéseit.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896159"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Hozzáférés Azure Media Services API-hoz az Azure CLI-vel
 
Ha az Azure AD szolgáltatás egyszerű hitelesítését szeretné használni a Azure Media Services API-hoz való kapcsolódáshoz, az alkalmazásnak olyan Azure AD-jogkivonatot kell igényelnie, amely a következő paraméterekkel rendelkezik:

* Azure AD-bérlői végpont
* Erőforrás-URI Media Services
* Erőforrás URI-ja REST Media Services
* Azure AD-alkalmazás értékei: az ügyfél-azonosító és az ügyfél titka

Részletes magyarázat: [Media Services V3 API](media-services-apis-overview.md#accessing-the-azure-media-services-api)-k elérése.

Ez a cikk bemutatja, hogyan használható az Azure CLI egy Azure AD-alkalmazás és-szolgáltatásnév létrehozásához, valamint a Azure Media Services erőforrások eléréséhez szükséges értékek beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek 

[Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md).

Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Lásd még:

- [A Media szolgáltatás számára fenntartott egységek méretezése – parancssori felület](media-reserved-units-cli-how-to.md)
- [Media Services-fiók létrehozása – parancssori felület](create-account-cli-how-to.md) 
- [Fiók hitelesítő adatainak alaphelyzetbe állítása – parancssori felület](cli-reset-account-credentials.md)
- [Eszközök létrehozása – parancssori felület](cli-create-asset.md)
- [Fájl feltöltése – parancssori felület](cli-upload-file-asset.md)
- [Átalakítások létrehozása – parancssori felület](cli-create-transform.md)
- [Kódolás egyéni átalakítással – parancssori felület](custom-preset-cli-howto.md)
- [Feladatok létrehozása – parancssori felület](cli-create-jobs.md)
- [EventGrid létrehozása – parancssori felület](job-state-events-cli-how-to.md)
- [Eszköz közzététele – parancssori felület](cli-publish-asset.md)
- [Szűrő – parancssori felület](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Következő lépések

A streaming végpontot, amelyről a tartalmat továbbítani kívánja, futó állapotban kell lennie. A következő CLI-parancs elindítja az alapértelmezett folyamatos átviteli végpontot:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

