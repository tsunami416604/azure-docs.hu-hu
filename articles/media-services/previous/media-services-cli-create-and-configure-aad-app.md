---
title: Az Azure CLI használatával azure AD-alkalmazást hozhat létre, és konfigurálhatja az Azure Media Services API eléréséhez | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja az Azure CLI-t egy Azure AD-alkalmazás létrehozásához, és konfigurálhatja az Azure Media Services API eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70035799"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLI használata Azure AD-alkalmazások létrehozásához és a Media Services API eléréséhez való konfigurálásához 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez a témakör bemutatja, hogyan használhatja az Azure CLI-t egy Azure Active Directory (Azure AD) alkalmazás és egyszerű szolgáltatás létrehozásához az Azure Media Services-erőforrások eléréséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. További részletek az [Azure ingyenes próbaverziója.](https://azure.microsoft.com/pricing/free-trial/) 
- Egy Media Services-fiók. További információ: [Azure Media Services-fiók létrehozása az Azure Portalon.](media-services-portal-create-account.md)

## <a name="use-the-azure-cloud-shell"></a>Az Azure Cloud Shell használata

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Indítsa el a Cloud Shell-t a portál felső navigációs ablaktáblájáról.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

További információ: [Az Azure Cloud Shell áttekintése.](../../cloud-shell/overview.md)

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Hozzon létre egy Azure AD-alkalmazást, és konfigurálja a médiafiókhoz való hozzáférést az Azure CLI-vel
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Példa:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Ebben a példában a **hatókör** a médiaszolgáltatások fiókjának teljes erőforráselérési útja. A **hatály** azonban bármilyen szinten lehet.

Például ez a következő szintek egyike lehet:
 
* Az **előfizetési** szint.
* Az **erőforráscsoport** szintje.
* Az **erőforrás** szint (például egy Médiafiók).

További információ: [Azure service principal with the Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Lásd még: [Szerepköralapú hozzáférés-vezérlés kezelése az Azure parancssori felületével.](../../role-based-access-control/role-assignments-cli.md) 

## <a name="next-steps"></a>További lépések

Kezdje el feltölteni a [fájlokat a fiókjába.](media-services-portal-upload-files.md)
