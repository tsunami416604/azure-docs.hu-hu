---
title: Az Azure Data Lake Storage Gen2-storage-fiók létrehozása |} A Microsoft Docs
description: Gyorsan megismerheti a Data Lake Storage Gen2-re az Azure Portalon, az Azure PowerShell vagy az Azure CLI használatával hozzáféréssel rendelkező új tárfiók létrehozása
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 256d79b743195d35d84e266acfc8b7ba220cb055
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975879"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-storage-account"></a>Gyors útmutató: Azure Data Lake Storage Gen2-storage-fiók létrehozása

Az Azure Data Lake Storage Gen2 [támogatja a hierarchikus Namespace szolgáltatás](data-lake-storage-introduction.md) directory-alapú natív biztosító fájlrendszer működik együtt a Hadoop elosztott fájlrendszer (HDFS) felhasználva. A 2. generációs Data Lake Storage-adatok a HDFS-ből az [ABFS illesztőprogramon](data-lake-storage-abfs-driver.md) keresztül érhetők el.

Ez a rövid útmutató bemutatja, hogyan hozhat létre fiókot az [Azure Portal](https://portal.azure.com/), az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

|           | Előfeltétel |
|-----------|--------------|
|Portál     | None         |
|PowerShell | Ez a rövid útmutatóhoz a PowerShell-modul Az.Storage verzió **0,7** vagy újabb. Az aktuális verzió azonosításához futtassa a `Get-Module -ListAvailable Az.Storage` parancsot. Ha nincs találat jelenik meg ez a parancs futtatása után, vagy ha egy eltérő verziójú **0,7** jelenik meg, akkor az a powershell-modul frissítéséhez. Tekintse meg a [frissítése a powershell-modul](#upgrade-your-powershell-module) című szakaszában talál.
|parancssori felület        | Az Azure-ba történő bejelentkezéshez és az Azure CLI parancsainak futtatásához az alábbi két módszer egyikét használhatja: <ul><li>A CLI-parancsok az Azure Portalról, az Azure Cloud Shell felületén futtathatók </li><li>Telepítheti a parancssori felületet, így helyben is futtathatja a CLI-parancsokat</li></ul>|

Ha a parancssori felületet használja, futtathatja az Azure Cloud Shellt vagy telepítheti a parancssori felületet helyileg.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gombra kattintva megjelenik egy interaktív kezelőfelület jelenik, amelyet az ebben a rövid útmutatóban található lépések futtatására használhat:

[![Képernyőkép a Portalon lévő Cloud Shell-ablakról](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. A rövid útmutatóhoz az Azure CLI 2.0.38-as vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Azure Data Lake Storage Gen2-kompatibilis tárfiók létrehozása

Mielőtt létrehozna egy fiókot, először létre kell hoznia egy erőforráscsoportot, amely logikai tárolóként szolgál majd a tárfiókok és bármely egyéb létrehozott Azure-erőforrás számára. Ha törölni szeretné a jelen rövid útmutató által létrehozott erőforrásokat, akkor egyszerűen törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó tárfiók, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek. További információt az erőforráscsoportokkal kapcsolatban [az Azure Resource Manager áttekintésében](../../azure-resource-manager/resource-group-overview.md) találhat.

> [!NOTE]
> A 2. generációs Data Lake Store funkcióinak használatához az új tárfiókokat **StorageV2 (általános célú V2)** típusúként kell létrehoznia.  

További információ a tárfiókokról: [Az Azure Storage-fiókok áttekintése](../common/storage-account-overview.md).

Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- A tárfiók nevének egyedinek kell lennie az Azure rendszerben. Két tárfióknak nem lehet azonos neve.

## <a name="create-an-account-using-the-azure-portal"></a>Fiókok létrehozása az Azure Portalon

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Portalon a következő lépések végrehajtásával hozhat létre egy erőforráscsoportot:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget.
2. Új erőforráscsoport hozzáadásához kattintson a **Hozzáadás** gombra.
3. Adja meg az új erőforráscsoport nevét.
4. Válassza ki azt az előfizetést, amelyben létre kívánja hozni az új erőforráscsoportot.
5. Válassza ki az erőforráscsoport helyét.
6. Kattintson a **Létrehozás** gombra.  

   ![Az erőforráscsoportok Azure Portalon történő létrehozását bemutató képernyőkép](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

> [!NOTE]
> A hierarchikus névtér jelenleg minden nyilvános régióban elérhető. Szuverén felhőkben jelenleg nem érhető el.

1. Az Azure Portalon nyissa ki bal oldalon a szolgáltatásmenüt, és válassza a **Minden szolgáltatás** lehetőséget. Ezután görgessen le a **Storage** szakaszig, és válassza a **Storage-fiókok** lehetőséget. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
2. Válassza ki a **előfizetés** és a **erőforráscsoport** korábban létrehozott.
3. Adja meg a tárfiók nevét.
4. A **Hely** mezőben adja meg az **USA 2. nyugati régiója** értéket.
5. Hagyja meg a következő mezők alapértelmezett értékeit: **teljesítmény**, **fióktípus**, **replikációs**, **hozzáférési szint**.
6. Válassza ki azt az előfizetést, amelyikben létre kívánja hozni a tárfiókot.
7. Válassza ki **tovább: Speciális >**
8. Az értékek területen hagyja **biztonsági** és **virtuális hálózatok** mezők megmaradnak az alapértelmezett értéke.
9. A **2. generációs Data Lake Storage (előzetes verzió)** szakaszban állítsa a **Hierarchikus névtér** beállítást **Engedélyezve** értékre.
10. Kattintson a **felülvizsgálat + létrehozás** a tárfiók létrehozásához.

    ![A tárfiók Azure Portalon történő létrehozását bemutató képernyőkép](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

Ezzel a tárfiók létrejött a portálon.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő **Továbbiak** gombra (**...**).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="create-an-account-using-powershell"></a>Fiók létrehozása a PowerShell használatával

Először telepítse a legújabb verzióját a [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) modul.

Ezután a powershell-modul frissítése, jelentkezzen be az Azure-előfizetése, hozzon létre egy erőforráscsoportot, és majd a storage-fiók létrehozása.

### <a name="upgrade-your-powershell-module"></a>A PowerShell-modul frissítése

Modul Az.Storage verziót telepíteni kell a PowerShell használatával kommunikálhat a Data Lake Storage Gen2, **0,7** vagy újabb.

Először nyissa meg egy PowerShell-munkamenetet emelt szintű engedélyekkel.

Ezután határozza meg, ha rendelkezik a telepített AzureRM.Storage modulban.

```powershell
Get-Module -ListAvailable AzureRM.Storage
```

Ha egy modul jelenik meg, majd távolítsa el azt.

```powershell
Uninstall-Module AzureRM.Storage -Force
```

A Az.Storage modul telepítése

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion .07 –AllowPrerelease –AllowClobber –Force
```

Az AzureRM kompatibilitási mód engedélyezéséhez.

```powershell
Enable-AzureRMAlias
```

Kompatibilitási üzemmódját, az azt jelenti, hogy minden olyan parancsfájlok, amelyek használják az AzureRM.Storage modulban továbbra is működhetnek annak ellenére már eltávolította a AzureRM.Storage modulban.

> [!NOTE]
> Powershell Az Azure-modulok olyan az előnyben részesített modulok az Azure-szolgáltatásokat a Powershell használatához. További tudnivalókért lásd: [az Azure PowerShell-lel Az új modul bevezetése](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azurermps-6.13.0).

### <a name="log-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetéshez

Használja a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat hitelesítéséhez.

```powershell
Login-AzureRmAccount
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az új erőforráscsoport PowerShell használatával történő létrehozása a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal történik: 

> [!NOTE]
> A hierarchikus névtér jelenleg minden nyilvános régióban elérhető. Szuverén felhőkben jelenleg nem érhető el.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

Egy helyileg redundáns tárolást (LRS) használó általános célú v2-tárfiók létrehozásához a PowerShellben használja a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) parancsot:

```powershell
$location = "westus2"

New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is: 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Fiók létrehozása az Azure CLI használatával

Azure Cloud Shell indításához jelentkezzen be a [az Azure portal](https://portal.azure.com).

Ha szeretne bejelentkezni a parancssori felület helyi telepítésen, futtassa a bejelentkezési parancsot:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>A CLI-bővítmény hozzáadása az Azure Data Lake általános 2

A parancssori felület használatával kommunikálhat a Data Lake Storage Gen2, kell adja hozzá egy bővítményt parancshéjban.

Ehhez adja meg a következő parancsot a Cloud Shellben vagy egy helyi shell segítségével: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ha az Azure CLI használatával kíván új erőforráscsoportot létrehozni, használja az [az group create](/cli/azure/group#az_group_create) parancsot.

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > A hierarchikus névtér jelenleg minden nyilvános régióban elérhető. Szuverén felhőkben jelenleg nem érhető el.

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

Egy helyileg redundáns tárolást (LRS) használó általános célú v2-tárfiók létrehozásához az Azure CLI-ben használja az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot.

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Data Lake Storage Gen2-funkciókkal rendelkező tárfiókot. Megtudhatja, hogyan fel-és letölteni az BLOB storage-fiókjába, a következő témakörben talál.

* [Az AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)