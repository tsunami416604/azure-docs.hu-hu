---
title: Az Azure Service Fabric - figyelés az OMS-ügynököt a beállítása |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be az OMS-ügynököt figyelés tárolók és az Azure Service Fabric-fürtök teljesítményszámlálói.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: dekapur
ms.openlocfilehash: 613e5a2a746d480f020af652e7bbaf5e80ed059d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Adja hozzá az OMS-ügynököt a fürthöz

Ez a cikk egy virtuálisgép-méretezési virtuáliskapcsoló-kiterjesztés beállítása a fürthöz hozzáadni az OMS-ügynököt, és csatlakoztassa a meglévő Azure Log Analytics-munkaterület lépéseket ismerteti. Ez lehetővé teszi a tárolók, alkalmazások és -figyelő gyűjtését diagnosztikai adatokat. Hozzáadva bővítményként, Azure Resource Manager biztosítja, hogy megkapja-e telepítve minden csomóponton, még ha méretezés a fürt.

> [!NOTE]
> Ez a cikk feltételezi, hogy rendelkezik-e az Azure Log Analytics-munkaterülethez állította be. Ha nem így tesz, látogasson el [Azure Naplóelemzés beállítása](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Az Azure parancssori felület használatával ügynök-bővítmény hozzáadása

A legjobb módszer az OMS-ügynököt a fürthöz hozzáadni a virtuálisgép-méretezési keresztül értéke elérhető API-kat az Azure parancssori felület segítségével. Ha az Azure parancssori felület még nem rendelkezik, látogasson el az Azure portálon, és nyissa meg a [felhő rendszerhéj](../cloud-shell/overview.md) példányt, vagy [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Miután a felhő rendszerhéj van szükség, ellenőrizze, hogy dolgozik, az erőforrás ugyanahhoz az előfizetéshez. Ellenőrizze a `az account show` , és győződjön meg arról, hogy a "name" érték megegyezik a fürt előfizetés.

2. A portálon lépjen az erőforráscsoporthoz, ahol a Naplóelemzési munkaterület. Kattintson a Naplóelemzési erőforrás (az erőforrás típusa lehet Naplóelemzési), a jobb oldali navigációs be, görgessen lefelé, és kattintson a **tulajdonságok**.

    ![Napló Analytics Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Vegye figyelembe a `workspaceId`. 

3. Konfigurálnia kell a `workspaceKey` az ügynök telepítése. A kulcs eléréséhez, kattintson a **speciális beállítások**alatt a *beállítások* a bal oldali navigációs szakasza. Kattintson a **Windows kiszolgálók** Ha meg vannak állandó Windows fürt, és **Linux kiszolgálók** egy Linux-fürt létrehozásakor. Szüksége lesz a *elsődleges kulcs* , amely megjelenik az ügynökök telepítéséhez a `workspaceKey`.

4. Futtassa a parancsot a fürt települ az OMS-ügynök használatával a `vmss extension set` a felhő rendszerhéj API:

    A Windows-fürt:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<LogAnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<LogAnalyticsworkspaceKey>'}"
    ```

    A Linux-fürt:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<LogAnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<LogAnalyticsworkspaceKey>'}"
    ```

    Íme egy példa egy Windows fürtnek való felvétel alatt OMS-ügynököt.

    ![OMS-ügynök cli parancs](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Futtassa a parancsot, ez a konfiguráció alkalmazása a már meglévő Virtuálisgép-példányok:  

    ```sh
    az vmss update-instances
    ```

    Ekkor az ügynök sikeresen hozzáadása a csomópontok kisebb, mint 15 perc. Ellenőrizheti, hogy az ügynökök használatával vannak adva a `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök keresztül a Resource Manager-sablon hozzáadása

A minta Resource Manager-sablonok, amelyek központi telepítése egy Azure Log Analytics-munkaterület, és adja hozzá a az ügynök a csomópontok mindegyikének érhető el [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) vagy [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Töltse le, és módosítsa a sablon az igényeinek leginkább megfelelő a fürtök telepítése.

## <a name="next-steps"></a>További lépések

* Megfelelő gyűjtése [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md). Az OMS-ügynököt specifikus teljesítményszámlálók adatainak összegyűjtése konfigurálásához tekintse át a [adatforrások konfigurálása](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Log Analytics beállítása konfigurálása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
