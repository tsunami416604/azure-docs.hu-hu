---
title: Soft delete – Azure-fájlmegosztás engedélyezése
description: Megtudhatja, hogyan engedélyezheti az Azure-fájlmegosztás helyreállítható törlését az adathelyreállításhoz, és megakadályozhatja a véletlen törlést.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 70081114995d46e7c5db7c9549cfce2fdff42975
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116491"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>A Soft delete engedélyezése az Azure-fájlmegosztás esetében

Az Azure Storage helyreállítható törlést biztosít a fájlmegosztás számára, így könnyebben állíthatja helyre az adatokat, amikor egy alkalmazás vagy más Storage-fiók felhasználója véletlenül törölte azt. További információ a Soft delete szolgáltatásról: az [Azure-fájlmegosztás véletlen törlésének megakadályozása](storage-files-prevent-file-share-deletion.md).

A következő részekben bemutatjuk, hogyan engedélyezheti és használhatja a Soft delete használatát az Azure-fájlmegosztás számára egy meglévő Storage-fiókban:

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/).
1. Navigáljon a Storage-fiókjához, és válassza a **Soft delete** elemet a **file Service**lehetőségnél.
1. Válassza az **engedélyezve** lehetőséget a fájlmegosztás helyreállítható **törléséhez**.
1. Válassza ki a **fájlmegosztás megőrzési időtartamát napokban** , és adja meg a választott számot.
1. A **Mentés** gombra kattintva erősítse meg az adatmegőrzési beállításokat.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Képernyőfelvétel a Storage-fiók Soft delete Settings panelről. A fájlmegosztás kiemelése szakasz, a váltás engedélyezése, a megőrzési időtartam beállítása és a mentés. Ezzel a megoldással engedélyezheti a Storage-fiókban lévő összes fájlmegosztás helyreállítható törlését.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Soft delete engedélyezéséhez frissítenie kell egy fájl ügyfél szolgáltatásának tulajdonságait. A következő példa egy Storage-fiókban lévő összes fájlmegosztás esetében lehetővé teszi a Soft delete használatát:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Ellenőrizheti, hogy engedélyezve van-e a Soft DELETE, és megtekintheti az adatmegőrzési szabályzatot a következő paranccsal:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Helyreállítható törölt fájlmegosztás visszaállítása

# <a name="portal"></a>[Portál](#tab/azure-portal)

A törölt fájlmegosztás visszaállítása:

1. Navigáljon a Storage-fiókjához, és válassza a **fájlmegosztás**lehetőséget.
1. A fájlmegosztás panelen engedélyezze a **törölt megosztások megjelenítése** lehetőséget a törölt megosztások megjelenítéséhez.

    Ez megjeleníti a jelenleg **törölt** állapotban lévő megosztásokat is.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Ha az Állapot oszlop, a Name (név) oszlop melletti oszlop törölve értékre van állítva, akkor a fájlmegosztás nem megfelelő törlésű állapotban van. És véglegesen törölve lesz a megadott megőrzési idő után.":::

1. Válassza ki a megosztást, és válassza a **Törlés**visszavonása lehetőséget, ezzel visszaállítja a megosztást.

    Megerősítheti a megosztás visszaállítását, mivel az állapota **aktívra**vált.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Ha az Állapot oszlopban a Name (név) oszlop melletti oszlop aktív értékre van állítva, akkor a fájlmegosztás vissza lett állítva.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A helyreállítható törölt fájlmegosztás visszaállításához használja a következő parancsot:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Nem kötelező törlés letiltása

Ha szeretné leállítani a Soft delete használatát, vagy véglegesen törölni szeretne egy fájlmegosztást, kövesse az alábbi utasításokat:

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Navigáljon a Storage-fiókjához, és válassza a **Beállítások**alatt a **Soft Delete (Törlés** ) lehetőséget.
1. A **fájlmegosztás területen válassza a** **Letiltva** lehetőséget a fájlmegosztás helyreállítható **törléséhez**.
1. A **Mentés** gombra kattintva erősítse meg az adatmegőrzési beállításokat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="A Soft delete letiltásával azonnal és véglegesen törölheti a Storage-fiókban lévő összes fájlmegosztást a szabadidőben.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő parancs használatával tilthatja le a helyreállítható törlést a Storage-fiókjában:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>További lépések

Az adatvédelem és a helyreállítás más formájával kapcsolatos további információkért tekintse meg a [Azure Files-Pillanatképek megosztásának áttekintését](storage-snapshots-files.md)ismertető cikket.