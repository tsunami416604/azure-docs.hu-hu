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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: a27f7597ddc934b925d63507a816ac5816b682d6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151043"
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
- [Egy Media Services-fiók létrehozása – CLI](./scripts/cli-create-account.md) 
- [Alaphelyzetbe fiókhitelesítő adatai – CLI](./scripts/cli-reset-account-credentials.md)
- [Eszközök – parancssori felület létrehozása](./scripts/cli-create-asset.md)
- [CLI - fájl feltöltése](./scripts/cli-upload-file-asset.md)
- [Átalakítások – parancssori felület létrehozása](./scripts/cli-create-transform.md)
- [Hozzon létre feladatokat – CLI](./scripts/cli-create-jobs.md)
- [EventGrid - létrehozása a parancssori felület](./scripts/cli-create-event-grid.md)
- [Teszi közzé az objektumot – CLI](./scripts/cli-publish-asset.md)
- [Szűrő – CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>További lépések

A folyamatos átviteli végponton, ahonnan tartalomstreameléshez rendelkezik a futó állapotot. A következő CLI-parancs elindítja az alapértelmezett Streaming Endpoint:


`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`
