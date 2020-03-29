---
title: Diagnosztikai naplózás engedélyezése az Azure Traffic Managerben
description: Ismerje meg, hogyan engedélyezheti a Traffic Manager-profil diagnosztikai naplózását, és hogyan érheti el az ennek eredményeként létrehozott naplófájlokat.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938677"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Diagnosztikai naplózás engedélyezése az Azure Traffic Managerben

Ez a cikk azt ismerteti, hogyan engedélyezheti a diagnosztikai naplózást és a hozzáférés naplóadatait egy Traffic Manager-profilhoz.

Az Azure Traffic Manager diagnosztikai naplók betekintést nyújthat a Traffic Manager profilerőforrás viselkedésébe. Például használhatja a profil naplóadatait annak meghatározására, hogy az egyes mintavételek miért időzött egy végpont ellen.

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/powershell)követett parancsokat, vagy a PowerShell t a számítógépről futtathatja. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShell t a számítógépről futtatja, szüksége van az Azure PowerShell-modulra, az 1.0.0-s vagy újabb verzióra. Futtathatja `Get-Module -ListAvailable Az` a telepített verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Login-AzAccount` az Azure-ba való bejelentkezéshez is futnia kell.

1. **A Traffic Manager-profil lekérése:**

    A diagnosztikai naplózás engedélyezéséhez szüksége van egy Traffic Manager-profil azonosítójára. A [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)segítségével engedélyezni kívánt Traffic Manager-profil lekérése. A kimenet tartalmazza a Traffic Manager-profil azonosítóadatait.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **A Traffic Manager-profil diagnosztikai naplózásának engedélyezése:**

    Engedélyezze a Traffic Manager-profil diagnosztikai naplózását a [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest)beállítással az előző lépésben kapott azonosító használatával. A következő parancs részletes naplókat tárol a Traffic Manager-profilhoz egy megadott Azure Storage-fiókhoz. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Diagnosztikai beállítások ellenőrzése:**

      Ellenőrizze a Traffic Manager-profil diagnosztikai beállításait a [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest)segítségével. A következő parancs megjeleníti az erőforrás naplózott kategóriáit.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Győződjön meg arról, hogy a Traffic Manager profilerőforráshoz társított összes naplókategória engedélyezve van. Ellenőrizze azt is, hogy a tárfiók megfelelően van-e beállítva.

## <a name="access-log-files"></a>Hozzáférés naplófájlok
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
1. Keresse meg az Azure Storage-fiókját a portálon.
2. Az Azure storage-fiók **Áttekintés lapján** a **Szolgáltatások** csoportban válassza a **Blobok lehetőséget.**
3. **A Tárolók**, válassza **insights-logs-probehealthstatusevents**, és keresse le a PT1H.json fájlt, és kattintson a **Letöltés** gombra a naplófájl egy példányának letöltéséhez és mentéséhez.

    ![A Traffic Manager-profil naplófájljainak elérése blobtárból](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>A Traffic Manager naplósémája

Az Azure Monitoron keresztül elérhető összes diagnosztikai napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasan bocsáthat ki egyedi tulajdonságokat a saját eseményeihez. A legfelső szintű diagnosztikai naplók sémáját lásd: [Támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplók.](../azure-monitor/platform/tutorial-dashboards.md)

Az alábbi táblázat az Azure Traffic Manager profilerőforrás-erőforrásra jellemző naplósémát tartalmazza.

|||||
|----|----|---|---|
|**Mező neve**|**Mező típusa**|**Meghatározás**|**Példa**|
|Végpontneve|Sztring|Annak a Traffic Manager-végpontnak a neve, amelynek állapota rögzítés alatt áll.|*myPrimaryEndpoint*|
|status|Sztring|A Traffic Manager-végpont állapotát, amely et megvizsgálták. Az állapot lehet **Fel** vagy **Le**.|**Fel**|
|||||

## <a name="next-steps"></a>További lépések

* További információ a [Traffic Manager figyeléséről](traffic-manager-monitoring.md)

