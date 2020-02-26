---
title: Prémium szintű Azure-fájlmegosztás létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre prémium szintű Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 237846ec3adda208126aeb22e7900cbf5118ee95
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598663"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Prémium szintű Azure-fájlmegosztás létrehozása
A prémium szintű fájlmegosztás a SSD-tárolóeszközökön érhető el, és hasznos az IO-igényes számítási feladatokhoz, beleértve az üzemeltetési adatbázisokat és a nagy teljesítményű számítástechnikai (HPC) szolgáltatásokat. A prémium szintű fájlmegosztás a FileStorage-fióknak nevezett, speciális célú Storage-fiókban van tárolva. A prémium fájlmegosztás nagy teljesítményű és nagyvállalati szintű alkalmazásokhoz lett tervezve, amelyek konzisztens, alacsony késésű, magas IOPS és nagy átviteli sebességű megosztásokat biztosítanak.

Ez a cikk bemutatja, hogyan hozhatja létre ezt az új fióktípus [Azure Portal](https://portal.azure.com/), Azure PowerShell és Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-erőforrások, például a prémium szintű Azure-fájlmegosztás eléréséhez Azure-előfizetésre van szükség. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Prémium fájlmegosztás létrehozása a Azure Portal használatával

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>FileStorage-fiók létrehozása

Most már készen áll a Storage-fiók létrehozására.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

1. A Azure Portal válassza a bal oldali menüben a **Storage-fiókok** lehetőséget.

    ![Azure Portal fő lap Storage-fiók kiválasztása](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. A **teljesítmény** beállításnál válassza a **prémium**lehetőséget.
1. Válassza a **Fiók típusa** lehetőséget, és válassza a **FileStorage**lehetőséget.
1. Hagyja meg a **replikálást** a **helyileg REDUNDÁNS tárolás (LRS)** alapértelmezett értékére.

    ![Storage-fiók létrehozása prémium fájlmegosztás esetén](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Kattintson a **Létrehozás** gombra.

A Storage-fiók erőforrásának létrehozása után navigáljon hozzá.

### <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

1. A Storage-fiók bal oldali menüjében görgessen a **Fájlszolgáltatások** szakaszhoz, majd válassza a **fájlok**lehetőséget.
1. Prémium fájlmegosztás létrehozásához válassza a **fájlmegosztás** lehetőséget.
1. Adjon meg egy nevet és egy kívánt kvótát a fájlmegosztás számára, majd válassza a **Létrehozás**lehetőséget.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik, további részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/storage/files/) .

   ![Prémium szintű fájlmegosztás létrehozása](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha meg szeretné tisztítani a cikkben létrehozott erőforrásokat, egyszerűen törölheti az erőforráscsoportot. Az erőforráscsoport törlésével a társított Storage-fiók, valamint az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

## <a name="create-a-premium-file-share-using-powershell"></a>Prémium fájlmegosztás létrehozása a PowerShell használatával

### <a name="create-an-account-using-powershell"></a>Fiók létrehozása a PowerShell használatával

Először telepítse a [PowerShellGet](/powershell/scripting/gallery/installing-psget) modul legújabb verzióját.

Ezután frissítse a PowerShell-modult, jelentkezzen be az Azure-előfizetésbe, hozzon létre egy erőforráscsoportot, majd hozzon létre egy Storage-fiókot.

### <a name="upgrade-your-powershell-module"></a>A PowerShell-modul frissítése

Ha prémium szintű fájlmegosztást szeretne használni a PowerShell-lel, telepítenie kell az az. Storage modul Version 1.4.0 vagy a legújabb az. Storage modult.

Először nyissa meg egy PowerShell-munkamenetet emelt szintű engedélyekkel.

Telepítse az az. Storage modult:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

Használja a `Connect-AzAccount` parancsot, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Új erőforráscsoport PowerShell-lel való létrehozásához használja a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage-fiók létrehozása

FileStorage Storage-fiók PowerShellből való létrehozásához használja a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) parancsot:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

Most, hogy rendelkezik egy FileStorage-fiókkal, létrehozhat egy prémium szintű fájlmegosztást. Hozzon létre egyet a [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) parancsmag használatával.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik, további részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/storage/files/) .

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új Storage-fiókot is, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Prémium fájlmegosztás létrehozása az Azure CLI-vel

Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

Ha be szeretné jelentkezni a CLI helyi telepítésére, először győződjön meg arról, hogy rendelkezik a legújabb verzióval, majd futtassa a login parancsot:

```cli
az login
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ha az Azure CLI használatával kíván új erőforráscsoportot létrehozni, használja az [az group create](/cli/azure/group) parancsot.

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage-fiók létrehozása

Az az [Storage Account Create](/cli/azure/storage/account) paranccsal hozzon létre egy FileStorage-fiókot az Azure CLI használatával.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése

A Storage-fiókok kulcsai a Storage-fiók erőforrásaihoz való hozzáférést szabályozzák. ebben a cikkben a kulcsot használjuk a prémium fájlmegosztás létrehozásához. A kulcsok automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys) paranccsal kérheti le a tárfiók tárfiókkulcsait:

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

Most, hogy rendelkezik egy FileStorage-fiókkal, létrehozhat egy prémium szintű fájlmegosztást. Hozzon létre egyet az az [Storage Share Create](/cli/azure/storage/share) paranccsal.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik, további részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/storage/files/) .

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

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy prémium szintű fájlmegosztást hozott létre. Ha többet szeretne megtudni a fiók által kínált teljesítményről, folytassa a tervezési útmutató teljesítményszint szakaszával.

> [!div class="nextstepaction"]
> [Fájlmegosztási rétegek](storage-files-planning.md#storage-tiers)