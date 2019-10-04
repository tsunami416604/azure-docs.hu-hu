---
title: Az Azure Media Services API - hozzáférés az Azure CLI |} A Microsoft Docs
description: Kövesse az ebben az útmutatóban az Azure Media Services API eléréséhez.
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
ms.openlocfilehash: 5dbcf446a609adcd0f1902fcca2ac19ad87f17b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65779675"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Hozzáférés az Azure Media Services API-t az Azure CLI-vel
 
Az Azure AD egyszerű szolgáltatásnév hitelesítése csatlakozni az Azure Media Services API használatához az alkalmazása szükséges, az Azure AD-token a következő paraméterekkel rendelkező kérelem:

* Az Azure AD-bérlő végpont
* A Media Services-erőforrás-URI
* Erőforrás URI-t a Media Services – REST
* Az Azure AD-alkalmazás értékeire: az ügyfél-azonosító és titkos Ügyfélkód

Részletes ismertetése: [fér hozzá a Media Services v3 API-k](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Ez a cikk bemutatja, hogyan hozzon létre egy Azure AD-alkalmazás és szolgáltatás egyszerű, és az Azure Media Services-erőforrások eléréséhez szükséges értékek beolvasása az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

Ellenőrizze, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Lásd még

- [Scale a Media szolgáltatás számára fenntartott egység – CLI](media-reserved-units-cli-how-to.md)
- [Egy Media Services-fiók létrehozása – CLI](create-account-cli-how-to.md) 
- [Alaphelyzetbe fiókhitelesítő adatai – CLI](cli-reset-account-credentials.md)
- [Eszközök – parancssori felület létrehozása](cli-create-asset.md)
- [CLI - fájl feltöltése](cli-upload-file-asset.md)
- [Átalakítások – parancssori felület létrehozása](cli-create-transform.md)
- [Az egyéni átalakítási – parancssori felület kódolása](custom-preset-cli-howto.md)
- [Hozzon létre feladatokat – CLI](cli-create-jobs.md)
- [EventGrid - létrehozása a parancssori felület](job-state-events-cli-how-to.md)
- [Teszi közzé az objektumot – CLI](cli-publish-asset.md)
- [Szűrő – CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>További lépések

A folyamatos átviteli végponton, ahonnan tartalomstreameléshez rendelkezik a futó állapotot. A következő CLI-parancs elindítja az alapértelmezett Streaming Endpoint:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

