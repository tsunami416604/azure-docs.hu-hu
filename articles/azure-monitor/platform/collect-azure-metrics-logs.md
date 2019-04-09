---
title: Az Azure service naplókat és mérőszámokat gyűjtése a Log Analytics-munkaterület |} A Microsoft Docs
description: Állítsa be a diagnosztikai naplókat és mérőszámokat írni a Log Analytics-munkaterületet az Azure Monitor Azure-erőforrások.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006263"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Az Azure service naplókat és mérőszámokat gyűjtése a Log Analytics-munkaterületet az Azure monitorban

Naplók és mérőszámok az Azure-szolgáltatásokhoz gyűjt négy különböző módja van:

1. Log Analytics-munkaterületet az Azure monitorban irányítani az Azure diagnostics (*diagnosztikai* az alábbi táblázatban)
2. A Log Analytics-munkaterületet az Azure Monitor az Azure storage-bA az Azure diagnostics (*tárolási* az alábbi táblázatban)
3. Összekötők az Azure-szolgáltatásokhoz (*összekötők* az alábbi táblázatban)
4. Parancsfájlok, összegyűjtése és majd ezeket az adatokat az Azure Monitor (üres az alábbi táblázatban és -szolgáltatásokhoz, amelyek nem szerepelnek a listán) Log Analytics-munkaterületen


| Szolgáltatás                 | Erőforrás típusa                           | Logs        | Mérőszámok     | Megoldás |
| --- | --- | --- | --- | --- |
| Alkalmazásátjárók    | Microsoft.Network/applicationGateways   | Diagnosztika | Diagnosztika | [Azure Application Gateway Analytics](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Az Application insights    |                                         | Összekötő   | Összekötő   | [Application Insights-összekötő](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (előzetes verzió) |
| Automation-fiókok     | Microsoft.Automation/AutomationAccounts | Diagnosztika |             | [További információ](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch-fiókok          | Microsoft.Batch/batchAccounts           | Diagnosztika | Diagnosztika | |
| Klasszikus a cloud services  |                                         | Storage     |             | [További információ](azure-storage-iis-table.md) |
| Kognitív szolgáltatások      | Microsoft.CognitiveServices/accounts    |             | Diagnosztika | |
| A Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnosztika |             | |
| A Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnosztika |             | |
| Event Hubs-névtér     | Microsoft.EventHub/namespaces           | Diagnosztika | Diagnosztika | |
| IoT Hubok                | Microsoft.Devices/IotHubs               |             | Diagnosztika | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnosztika |             | [KeyVault Analytics](../insights/azure-key-vault.md) |
| Terheléselosztók          | Microsoft.Network/loadBalancers         | Diagnosztika |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnosztika | Diagnosztika | |
| Network Security Groups (Hálózati biztonsági csoportok) | Microsoft.Network/networksecuritygroups | Diagnosztika |             | [Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| Helyreállítási tárak         | Microsoft.RecoveryServices/vaults       |             |             | [Az Azure Recovery Services-Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Szolgáltatások keresése         | Microsoft.Search/searchServices         | Diagnosztika | Diagnosztika | |
| Service Bus-névtér   | Microsoft.ServiceBus/namespaces         | Diagnosztika | Diagnosztika | [Service Bus-elemzés (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric-elemzés (előzetes verzió)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnosztika | [Azure SQL Analytics (előzetes)](../insights/azure-sql.md) |
| Storage                 |                                         |             | Szkript      | [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuális gépek        | Microsoft.Compute/virtualMachines       | Mellék   | Mellék <br> Diagnosztika  | |
| Virtuális gépek méretezési csoportjai | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnosztika | |
| Webkiszolgálófarmok        | Microsoft.Web/serverfarms               |             | Diagnosztika | |
| Webhelyek               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnosztika | [Azure Web Apps Analytics (előzetes verzió)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Figyelés az Azure-beli virtuális gépek (Linux és Windows), javasoljuk, hogy telepíti a [Log Analytics Virtuálisgép-bővítmény](../../azure-monitor/learn/quick-collect-azurevm.md). Az ügynököt a virtuális gépekről gyűjtött elemzéseket biztosít. Virtuálisgép-méretezési csoportokhoz is használhatja a bővítményt.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>A Log Analyticshez való közvetlen az Azure diagnostics
Számos Azure-erőforrás diagnosztikai naplók és mérőszámok írhatók közvetlenül az Azure Monitor egy Log Analytics-munkaterületen, és ez az előnyben részesített módja az elemzési adatokat gyűjt. Az Azure diagnostics használatakor az adatok írása azonnal a munkaterületet, és nem kell először a tárolás az adatokat írni.

Azure-erőforrások, amelyek támogatják a [az Azure monitor](../../azure-monitor/overview.md) közvetlenül a Log Analytics-munkaterület is a naplók és mérőszámok küldése.

> [!NOTE]
> Többdimenziós metrikák diagnosztikai beállításokon keresztül egy Log Analytics-munkaterülettel küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Azonban a metrika jelenik meg az összes bejövő üzenetek összes diagnosztikai beállításokon keresztül exportálásakor várólistára helyezi az Eseményközpont.
>
>

* A rendelkezésre álló metrikák részleteit, tekintse meg a [az Azure monitorban támogatott mérőszámok](../../azure-monitor/platform/metrics-supported.md).
* A részletes naplók, tekintse meg [szolgáltatások és a séma támogatja a diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Diagnosztika engedélyezése a PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő PowerShell-példa bemutatja, hogyan használható [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) ahhoz, hogy a hálózati biztonsági csoport diagnosztikai. Ugyanezzel a módszerrel együttműködik az összes támogatott erőforrás - beállítása `$resourceId` , az erőforrás diagnosztikai számára engedélyezni szeretné az erőforrás-azonosítója.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
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

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>A storage, akkor a Log Analytics az Azure diagnostics

Bizonyos erőforrások belül naplóinak gyűjtéséhez is lehet a naplók küldése az Azure storage, és állítsa be a Log Analytics-munkaterületet, olvassa el a naplókat a storage-ból.

Az Azure Monitor használatával Ez a megközelítés a következő erőforrások és a naplók az Azure storage-ból a diagnosztikai adatok gyűjtéséhez:

| Erőforrás | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Működési események <br> Reliable Actors-események <br> Reliable Services-esemény |
| Virtuális gépek |Linux Syslog <br> Windows-esemény <br> IIS-napló <br> Windows ETWEvent |
| Webes szerepkörök <br> Feldolgozói szerepkörök |Linux Syslog <br> Windows-esemény <br> IIS-napló <br> Windows ETWEvent |

> [!NOTE]
> Ha diagnosztikai küld egy tárfiókba, és a Ha a Log Analytics-munkaterület olvassa be az adatokat a tárfiók storage és a tranzakciókért a megszokott Azure adatforgalmi díjakat számítunk fel.
>
>

Lásd: [a blob storage-események az IIS és a table storage](azure-storage-iis-table.md) tudhat meg többet az Azure Monitor hogyan gyűjthet a ezeket a naplókat.

## <a name="connectors-for-azure-services"></a>Az Azure-szolgáltatásokhoz az összekötők

Nincs olyan összekötő az Application Insights, amely lehetővé teszi a Log Analytics-munkaterület kell küldeni az Application Insights által gyűjtött adatokat.

Tudjon meg többet a [Application Insights-összekötő](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>A szkriptek gyűjthet, és ezeket az adatokat a Log Analytics-munkaterület

Az Azure-szolgáltatásokhoz, amelyek nem rendelkeznek közvetlen módszer naplók és mérőszámok küldése a Log Analytics-munkaterület egy Azure Automation-szkript használatával a naplók és mérőszámok gyűjtése. A parancsfájl is elküldheti az adatokat a munkaterület használatával a [adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md)

Az Azure-sablon szerepel [példák az Azure Automationnel](https://azure.microsoft.com/resources/templates/?term=OMS) szolgáltatások adatokat gyűjteni, és küldje el az Azure monitornak.

## <a name="next-steps"></a>További lépések

* [A blob storage használata az IIS és a table storage-események](azure-storage-iis-table.md) , olvassa el a naplókat az Azure-szolgáltatások, a table storage vagy az IIS-napló írása blobtárolókba írási diagnosztika.
* [Megoldások engedélyezése](../../azure-monitor/insights/solutions.md) nyújt betekintést az adatokat.
* [Keresési lekérdezéseket használnak](../../azure-monitor/log-query/log-query-overview.md) az adatok elemzéséhez.
