---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488025"
---
Ez a hiba fordulhat elő, amikor az Azure File Sync szolgáltatás nem érhető el a kiszolgálóról. Ez a hiba elhárításához feldolgozása révén az alábbi lépéseket:

1. Ellenőrizze a Windows-szolgáltatás `FileSyncSvc.exe` nem blokkolja a tűzfal.
2. Győződjön meg arról, hogy a 443-as porton a kimenő kapcsolatokat, az Azure File Sync szolgáltatás nyitva-e. Az ehhez a `Test-NetConnection` parancsmagot. Az URL-címet a `<azure-file-sync-endpoint>` helyőrző alább tekintheti a [Azure File Sync proxy és tűzfal beállításainak](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) dokumentum. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Győződjön meg arról, hogy a proxy konfigurációs van beállítva, az elvárt módon működik. Az ehhez a `Get-StorageSyncProxyConfiguration` parancsmagot. A proxykonfiguráció konfigurálásáról az Azure File Sync megtalálható a [Azure File Sync proxy és tűzfal beállításainak](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Hibaelhárítás a hálózati kapcsolat további segítségért forduljon a rendszergazdához.