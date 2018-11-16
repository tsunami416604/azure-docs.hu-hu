---
title: Az Azure service naplókat és mérőszámokat gyűjtése a Log Analytics |} A Microsoft Docs
description: Állítsa be a diagnosztikai naplókat és mérőszámokat írni a Log Analytics Azure-erőforrások.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: d7359c4c151d93dcd40bd475e865c76399ff5355
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711776"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Naplók gyűjtése az Azure service és a Log Analytics használati metrikái

Naplók és mérőszámok az Azure-szolgáltatásokhoz gyűjt négy különböző módja van:

1. A Log Analyticshez való közvetlen az Azure diagnostics (*diagnosztikai* az alábbi táblázatban)
2. A Log Analytics az Azure storage-bA az Azure diagnostics (*tárolási* az alábbi táblázatban)
3. Összekötők az Azure-szolgáltatásokhoz (*összekötők* az alábbi táblázatban)
4. Parancsfájlok, összegyűjtése és majd ezeket az adatokat a Log analyticsbe (üres az alábbi táblázatban és -szolgáltatásokhoz, amelyek nem szerepelnek a listán)


| Szolgáltatás                 | Erőforrás típusa                           | Logs        | Mérőszámok     | Megoldás |
| --- | --- | --- | --- | --- |
| Alkalmazásátjárók    | Microsoft.Network/applicationGateways   | Diagnosztika | Diagnosztika | [Az Azure Application Gateway Analytics](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Az Application insights    |                                         | Összekötő   | Összekötő   | [Application Insights-összekötő](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (előzetes verzió) |
| Automation-fiókok     | Microsoft.Automation/AutomationAccounts | Diagnosztika |             | [További információ](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch-fiókok          | Microsoft.Batch/batchAccounts           | Diagnosztika | Diagnosztika | |
| Klasszikus a cloud services  |                                         | Storage     |             | [További információ](log-analytics-azure-storage-iis-table.md) |
| Kognitív szolgáltatások      | Microsoft.CognitiveServices/accounts    |             | Diagnosztika | |
| A Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnosztika |             | |
| A Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnosztika |             | |
| Event Hubs-névtér     | Microsoft.EventHub/namespaces           | Diagnosztika | Diagnosztika | |
| IoT Hubok                | Microsoft.Devices/IotHubs               |             | Diagnosztika | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnosztika |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Terheléselosztók          | Microsoft.Network/loadBalancers         | Diagnosztika |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnosztika | Diagnosztika | |
| Network Security Groups (Hálózati biztonsági csoportok) | Microsoft.Network/networksecuritygroups | Diagnosztika |             | [Azure-beli hálózati biztonsági csoport Analytics](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Helyreállítási tárak         | Microsoft.RecoveryServices/vaults       |             |             | [Az Azure Recovery Services-Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Szolgáltatások keresése         | Microsoft.Search/searchServices         | Diagnosztika | Diagnosztika | |
| Service Bus-névtér   | Microsoft.ServiceBus/namespaces         | Diagnosztika | Diagnosztika | [Service Bus-elemzés (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric-elemzés (előzetes verzió)](../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnosztika | [Az Azure SQL Analytics (előzetes verzió)](log-analytics-azure-sql.md) |
| Storage                 |                                         |             | Szkript      | [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuális gépek        | Microsoft.Compute/virtualMachines       | Mellék   | Mellék <br> Diagnosztika  | |
| Virtuális gépek méretezési csoportjai | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnosztika | |
| Webkiszolgálófarmok        | Microsoft.Web/serverfarms               |             | Diagnosztika | |
| Webhelyek               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnosztika | [Az Azure Web Apps Analytics (előzetes verzió)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Figyelés az Azure-beli virtuális gépek (Linux és Windows), javasoljuk, hogy telepíti a [Log Analytics Virtuálisgép-bővítmény](log-analytics-quick-collect-azurevm.md). Az ügynököt a virtuális gépekről gyűjtött elemzéseket biztosít. Virtuálisgép-méretezési csoportokhoz is használhatja a bővítményt.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>A Log Analyticshez való közvetlen az Azure diagnostics
Számos Azure-erőforrás írása a diagnosztikai naplók és közvetlenül a Log Analytics és a metrikák az elemzési adatok gyűjtése az előnyben részesített módja. Az Azure diagnostics használatakor az adatok kerülnek azonnal a Log Analytics, és nem kell először a tárolás az adatokat írni.

Azure-erőforrások, amelyek támogatják a [az Azure monitor](../azure-monitor/overview.md) közvetlenül a Log Analytics szolgáltatáshoz is a naplók és mérőszámok küldése.

> [!NOTE]
> A többdimenziós metrikák elküldése a Log Analyticsbe diagnosztikai beállításokon keresztül jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Azonban a metrika jelenik meg az összes bejövő üzenetek összes diagnosztikai beállításokon keresztül exportálásakor várólistára helyezi az Eseményközpont.
>
>

* A rendelkezésre álló metrikák részleteit, tekintse meg a [az Azure monitorban támogatott mérőszámok](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* A részletes naplók, tekintse meg [szolgáltatások és a séma támogatja a diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Diagnosztika engedélyezése a PowerShell-lel
Van szüksége a 2016. November (v2.3.0), vagy újabb kiadását [Azure PowerShell-lel](/powershell/azure/overview).

A következő PowerShell-példa bemutatja, hogyan használható [Set-azurermdiagnosticsetting parancshoz](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) ahhoz, hogy a hálózati biztonsági csoport diagnosztikai. Ugyanezzel a módszerrel együttműködik az összes támogatott erőforrás - beállítása `$resourceId` , az erőforrás diagnosztikai számára engedélyezni szeretné az erőforrás-azonosítója.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>A diagnosztika a Resource Manager-sablonok

Diagnosztikai erőforrás engedélyezéséhez a létrehozást, és a diagnosztika küldött és a Log Analytics-munkaterületre használhat egy sablont az alábbihoz hasonló. Ebben a példában van egy Automation-fiókot, de minden támogatott erőforrástípus esetén működik.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>A storage, akkor a Log Analytics az Azure diagnostics

Belül az egyes erőforrások naplóinak gyűjtéséhez is lehet küldeni a naplókat az Azure storage-, és állítsa be a Log Analyticsben, hogy olvassa el a naplókat a storage-ból.

A log Analytics használatával Ez a megközelítés az alábbi erőforrások és a naplók az Azure storage-ból a diagnosztikai adatok gyűjtéséhez:

| Erőforrás | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Működési események <br> Reliable Actors-események <br> Reliable Services-esemény |
| Virtuális gépek |Linux Syslog <br> Windows-esemény <br> IIS-napló <br> Windows ETWEvent |
| Webes szerepkörök <br> Feldolgozói szerepkörök |Linux Syslog <br> Windows-esemény <br> IIS-napló <br> Windows ETWEvent |

> [!NOTE]
> Tárolás és a tranzakciókért, ha küld diagnosztikai tárfiók és a Ha a Log Analytics olvassa be az adatokat a tárfiók a Azure megszokott adatforgalmi díjakat számítunk fel.
>
>

Lásd: [a blob storage-események az IIS és a table storage](log-analytics-azure-storage-iis-table.md) tudhat meg többet a Log Analytics hogyan gyűjthet a ezeket a naplókat.

## <a name="connectors-for-azure-services"></a>Az Azure-szolgáltatásokhoz az összekötők

Nincs egy összekötőt, az Application Insights, amely lehetővé teszi a Log Analytics felé küldött kell Application Insights által gyűjtött adatokat.

Tudjon meg többet a [Application Insights-összekötő](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>A szkriptek gyűjtése, és ezeket az adatokat a Log Analyticsbe

Az Azure-szolgáltatásokhoz, amelyek nem rendelkeznek a naplók és mérőszámok küldése a Log Analyticshez való közvetlen módszer egy Azure Automation-szkript használatával a naplók és mérőszámok gyűjtése. A szkript ezután is küldheti az adatokat a Log Analytics használatával az [adatgyűjtő API](log-analytics-data-collector-api.md)

Az Azure-sablon szerepel [példák az Azure Automationnel](https://azure.microsoft.com/resources/templates/?term=OMS) adatokat gyűjteni a szolgáltatások és a Log Analytics felé.

## <a name="next-steps"></a>További lépések

* [A blob storage használata az IIS és a table storage-események](log-analytics-azure-storage-iis-table.md) , olvassa el a naplókat az Azure-szolgáltatások, a table storage vagy az IIS-napló írása blobtárolókba írási diagnosztika.
* [Megoldások engedélyezése](../azure-monitor/insights/solutions.md) nyújt betekintést az adatokat.
* [Keresési lekérdezéseket használnak](log-analytics-queries.md) az adatok elemzéséhez.
