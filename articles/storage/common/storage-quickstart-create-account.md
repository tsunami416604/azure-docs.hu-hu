---
title: Storage-fiók létrehozása – Azure Storage
description: Ebben a útmutatóban megtudhatja, hogyan hozhat létre egy Storage-fiókot a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával. Az Azure Storage-fiók egyedi névteret biztosít a Microsoft Azure-ban a létrehozott Azure Storage-adatobjektumok tárolásához és eléréséhez.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 338ba468fb1442488da78f09fc72a22186cd0a51
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673217"
---
# <a name="create-a-storage-account"></a>Tárfiók létrehozása

Egy Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: blobokat, fájlokat, várólistákat, táblákat és lemezeket. A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP-vagy HTTPS-kapcsolaton keresztül. Az Azure Storage-fiókban tárolt adatai tartósak, a biztonságos és a nagy mértékben méretezhetők.

Ebben a útmutatóban megtudhatja, hogyan hozhat létre egy Storage-fiókot a [Azure Portal](https://portal.azure.com/), a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)vagy egy [Azure Resource Manager sablon](../../azure-resource-manager/resource-group-overview.md)használatával.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Nincs.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ennek a cikknek a használatához a Azure PowerShell modul az 0,7-es vagy újabb verziója szükséges. Az aktuális verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Jelentkezzen be az Azure-ba, és futtassa az Azure CLI-parancsokat kétféleképpen:

- A CLI-parancsokat a Azure Cloud Shell Azure Portal belül is futtathatja.
- Telepítheti a CLI-t, és helyileg is futtathatja a CLI-parancsokat.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI előre telepítve és konfigurálva van a fiókjával való használatra. Kattintson a **Cloud Shell** gombra a Azure Portal jobb felső részén található menüben:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gomb egy interaktív felületet indít el, amelyet a jelen útmutatóban ismertetett lépések futtatására használhat:

[![Képernyőkép a Portalon lévő Cloud Shell-ablakról](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

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

Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

A CLI helyi telepítésére való bejelentkezéshez futtassa az az [login](/cli/azure/reference-index#az-login) parancsot:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Ezzel készen áll a tárfiók létrehozására.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

Az **általános célú v2**-tárfiókok az összes Azure Storage-szolgáltatáshoz (blobokhoz, fájlokhoz, üzenetsorokhoz, táblákhoz és lemezekhez) hozzáférést biztosítanak. Az itt leírt lépések egy általános célú v2-es Storage-fiókot hoznak létre, de a bármilyen típusú Storage-fiók létrehozásának lépései hasonlóak.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Először hozzon létre egy új erőforráscsoportot a PowerShell használatával a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Ha nem biztos abban, hogy melyik régiót kell megadnia a `-Location` paraméterhez, lekérheti az előfizetéséhez tartozó támogatott régiók listáját a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) paranccsal:

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Következő lépésként hozzon létre egy általános célú v2-es Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancs használatával a Read-Access geo-redundáns tárolással (ra-GRS). Ne feledje, hogy a Storage-fiók nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Egy másik replikációs lehetőséggel rendelkező általános célú v2 Storage-fiók létrehozásához helyettesítse be a kívánt értéket az alábbi táblázatban az **SkuName** paraméterhez.

|Replikációs beállítás  |SkuName paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard szintű LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |
|Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió)    |Standard_GZRS         |
|Olvasási hozzáférésű geo-Zone-redundáns tárolás (RA-GZRS) (előzetes verzió)    |Standard_RAGZRS         |

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

Ezután hozzon létre egy általános célú v2-es Storage-fiókot az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) paranccsal az olvasási hozzáférésű geo-redundáns tárolással. Ne feledje, hogy a Storage-fiók nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Egy másik replikációs lehetőséggel rendelkező általános célú v2 Storage-fiók létrehozásához helyettesítse be a kívánt értéket az alábbi táblázatban az **SKU** paraméterhez.

|Replikációs beállítás  |sku paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard szintű LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |
|Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió)    |Standard_GZRS         |
|Olvasási hozzáférésű geo-Zone-redundáns tárolás (RA-GZRS) (előzetes verzió)    |Standard_RAGZRS         |

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Az Azure PowerShell vagy az Azure CLI használatával üzembe helyezhet egy Resource Manager-sablont egy Storage-fiók létrehozásához. Az ebben a útmutatóban használt sablon [Azure Resource Manager Gyorsindítás sablonokból](https://azure.microsoft.com/resources/templates/101-storage-account-create/)származik. A parancsfájlok futtatásához válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**lehetőséget.

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

A sablonok létrehozásával kapcsolatos további információkért lásd:

- [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/).
- A [Storage-fiók sablonjának referenciája](/azure/templates/microsoft.storage/allversions).
- [További, a Storage-fiók sablonjának mintái](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Az elérhető replikációs beállításokkal kapcsolatban további információt a [Storage replikálási lehetőségeit](storage-redundancy.md) ismertető szakaszban talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha meg szeretné tisztítani a jelen útmutatóban létrehozott erőforrásokat, törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő **Továbbiak** gombra ( **...** ).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új Storage-fiókot is, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot:

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Ha el szeretné távolítani az erőforráscsoportot és a hozzá tartozó erőforrásokat, beleértve az új Storage-fiókot, használja a Azure PowerShell vagy az Azure CLI-t.

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

Ebben a útmutatóban egy általános célú v2 standard Storage-fiókot hozott létre. Ha meg szeretné tudni, hogyan tölthet fel és tölthet le blobokat a Storage-fiókjába, folytassa a blob Storage egyik rövid útmutatójának használatával.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Blobok használata a PowerShell segítségével](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Blobok használata az Azure parancssori felületének használatával](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

---
