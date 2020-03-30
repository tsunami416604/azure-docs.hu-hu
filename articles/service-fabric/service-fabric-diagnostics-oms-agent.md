---
title: Teljesítményfigyelés az Azure Monitor naplóival
description: Megtudhatja, hogyan állíthatja be a Log Analytics-ügynököt az Azure Service Fabric-fürtök tárolóinak és teljesítményszámlálóinak figyelésére.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: c3c1bf511f3313e7408d6ce90b73de60bd1309f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366745"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Teljesítményfigyelés az Azure Monitor naplóival

Ez a cikk ismerteti a log analytics-ügynök hozzáadása a fürt virtuálisgép-méretezési csoport bővítményeként, és csatlakoztassa a meglévő Azure Log Analytics-munkaterülethez. Ez lehetővé teszi a tárolók, alkalmazások és teljesítményfigyelés diagnosztikai adatainak gyűjtését. Azáltal, hogy a virtuálisgép-méretezési készlet erőforrás kiterjesztéseként adja hozzá, az Azure Resource Manager biztosítja, hogy minden csomópontra telepítve legyen, még a fürt méretezésekor is.

> [!NOTE]
> Ez a cikk feltételezi, hogy rendelkezik egy Azure Log Analytics-munkaterület már be van állítva. Ha nem, menjen át az [Azure Monitor-naplók beállítása elemre.](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Az ügynökbővítmény hozzáadása az Azure CLI-n keresztül

A legjobb módja annak, hogy a Log Analytics-ügynök hozzáadása a fürthöz az Azure CLI-vel elérhető virtuálisgép-méretezési készlet API-kon keresztül. Ha még nincs beállítva az Azure CLI beállítása, menjen át az Azure Portalra, és nyisson meg egy Cloud Shell-példányt, vagy [telepítse az Azure CLI-t.](https://docs.microsoft.com/cli/azure/install-azure-cli) [Cloud Shell](../cloud-shell/overview.md)

1. Miután a Cloud Shell kérte, győződjön meg arról, hogy dolgozik ugyanabban az előfizetésben, mint az erőforrás. Ellenőrizze ezt, `az account show` és győződjön meg arról, hogy a "név" érték megegyezik a fürt előfizetésével.

2. A portálon keresse meg azt az erőforráscsoportot, amelyben a Log Analytics-munkaterület található. Kattintson a naplóelemzési erőforrásra (az erőforrás típusa Log Analytics-munkaterület lesz). Miután az erőforrás-áttekintés oldalon, kattintson a **Speciális beállítások** a beállítások szakaszban a bal oldali menüben.

    ![Naplóelemzési tulajdonságok lapja](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Kattintson a **Windows-kiszolgálókra,** ha windowsos fürtöt, **linuxos kiszolgálókat** pedig Linux-fürt létrehozásához. Ezen az oldalon `workspace ID` megjelenik az Ön és `workspace key` (elsődleges kulcsként szerepel a portálon). Mindkettőre szüksége lesz a következő lépéshez.

4. Futtassa a parancsot a Log Analytics-ügynök `vmss extension set` fürtre való telepítéséhez az API használatával:

    Windows-fürt esetén:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Linux-fürt esetén:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Íme egy példa a Log Analytics-ügynök hozzáadása a Windows-fürthöz.

    ![Log Analytics ügynök cli parancs](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Ez kevesebb, mint 15 min sikeresen hozzá az ügynök a csomópontokhoz. Ellenőrizheti, hogy az ügynökök hozzá `az vmss extension list` lettek-e adva az API használatával:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök hozzáadása az Erőforrás-kezelő sablonon keresztül

Az Azure Log Analytics-munkaterületet üzembe helyező és az egyes csomópontokhoz ügynököt hozzáadó mintaerőforrás-kezelősablonok [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) vagy [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)rendszeren érhetők el.

Letöltheti és módosíthatja ezt a sablont az igényeinek leginkább megfelelő fürt központi telepítéséhez.

## <a name="view-performance-counters"></a>Teljesítményszámlálók megtekintése

Most, hogy hozzáadta a Log Analytics-ügynököt, menjen át a Log Analytics-portálra, és válassza ki, hogy mely teljesítményszámlálókat szeretné begyűjteni.

1. Az Azure Portalon nyissa meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics-megoldást. Válassza a **ServiceFabric\<nameOfLog\>AnalyticsWorkspace**lehetőséget.

2. Kattintson a **Log Analytics** elemre.

3. Kattintson **a Speciális beállítások gombra.**

4. Kattintson **az Adatok**menü **Windows vagy Linux teljesítményszámlálói**elemre. Van egy lista az alapértelmezett számlálók közül választhat, hogy engedélyezze, és beállíthatja a gyűjtemény időközéist is. [További teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) is hozzáadhat a gyűjtéshez. A cikk a megfelelő [article](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)formátumra hivatkozik.

5. Kattintson **a Mentés**gombra, majd **az OK gombra.**

6. Zárja be a Speciális beállítások panelt.

7. Az Általános fejléc alatt kattintson a **Munkaterület összegzése gombra.**

8. A csempék az egyes engedélyezett megoldások, beleértve a Service Fabric egy grafikon formájában jelennek meg. Kattintson a **Service Fabric** graph a Service Fabric Analytics-megoldás folytatásához.

9. Látni fogja, néhány csempe grafikonok a működési csatorna és a megbízható szolgáltatási események. A kiválasztott számlálókhoz áramló adatok grafikus ábrázolása a Csomópont-metrikák alatt jelenik meg.

10. Kattintson a tároló metrika grafikon további részletek megtekintéséhez. A teljesítményszámláló-adatokat a fürteseményekhez hasonlóan is lekérdezheti, és szűrheti a csomópontokat, a perf számláló nevét és az értékeket a Kusto lekérdezési nyelv használatával.

![Log Analytics perf számlálólekérdezése](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>További lépések

* Gyűjtse össze a megfelelő [teljesítményszámlálókat.](service-fabric-diagnostics-event-generation-perf.md) Ha a Log Analytics-ügynököt meghatározott teljesítményszámlálók gyűjtésére szeretné konfigurálni, tekintse át [az adatforrások konfigurálását.](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources)
* Az Azure Monitor naplóinak konfigurálása [az automatikus riasztás](../log-analytics/log-analytics-alerts.md) beállításához a detektálás és a diagnosztika segítése érdekében
* Alternatív megoldásként teljesítményszámlálókat gyűjthet az [Azure Diagnostics bővítményen keresztül, és elküldheti őket az Application Insightsnak](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
