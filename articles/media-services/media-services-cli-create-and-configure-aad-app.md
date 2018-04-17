---
title: Parancssori felület 2.0 segítségével az Azure AD-alkalmazás létrehozása és konfigurálása az Azure Media Services API eléréséhez |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan CLI 2.0 használata az Azure AD-alkalmazás létrehozása és konfigurálása az Azure Media Services API eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: b640455b151d0e5d4701b8c076ee1a587b92f5b6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Parancssori felület 2.0 használatával hozzon létre egy AAD-alkalmazást, és konfigurálja úgy, hogy az Azure Media Services API eléréséhez

Ez a témakör bemutatja, hogyan CLI 2.0 használatával hozzon létre egy Azure Active Directory (Azure AD) alkalmazás és szolgáltatás egyszerű Azure Media Services-erőforrások eléréséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [hozzon létre egy Azure Media Services-fiók az Azure portál használatával](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Az Azure-felhőbe rendszerhéj használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Indítsa el a felhő rendszerhéj a portál a felső navigációs ablaktáblán.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

További információkért lásd: [áttekintés az Azure felhőalapú rendszerhéj](../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Az Azure AD-alkalmazás létrehozása és az adathordozó-fiókhoz való hozzáférés konfigurálása a CLI 2.0
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Példa:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Ebben a példában a **hatókör** van a teljes erőforrás elérési útja a Media services-fiók. Azonban a **hatókör** bármely szinten lehet.

Például lehet a következő szintek közül:
 
* A **előfizetés** szintjét.
* A **erőforráscsoport** szintjét.
* A **erőforrás** szint (például egy adathordozó fiókot).

További információkért lásd: [hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Lásd még: [Manage Role-Based hozzáférés-vezérlés az Azure parancssori felület](../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlok feltöltése a fiókjához](media-services-portal-upload-files.md).
