---
title: Hozzon létre egy storage-fiók – Azure Storage
description: Az útmutató a cikkben megismerheti hozzon létre egy tárfiókot, az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatával. Az Azure Storage-fiók egyedi névteret biztosít a Microsoft Azure-ban a létrehozott Azure Storage-adatobjektumok tárolásához és eléréséhez.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234175"
---
# <a name="create-a-storage-account"></a>Tárfiók létrehozása

Egy Azure storage-fiókot tartalmazza az összes az Azure Storage-adatobjektumok: blobok, fájlok, üzenetsorok, táblák és lemezek. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatok által elérhető, bárhol a világon HTTP vagy HTTPS. Az Azure storage-fiókja adatai tartós és magas rendelkezésre állású, biztonságos és rugalmasan méretezhető.

Az útmutató a cikkből megtudhatja, hozzon létre egy tárolási fiókot a [az Azure portal](https://portal.azure.com/), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview), [Azure CLI-vel](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), vagy egy [Azure Resource Manager sablon](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Nincs.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A cikkben található útmutató az Azure PowerShell-modul Az 0,7 vagy újabb verziója szükséges. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Jelentkezzen be az Azure-ba, és az Azure CLI parancsainak futtatásához a két módszer egyikével:

- Az Azure Cloud Shellben futtathatja a CLI-parancsok az Azure Portalon.
- A CLI telepítése, és a helyi CLI-parancsok futtatásához.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI-vel előre telepítve és a fiókjával való használat konfigurálva van. Kattintson a **Cloud Shell** az Azure portal jobb felső részén a menü gombra:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gombra kattintva jelenik, amely segítségével útmutató ebben a cikkben ismertetett lépések futtassa interaktív shell:

[![Képernyőkép a Portalon lévő Cloud Shell-ablakról](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. A cikkben található útmutató megköveteli, hogy futnak-e az Azure CLI 2.0.4-es vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Nincs.

---

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat hitelesítéséhez.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure Cloud Shell indításához jelentkezzen be a [az Azure portal](https://portal.azure.com).

Jelentkezzen be a parancssori felület helyi telepítésen, futtassa a [az bejelentkezési](/cli/azure/reference-index#az-login) parancsot:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Ezzel készen áll a tárfiók létrehozására.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

Az **általános célú v2**-tárfiókok az összes Azure Storage-szolgáltatáshoz (blobokhoz, fájlokhoz, üzenetsorokhoz, táblákhoz és lemezekhez) hozzáférést biztosítanak. Az itt leírt lépéseket egy általános célú v2-tárfiók létrehozása, de bármilyen típusú storage-fiók létrehozásának lépései is hasonlóak.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Először hozzon létre egy új erőforráscsoportot a PowerShell a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Ha nem biztos abban, hogy melyik régiót kell megadnia a `-Location` paramétert, kérheti a támogatott régiók listáját az előfizetéséhez tartozó a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsot:

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Ezután hozzon létre egy általános célú v2-tárfiók az írásvédett georedundáns tárolás (RA-GRS) használatával a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancsot. Ne feledje, hogy a tárfiók nevére egyedinek kell lenniük Azure-ban, így zárójelben a helyőrző értékét cserélje le a saját egyedi érték:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Egy másik replikációs beállítással egy általános célú v2-tárfiók létrehozásához illessze be a kívánt értéket az alábbi táblázat a **SkuName** paraméter.

|Replikációs beállítás  |SkuName paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard_LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

Ezután hozzon létre egy általános célú v2-tárfiók az írásvédett georedundáns tárolás használatával az [az tárfiók létrehozása](/cli/azure/storage/account#az_storage_account_create) parancsot. Ne feledje, hogy a tárfiók nevére egyedinek kell lenniük Azure-ban, így zárójelben a helyőrző értékét cserélje le a saját egyedi érték:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Egy másik replikációs beállítással egy általános célú v2-tárfiók létrehozásához illessze be a kívánt értéket az alábbi táblázat a **termékváltozat** paraméter.

|Replikációs beállítás  |sku paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard_LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Azure Powershell vagy az Azure CLI használatával hozzon létre egy tárfiókot a Resource Manager-sablon üzembe helyezése. Az útmutató a cikkben használt sablon [Azure Resource Manager gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A parancsfájlok futtatásához válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**.

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

Sablonok létrehozásával kapcsolatban lásd:

- [Az Azure Resource Manager dokumentációja](/azure/azure-resource-manager/).
- [Storage-fiók sablonreferenciában](/azure/templates/microsoft.storage/allversions).
- [További tárolási fiók sablonminták](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Az elérhető replikációs beállításokkal kapcsolatban további információt a [Storage replikálási lehetőségeit](storage-redundancy.md) ismertető szakaszban talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné a cikkben található útmutató által létrehozott erőforrásokat, törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő **Továbbiak** gombra ( **...** ).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot:

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Azure PowerShell vagy az Azure CLI használatával távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>További lépések

Az útmutató a cikkben létrehozott egy általános célú v2 standard szintű tárfiókot. Megtudhatja, hogyan fel-és letölteni az BLOB storage-fiókjában, folytassa a Blob storage rövid útmutatók egyik.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Blobok használata a PowerShell segítségével](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Az Azure CLI-vel blobok használata](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

---
