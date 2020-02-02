---
title: Diagnosztikai naplózás engedélyezése az Azure-ban Traffic Manager
description: Megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást a Traffic Manager profiljához, és hogyan férhet hozzá az eredményként létrehozott naplófájlokhoz.
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
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938677"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Diagnosztikai naplózás engedélyezése az Azure-ban Traffic Manager

Ez a cikk azt ismerteti, hogyan engedélyezhető a diagnosztikai naplózás és a hozzáférés naplózási adatai egy Traffic Manager profilhoz.

Az Azure Traffic Manager diagnosztikai naplói betekintést nyerhetnek a Traffic Manager profil erőforrásának viselkedésére. A profil naplójának használatával például megadhatja, hogy az egyes mintavételek miért nem voltak időtúllépések egy végponton.

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0-ra vagy az újabb verzióra. `Get-Module -ListAvailable Az` futtatásával megkeresheti a telepített verziót. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor az Azure-ba való bejelentkezéshez is `Login-AzAccount` kell futtatnia.

1. **Traffic Manager profil beolvasása:**

    A diagnosztikai naplózás engedélyezéséhez szükség van egy Traffic Manager-profil AZONOSÍTÓJÁRA. Kérje le azt a Traffic Manager-profilt, amelyen engedélyezni szeretné a diagnosztikai naplózást a [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)használatával. A kimenet tartalmazza a Traffic Manager profil AZONOSÍTÓjának adatait.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Diagnosztikai naplózás engedélyezése a Traffic Manager profilhoz:**

    Engedélyezze a diagnosztikai naplózást a Traffic Manager profilhoz az előző lépésben a [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest)paranccsal beszerzett azonosító használatával. A következő parancs a Traffic Manager profilhoz tartozó részletes naplókat tárolja egy megadott Azure Storage-fiókban. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Diagnosztikai beállítások ellenőrzése:**

      Ellenőrizze a Traffic Manager-profil diagnosztikai beállításait a [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest)használatával. A következő parancs megjeleníti az adott erőforráshoz naplózott kategóriákat.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Győződjön meg arról, hogy az Traffic Manager profil erőforrásához társított összes naplózási kategória engedélyezve van. Ellenőrizze azt is, hogy a Storage-fiók megfelelően van-e beállítva.

## <a name="access-log-files"></a>Hozzáférési naplófájlok
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
1. Navigáljon az Azure Storage-fiókjához a portálon.
2. Az Azure Storage-fiók **Áttekintés** lapján a **szolgáltatások** területen válassza a **Blobok**elemet.
3. A **tárolók**területen válassza az elemzések **-naplók-probehealthstatusevents**lehetőséget, majd lépjen le a PT1H. JSON fájlhoz, és kattintson a **Letöltés** gombra a naplófájl másolatának letöltéséhez és mentéséhez.

    ![A Traffic Manager profilhoz tartozó naplófájlok elérése blob Storage-ból](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager naplózási séma

Az Azure Monitoron keresztül elérhető diagnosztikai naplók közös felső szintű sémával rendelkeznek, és minden szolgáltatás számára rugalmasságot biztosítanak, hogy egyedi tulajdonságokat nyújtsanak a saját eseményeiknek. A legfelső szintű diagnosztikai naplók sémája: [támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplóihoz](../azure-monitor/platform/tutorial-dashboards.md).

Az alábbi táblázat az Azure Traffic Manager-profil erőforrásához tartozó naplók sémáját tartalmazza.

|||||
|----|----|---|---|
|**Mezőnév**|**Mező típusa**|**Definíció**|**Példa**|
|Végpontneve|Sztring|Annak az Traffic Manager végpontnak a neve, amelynek az állapotát rögzíti a rendszer.|*myPrimaryEndpoint*|
|Állapot|Sztring|A kipróbált Traffic Manager végpont állapota. Az állapot lehet akár **felfelé** , akár **lefelé**is.|**Fel**|
|||||

## <a name="next-steps"></a>Következő lépések

* További információ a [Traffic Manager figyelésről](traffic-manager-monitoring.md)

