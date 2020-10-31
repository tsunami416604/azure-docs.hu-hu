---
title: Soft delete – Azure-fájlmegosztás engedélyezése
description: Megtudhatja, hogyan engedélyezheti az Azure-fájlmegosztás helyreállítható törlését az adathelyreállításhoz, és megakadályozhatja a véletlen törlést.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126141"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>A Soft delete engedélyezése az Azure-fájlmegosztás esetében

Az Azure Storage helyreállítható törlést biztosít a fájlmegosztás számára, így könnyebben állíthatja helyre az adatokat, amikor egy alkalmazás vagy más Storage-fiók felhasználója véletlenül törölte azt. További információ a Soft delete szolgáltatásról: az [Azure-fájlmegosztás véletlen törlésének megakadályozása](storage-files-prevent-file-share-deletion.md).

A következő részekben bemutatjuk, hogyan engedélyezheti és használhatja a Soft delete használatát az Azure-fájlmegosztás számára egy meglévő Storage-fiókban:

# <a name="portal"></a>[Portál](#tab/azure-portal)

## <a name="getting-started"></a>Első lépések

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/).
1. Navigáljon a Storage-fiókjához, és válassza a **Soft delete** elemet a **file Service** lehetőségnél.
1. Válassza az **engedélyezve** lehetőséget a fájlmegosztás helyreállítható **törléséhez** .
1. Válassza ki a **fájlmegosztás megőrzési időtartamát napokban** , és adja meg a választott számot.
1. A **Mentés** gombra kattintva erősítse meg az adatmegőrzési beállításokat.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Képernyőfelvétel a Storage-fiók Soft delete Settings panelről. A fájlmegosztás kiemelése szakasz, a váltás engedélyezése, a megőrzési időtartam beállítása és a mentés. Ezzel a megoldással engedélyezheti a Storage-fiókban lévő összes fájlmegosztás helyreállítható törlését.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Előfeltétel

A Soft delete parancsmagok az az. Storage modul [3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0) -es verziójában érhetők el. 

## <a name="getting-started-with-powershell"></a>Bevezetés a PowerShell használatába

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

1. Navigáljon a Storage-fiókjához, és válassza a **fájlmegosztás** lehetőséget.
1. A fájlmegosztás panelen engedélyezze a **törölt megosztások megjelenítése** lehetőséget a törölt megosztások megjelenítéséhez.

    Ez megjeleníti a jelenleg **törölt** állapotban lévő megosztásokat is.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Képernyőfelvétel a Storage-fiók Soft delete Settings panelről. A fájlmegosztás kiemelése szakasz, a váltás engedélyezése, a megőrzési időtartam beállítása és a mentés. Ezzel a megoldással engedélyezheti a Storage-fiókban lévő összes fájlmegosztás helyreállítható törlését.":::

1. Válassza ki a megosztást, és válassza a **Törlés** visszavonása lehetőséget, ezzel visszaállítja a megosztást.

    Megerősítheti a megosztás visszaállítását, mivel az állapota **aktívra** vált.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Képernyőfelvétel a Storage-fiók Soft delete Settings panelről. A fájlmegosztás kiemelése szakasz, a váltás engedélyezése, a megőrzési időtartam beállítása és a mentés. Ezzel a megoldással engedélyezheti a Storage-fiókban lévő összes fájlmegosztás helyreállítható törlését.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Soft delete parancsmagok az az. Storage modul 3.0.0-es verziójában érhetők el. A helyreállítható törölt fájlmegosztás visszaállításához használja a következő parancsot:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Nem kötelező törlés letiltása

Ha szeretné leállítani a Soft delete használatát, vagy véglegesen törölni szeretne egy fájlmegosztást, kövesse az alábbi utasításokat:

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Navigáljon a Storage-fiókjához, és válassza a **Beállítások** alatt a **Soft Delete (Törlés** ) lehetőséget.
1. A **fájlmegosztás területen válassza a** **Letiltva** lehetőséget a fájlmegosztás helyreállítható **törléséhez** .
1. A **Mentés** gombra kattintva erősítse meg az adatmegőrzési beállításokat.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Képernyőfelvétel a Storage-fiók Soft delete Settings panelről. A fájlmegosztás kiemelése szakasz, a váltás engedélyezése, a megőrzési időtartam beállítása és a mentés. Ezzel a megoldással engedélyezheti a Storage-fiókban lévő összes fájlmegosztás helyreállítható törlését.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Soft delete parancsmagok az az. Storage modul 3.0.0-es verziójában érhetők el. A következő parancs használatával tilthatja le a helyreállítható törlést a Storage-fiókjában:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Következő lépések

Az adatvédelem és a helyreállítás más formájával kapcsolatos további információkért tekintse meg a [Azure Files-Pillanatképek megosztásának áttekintését](storage-snapshots-files.md)ismertető cikket.
