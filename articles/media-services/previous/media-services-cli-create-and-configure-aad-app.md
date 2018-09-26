---
title: Azure CLI használatával az Azure AD-alkalmazás létrehozása és konfigurálása, hogy az Azure Media Services API eléréséhez |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható az Azure CLI az Azure AD-alkalmazás létrehozása és konfigurálása, hogy az Azure Media Services API eléréséhez.
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
ms.openlocfilehash: fcd0ea10bd39f9e7252e114e8d6401a4fe0ecadb
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096548"
---
# <a name="use-azure-cli-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Azure CLI használatával az AAD-alkalmazás létrehozása és konfigurálása, hogy az Azure Media Services API eléréséhez

Ez a témakör bemutatja, hogyan hozzon létre egy Azure Active Directory (Azure AD-) alkalmazás és egyszerű szolgáltatás Azure Media Services-erőforrások eléréséhez az Azure CLI használatával. 

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [hozzon létre egy Azure Media Services-fiók az Azure portal használatával](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Az Azure Cloud Shell használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Indítsa el a Cloud Shellben a felső navigációs panelen, a portál.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

További információkért lásd: [áttekintése az Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Az Azure AD-alkalmazás létrehozása és az adathordozó-fiókhoz való hozzáférés konfigurálása az Azure CLI-vel
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Példa:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Ebben a példában a **hatókör** van a teljes erőforrás elérési útja a Media services-fiók. Azonban a **hatókör** bármilyen szinten is lehet.

Például annak oka az lehet a következő szintek egyikét:
 
* A **előfizetés** szintjét.
* A **erőforráscsoport** szintjét.
* A **erőforrás** szintet (például adathordozó-fiók).

További információkért lásd: [egy Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

További tájékoztatás [Manage Role-Based hozzáférés-vezérlés az Azure parancssori felületével](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlokat tölthet fel a fiók](media-services-portal-upload-files.md).
