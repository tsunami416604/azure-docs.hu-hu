---
title: Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban
description: Azonosíthatja és elháríthatja az Azure-fájlmegosztás fájljainak törlési hibáját.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74196483"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban

A fájlok Azure-Fájlmegosztásből való törlésének sikertelensége több tünettel is rendelkezhet:

**1. tünet:**

Az alábbi két probléma egyike miatt nem sikerült törölni egy fájlt az Azure file share szolgáltatásban:

* A törlésre kijelölt fájl
* Lehetséges, hogy a megadott erőforrást SMB-ügyfél használja

**2. tünet:**

Nem áll rendelkezésre elegendő kvóta a parancs feldolgozásához

## <a name="cause"></a>Ok

1816-as hiba történik, amikor eléri a fájlhoz engedélyezett egyidejű nyitott kezelők felső korlátját azon a számítógépen, amelyen a fájlmegosztás csatlakoztatva van. További információ: az [Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Megoldás:

Csökkentse az egyidejű megnyitott fogópontok számát néhány leíró bezárásával.

## <a name="prerequisite"></a>Előfeltétel

### <a name="install-the-latest-azure-powershell-module"></a>A legújabb Azure PowerShell modul telepítése

* [Az Azure PowerShell-modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Kapcsolódás az Azure-hoz:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Válassza ki a cél Storage-fiók előfizetését:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Környezet létrehozása a cél Storage-fiókhoz:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>A fájlmegosztás aktuálisan megnyitott leíróinak beolvasása:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Példa eredménye:

|HandleId|Útvonal|ClientIp (Ügyfél IP-címe)|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Új mappa/test. zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|teszt. zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Megnyitott leíró lezárása:

A megnyitott leíró bezárásához használja a következő parancsot:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>További lépések

* [A Windows Azure Files hibáinak megoldása](storage-troubleshoot-windows-file-connection-problems.md)
* [A Linux Azure Files hibáinak megoldása](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure-fájlok szinkronizálásának hibaelhárítása](storage-sync-files-troubleshoot.md)