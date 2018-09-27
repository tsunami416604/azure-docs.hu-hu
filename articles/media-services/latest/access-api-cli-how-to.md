---
title: Az Azure Media Services API - hozzáférés az Azure CLI |} A Microsoft Docs
description: Kövesse az ebben az útmutatóban az Azure Media Services API eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e20cac5f1063589bdbfee0f384ac6af5a39811ed
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096791"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Hozzáférés az Azure Media Services API-t az Azure CLI-vel
 
Az Azure AD egyszerű szolgáltatásnév hitelesítése az Azure Media Services API csatlakozni kell használnia. Az alkalmazásnak kell az Azure AD-token a következő paraméterekkel rendelkező kérelem:

* Az Azure AD-bérlő végpont
* A Media Services-erőforrás-URI
* Erőforrás URI-t a Media Services – REST
* Az Azure AD-alkalmazás értékeire: az ügyfél-azonosító és titkos Ügyfélkód

Ez a cikk bemutatja, hogyan hozzon létre egy Azure AD-alkalmazás és szolgáltatás egyszerű, és az Azure Media Services-erőforrások eléréséhez szükséges értékek beolvasása az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek 

Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](create-account-cli-quickstart.md) leírt módon.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure Portalra](http://portal.azure.com), és indítsa el a **CloudShell** szolgáltatást a parancssori felületi parancsok végrehajtásához, ahogy az az alábbi lépésekben látható.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-ás vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
