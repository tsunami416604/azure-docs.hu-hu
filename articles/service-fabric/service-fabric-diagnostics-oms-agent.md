---
title: "Az Azure Service Fabric - figyelés az OMS-ügynököt a beállítása |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be az OMS-ügynököt figyelés tárolók és az Azure Service Fabric-fürtök teljesítményszámlálói."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Adja hozzá az OMS-ügynököt a fürthöz

Ez a cikk egy virtuálisgép-méretezési virtuáliskapcsoló-kiterjesztés beállítása a fürthöz hozzáadni az OMS-ügynököt, és csatlakoztassa a meglévő OMS Naplóelemzési munkaterület lépéseket ismerteti. Ez lehetővé teszi a tárolók, alkalmazások és -figyelő gyűjtését diagnosztikai adatokat. Hozzáadva bővítményként, Azure Resource Manager biztosítja, hogy megkapja-e telepítve minden csomóponton, még ha méretezés a fürt.

> [!NOTE]
> Ez a cikk feltételezi, hogy rendelkezik-e az OMS Naplóelemzési munkaterület állította be. Ha nem így tesz, látogasson el [OMS Naplóelemzési beállítása](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Az Azure parancssori felület használatával ügynök-bővítmény hozzáadása

A legjobb módszer az OMS-ügynököt a fürthöz hozzáadni a virtuálisgép-méretezési keresztül értéke elérhető API-kat az Azure parancssori felület segítségével. Ha az Azure parancssori felület még nem rendelkezik, látogasson el az Azure portálon, és nyissa meg a [felhő rendszerhéj](../cloud-shell/overview.md) példányt, vagy [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Miután a felhő rendszerhéj van szükség, ellenőrizze, hogy dolgozik, az erőforrás ugyanahhoz az előfizetéshez. Ellenőrizze a `az account show` , és győződjön meg arról, hogy a "name" érték megegyezik a fürt előfizetés.

2. A portálon lépjen az erőforráscsoporthoz, ahol az OMS-munkaterület. Kattintson a Naplóelemzési erőforrás (az erőforrás típusa lehet Naplóelemzési), a jobb oldali navigációs be, görgessen lefelé, és kattintson a **tulajdonságok**.

    ![OMS Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Vegye figyelembe a `workspaceId`. 

3. Konfigurálnia kell a `workspaceKey` az ügynök telepítése. A kulcs eléréséhez, kattintson a **speciális beállítások**alatt a *beállítások* a bal oldali navigációs szakasza. Kattintson a **Windows kiszolgálók** Ha meg vannak állandó Windows fürt, és **Linux kiszolgálók** egy Linux-fürt létrehozásakor. Szüksége lesz a *elsődleges kulcs* , amely megjelenik az ügynökök telepítéséhez a `workspaceKey`.

4. Futtassa a parancsot a fürt települ az OMS-ügynök használatával a `vmss extension set` a felhő rendszerhéj API:

    A Windows-fürt:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    A Linux-fürt:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Íme egy példa egy Windows fürtnek való felvétel alatt OMS-ügynököt.

    ![OMS-ügynök cli parancs](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    Ekkor az ügynök sikeresen hozzáadása a csomópontok kisebb, mint 15 perc. Ellenőrizheti, hogy az ügynökök használatával vannak adva a `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök keresztül a Resource Manager-sablon hozzáadása

A minta Resource Manager-sablonok, amelyek központi telepítése egy OMS Naplóelemzési munkaterület, és adja hozzá a az ügynök a csomópontok mindegyikének érhető el [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) vagy [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Töltse le, és módosítsa a sablon az igényeinek leginkább megfelelő a fürtök telepítése.

## <a name="next-steps"></a>Következő lépések

* Megfelelő gyűjtése [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md). Konfigurálhatja az egyes teljesítményszámlálókra, head (az OMS szolgáltatáshoz erőforrás tetején csatolt) OMS-portálon való átvételéhez OMS-ügynököt. Kattintson a **Kezdőlap > Beállítások > adatok > Windows-teljesítményszámlálók** vagy **Linux teljesítményszámlálók** , és válassza a számlálók szeretne gyűjteni.
* Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
