---
title: Az Azure Traffic Managerben diagnosztikai naplózás engedélyezése
description: Tudnivalók a Traffic Manager-profil a diagnosztikai naplózás engedélyezése és eredményeként létrehozott naplófájljait.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: abdc50d6d3d27ab7611994089345a997afc72cae
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082512"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Az Azure Traffic Managerben diagnosztikai naplózás engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a diagnosztikai naplózás és a hozzáférés naplózása egy Traffic Manager-profil.

Az Azure Traffic Manager-diagnosztikai naplók segítségével betekintést a Traffic Manager-profil erőforrás viselkedését. Például a profil naplóadatok használatával határozza meg, miért egyéni mintavételek egy végponton időtúllépés.

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése

A következő parancsokat futtathat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha futtatja a PowerShell a számítógépről, akkor a *AzureRM* PowerShell-modult, 6.13.1 vagy újabb. Futtathat `Get-Module -ListAvailable AzureRM` a telepített verzió azonosításához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha Ön helyileg futtatja a Powershellt, is futtatni szeretné `Login-AzureRmAccount` bejelentkezni az Azure-bA.

1. **A Traffic Manager-profil beolvasása:**

    Diagnosztikai naplózás engedélyezése, szüksége van a Traffic Manager-profil azonosítója. Az engedélyezni kívánt diagnosztikai naplózás a Traffic Manager-profil beolvasása [Get-azurermtrafficmanagerprofile parancsmag](/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile). A kimenet a Traffic Manager-profil azonosító információkat tartalmaz.

    ```azurepowershell-interactive
    Get-AzureRmTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **A Traffic Manager-profilhoz tartozó diagnosztikai naplózás engedélyezése:**

    A Traffic Manager-profil használatával az előző lépésben beszerzett Azonosítóját a diagnosztikai naplózás engedélyezése [Set-azurermdiagnosticsetting parancshoz](https://docs.microsoft.com/powershell/module/azurerm.insights/set-azurermdiagnosticsetting?view=latest). A következő parancsot a Traffic Manager-profilhoz, a megadott Azure Storage-fiók részletes naplók tárolja. 

      ```azurepowershell-interactive
    Set-AzureRmDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Diagnosztikai beállítások ellenőrzése:**

      Ellenőrizze a diagnosztikai beállításokat a Traffic Manager-profil [Get-azurermdiagnosticsetting parancshoz](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermdiagnosticsetting?view=latest). A következő parancsot a kategóriák erőforrás esetében naplózójának jeleníti meg.

     ```azurepowershell-interactive
     Get-AzureRmDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Győződjön meg arról, hogy minden naplófájlt, mert engedélyezve van a Traffic Manager-profil erőforrás megjelenített hozzárendelt kategóriák. Azt is ellenőrizze, hogy a tárfiók helyesen van-e beállítva.

## <a name="access-log-files"></a>Hozzáférés naplófájlok
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Keresse meg a portálon az Azure Storage-fiókot.
2. Az a **áttekintése** oldal az Azure Storage-fiók, a **szolgáltatások** kiválasztása **Blobok**.
3. A **tárolók**válassza **insights-logs-probehealthstatusevents**, és navigáljon a PT1H.json fájlra, majd kattintson az **letöltése** letöltéséhez és mentéséhez másolatának a napló a fájl.

    ![Hozzáférés naplófájlokat egy blob storage-ból a Traffic Manager-profil](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>A TRAFFIC Manager séma

Azure monitoron keresztül elérhető diagnosztikai naplók megosztása rugalmasságot biztosít az egyes szolgáltatások saját események egyedi tulajdonságokat kibocsátható egy közös legfelső szintű sémát. Legfelső szintű diagnosztikai naplók séma, lásd: [támogatott szolgáltatások, sémákat és kategóriák az Azure diagnosztikai naplók](../azure-monitor/platform/tutorial-dashboards.md).

A következő táblázat az Azure Traffic Manager-profil erőforráshoz adott naplók séma tartalmaz.

|||||
|----|----|---|---|
|**Mező neve**|**Mező típusa**|**Definíció**|**Példa**|
|EndpointName|Karakterlánc|A Traffic Manager-végpont, amelynek állapota rögzítve neve.|*myPrimaryEndpoint*|
|status|Karakterlánc|A Traffic Manager-végpont, amelyek a vizsgált volt állapotát. Az állapot lehet **mentése** vagy **le**.|**Felfelé**|
|||||

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Traffic Manager-figyelés](traffic-manager-monitoring.md)

