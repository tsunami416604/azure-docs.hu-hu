---
title: Blokkblob-tárfiók létrehozása - Azure Storage | Microsoft dokumentumok
description: Bemutatja, hogyan hozhat létre egy Azure BlockBlobStorage-fiókot prémium szintű teljesítményjellemzőkkel.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536904"
---
# <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage-fiók létrehozása

A BlockBlobStorage fiók típusú lehetővé teszi, hogy blokk blobok prémium szintű teljesítményjellemzőkkel. Az ilyen típusú tárfiók magas tranzakciós rátával rendelkező vagy nagyon gyors hozzáférési időt igénylő számítási feladatokra van optimalizálva. Ez a cikk bemutatja, hogyan hozhat létre egy BlockBlobStorage-fiókot az Azure Portal, az Azure CLI vagy az Azure PowerShell használatával.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

A BlockBlobStorage-fiókokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nincs.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ez az útmutató cikk az Azure PowerShell-modul Az 1.2.0-s vagy újabb verzióját igényli. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bejelentkezhet az Azure-ba, és kétféleképpen futtathatja az Azure CLI-parancsokat:

- Cli-parancsokat futtathat az Azure Portalon belül, az Azure Cloud Shellben.
- Telepítheti a CLI-t, és helyileg futtathatja a CLI parancsokat.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI előre telepített és a fiókjával való használatra van konfigurálva. Kattintson a **Felhőshell** gombra az Azure Portal jobb felső részén található menüben:

[![Felhőhéj](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gomb egy interaktív rendszerhéjat indít el, amelynek segítségével futtathatja az útmutatóban ismertetett lépéseket:

[![Képernyőkép a Portál Felhőhéj ablakát ábrázoló képernyőképről](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. Ez az útmutató cikk megköveteli, hogy az Azure CLI 2.0.46-os vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

---

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

# <a name="portal"></a>[Portál](#tab/azure-portal)

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Jelentkezzen be az Azure-előfizetésbe a paranccsal, `Connect-AzAccount` és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure Cloud Shell elindításához jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

A CLI helyi telepítésébe való bejelentkezéshez futtassa az [az login](/cli/azure/reference-index#az-login) parancsot:

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>BlockBlobStorage-fiók létrehozása

## <a name="portal"></a>[Portál](#tab/azure-portal)
BlockBlobStorage-fiók létrehozásához az Azure Portalon kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza a Storage > **storage->** fiók > **minden szolgáltatás** **lehetőséget.**

1. A **Tárfiókok csoportban**válassza **a Hozzáadás**lehetőséget.

1. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre szeretné hozni a tárfiókot.

1. Az **Erőforráscsoport** mezőben jelöljön ki egy meglévő erőforráscsoportot, vagy válassza **az Új létrehozása**lehetőséget, és adja meg az új erőforráscsoport nevét.

1. A **Tárfiók neve** mezőben adja meg a fiók nevét. Vegye figyelembe az alábbi irányelveket:

   - A névnek egyedinek kell lennie az Azure-ban.
   - A név nek három és 24 karakter közötti nek kell lennie.
   - A név csak számokat és kisbetűket tartalmazhat.

1. A **Raktár** mezőben válassza ki a tárfiók helyét, vagy használja az alapértelmezett helyet.

1. A többi beállításhoz állítsa be a következőket:

   |Mező     |Érték  |
   |---------|---------|
   |**Teljesítmény**    |  Válassza a **Premium**lehetőséget.   |
   |**Számla fajta**    | Válassza **a BlockBlobStorage lehetőséget.**      |
   |**Replikációs**    |  Hagyja meg a **helyileg redundáns tárolás (LRS) alapértelmezett beállítását.**      |

   ![A portál felhasználói felületét jeleníti meg egy blokkblob-tárfiók létrehozásához](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Válassza **a Véleményezés + létrehozás** lehetőséget a tárfiók beállításainak áttekintéséhez.

1. Kattintson a **Létrehozás** gombra.

## <a name="azure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként).

1. Futtassa a következő parancsot, `Az` és győződjön meg arról, hogy a PowerShell-modul legújabb verziója telepítve van.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Nyisson meg egy új PowerShell-konzolt, és jelentkezzen be Azure-fiókjával.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Szükség esetén hozzon létre egy új erőforráscsoportot. Cserélje le az árajánlatokban lévő értékeket, és futtassa a következő parancsot.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Hozza létre a BlockBlobStorage-fiókot. Cserélje le az árajánlatokban lévő értékeket, és futtassa a következő parancsot.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával hozzon létre egy blokkblob-fiókot, először telepítenie kell az Azure CLI v-t. 2.0.46 vagy újabb verzió. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Jelentkezzen be Azure-előfizetésbe.

   ```azurecli
   az login
   ```

1. Szükség esetén hozzon létre egy új erőforráscsoportot. Cserélje le az értékeket zárójelben (a zárójelekkel együtt), és futtassa a következő parancsot.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Hozza létre a BlockBlobStorage-fiókot. Cserélje le az értékeket zárójelben (a zárójelekkel együtt), és futtassa a következő parancsot.

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

- A tárfiókokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

- Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
