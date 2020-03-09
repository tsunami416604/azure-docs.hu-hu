---
title: Tárfiók létrehozása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre egy Storage-fiókot a Azure Portal, Azure PowerShell vagy az Azure CLI használatával. Az Azure Storage-fiók egy egyedi névteret biztosít a Microsoft Azure az adatai tárolásához és eléréséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397201"
---
# <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Egy Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: blobokat, fájlokat, várólistákat, táblákat és lemezeket. A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP-vagy HTTPS-kapcsolaton keresztül. Az Azure Storage-fiókban tárolt adatai tartósak, a biztonságos és a nagy mértékben méretezhetők.

Ebben a útmutatóban megtudhatja, hogyan hozhat létre egy Storage-fiókot a [Azure Portal](https://portal.azure.com/), a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)vagy egy [Azure Resource Manager sablon](../../azure-resource-manager/management/overview.md)használatával.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nincs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha Azure Storage-fiókot szeretne létrehozni a PowerShell-lel, győződjön meg arról, hogy telepítette Azure PowerShell modult az 0,7-es vagy újabb verzióra. További információkért lásd: [a Azure PowerShell bemutatása az modul](/powershell/azure/new-azureps-module-az).

A jelenlegi verziójának megkereséséhez futtassa a következő parancsot:

```powershell
Get-InstalledModule -Name "Az"
```

Azure PowerShell telepítéséhez vagy frissítéséhez tekintse meg a [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps)című témakört.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="template"></a>[Sablon](#tab/template)

Nincs.

---

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

# <a name="portal"></a>[Portal](#tab/azure-portal)

Jelentkezzen be az [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

A CLI helyi telepítésére való bejelentkezéshez futtassa az az [login](/cli/azure/reference-index#az-login) parancsot:

```azurecli-interactive
az login
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Most már készen áll egy Storage-fiók létrehozására.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

Az **általános célú v2**-tárfiókok az összes Azure Storage-szolgáltatáshoz (blobokhoz, fájlokhoz, üzenetsorokhoz, táblákhoz és lemezekhez) hozzáférést biztosítanak. Az itt leírt lépések egy általános célú v2-es Storage-fiókot hoznak létre, de a bármilyen típusú Storage-fiók létrehozásának lépései hasonlóak.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Először hozzon létre egy új erőforráscsoportot a PowerShell használatával a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Ha nem biztos abban, hogy melyik régiót kell megadnia a `-Location` paraméterhez, lekérheti az előfizetéséhez tartozó támogatott régiók listáját a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) paranccsal:

```powershell
Get-AzLocation | select Location
```

Következő lépésként hozzon létre egy általános célú v2-es Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancs használatával a Read-Access geo-redundáns tárolással (ra-GRS). Ne feledje, hogy a Storage-fiók nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Ha [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)használatát tervezi, a paraméterek listájában adja meg `-EnableHierarchicalNamespace $True`.

Egy másik replikációs lehetőséggel rendelkező általános célú v2 Storage-fiók létrehozásához helyettesítse be a kívánt értéket az alábbi táblázatban az **SkuName** paraméterhez.

|Replikációs beállítás  |SkuName paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard_LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |
|Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió)    |Standard_GZRS         |
|Olvasási hozzáférésű geo-Zone-redundáns tárolás (RA-GZRS) (előzetes verzió)    |Standard_RAGZRS         |

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

Ezután hozzon létre egy általános célú v2-es Storage-fiókot az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) paranccsal az olvasási hozzáférésű geo-redundáns tárolással. Ne feledje, hogy a Storage-fiók nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Ha [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)használatát tervezi, a paraméterek listájában adja meg `--enable-hierarchical-namespace true`. 

Egy másik replikációs lehetőséggel rendelkező általános célú v2 Storage-fiók létrehozásához helyettesítse be a kívánt értéket az alábbi táblázatban az **SKU** paraméterhez.

|Replikációs beállítás  |sku paraméter  |
|---------|---------|
|Helyileg redundáns tárolás (LRS)     |Standard_LRS         |
|Zónaredundáns tárolás (ZRS)     |Standard_ZRS         |
|Georedundáns tárolás (GRS)     |Standard_GRS         |
|Írásvédett georedundáns tárolás (GRS)     |Standard_RAGRS         |
|Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió)    |Standard_GZRS         |
|Olvasási hozzáférésű geo-Zone-redundáns tárolás (RA-GZRS) (előzetes verzió)    |Standard_RAGZRS         |

# <a name="template"></a>[Sablon](#tab/template)

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

> [!NOTE]
> Ez a sablon csak példaként szolgál. Számos olyan Storage-fiók beállításai vannak, amelyek nem a sablon részeként vannak konfigurálva. Ha például a [Azure Data Lake Storaget](https://azure.microsoft.com/services/storage/data-lake-storage/)szeretné használni, a sablont úgy kell módosítania, hogy az `StorageAccountPropertiesCreateParameters` objektum `isHnsEnabledad` tulajdonságát `true`értékre állítja. 

A sablon módosításával vagy újak létrehozásával kapcsolatos további információkért lásd:

- [Azure Resource Manager dokumentáció](/azure/azure-resource-manager/).
- A [Storage-fiók sablonjának referenciája](/azure/templates/microsoft.storage/allversions).
- [További, a Storage-fiók sablonjának mintái](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Az elérhető replikációs beállításokkal kapcsolatban további információt a [Storage replikálási lehetőségeit](storage-redundancy.md) ismertető szakaszban talál.

## <a name="delete-a-storage-account"></a>Tárfiók törlése

A Storage-fiók törlése törli a teljes fiókot, beleértve a fiókban lévő összes adattal, és nem vonható vissza.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigáljon a [Azure Portal](https://portal.azure.com)Storage-fiókjához.
1. Kattintson a **Delete** (Törlés) gombra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Storage-fiók törléséhez használja a [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Storage-fiók törléséhez használja az az [Storage Account delete](/cli/azure/storage/account#az-storage-account-delete) parancsot:

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

A Storage-fiók törléséhez használja a Azure PowerShell vagy az Azure CLI-t.

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

Másik lehetőségként törölheti az erőforráscsoportot, amely törli a Storage-fiókot és az erőforráscsoport egyéb erőforrásait. Az erőforráscsoportok törlésével kapcsolatos további információkért lásd: [erőforráscsoport és erőforrások törlése](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> A törölt tárfiókokat nem lehet visszaállítani, és nem lehet a törlés előtt abban tárolt tartalmakat helyreállítani. A fiók törlése előtt készítsen biztonsági másolatot minden menteni kívánt tartalomról. Ez igaz a fiókban lévő összes erőforrásra is – ha töröl egy blobot, táblát, üzenetsort vagy fájlt, az véglegesen törölve lesz.
>
> Ha egy Azure virtuális géppel társított tárfiókot próbál törölni, egy hibaüzenetet kaphat, amely szerint a tárfiók még használatban van. A hiba elhárításához a Storage- [fiókok törlésekor a hibák elhárítása](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)című témakörben talál segítséget.

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban egy általános célú v2 standard Storage-fiókot hozott létre. Ha meg szeretné tudni, hogyan tölthet fel és tölthet le blobokat a Storage-fiókjába, folytassa a blob Storage egyik rövid útmutatójának használatával.

# <a name="portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Blobok használata a PowerShell segítségével](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Blobok használata az Azure parancssori felületének használatával](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Sablon](#tab/template)

> [!div class="nextstepaction"]
> [Blobok használata az Azure Portal segítségével](../blobs/storage-quickstart-blobs-portal.md)

---
