---
title: Figyelés beállítása az Azure Monitor-naplókkal
description: Megtudhatja, hogyan állíthatja be az Azure Monitor naplókat az Azure Service Fabric-fürtök figyeléséhez az események megjelenítéséhez és elemzéséhez.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609927"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Fürt Azure Monitor-naplóinak beállítása

Az Azure Monitor naplók a mi javaslatunk a fürtszintű események figyelésére. A Log Analytics-munkaterületet az Azure Resource Manageren, a PowerShellen vagy az Azure Marketplace-en keresztül állíthatja be. Ha a központi telepítés frissített Resource Manager-sablonját tartja fenn későbbi használatra, használja ugyanazt a sablont az Azure Monitor naplói környezet beállításához. Üzembe helyezés a Marketplace-en keresztül könnyebb, ha már van egy fürt telepített diagnosztika engedélyezve van. Ha nem rendelkezik előfizetési szintű hozzáféréssel abban a fiókban, amelyre telepíti, telepítse a PowerShell vagy az Erőforrás-kezelő sablon használatával.

> [!NOTE]
> Az Azure Monitor naplók beállítása a fürt figyeléséhez, fürtszintű vagy platformszintű események megtekintéséhez engedélyeznie kell a diagnosztikát. Tekintse [meg, hogyan állíthatja be a diagnosztikát a Windows-fürtökben,](service-fabric-diagnostics-event-aggregation-wad.md) és hogyan állíthatja be a [diagnosztikát linuxos fürtökben](service-fabric-diagnostics-oms-syslog.md) további

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Log Analytics-munkaterület üzembe helyezése az Azure Marketplace használatával

Ha egy fürt üzembe helyezése után szeretne hozzáadni egy Log Analytics-munkaterületet, nyissa meg az Azure Piactér portálon, és keresse meg a **Service Fabric Analytics című**területet. Ez egy egyéni megoldás a Service Fabric-telepítésekhez, amelyek a Service Fabric-re vonatkozó adatokat rendelkeznek. Ebben a folyamatban hozza létre mind a megoldást (az irányítópultot az elemzések megtekintéséhez) és a munkaterületet (az alapul szolgáló fürtadatok összesítését).

1. Válassza a bal oldali navigációs menü **Új** parancsát. 

2. Keressen a **Service Fabric Analytics kifejezésre.** Válassza ki a megjelenő erőforrást.

3. Kattintson a **Létrehozás** gombra.

    ![Szolgáltatás fabric analytics a Marketplace-en](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. A Service Fabric Analytics létrehozási ablakban válassza az **OMS-munkaterület** **kiválasztása** lehetőséget, majd **hozzon létre egy új munkaterületet.** Töltse ki a szükséges bejegyzéseket. Az egyetlen követelmény itt az, hogy a Service Fabric-fürt és a munkaterület előfizetése megegyezik. A bejegyzések ellenőrzése után a munkaterület megkezdi a telepítést. Az üzembe helyezés mindössze néhány percet vesz igénybe.

5. Ha végzett, válassza a **Create** Again lehetőséget a Service Fabric Analytics létrehozási ablak alján. Győződjön meg arról, hogy az új munkaterület megjelenik az **OMS-munkaterület**alatt. Ez a művelet hozzáadja a megoldást a létrehozott munkaterülethez.

Windows használata esetén folytassa az alábbi lépésekkel az Azure Monitor-naplók csatlakoztatásához a fürtesemények tárolási helyéhez való csatlakoztatásához. 

>[!NOTE]
>A Service Fabric Analytics megoldás csak Windows-fürtök támogatott. Linux-fürtök esetén tekintse meg cikkünket [aLinux-fürtök Azure Monitor-naplóinak beállításáról.](service-fabric-diagnostics-oms-syslog.md)  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>A Log Analytics munkaterület csatlakoztatása a fürthöz 

1. A munkaterületet csatlakoztatni kell a fürtből érkező diagnosztikai adatokhoz. Nyissa meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics-megoldást. Válassza ki **a ServiceFabric\<nameOfWorkspace lehetőséget,\> ** és lépjen az áttekintő lapra. Itt módosíthatja a megoldás beállításait, munkaterületi beállításokat, és elérheti a Log Analytics munkaterületet.

2. A bal oldali navigációs menü **Munkaterület-adatforrások**csoportban válassza **a Tárfiókok naplói**lehetőséget.

3. A **Tárfiók naplók** lapon válassza **a Hozzáadás** felül a fürt naplóinak a munkaterülethez való hozzáadásához.

4. Válassza **a Tárfiók** a fürtben létrehozott megfelelő fiók hozzáadásához válassza a tárfiókot. Ha az alapértelmezett nevet használta, a tárfiók **sfdg\<\>resourceGroupName**. Ezt a fürt üzembe helyezéséhez használt Azure Resource Manager sablonnal is megerősítheti, ha ellenőrzi az **applicationDiagnosticsStorageAccountName**értékét. Ha a név nem jelenik meg, görgessen le, és válassza a **További betöltése**lehetőséget. Válassza ki a tárfiók nevét.

5. Adja meg az adattípust. Állítsa be a **Service Fabric-események.**

6. Győződjön meg arról, hogy a forrás automatikusan **WADServiceFabric\*EventTable**lesz állítva.

7. Válassza **az OK gombot,** ha a munkaterületet a fürt naplóihoz szeretné csatlakoztatni.

    ![Tárfiók-naplók hozzáadása az Azure Monitor-naplókhoz](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A fiók most megjelenik a tárfiók naplóinak részeként a munkaterület adatforrásaiban.

A Service Fabric Analytics-megoldást egy Log Analytics-munkaterülethez adta hozzá, amely most már megfelelően csatlakozik a fürt platform- és alkalmazásnaplótáblájához. Ugyanígy további forrásokat is hozzáadhat a munkaterülethez.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Az Azure Monitor-naplók üzembe helyezése az Azure Resource Managerrel

Amikor egy fürtöt erőforrás-kezelő sablon használatával telepít, a sablon létrehoz egy új Log Analytics-munkaterületet, hozzáadja a Service Fabric-megoldást a munkaterülethez, és úgy konfigurálja, hogy adatokat olvasson a megfelelő tárolótáblákból.

Ezt [a mintasablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) a követelményeknek megfelelően használhatja és módosíthatja. Ez a sablon a következő

* 5 csomós service fabric-fürt létrehozása
* Log Analytics-munkaterületi és Service Fabric-megoldás létrehozása
* A Log Analytics-ügynök konfigurálása 2 minta teljesítményszámláló összegyűjtésére és elküldésére a munkaterületre
* Konfigurálja a WAD-t a Service Fabric gyűjtésére és az Azure storage-táblákra (WADServiceFabric*EventTable) történő elküldésére
* A Log Analytics munkaterület et úgy konfigurálja, hogy ezekből a táblákból olvassa be az eseményeket.


A sablont erőforrás-kezelői frissítésként telepítheti `New-AzResourceGroupDeployment` a fürtre az Azure PowerShell-modul API-jával. Egy példa parancs lenne:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Az Azure Resource Manager észleli, hogy ez a parancs egy meglévő erőforrás frissítése. Csak a meglévő üzembe helyezést és a rendelkezésre álló új sablon t.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Az Azure Monitor-naplók üzembe helyezése az Azure PowerShell használatával

A parancs használatával a naplóelemzési erőforrást `New-AzOperationalInsightsWorkspace` a PowerShellen keresztül is telepítheti. A módszer használatához győződjön meg arról, hogy telepítette [az Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/install-Az-ps) Ezzel a parancsfájllal új Log Analytics-munkaterületet hozhat létre, és hozzáadhatja hozzá a Service Fabric-megoldást: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Ha elkészült, kövesse az előző szakaszlépéseit az Azure Monitor-naplók csatlakoztatásához a megfelelő tárfiókhoz.

A PowerShell használatával más megoldásokat is hozzáadhat, vagy egyéb módosításokat is elérhet a Log Analytics-munkaterületen. További információ: [Azure Monitor-naplók kezelése a PowerShell használatával.](../azure-monitor/platform/powershell-workspace-configuration.md)

## <a name="next-steps"></a>További lépések
* [Telepítse a Log Analytics-ügynököt](service-fabric-diagnostics-oms-agent.md) a csomópontokra teljesítményszámlálók összegyűjtéséhez, valamint a docker-statisztikák és naplók gyűjtéséhez a tárolókhoz
* Ismerkedjen meg az Azure Monitor naplóinak részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal
* [Egyéni nézetek létrehozása az Azure Monitor naplóiban a Nézettervező használatával](../azure-monitor/platform/view-designer.md)
