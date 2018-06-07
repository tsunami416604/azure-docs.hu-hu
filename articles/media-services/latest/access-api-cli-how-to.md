---
title: Hozzáférés az Azure Media Services API - CLI 2.0 |} Microsoft Docs
description: Ez az útmutató az Azure Media Services API eléréséhez kövesse.
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
ms.openlocfilehash: 9295c3f9dfabc8ef7749758e926df443843720a1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639775"
---
# <a name="access-azure-media-services-api-with-cli-20"></a>Hozzáférés az Azure Media Services API CLI 2.0
 
Az Azure AD szolgáltatás egyszerű hitelesítést kell használnia csatlakozni az Azure Media Services API. Az alkalmazás kell igényelni egy az Azure AD-jogkivonatot, amely a következő paraméterekkel rendelkezik:

* Azure AD-bérlő végpont
* A Media Services erőforrás URI azonosítója
* Erőforrás URI azonosítója a többi Media Services szolgáltatásokhoz
* Az Azure AD alkalmazás értékek: az ügyfél-azonosító és a titkos ügyfélkódot

Ez a cikk bemutatja, hogyan CLI 2.0 használata az Azure AD-alkalmazás és szolgáltatás egyszerű létrehozása, és az Azure Media Services-erőforrások eléréséhez szükséges értékeket.

## <a name="prerequisites"></a>Előfeltételek 

Hozzon létre egy új Azure Media Services-fiók leírtak [a gyors üzembe helyezés](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure Portalra](http://portal.azure.com), és indítsa el a **CloudShell** szolgáltatást a parancssori felületi parancsok végrehajtásához, ahogy az az alábbi lépésekben látható.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-ás vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Lásd még

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
