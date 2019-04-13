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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 1b872c5c2ff0f581300a843650d7434c7c526c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545619"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Hozzáférés az Azure Media Services API-t az Azure CLI-vel
 
Az Azure AD egyszerű szolgáltatásnév hitelesítése az Azure Media Services API csatlakozni kell használnia. Az alkalmazásnak kell az Azure AD-token a következő paraméterekkel rendelkező kérelem:

* Az Azure AD-bérlő végpont
* A Media Services-erőforrás-URI
* Erőforrás URI-t a Media Services – REST
* Az Azure AD-alkalmazás értékeire: az ügyfél-azonosító és titkos Ügyfélkód

További információkért lásd: [fejlesztés a Media Services v3 API-k](media-services-apis-overview.md).

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
