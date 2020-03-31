---
title: Create a storage account
titleSuffix: Azure Storage
description: Ismerje meg, hogy hozzon létre egy tárfiókot az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatával. Az Azure-tárfiók egyedi névteret biztosít a Microsoft Azure-ban az adatok tárolásához és eléréséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255404"
---
# <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Az Azure storage-fiók tartalmazza az összes Azure Storage-adatobjektumok: blobok, fájlok, várólisták, táblák és lemezek. A tárfiók egyedi névteret biztosít az Azure Storage-adatokhoz, amely a világ bármely pontjáról http- vagy HTTPS-en keresztül érhető el. Az Azure storage-fiókjában lévő adatok tartósak és magas rendelkezésre állásúak, biztonságosak és nagymértékben méretezhetők.

Ebben az útmutató cikkben megtudhatja, hogyan hozhat létre tárfiókot az [Azure Portal](https://portal.azure.com/), az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)vagy az [Azure Resource Manager-sablon](../../azure-resource-manager/management/overview.md)használatával.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nincs.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Azure-tárfiók létrehozásához a PowerShell, győződjön meg arról, hogy telepítette az Azure PowerShell-modul Az 0.7-es vagy újabb verziója. További információ: [Az Azure PowerShell Az modul bemutatása.](/powershell/azure/new-azureps-module-az)

Az aktuális verzió megkereséséhez futtassa a következő parancsot:

```powershell
Get-InstalledModule -Name "Az"
```

Az Azure PowerShell telepítéséről vagy frissítéséről az [Azure PowerShell-modul telepítése](/powershell/azure/install-Az-ps)című témakörben látható.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bejelentkezhet az Azure-ba, és kétféleképpen futtathatja az Azure CLI-parancsokat:

- Cli-parancsokat futtathat az Azure Portalon belül, az Azure Cloud Shellben.
- Telepítheti a CLI-t, és helyileg futtathatja a CLI parancsokat.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI előre telepített és a fiókjával való használatra van konfigurálva. Kattintson a **Felhőshell** gombra az Azure Portal jobb felső részén található menüben:

[![Felhőhéj](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gomb egy interaktív rendszerhéjat indít el, amelynek segítségével futtathatja az útmutatóban ismertetett lépéseket:

[![Képernyőkép a Portál Felhőhéj ablakát ábrázoló képernyőképről](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. Ez az útmutató cikk megköveteli, hogy az Azure CLI 2.0.4-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

# <a name="template"></a>[Sablon](#tab/template)

Nincs.

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

```azurecli-interactive
az login
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Create a storage account

Most már készen áll egy tárfiók létrehozására.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre új erőforráscsoportot.

Az **általános célú v2**-tárfiókok az összes Azure Storage-szolgáltatáshoz (blobokhoz, fájlokhoz, üzenetsorokhoz, táblákhoz és lemezekhez) hozzáférést biztosítanak. Az itt ismertetett lépések egy általános célú v2-es tárfiókot hoznak létre, de a tárfiók bármely típusának létrehozásához szükséges lépések hasonlóak.

# <a name="portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Először hozzon létre egy új erőforráscsoportot a PowerShell használatával a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Ha nem biztos abban, hogy melyik `-Location` régiót adja meg a paraméterhez, a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) paranccsal lekérheti az előfizetés támogatott régióinak listáját:

```powershell
Get-AzLocation | select Location
```

Ezután hozzon létre egy általános célú v2-es tárfiókot olvasási hozzáférésű georedundáns tárral (RA-GRS) a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) paranccsal. Ne feledje, hogy a tárfiók nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értéket a saját egyedi értékére:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Ha az [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)Storage `-EnableHierarchicalNamespace $True` használatát tervezi, szerepeljen a paraméterek listájában.

Ha egy általános célú v2-es tárfiókot szeretne létrehozni egy másik replikációs beállítással, helyettesítse a kívánt értéket az alábbi táblázatban a **SkuName** paraméterrel.

|Replikációs beállítás  |SkuName paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard_LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |
|Geozónaredundáns tárolás (GZRS) (előzetes verzió)    |Standard_GZRS         |
|Olvasási hozzáférésű geozónaredundáns tárolás (RA-GZRS) (előzetes verzió)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ezután hozza létre az új erőforráscsoportot az Azure CLI használatával, az [az group create](/cli/azure/group#az_group_create) paranccsal.

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Ha nem biztos abban, hogy melyik régiót kell megadnia a `--location` paraméterhez, az előfizetéséhez tartozó támogatott régiók listáját az [az account list-locations](/cli/azure/account#az_account_list) paranccsal kérheti le.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Ezután hozzon létre egy általános célú v2-es tárfiókot olvasási hozzáférésű georedundáns tárral az [az storage-fiók create](/cli/azure/storage/account#az_storage_account_create) parancs használatával. Ne feledje, hogy a tárfiók nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értéket a saját egyedi értékére:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Ha az [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)Storage `--enable-hierarchical-namespace true` használatát tervezi, szerepeljen a paraméterek listájában. 

Általános célú v2-es tárfiók létrehozásához egy másik replikációs beállítással, helyettesítse a kívánt értéket az alábbi táblázatban a **termékváltozat** paraméter.

|Replikációs beállítás  |sku paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard_LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |
|Geozónaredundáns tárolás (GZRS) (előzetes verzió)    |Standard_GZRS         |
|Olvasási hozzáférésű geozónaredundáns tárolás (RA-GZRS) (előzetes verzió)    |Standard_RAGZRS         |

# <a name="template"></a>[Sablon](#tab/template)

Az Azure Powershell vagy az Azure CLI használatával üzembe helyezhet egy Resource Manager-sablont egy tárfiók létrehozásához. Az útmutató cikkben használt sablon az [Azure Resource Manager rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-storage-account-create/) A parancsfájlok futtatásához válassza **a Próbálja ki** az Azure Cloud rendszerhéj megnyitásához. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, és válassza a **Beillesztés parancsot.**

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Ez a sablon csak példaként szolgál. Számos tárfiók-beállítás van, amelyek nincsenek konfigurálva a sablon részeként. Ha például [az Azure Data Lake Storage-t](https://azure.microsoft.com/services/storage/data-lake-storage/)szeretné használni, `isHnsEnabledad` módosítsa `StorageAccountPropertiesCreateParameters` ezt `true`a sablont az objektum tulajdonságának beállításával. 

A sablon módosításáról és újak létrehozásáról az e-mail ben olvashat:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/).
- [A tárfiók sablonjának hivatkozása](/azure/templates/microsoft.storage/allversions).
- [További tárfióksablon-minták](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Az elérhető replikációs beállításokkal kapcsolatban további információt a [Storage replikálási lehetőségeit](storage-redundancy.md) ismertető szakaszban talál.

## <a name="delete-a-storage-account"></a>Tárfiók törlése

A tárfiók törlése törli a teljes fiókot, beleértve a fiók összes adatát is, és nem vonható vissza.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Keresse meg a tárfiókot az [Azure Portalon.](https://portal.azure.com)
1. Kattintson a **Törlés** gombra.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A tárfiók törléséhez használja az [Eltávolítás-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A tárfiók törléséhez használja az [az storage fiók törlését](/cli/azure/storage/account#az-storage-account-delete) parancs:

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

A tárfiók törléséhez használja az Azure PowerShell vagy az Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Másik lehetőségként törölheti az erőforráscsoportot, amely törli a tárfiókot és az erőforráscsoport bármely más erőforrását. Az erőforráscsoportok törléséről az [Erőforráscsoport és az erőforrások törlése című](../../azure-resource-manager/management/delete-resource-group.md)témakörben talál további információt.

> [!WARNING]
> A törölt tárfiókokat nem lehet visszaállítani, és nem lehet a törlés előtt abban tárolt tartalmakat helyreállítani. A fiók törlése előtt készítsen biztonsági másolatot minden menteni kívánt tartalomról. Ez igaz a fiókban lévő összes erőforrásra is – ha töröl egy blobot, táblát, üzenetsort vagy fájlt, az véglegesen törölve lesz.
>
> Ha egy Azure virtuális géppel társított tárfiókot próbál törölni, egy hibaüzenetet kaphat, amely szerint a tárfiók még használatban van. A hiba elhárításához olvassa el a [Tárfiókok törlésekor előforduló hibák elhárítása című témakört.](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben létrehozott egy általános célú v2 standard szintű tárfiókot. Ha meg szeretné tudni, hogyan tölthet fel és tölthet le blobokat a tárfiókba és onnan, folytassa a Blob storage-rövid útmutatók egyikével.

# <a name="portal"></a>[Portál](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Blobok használata a PowerShell segítségével](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Blobok használata az Azure CLI használatával](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Sablon](#tab/template)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

---
