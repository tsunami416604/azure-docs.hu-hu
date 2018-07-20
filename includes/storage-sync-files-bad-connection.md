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
ms.openlocfilehash: 754562487f0fe9f825107445a3059cc15a1faa26
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39146239"
---
Ez a hiba fordulhat elő, amikor az Azure File Sync szolgáltatás nem érhető el a kiszolgálóról. Ez a hiba elhárításához feldolgozása révén az alábbi lépéseket:

1. Ellenőrizze a Windows-szolgáltatás `FileSyncSvc.exe` nem blokkolja a tűzfal.
2. Győződjön meg arról, hogy a 443-as porton a kimenő kapcsolatokat, az Azure File Sync szolgáltatás nyitva-e. Az ehhez a `Test-NetConnection` parancsmagot. Az URL-címet a `<azure-file-sync-endpoint>` helyőrző alább tekintheti a [Azure File Sync proxy és tűzfal beállításainak](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) dokumentum. 

    ```PowerShell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Győződjön meg arról, hogy a proxy konfigurációs van beállítva, az elvárt módon működik. Az ehhez a `Get-StorageSyncProxyConfiguration` parancsmagot. A proxykonfiguráció konfigurálásáról az Azure File Sync megtalálható a [Azure File Sync proxy és tűzfal beállításainak](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```PowerShell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Hibaelhárítás a hálózati kapcsolat további segítségért forduljon a rendszergazdához.