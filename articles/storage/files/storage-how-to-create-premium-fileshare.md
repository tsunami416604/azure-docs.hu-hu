---
title: Prémium szintű Azure fájlmegosztás létrehozása
description: Ebből a cikkből megismerheti, hogyan hozhat létre egy prémium szintű Azure-beli fájlmegosztás.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 265a1cf0a8a5e1e099a4ec7a9f0d674e0c474dd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190100"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Egy prémium szintű Azure fájlmegosztás létrehozása
Prémium szintű fájlmegosztások (előzetes verzió) a tartós állapotú lemezt (SSD) adathordozók érhetők el, és hasznos i/o-igényes számítási feladatokhoz, például adatbázisok és a nagy teljesítményű feldolgozási (HPC). Prémium szintű fájlmegosztások az olyan speciális célú tárfiók típusának, egy FileStorage fiókja üzemelnek. Prémium szintű fájlmegosztások a magas teljesítmény és a vállalati alkalmazásokat, így konzisztens alacsony késleltetésű, magas iops-érték és nagy átviteli sebességű megosztások tervezték.

Ez a cikk bemutatja, hogyan hozzon létre az új fiók típusa [az Azure portal](https://portal.azure.com/), az Azure PowerShell és az Azure CLI.

## <a name="prerequisites"></a>Előfeltételek

Többek között a prémium szintű Azure-fájlmegosztások Azure-erőforrások eléréséhez szüksége lesz egy Azure-előfizetést. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Hozzon létre egy prémium szintű fájlmegosztást az Azure portal használatával

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Filestorage (előzetes verzió) storage-fiók létrehozása

Most már készen áll a tárfiók létrehozásához.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

1. Az Azure Portalon válassza ki a **Tárfiókok** a bal oldali menüben.

    ![Az Azure portál fő lapján válassza ki a tárfiókot](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. A **teljesítmény** kiválasztása **prémium**.
1. Válassza ki **fióktípus** válassza **FileStorage (előzetes verzió)** .
1. Hagyja **replikációs** , az alapértelmezett értékre való beállítása **helyileg redundáns tárolás (LRS)** .

    ![Fájlmegosztás prémium szintű storage-fiók létrehozása](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Kattintson a **Létrehozás** gombra.

Miután létrejött a tárfiók típusú erőforrást, keresse meg azt.

### <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

1. A bal oldali menüben, a tárfiók, görgessen a **Fájlszolgáltatás** területen, majd válassza ki **fájlok (előzetes verzió)** .
1. Válassza ki **+ fájlmegosztás** egy prémium szintű fájlmegosztás létrehozásához.
1. Adja meg a fájlmegosztás nevét és a egy kívánt kvótát, majd válassza ki **létrehozás**.

> [!NOTE]
> Létesített megosztás méretek által meghatározott a fájlmegosztás kvótájának fájlmegosztások számlázása a kiosztott méretét, tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/files/) további részletekért.

   ![Prémium szintű fájlmegosztás létrehozása](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné a jelen cikkben létrehozott erőforrásokat, egyszerűen törölheti az erőforráscsoportot. Emellett az erőforráscsoport törlése törli a kapcsolódó tárfiók, valamint az erőforráscsoporthoz társított összes többi erőforrást.

## <a name="create-a-premium-file-share-using-powershell"></a>Fájlmegosztás létrehozása egy prémium szintű PowerShell-lel

### <a name="create-an-account-using-powershell"></a>Fiók létrehozása a PowerShell használatával

Először telepítse a legújabb verzióját a [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) modul.

Ezután a powershell-modul frissítése, jelentkezzen be az Azure-előfizetése, hozzon létre egy erőforráscsoportot, és majd a storage-fiók létrehozása.

### <a name="upgrade-your-powershell-module"></a>A PowerShell-modul frissítése

Prémium szintű fájlmegosztás a PowerShell használatával kommunikál, kell a legújabb Az.Storage modul telepítése.

Először nyissa meg egy PowerShell-munkamenetet emelt szintű engedélyekkel.

A Az.Storage modul telepítése:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetés

Használja a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat hitelesítéséhez.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A PowerShell használatával egy új erőforráscsoport létrehozásához használja a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-preview-storage-account"></a>FileStorage (előzetes verzió) storage-fiók létrehozása

A PowerShell (előzetes verzió) filestorage storage-fiók létrehozásához használja a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancsot:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

Most, hogy van egy FileStorage fiókja, létrehozhat egy prémium szintű fájlmegosztást. Használja a [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) parancsmaggal hozzon létre egyet.

> [!NOTE]
> Létesített megosztás méretek által meghatározott a fájlmegosztás kvótájának fájlmegosztások számlázása a kiosztott méretét, tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/files/) további részletekért.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Fájlmegosztás létrehozása egy prémium szintű Azure CLI használatával

Azure Cloud Shell indításához jelentkezzen be a [az Azure portal](https://portal.azure.com).

Ha szeretne bejelentkezni a parancssori felület helyi telepítésen, futtassa a bejelentkezési parancsot:

```cli
az login
```

### <a name="add-the-preview-storage-cli-extension"></a>Az előzetes verzió storage CLI-bővítmény hozzáadása

Mivel a prémium szintű fájlmegosztások előzetes verziójú funkció, kell adja hozzá az előzetes verzió bővítményt parancshéjban. Ehhez adja meg a következő parancsot a Cloud Shellben vagy egy helyi shell segítségével: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ha az Azure CLI használatával kíván új erőforráscsoportot létrehozni, használja az [az group create](/cli/azure/group) parancsot.

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage storage-fiók létrehozása

FileStorage tárfiók létrehozása az Azure parancssori felületen, használja a [az tárfiók létrehozása](/cli/azure/storage/account) parancsot.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése

A storage-fiókban, ez a cikk az erőforrásokhoz való hozzáférésének tárfiókkulcsok, a kulcsot használunk ahhoz, hogy hozzon létre egy prémium szintű fájlmegosztást. A kulcsok automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys) paranccsal kérheti le a tárfiók tárfiókkulcsait:

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

Most, hogy van egy filestorage fiókja, létrehozhat egy prémium szintű fájlmegosztást. Használja a [az tárolási fájlmegosztás létrehozása](/cli/azure/storage/share) parancs használatával hozzon létre egyet.

> [!NOTE]
> Létesített megosztás méretek által meghatározott a fájlmegosztás kvótájának fájlmegosztások számlázása a kiosztott méretét, tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/files/) további részletekért.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy prémium szintű fájlmegosztást. Ennek a fióknak kínál a teljesítmény kapcsolatos további információkért folytassa a teljesítmény szint szakasz a tervezési útmutató.

> [!div class="nextstepaction"]
> [A fájl megosztási teljesítményszintek](storage-files-planning.md#file-share-performance-tiers)