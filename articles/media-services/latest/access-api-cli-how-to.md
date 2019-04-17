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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617835"
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

## <a name="next-steps"></a>További lépések

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
