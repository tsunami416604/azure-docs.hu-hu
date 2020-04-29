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
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536904"
---
# <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage-fiók létrehozása

A BlockBlobStorage fiók típusa lehetővé teszi, hogy a prémium szintű teljesítménnyel rendelkező blokk blobokat hozzon létre. Ez a típusú Storage-fiók olyan számítási feladatokhoz van optimalizálva, amelyek nagy tranzakciós díjszabással rendelkeznek, vagy amelyek nagyon gyors hozzáférési időt igényelnek. Ez a cikk bemutatja, hogyan hozhat létre BlockBlobStorage-fiókot a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

A BlockBlobStorage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nincs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a cikk a Azure PowerShell modult igényli az az Version 1.2.0 vagy újabb verzióra. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jelentkezzen be az Azure-ba, és futtassa az Azure CLI-parancsokat kétféleképpen:

- A CLI-parancsokat a Azure Cloud Shell Azure Portal belül is futtathatja.
- Telepítheti a CLI-t, és helyileg is futtathatja a CLI-parancsokat.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI előre telepítve és konfigurálva van a fiókjával való használatra. Kattintson a **Cloud Shell** gombra a Azure Portal jobb felső részén található menüben:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gomb egy interaktív felületet indít el, amelyet a jelen útmutatóban ismertetett lépések futtatására használhat:

[![A portál Cloud Shell ablakát ábrázoló képernyőfelvétel](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. Ehhez a cikkhez az Azure CLI 2.0.46 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

---

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

# <a name="portal"></a>[Portál](#tab/azure-portal)

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

A CLI helyi telepítésére való bejelentkezéshez futtassa az az [login](/cli/azure/reference-index#az-login) parancsot:

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage-fiók létrehozása

## <a name="portal"></a>[Portál](#tab/azure-portal)
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

   |Mező     |Érték  |
   |---------|---------|
   |**Teljesítmény**    |  Válassza a **prémium**lehetőséget.   |
   |**Fiók típusa**    | Válassza a **BlockBlobStorage**lehetőséget.      |
   |**Replikáció**    |  Hagyja meg a **helyileg redundáns tárolás (LRS)** alapértelmezett beállítását.      |

   ![Megjeleníti a portál felhasználói felületét egy blokk blob Storage-fiók létrehozásához](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. A Storage-fiók beállításainak áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.

1. Kattintson a **Létrehozás** gombra.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként).

1. A következő parancs futtatásával ellenőrizze, hogy telepítve van-e `Az` a PowerShell-modul legújabb verziója.

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

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

---

## <a name="next-steps"></a>További lépések

- A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)).
