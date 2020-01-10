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
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772969"
---
Ez a hiba akkor következhet be, ha az Azure File Sync nem érhető el a kiszolgálóról. A hiba elhárításához végezze el a következő lépéseket:

1. Ellenőrizze, hogy a tűzfal nem blokkolja-e a Windows-szolgáltatás `FileSyncSvc.exe`ét.
2. Ellenőrizze, hogy a 443-es port nyitva van-e a kimenő kapcsolatokhoz a Azure File Sync szolgáltatással. Ezt a `Test-NetConnection` parancsmaggal teheti meg. Az alábbi `<azure-file-sync-endpoint>` helyőrző URL-címe [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) című dokumentumban található. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ügyeljen arra, hogy a proxykonfiguráció a vártnak megfelelően legyen beállítva. Ez a `Get-StorageSyncProxyConfiguration` parancsmaggal végezhető el. Az Azure File Sync proxykonfigurációjának beállításáról [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) című dokumentumban talál további információt.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. A test-StorageSyncNetworkConnectivity parancsmaggal ellenőrizze, hogy van-e hálózati kapcsolat a szolgáltatási végpontokkal. További információért lásd: [hálózati kapcsolat tesztelése a szolgáltatási végpontokkal](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. További segítségért forduljon a hálózati rendszergazdához, és kérjen segítséget a hálózati kapcsolatról.
