---
title: Az Azure Service Fabric - figyelés a Log Analytics beállítása |} A Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a Log Analytics jelenítenek meg, és az események az Azure Service Fabric-fürtök figyelése.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 00fc04afd26da2ef5741eec308835bb8c897c26b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077341"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Fürt Log Analytics beállítása

A log Analytics a fürt események monitorozása az ajánlott megoldás. Beállíthat a Log Analytics-munkaterületet az Azure Resource Manager, a PowerShell vagy az Azure Marketplace-en keresztül. Ha fenntartja az üzembe helyezés későbbi használat céljából egy frissített Resource Manager-sablon, használja ugyanazt a sablont a Log Analytics-környezetet. Marketplace-en keresztüli üzembe helyezést az egyszerűbb, ha már rendelkezik egy diagnosztika engedélyezve van az üzembe helyezett fürt. Ha a fiók, amelyre telepíti, akkor nem kell az előfizetési szintű hozzáféréssel, üzembe helyezése PowerShell vagy a Resource Manager-sablon használatával.

> [!NOTE]
> A Log Analytics beállítása a fürt monitorozásához, szüksége lesz a diagnosztika engedélyezve van a fürt-szintű vagy platformszintű események megtekintéséhez. Tekintse meg [diagnosztika a Windows-fürtök beállítása](service-fabric-diagnostics-event-aggregation-wad.md) és [Linux-fürtöket-diagnosztika beállítása](service-fabric-diagnostics-event-aggregation-lad.md) további

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Log Analytics-munkaterület üzembe az Azure Marketplace-en

Ha szeretne egy fürt üzembe helyezését követően adja hozzá a Log Analytics-munkaterületet, Azure Marketplace-en nyissa meg a portálon, és keressen **Service Fabric-elemzés**. Ez a Service Fabric-telepítésekhez egy egyéni megoldás, amely rendelkezik a Service Fabric jellemző adatok. Ezt a folyamatot fog létrehozni a megoldás (az irányítópultra való insights) és a munkaterület (ilyenkor az összesítést az alapul szolgáló fürt adatok).

1. Válassza ki **új** a bal oldali navigációs menüben. 

2. Keresse meg **Service Fabric-elemzés**. Válassza ki az erőforrást, amely akkor jelenik meg.

3. Kattintson a **Létrehozás** gombra.

    ![Service Fabric-elemzés a Marketplace-en](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Jelölje ki a Service Fabric-elemzés létrehozása ablakban **válasszon ki egy munkaterületet** a a **OMS-munkaterület** mezőben, majd **hozzon létre egy új munkaterületet**. Adja meg a szükséges tételeket. Az egyetlen követelménye, hogy a Service Fabric-fürt és a munkaterület előfizetés azonos. Ha a bejegyzések érvényesítése a munkaterület üzembe helyezéséhez elindul. A telepítés néhány percet vesz igénybe.

5. Amikor végzett, válassza ki a **létrehozás** újra a Service Fabric-elemzés létrehozása ablak alján. Győződjön meg arról, hogy az új munkaterület alatt jelenik meg, **OMS-munkaterület**. Ez a művelet hozzáadja a létrehozott munkaterületet, a megoldás.

Ha Windows rendszert továbbra is a Log Analytics csatlakozni a fürthöz kapcsolódó események tárolására a storage-fiókot az alábbi lépéseket követve. 

>[!NOTE]
>A Linux-fürtök használatát teszi lehetővé a még nem áll rendelkezésre. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>A Log Analytics-munkaterület csatlakozni a fürthöz 

1. A munkaterület csatlakoztatva kell lennie a fürt származó diagnosztikai adatokhoz. Nyissa meg az erőforráscsoport, amelyben létrehozta a Service Fabric-elemzés megoldás. Válassza ki **ServiceFabric\<nameOfWorkspace\>**  és annak – áttekintés oldalra lépjen. Itt munkaterület beállításait, a megoldásbeállítások módosíthatja, és a Log Analytics-munkaterület eléréséhez.

2. A bal oldali navigációs menü alatt **munkaterület adatforrásai**válassza **tárfióknaplók**.

3. Az a **tárfióknaplók** lapon jelölje be **Hozzáadás** hozzáadása a fürt naplóit a munkaterület tetején.

4. Válassza ki **tárfiók** létre a fürtön a megfelelő fiók hozzáadásához. Ha az alapértelmezett nevet használt, a storage-fiók van **sfdg\<resourceGroupName\>**. Azt is ellenőrizheti az Azure Resource Manager sablon által használt érték ellenőrzése a fürt, üzembe helyezéséhez használt **applicationDiagnosticsStorageAccountName**. Ha a név nem jelenik meg, görgessen lefelé, és válassza ki **Továbbiak betöltése**. Válassza ki a tárfiók nevét.

5. Adja meg az adatok típusát. Állítsa be **Service Fabric-események**.

6. Győződjön meg arról, hogy a forrás értéke automatikusan **WADServiceFabric\*címke**.

7. Válassza ki **OK** munkaterületét kapcsolódni a fürt naplóit.

    ![Tárfióknaplók hozzáadása a Log Analyticsbe](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A fiókkal most már megjelenik-e, a storage-fiók részeként jelentkezik be a munkaterület adatforrásai.

A Service Fabric-elemzés megoldás hozzáadott egy Log Analytics-munkaterületen, amely most már megfelelően csatlakozik-e a fürt platformot és alkalmazást naplótábláját. Ugyanúgy adhat hozzá további források a munkaterületre.


## <a name="deploy-log-analytics-with-azure-resource-manager"></a>A Log Analytics és Azure Resource Manager üzembe helyezése

Egy fürtöt egy Resource Manager-sablon használatával történő telepítésekor a sablon létrehoz egy új Log Analytics-munkaterületet, a Service Fabric megoldást ad hozzá a munkaterülethez, és konfigurálja, hogy a megfelelő tárolási táblákból származó adatokat olvasni.

Használ, és módosítsa [ezt a mintasablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) az igényeknek. Ez a sablon a következőket hajtja végre

* Létrehoz egy 5 csomópontos Service Fabric-fürt
* Létrehoz egy Log Analytics-munkaterület és egy Service Fabric-megoldás
* Konfigurálja a Log Analytics-ügynököt, hogy összegyűjti és elküldi a munkaterület 2 minta-teljesítményszámlálók
* A Service Fabric összegyűjtése a WAD konfigurálja, és elküldi őket az Azure storage-táblák (WADServiceFabric * címke)
* Konfigurálja a Log Analytics-munkaterületet, olvassa az eseményeket, ezek a táblák


Telepítheti a sablont egy Resource Manager-frissítés, és a fürt használatával a `New-AzureRmResourceGroupDeployment` az AzureRM PowerShell-modul API-t. A példában a parancs a következő lesz:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Az Azure Resource Manager azt észleli, hogy ez a parancs egy meglévő erőforrás frissítése. Csak feldolgozza a módosításokat a vezetői a meglévő központi telepítési sablont és a megadott új sablon között.

## <a name="deploy-log-analytics-with-azure-powershell"></a>Az Azure PowerShell használatával Log Analytics üzembe helyezése

A Log Analytics-erőforrás PowerShell használatával is telepítheti a `New-AzureRmOperationalInsightsWorkspace` parancsot. Ezt a módszert használja, győződjön meg arról, hogy telepítve van-e [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Ez a szkript használatával hozzon létre egy új Log Analytics-munkaterületet, és hozzá tud adni a Service Fabric megoldást: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Ha elkészült, kövesse az előző szakaszban fiókhoz való csatlakozás a Log Analytics a megfelelő tárolási.

Más megoldások hozzáadása is, vagy más módosításokat a Log Analytics-munkaterület PowerShell használatával. További tudnivalókért lásd: [kezelése a Log Analytics PowerShell-lel](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>További lépések
* [A Log Analytics-ügynök telepítésének](service-fabric-diagnostics-oms-agent.md) alakzatot a csomópontok összegyűjtéséhez a teljesítményszámlálókat és a docker-statisztikák és a tárolókhoz naplóinak összegyűjtésére
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciók a Log Analytics részeként érhető el
* [Nézettervező segítségével egyéni nézeteket hozhat létre a Log Analyticsben](../azure-monitor/platform/view-designer.md)
