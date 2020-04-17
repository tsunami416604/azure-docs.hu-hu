---
title: Nagy fájlmegosztások engedélyezése és létrehozása – Azure Files
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és hozhat létre nagy fájlmegosztásokat.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537678"
---
# <a name="enable-and-create-large-file-shares"></a>Nagy fájlmegosztások engedélyezése és létrehozása

Ha engedélyezi a nagy fájlmegosztásokat a tárfiókban, a fájlmegosztások akár 100 TiB-re is méretezhetők. Ezt a méretezést engedélyezheti a meglévő tárfiókok a meglévő fájlmegosztások.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
- Ha az Azure CLI-t kívánja használni, [telepítse a legújabb verziót.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Ha az Azure PowerShellt kívánja használni, [telepítse a legújabb verziót.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="restrictions"></a>Korlátozások

Egyelőre csak helyileg redundáns tárolás (LRS) vagy zónaredundáns tárolás (ZRS) használható nagy fájlmegosztás-kompatibilis fiókokon. Nem használhat geozónaredundáns tárolást (GZRS), georedundáns tárolást (GRS) vagy olvasási hozzáférésű georedundáns tárolást (RA-GRS).
A nagy fájlmegosztások engedélyezése egy fiókon visszafordíthatatlan folyamat. Miután engedélyezte, nem tudja konvertálni fiókját GZRS, GRS vagy RA-GRS-re.

## <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

### <a name="portal"></a>Portál

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. 
1. Az erőforrások listájában adja meg a **Tárfiókok .** Gépelés közben a lista a bevitt adatok alapján szűr. Válassza a **Tárfiókok lehetőséget.**
1. A megjelenő **Tárházfiókok** ablakban válassza a **Hozzáadás**lehetőséget.
1. Válassza ki a tárfiók létrehozásához használt előfizetést.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Adja meg az új erőforráscsoport nevét.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ezután adja meg a tárfiók nevét. A névnek egyedinek kell lennie az Azure-ban. A névnek 3 és 24 karakter között kell lennie, és csak számokat és kisbetűket tartalmazhat.
1. Válassza ki a tárhelyfiók helyét.
1. Állítsa a **replikációt helyileg redundáns vagy** **zónaredundáns tárolóra.**
1. Hagyja a mezőket az alapértelmezett értékükön:

   |Mező  |Érték  |
   |---------|---------|
   |Üzemi modell     |Resource Manager         |
   |Teljesítmény     |Standard         |
   |Fióktípus     |StorageV2 (általános célú v2)         |
   |Hozzáférési szint     |Gyakori         |

1. Válassza a **Speciális**lehetőséget, majd a **Nagy fájlmegosztások**jobb oldalán található **Engedélyezve** választógombot.
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

    ![Képernyőkép az Azure Portalúj tárfiókjának "Engedélyezve" választógombjával](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Kattintson a **Létrehozás** gombra.

### <a name="cli"></a>parancssori felület

Először [telepítse az Azure CLI legújabb verzióját,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) hogy nagy fájlmegosztásokat engedélyezhessen.

Nagy fájlmegosztásokkal rendelkező tárfiókolétrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` `<yourResourceGroup>`ki `<yourDesiredRegion>` a , és az adatokat.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Először [telepítse a PowerShell legújabb verzióját,](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) hogy nagy fájlmegosztásokat engedélyezhessen.

Nagy fájlmegosztásokkal rendelkező tárfiókolétrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` `<yourResourceGroup>`ki `<yourDesiredRegion>` a , és az adatokat.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Nagyméretű fájlmegosztások engedélyezése meglévő fiókban

A meglévő fiókokon is engedélyezhet nagy fájlmegosztásokat. Ha nagy fájlmegosztásokat engedélyez, nem konvertálhat GZRS-re, GRS-re vagy RA-GRS-re. A nagy fájlmegosztások engedélyezése visszafordíthatatlan ezen a tárfiókon.

### <a name="portal"></a>Portál

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com)és nyissa meg azt a tárfiókot, ahol engedélyezni szeretné a nagy fájlmegosztásokat.
1. Nyissa meg a tárfiókot, és válassza a **Konfiguráció**lehetőséget.
1. Válassza az **Engedélyezve** **nagyfájlmegosztásokon**lehetőséget, majd a **Mentés**lehetőséget.
1. Válassza **az Áttekintés** lehetőséget, majd a **Frissítés**lehetőséget.

![Az Engedélyezve választógomb kiválasztása egy meglévő tárfiókon az Azure Portalon](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Most már engedélyezte a nagy fájlmegosztásokat a tárfiókban. Ezután frissítenie kell a meglévő megosztáskvótáját, hogy kihasználja a megnövekedett kapacitást és a méretezést.

Ha a következő hibaüzenet jelenik meg: "A fiókhoz még nem érhetők el nagy fájlmegosztások", akkor előfordulhat, hogy a régió a bevezetés befejezése előtt van. Ha sürgősen nagy fájlmegosztásra van szüksége, forduljon az ügyfélszolgálathoz.

### <a name="cli"></a>parancssori felület

Ha nagy fájlmegosztásokat szeretne engedélyezni a meglévő fiókjában, használja a következő parancsot. Cserélje `<yourStorageAccountName>` `<yourResourceGroup>` ki és az adatait.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Ha nagy fájlmegosztásokat szeretne engedélyezni a meglévő fiókjában, használja a következő parancsot. Cserélje `<yourStorageAccountName>` `<yourResourceGroup>` ki és az adatait.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Nagy fájlmegosztás létrehozása

Miután engedélyezte a nagy fájlmegosztásokat a tárfiókban, magasabb kvótákkal hozhat létre fájlmegosztásokat a fiókban. 

### <a name="portal"></a>Portál

Nagy fájlmegosztás létrehozása majdnem megegyezik a szabványos fájlmegosztás létrehozásával. A fő különbség az, hogy legfeljebb 100 TiB kvótát állíthat be.

1. A tárfiókban válassza a **Fájlmegosztások**lehetőséget.
1. Válassza a **+ Fájlmegosztás** lehetőséget.
1. Adja meg a fájlmegosztás nevét. A kívánt kvótaméretet is beállíthatja, legfeljebb 100 TiB-ig. Ezután válassza **a Létrehozás lehetőséget.** 

![Az Azure Portal felhasználói felülete a név és a kvóta mezőket jeleníti meg](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>parancssori felület

Nagy fájlmegosztás létrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` `<yourStorageAccountKey>`ki `<yourFileShareName>` a , és az adatokat.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Nagy fájlmegosztás létrehozásához használja a következő parancsot. Cserélje `<YourStorageAccountName>` `<YourStorageAccountKey>`ki `<YourStorageAccountFileShareName>` a , és az adatokat.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Meglévő fájlmegosztások kibontása

Miután engedélyezte a nagy fájlmegosztásokat a tárfiókban, a fiókban meglévő fájlmegosztásokat is kiterjesztheti a magasabb kvótára. 

### <a name="portal"></a>Portál

1. A tárfiókban válassza a **Fájlmegosztások**lehetőséget.
1. Kattintson a jobb gombbal a fájlmegosztásra, és válassza **a Kvóta parancsot.**
1. Írja be a kívánt új méretet, majd kattintson az **OK gombra.**

![Az Azure Portal felhasználói felülete a meglévő fájlmegosztások kvótájával](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>parancssori felület

A kvóta maximális méretének beállításához használja a következő parancsot. Cserélje `<yourStorageAccountName>` `<yourStorageAccountKey>`ki `<yourFileShareName>` a , és az adatokat.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

A kvóta maximális méretének beállításához használja a következő parancsot. Cserélje `<YourStorageAccountName>` `<YourStorageAccountKey>`ki `<YourStorageAccountFileShareName>` a , és az adatokat.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>További lépések

* [Fájlmegosztás csatlakoztatása és csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása és csatlakoztatása Linuxon](storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása és csatlakoztatása macOS rendszeren](storage-how-to-use-files-mac.md)
