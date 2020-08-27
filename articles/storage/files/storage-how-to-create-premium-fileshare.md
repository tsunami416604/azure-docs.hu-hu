---
title: Prémium szintű Azure-fájlmegosztás létrehozása
description: Ismerje meg, hogyan hozhat létre Azure Premium-fájlmegosztást a Azure Portal, a Azure PowerShell modul vagy az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 27bedb2a5d9f95632141ce332773e0f4f9c696d5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930638"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Prémium szintű Azure-fájlmegosztás létrehozása

A prémium szintű fájlmegosztás a SSD-tárolóeszközökön érhető el, és hasznos az IO-igényes számítási feladatokhoz, beleértve az üzemeltetési adatbázisokat és a nagy teljesítményű számítástechnikai (HPC) szolgáltatásokat. A prémium szintű fájlmegosztás a FileStorage-fióknak nevezett, speciális célú Storage-fiókban van tárolva. A prémium fájlmegosztás nagy teljesítményű és nagyvállalati szintű alkalmazásokhoz lett tervezve, amelyek konzisztens, alacsony késésű, magas IOPS és nagy átviteli sebességű megosztásokat biztosítanak.

Ez a cikk bemutatja, hogyan hozhatja létre ezt az új számlatípust a [Azure Portal](https://portal.azure.com/), a Azure PowerShell modul és az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Ha a Azure PowerShell modult szeretné használni, [telepítse a legújabb verziót](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>FileStorage-fiók létrehozása

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. A prémium szintű fájlmegosztás FileStorage-fiókot igényel.

# <a name="portal"></a>[Portál](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

Most már készen áll a Storage-fiók létrehozására.

1. A Azure Portal válassza a bal oldali menüben a **Storage-fiókok** lehetőséget.

    ![Azure Portal fő lap Storage-fiók kiválasztása](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. A **teljesítmény** beállításnál válassza a **prémium**lehetőséget.

    Ki kell választania a **prémium** szintű **FileStorage** , hogy elérhető legyen a **Fiók típusa** legördülő menüben.

1. Válassza a **Fiók típusa** lehetőséget, és válassza a **FileStorage**lehetőséget.
1. Hagyja meg a **replikálást** a **helyileg REDUNDÁNS tárolás (LRS)** alapértelmezett értékére.

    ![Storage-fiók létrehozása prémium fájlmegosztás esetén](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget.

A Storage-fiók erőforrásának létrehozása után navigáljon hozzá.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Használja az `Connect-AzAccount` parancsot, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

Ha be szeretné jelentkezni a CLI helyi telepítésére, győződjön meg arról, hogy rendelkezik a legújabb verzióval, majd jelentkezzen be:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

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
---

## <a name="create-a-premium-file-share"></a>Prémium szintű fájlmegosztás létrehozása

Most, hogy létrehozott egy FileStorage-fiókot, létrehozhat egy prémium fájlmegosztást a Storage-fiókon belül.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Storage-fiók bal oldali menüjében görgessen a **Fájlszolgáltatások** szakaszhoz, majd válassza a **fájlok**lehetőséget.
1. Prémium fájlmegosztás létrehozásához válassza a **fájlmegosztás** lehetőséget.
1. Adjon meg egy nevet és egy kívánt kvótát a fájlmegosztás számára, majd válassza a **Létrehozás**lehetőséget.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage/files/) olvasható.

   ![Prémium szintű fájlmegosztás létrehozása](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha prémium szintű fájlmegosztást szeretne létrehozni a Azure PowerShell modullal, használja a [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) parancsmagot.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage/files/) olvasható.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Prémium fájlmegosztás Azure CLI-vel való létrehozásához használja az az [Storage Share Create](/cli/azure/storage/share) parancsot.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage/files/) olvasható.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha meg szeretné tisztítani a cikkben létrehozott erőforrásokat, törölje az erőforráscsoportot. Az erőforráscsoport törlésével a társított Storage-fiók, valamint az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha meg szeretné tisztítani a cikkben létrehozott erőforrásokat, törölje az erőforráscsoportot. Az erőforráscsoport törlésével a társított Storage-fiók, valamint az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új Storage-fiókot is, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha meg szeretné tisztítani a cikkben létrehozott erőforrásokat, törölje az erőforráscsoportot. Az erőforráscsoport törlésével a társított Storage-fiók, valamint az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy prémium szintű fájlmegosztást hozott létre. Ha többet szeretne megtudni a fiók által kínált teljesítményről, folytassa a tervezési útmutató teljesítményszint szakaszával.

> [!div class="nextstepaction"]
> [Fájlmegosztási rétegek](storage-files-planning.md#storage-tiers)
