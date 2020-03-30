---
title: Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban
description: Azonosítsa és hárítsa el a fájlok Azure File Share-ből való törlésének sikertelenségét.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196483"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-fájlmegosztás – nem sikerült a fájlok törlése valamelyik Azure-fájlmegosztásban

A fájlok Azure Fájlmegosztásból való törlésének sikertelenségének több tünete is lehet:

**1. tünet:**

Az alábbi két probléma egyike miatt nem sikerült törölni egy fájlt az azure-fájlmegosztásban:

* A törlésre megjelölt fájl
* Előfordulhat, hogy a megadott erőforrást egy SMB-ügyfél használja

**2. tünet:**

Nincs elég kvóta a parancs feldolgozásához

## <a name="cause"></a>Ok

A 1816-os hiba akkor fordul elő, amikor eléri a fájl hoz a fájlhoz engedélyezett egyidejű nyitott leírók felső korlátját azon a számítógépen, amelyen a fájlmegosztás tvan. További információt az [Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistájában](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)talál.

## <a name="resolution"></a>Megoldás:

Néhány fogópont bezárásával csökkentse az egyidejűnyitott fogópontok számát.

## <a name="prerequisite"></a>Előfeltétel

### <a name="install-the-latest-azure-powershell-module"></a>A legújabb Azure PowerShell-modul telepítése

* [Az Azure PowerShell-modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Csatlakozás az Azure-hoz:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Válassza ki a céltárfiók előfizetését:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Hozzon létre környezetet a céltárfiókhoz:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>A fájlmegosztás aktuális megnyitott leíróinak beszerezése:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Példa eredmény:

|HandleId|Útvonal|ClientIp (Ügyfél IP-címe)|Ügyfélport|OpenTime (Nyílt idő)|Last ReconnectTime|Fájlazonosító|Szülőazonosító|Munkamenet|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Új mappa/teszt.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|teszt.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Nyitott fogópont bezárása:

Nyitott leíró bezárásához használja a következő parancsot:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>További lépések

* [Azure-fájlok – problémamegoldás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure-fájlok – problémamegoldás Linux alatt](storage-troubleshoot-linux-file-connection-problems.md)
* [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)