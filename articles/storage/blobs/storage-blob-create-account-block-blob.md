---
title: Hozzon létre egy block blob storage-fiók – Azure Storage |} A Microsoft Docs
description: Bemutatja, hogyan hozhat létre egy Azure block blob storage-fiókot a prémium szintű teljesítményt nyújt.
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
author: twooley
ms.author: twooley
ms.subservice: blobs
ms.openlocfilehash: 16ecade7a8d0049e098bb06bd14828b19934f5f5
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373045"
---
# <a name="create-a-block-blob-storage-account"></a>Block blob storage-fiók létrehozása

A block blob storage-fiók típusú használatával hozhatók létre a blokkblobok használatát támogatják a prémium szintű teljesítményt nyújt. Ez a tárfióktípus magas tranzakciók díjak a számítási feladatokhoz van optimalizálva. vagy igénylő nagyon gyors elérés időpontját. Ez a cikk bemutatja, hogyan block blob storage-fiók létrehozása az Azure Portalon, az Azure CLI-vel vagy az Azure PowerShell használatával.

További információ a block blob storage-fiókok, lásd: lásd: [az Azure storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Fiók létrehozása az Azure Portalon

Az Azure Portalon block blob storage-fiók létrehozásához kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a **minden szolgáltatás** > a **tárolási** kategória > **tárfiókok**.

1. A **tárfiókok**válassza **Hozzáadás**.

1. Az a **előfizetés** mezőben válassza ki az előfizetést, amelyben a tárfiók létrehozásához.

1. Az a **erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása**, és adja meg az új erőforráscsoport nevét.

1. Az a **tárfióknevet** mezőben adjon meg egy nevet a fiókhoz. Vegye figyelembe a következő irányelveket:

   - A nevének egyedinek kell lennie a Azure-ban.
   - A névnek kell lennie 3 és 24 közötti karakternél hosszabb.
   - A név csak számokat és kisbetűket tartalmazhatnak.

1. Az a **hely** mezőben válassza ki a tárfiók helyét, vagy használja az alapértelmezett helyet.

1. A többi beállítást adja meg a következőket:

   |Mező     |Érték  |
   |---------|---------|
   |**Teljesítmény**    |  Válassza ki **prémium**.   |
   |**Fióktípus**    | Válassza ki **BlockBlobStorage**.      |
   |**Replikáció**    |  Ne módosítsa az alapértelmezett, **helyileg redundáns tárolás (LRS)**.      |

   ![Megjeleníti a portál felhasználói Felületét block blob storage-fiók létrehozása](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Válassza ki **tekintse át + létrehozása** a tárfiók-beállítások áttekintéséhez.

1. Kattintson a **Létrehozás** gombra.

### <a name="create-account-using-azure-powershell"></a>Fiók létrehozása az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet (Futtatás rendszergazdaként).

1. Futtassa a következő parancsot, hogy a legújabb verzióját a `Az` PowerShell-modul telepítve van.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Nyisson meg egy új PowerShell-konzolt, és jelentkezzen be az Azure-fiókjával.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Szükség esetén hozzon létre egy új erőforráscsoportot. Cserélje le az értékeket az ajánlatok, és futtassa a következő parancsot.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. A block blob storage-fiók létrehozása. Cserélje le az értékeket az ajánlatok, és futtassa a következő parancsot.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Fiók létrehozása az Azure CLI használatával

Block blob-fiók létrehozása az Azure CLI-vel, először telepítenie kell az Azure parancssori felület. 2.0.46 vagy újabb verziója. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Jelentkezzen be az Azure-előfizetéshez.

   ```azurecli
   az login
   ```

1. Szükség esetén hozzon létre egy új erőforráscsoportot. Cserélje le a zárójelben (beleértve a zárójeleket) az értékeket, és futtassa a következő parancsot.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. A block blob storage-fiók létrehozása. Cserélje le a zárójelben (beleértve a zárójeleket) az értékeket, és futtassa a következő parancsot.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>További lépések

- További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
