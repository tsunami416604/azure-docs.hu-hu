---
title: Azure Data Lake Storage Gen2 Storage-fiók létrehozása | Microsoft Docs
description: Gyorsan megtudhatja, hogyan hozhat létre Data Lake Storage Gen2 hozzáféréssel rendelkező új Storage-fiókot a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e8deb8ed16186862349cecf70c9d617a4ad30399
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806898"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Azure Data Lake Storage Gen2 Storage-fiók létrehozása

Azure Data Lake Storage Gen2 [támogatja a hierarchikus névteret](data-lake-storage-introduction.md) , amely a Hadoop-ELOSZTOTT fájlrendszer (HDFS) való együttműködésre szabott natív címtár-alapú tárolót biztosít. A 2. generációs Data Lake Storage-adatok a HDFS-ből az [ABFS illesztőprogramon](data-lake-storage-abfs-driver.md) keresztül érhetők el.

Ez a cikk bemutatja, hogyan hozhat létre fiókot a Azure Portal, a Azure PowerShell vagy az Azure CLI segítségével.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

|           | Előfeltétel |
|-----------|--------------|
|Portál     | None         |
|PowerShell | Ehhez a cikkhez a PowerShell-modul az. Storage **0,7** -es vagy újabb verziójára van szükség. Az aktuális verzió megkereséséhez futtassa a `Get-Module -ListAvailable Az.Storage` parancsot. Ha a parancs futtatása után nem jelenik meg eredmény, vagy ha a **0,7** -nál kisebb verzió jelenik meg, akkor frissítenie kell a PowerShell-modult. Lásd a jelen útmutató [PowerShell-modul frissítése](#upgrade-your-powershell-module) című szakaszát.
|CLI        | Jelentkezzen be az Azure-ba, és futtassa az Azure CLI-parancsokat kétféleképpen: <ul><li>A CLI-parancsok az Azure Portalról, az Azure Cloud Shell felületén futtathatók </li><li>Telepítheti a parancssori felületet, így helyben is futtathatja a CLI-parancsokat</li></ul>|

Ha a parancssori felületet használja, futtathatja az Azure Cloud Shellt vagy telepítheti a parancssori felületet helyileg.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

A gomb egy interaktív felületet indít el, amelyet a cikkben ismertetett lépések futtatására használhat:

[![Képernyőkép a Portalon lévő Cloud Shell-ablakról](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. Ehhez a cikkhez az Azure CLI 2.0.38 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Azure Data Lake Storage Gen2-kompatibilis tárfiók létrehozása

Egy Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: blobokat, fájlokat, várólistákat, táblákat és lemezeket. A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatok számára, amely a világon bárhonnan elérhető HTTP-vagy HTTPS-kapcsolaton keresztül. Az Azure Storage-fiókban tárolt adatai tartósak, a biztonságos és a nagy mértékben méretezhetők.

> [!NOTE]
> A 2. generációs Data Lake Store funkcióinak használatához az új tárfiókokat **StorageV2 (általános célú V2)** típusúként kell létrehoznia.  

További információ a tárfiókokról: [Az Azure Storage-fiókok áttekintése](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Fiókok létrehozása az Azure Portalon

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

### <a name="create-a-storage-account"></a>Create a storage account

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

> [!NOTE]
> A hierarchikus névtér jelenleg minden nyilvános régióban elérhető.

1. Válassza ki azt az előfizetést, amelyikben létre kívánja hozni a tárfiókot.
2. A Azure Portal kattintson az **erőforrás létrehozása** gombra, majd válassza a Storage- **fiók**lehetőséget.
3. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Adja meg az új erőforráscsoport nevét.
   
   Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot.

4. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
5. Válassza ki a helyet.
6. Győződjön meg arról, hogy a **StorageV2 (általános célú v2)** a **Fiók típusa** legördülő listából kiválasztva jelenik meg.
7. Szükség esetén módosítsa a következő mezők értékeit: **teljesítmény**, **replikálás**, **hozzáférési szintek**. További információ ezekről a lehetőségekről: [Az Azure Storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Válassza a **speciális** lapot.
10. A **Data Lake Storage Gen2** szakaszban a **hierarchikus névtér** beállítása **engedélyezve**értékre.
11. Kattintson az **Ellenőrzés és létrehozás** elemre a tárfiók létrehozásához.

Ezzel a tárfiók létrejött a portálon.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő **Továbbiak** gombra ( **...** ).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="create-an-account-using-powershell"></a>Fiók létrehozása a PowerShell használatával

Először telepítse a [PowerShellGet](/powershell/scripting/gallery/installing-psget) modul legújabb verzióját.

Ezután frissítse a PowerShell-modult, jelentkezzen be az Azure-előfizetésbe, hozzon létre egy erőforráscsoportot, majd hozzon létre egy Storage-fiókot.

### <a name="upgrade-your-powershell-module"></a>A PowerShell-modul frissítése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a PowerShell használatával szeretne Data Lake Storage Gen2 kommunikálni, telepítenie kell a modult az. Storage **0,7** -es vagy újabb verziójára.

Először nyisson meg egy PowerShell-munkamenetet emelt szintű engedélyekkel.

Az az. Storage modul telepítése

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

Használja a `Login-AzAccount` parancsot, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Új erőforráscsoport PowerShell-lel való létrehozásához használja a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot: 

> [!NOTE]
> A hierarchikus névtér jelenleg minden nyilvános régióban elérhető.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

Ha egy általános célú v2-es Storage-fiókot szeretne létrehozni a PowerShellből a helyileg redundáns tárolással (LRS), használja a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancsot:

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új Storage-fiókot is, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Fiók létrehozása az Azure CLI használatával

Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

Ha be szeretné jelentkezni a CLI helyi telepítésére, futtassa a login parancsot:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>A CLI-bővítmény hozzáadása a Azure Data Lake Gen 2 számára

Ha a parancssori felülettel kívánja használni a Data Lake Storage Gen2t, hozzá kell adnia egy bővítményt a rendszerhéjhoz.

Ehhez írja be a következő parancsot a Cloud Shell vagy egy helyi rendszerhéj használatával: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha az Azure CLI használatával kíván új erőforráscsoportot létrehozni, használja az [az group create](/cli/azure/group) parancsot.

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > A hierarchikus névtér jelenleg minden nyilvános régióban elérhető.

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

Egy helyileg redundáns tárolást (LRS) használó általános célú v2-tárfiók létrehozásához az Azure CLI-ben használja az [az storage account create](/cli/azure/storage/account) parancsot.

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --enable-hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben Data Lake Storage Gen2 képességekkel rendelkező Storage-fiókot hozott létre. A következő témakörből megtudhatja, hogyan tölthet fel és tölthet le blobokat a Storage-fiókjából, illetve hogyan töltheti le azokat.

* [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
