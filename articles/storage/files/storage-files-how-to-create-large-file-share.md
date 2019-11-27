---
title: Nagyméretű fájlmegosztás engedélyezése és létrehozása – Azure Files
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és hozhatja létre a nagyméretű fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422724"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Nagyméretű fájlmegosztás engedélyezése és létrehozása

A standard fájlmegosztás eredetileg legfeljebb 5 TiB-ra méretezhető, így a nagyméretű fájlmegosztás esetében akár 100 TiB-ra is méretezhető. Alapértelmezés szerint a prémium szintű fájlmegosztás akár 100 TiB-ra is méretezhető. 

A standard fájlmegosztás használatával akár 100 TiB-ra is szükség lehet a Storage-fiók nagyméretű fájlmegosztás használatára való méretezéséhez. Engedélyezheti egy meglévő fiókot, vagy létrehozhat egy új fiókot nagyméretű fájlmegosztás használatához.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- Ha az Azure CLI-t szeretné használni, győződjön meg arról, hogy [a legújabb verziót telepíti](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Ha Azure PowerShell szeretne használni, győződjön meg arról, hogy [a legújabb verziót telepíti](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Korlátozások

A nagyméretű fájlmegosztást engedélyező fiókok támogatják a LRS vagy a ZRS. Egyelőre a nagyméretű fájlmegosztást engedélyező fiókok nem támogatják a GZRS, a GRS és az RA-GRS. A nagyméretű fájlmegosztás egy fiókban való engedélyezése visszafordíthatatlan folyamat, miután engedélyezte a fiókját, nem alakítható át GZRS, GRS vagy RA-GRS értékre.

## <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

### <a name="portal"></a>Portál

Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Tárfiókok** lehetőséget.
1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Az alábbi képen látható módon nevezze el az új erőforráscsoportot.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válasszon egy helyet a Storage-fiókhoz, győződjön meg arról, hogy az [egyike a LFS által támogatott régióknak](storage-files-planning.md#regional-availability).
1. Állítsa a replikálást **helyileg redundáns tárolási** vagy **zóna-redundáns tárterületre**.
1. Ne módosítsa a következő mezők alapértelmezett értékeit:

   |Mező  |Érték  |
   |---------|---------|
   |Üzemi modell     |Resource Manager         |
   |Teljesítmény     |Standard         |
   |Fióktípus     |StorageV2 (általános célú v2)         |
   |Hozzáférési szint     |Gyakori         |

1. Válassza a **speciális** lehetőséget, majd a **nagyméretű fájlmegosztás**esetén válassza az **engedélyezve** lehetőséget.
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

    ![Large-file-shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Kattintson a **Létrehozás** gombra.


### <a name="cli"></a>parancssori felület

Először is győződjön meg arról, hogy [a legújabb verziót telepíti](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), így engedélyezheti a szolgáltatást.

Ha a nagyméretű fájlmegosztást engedélyező Storage-fiókot szeretne létrehozni, cserélje le `<yourStorageAccountName>`, `<yourResourceGroup>`és `<yourDesiredRegion>` értékeket az értékekkel, majd használja a következő parancsot:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Először is győződjön meg arról, hogy [a legújabb verziót telepíti](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), így engedélyezheti a szolgáltatást.

Ha a nagyméretű fájlmegosztást engedélyező Storage-fiókot szeretne létrehozni, cserélje le `<yourStorageAccountName>`, `<yourResourceGroup>`és `<yourDesiredRegion>` értékeket az értékekkel, majd használja a következő parancsot:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Engedélyezés meglévő fiókban

### <a name="portal"></a>Portál

A nagyméretű fájlmegosztást is engedélyezheti a meglévő fiókokon. Ha ezt teszi, a fiók többé nem lesz képes a GZRS, GRS vagy RA-GRS átalakításra. Ez a lehetőség nem vonható vissza ezen a fiókon.

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és navigáljon ahhoz a Storage-fiókhoz, amelyen engedélyezni szeretné a nagyméretű fájlmegosztást.
1. Nyissa meg a Storage-fiókot, és válassza a **konfiguráció**lehetőséget.
1. Válassza az **engedélyezve** lehetőséget a **nagyméretű fájlmegosztás**elemnél, majd kattintson a Mentés gombra.
1. Válassza az **Áttekintés** lehetőséget, és válassza a **frissítés**lehetőséget.

![Enable-Large-file-shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Ezzel engedélyezte a nagyméretű fájlmegosztás használatát a Storage-fiókban.

Ha a következő hibaüzenetet kapja: "a nagyméretű fájlmegosztás még nem érhető el a fiókhoz." Megvárhatja, amíg a régiója valószínűleg a bevezetésének a közepén van, vagy ha sürgősen szüksége van rá, forduljon a támogatási szolgálathoz.

### <a name="cli"></a>parancssori felület

Engedélyezheti a nagyméretű fájlmegosztást a meglévő fiókjaiban. Ha ezt teszi, a fiók többé nem lesz képes a GZRS, GRS vagy RA-GRS átalakításra. Ez a lehetőség nem vonható vissza ezen a fiókon.

Cserélje le `<yourStorageAccountName>` és `<yourResourceGroup>` a következő parancsra, majd használja a nagyméretű fájlmegosztás engedélyezésére a meglévő fiókjában:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Engedélyezheti a nagyméretű fájlmegosztást a meglévő fiókjaiban. Ha ezt teszi, a fiók többé nem lesz képes a GZRS, GRS vagy RA-GRS átalakításra. Ez a lehetőség nem vonható vissza ezen a fiókon.

Cserélje le `<yourStorageAccountName>` és `<yourResourceGroup>` a következő parancsra, majd használja a nagyméretű fájlmegosztás engedélyezésére a meglévő fiókjában:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Nagyméretű fájlmegosztás létrehozása

### <a name="portal"></a>Portál

A nagyméretű fájlmegosztás létrehozása majdnem azonos a szabványos fájlmegosztás létrehozásával. A fő különbség az, hogy a kvótát akár 100 TiB-re is beállíthatja.

1. A Storage-fiókban válassza a **fájlmegosztás**lehetőséget.
1. Válassza a **+ fájlmegosztás**lehetőséget.
1. Adjon meg egy nevet a fájlmegosztás számára, és (opcionálisan) a kvóta kívánt méretét, akár 100 TiB-t, majd válassza a **Létrehozás**lehetőséget. 

![Large-file-shares-Create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>parancssori felület

Ha engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, akkor a fiókban több kvótával is létrehozhat fájlmegosztást. Cserélje le a `<yourStorageAccountName>`, `<yourStorageAccountKey>`és `<yourFileShareName>` értéket az alábbi parancsban az értékekkel, majd a használatával nagyméretű fájlmegosztást is létrehozhat:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Ha engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, akkor a fiókban több kvótával is létrehozhat fájlmegosztást. Cserélje le a `<YourStorageAccountName>`, `<YourStorageAccountKey>`és `<YourStorageAccountFileShareName>` értéket az alábbi parancsban az értékekkel, majd a használatával nagyméretű fájlmegosztást is létrehozhat:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Meglévő fájlmegosztás kibontása

### <a name="portal"></a>Portál

Miután engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, kiterjesztheti a meglévő megosztásokat a magasabb kvótára.

1. A Storage-fiókban válassza a **fájlmegosztás**lehetőséget.
1. Kattintson a jobb gombbal a fájlmegosztás elemre, és válassza a **kvóta**lehetőséget.
1. Adja meg a kívánt új méretet, majd kattintson **az OK gombra**.

![Update-Large-file-share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>parancssori felület

Ha engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, akkor a fiókban meglévő fájlmegosztást kiterjesztheti a magasabb kvótára. Cserélje le a `<yourStorageAccountName>`, `<yourStorageAccountKey>`és `<yourFileShareName>` értéket az alábbi parancsban az értékekkel, majd a kvótát beállíthatja a maximális méretre:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Ha engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, akkor a fiókban meglévő fájlmegosztást kiterjesztheti a magasabb kvótára. Cserélje le a `<YourStorageAccountName>`, `<YourStorageAccountKey>`és `<YourStorageAccountFileShareName>` értéket az alábbi parancsban az értékekkel, majd a kvótát beállíthatja a maximális méretre:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Következő lépések

* [Fájlmegosztás csatlakoztatása – Windows](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása – Linux](../storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása – macOS](storage-how-to-use-files-mac.md)