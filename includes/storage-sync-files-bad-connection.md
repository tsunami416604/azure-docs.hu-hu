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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75772969"
---
Ez a hiba akkor következhet be, ha az Azure File Sync nem érhető el a kiszolgálóról. A hiba elhárításához végezze el a következő lépéseket:

1. Ellenőrizze, hogy `FileSyncSvc.exe` a tűzfal nem blokkolja-e a Windows-szolgáltatást.
2. Ellenőrizze, hogy a 443-as port nyitva áll-e az Azure File Sync szolgáltatás kimenő kapcsolatai számára. Ezt megteheti a `Test-NetConnection` parancsmaggal. Az alábbi `<azure-file-sync-endpoint>` helyőrző URL-címe [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) című dokumentumban található. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Ügyeljen arra, hogy a proxykonfiguráció a vártnak megfelelően legyen beállítva. Ez a `Get-StorageSyncProxyConfiguration` parancsmaggal végezhető el. Az Azure File Sync proxykonfigurációjának beállításáról [Az Azure File Sync proxy- és tűzfalbeállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) című dokumentumban talál további információt.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. A Test-StorageSyncNetworkConnectivity parancsmag segítségével ellenőrizze a hálózati kapcsolatot a szolgáltatás végpontjaihoz. További információ: [Hálózati kapcsolat tesztelése a szolgáltatásvégpontokhoz.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)    

5. A hálózati kapcsolattal kapcsolatos további segítségesetén forduljon a hálózati rendszergazdához.
