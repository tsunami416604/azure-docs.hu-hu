---
title: Blokk blob Storage-fiók létrehozása – Azure Storage | Microsoft Docs
description: Bemutatja, hogyan hozhat létre egy prémium szintű teljesítménnyel rendelkező Azure BlockBlobStorage-fiókot.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881864"
---
# <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage-fiók létrehozása

A BlockBlobStorage fiók típusa lehetővé teszi, hogy a prémium szintű teljesítménnyel rendelkező blokk blobokat hozzon létre. Ez a típusú Storage-fiók olyan számítási feladatokhoz van optimalizálva, amelyek nagy tranzakciós díjszabással rendelkeznek, vagy amelyek nagyon gyors hozzáférési időt igényelnek. Ez a cikk bemutatja, hogyan hozhat létre BlockBlobStorage-fiókot a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával.

A BlockBlobStorage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Ha BlockBlobStorage-fiókot szeretne létrehozni a Azure Portalban, kövesse az alábbi lépéseket:

1. A Azure Portal válassza a **minden szolgáltatás** > a **tárolási** kategória > **Storage-fiókok**lehetőséget.

1. A **Storage-fiókok**területen válassza a **Hozzáadás**lehetőséget.

1. Az **előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre szeretné hozni a Storage-fiókot.

1. Az **erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget, és adja meg az új erőforráscsoport nevét.

1. A **Storage-fiók neve** mezőben adja meg a fiók nevét. Vegye figyelembe a következő irányelveket:

   - A névnek egyedinek kell lennie az Azure-ban.
   - A névnek három – 24 karakter hosszúnak kell lennie.
   - A név csak számokat és kisbetűket tartalmazhat.

1. A **hely** mezőben válassza ki a Storage-fiók helyét, vagy használja az alapértelmezett helyet.

1. A többi beállításnál konfigurálja a következőket:

   |Mező     |Value (Díj)  |
   |---------|---------|
   |**Teljesítmény**    |  Válassza a **prémium**lehetőséget.   |
   |**Fióktípus**    | Válassza a **BlockBlobStorage**lehetőséget.      |
   |**Replikáció**    |  Hagyja meg a **helyileg redundáns tárolás (LRS)** alapértelmezett beállítását.      |

   ![Megjeleníti a portál felhasználói felületét egy blokk blob Storage-fiók létrehozásához](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. A Storage-fiók beállításainak áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.

1. Kattintson a **Létrehozás** gombra.

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként).

1. A következő parancs futtatásával ellenőrizze, hogy telepítve van-e a `Az` PowerShell-modul legújabb verziója.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Nyisson meg egy új PowerShell-konzolt, és jelentkezzen be az Azure-fiókjával.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Ha szükséges, hozzon létre egy új erőforráscsoportot. Cserélje le az értékeket az idézőjelek között, és futtassa a következő parancsot.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Hozza létre a BlockBlobStorage-fiókot. Cserélje le az értékeket az idézőjelek között, és futtassa a következő parancsot.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával szeretne blokk-blob-fiókot létrehozni, először telepítenie kell az Azure CLI v-t. 2.0.46 vagy újabb verzió. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Jelentkezzen be az Azure-előfizetésbe.

   ```azurecli
   az login
   ```

1. Ha szükséges, hozzon létre egy új erőforráscsoportot. Cserélje le a zárójelben lévő értékeket (beleértve a zárójeleket is), majd futtassa a következő parancsot.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Hozza létre a BlockBlobStorage-fiókot. Cserélje le a zárójelben lévő értékeket (beleértve a zárójeleket is), majd futtassa a következő parancsot.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Következő lépések

- További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
