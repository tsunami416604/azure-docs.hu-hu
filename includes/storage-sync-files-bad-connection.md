---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391667"
---
Ez a hiba akkor fordulhat elő, ha a Azure File Sync szolgáltatás nem érhető el a kiszolgálóról. Ezt a hibát a következő lépésekkel végezheti el:

1. Ellenőrizze, hogy a tűzfal nem blokkolja-e a Windows `FileSyncSvc.exe` szolgáltatást.
2. Ellenőrizze, hogy a 443-es port nyitva van-e a kimenő kapcsolatokhoz a Azure File Sync szolgáltatással. Ezt a `Test-NetConnection` parancsmaggal teheti meg. Az alábbi `<azure-file-sync-endpoint>` helyőrző URL-címe a [Azure file Sync proxy-és tűzfalbeállítások](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) dokumentumban található. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Győződjön meg arról, hogy a proxy konfigurációja a várt módon van beállítva. Ezt a `Get-StorageSyncProxyConfiguration` parancsmaggal teheti meg. A Azure File Sync proxy-konfigurációjának konfigurálásával kapcsolatos további információkért tekintse meg a [Azure file Sync proxy és a tűzfal beállításait](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. További segítségért forduljon a hálózati rendszergazdához, és kérjen segítséget a hálózati kapcsolatról.