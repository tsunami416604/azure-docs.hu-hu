---
title: Azure szolgáltatás és a metrikák összegyűjtheti Naplóelemzési |} Microsoft Docs
description: Diagnosztika konfigurálása az Azure-erőforrások Naplóelemzési naplók és a metrikák írni.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e8c930acd12b5197238be48722947dac5380be6
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751411"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Azure szolgáltatás és a metrikák Naplóelemzési használható gyűjtése

Folyamatosan naplókat és az Azure-szolgáltatások metrikáját négy különböző módja van:

1. Az Azure diagnostics közvetlen szolgáltatáshoz (*diagnosztika* az alábbi táblázatban)
2. Az Azure storage szolgáltatáshoz Azure diagnostics (*tárolási* az alábbi táblázatban)
3. Az Azure szolgáltatások összekötők (*összekötők* az alábbi táblázatban)
4. Az adatgyűjtésre és majd visszaküldeni az adatok Naplóelemzési (az alábbi táblázatban és szolgáltatásokról, amelyek nem szerepelnek a listán az üres), parancsfájlok


| Szolgáltatás                 | Erőforrás típusa                           | Logs        | Mérőszámok     | Megoldás |
| --- | --- | --- | --- | --- |
| Alkalmazásátjárók    | Microsoft.Network/applicationGateways   | Diagnosztika | Diagnosztika | [Az Azure alkalmazás átjáró elemzés](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Összekötő   | Összekötő   | [Application Insights-összekötő](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (előzetes verzió) |
| Automation-fiókok     | Microsoft.Automation/AutomationAccounts | Diagnosztika |             | [További információ](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch-fiókok          | Microsoft.Batch/batchAccounts           | Diagnosztika | Diagnosztika | |
| Klasszikus cloud services csomag  |                                         | Storage     |             | [További információ](log-analytics-azure-storage-iis-table.md) |
| Kognitív szolgáltatások      | Microsoft.CognitiveServices/accounts    |             | Diagnosztika | |
| A Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnosztika |             | |
| A Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnosztika |             | |
| Eseményközpont-névtér     | Microsoft.EventHub/namespaces           | Diagnosztika | Diagnosztika | |
| IoT Hubok                | Microsoft.Devices/IotHubs               |             | Diagnosztika | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnosztika |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Terheléselosztók          | Microsoft.Network/loadBalancers         | Diagnosztika |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnosztika | Diagnosztika | |
| Network Security Groups (Hálózati biztonsági csoportok) | Microsoft.Network/networksecuritygroups | Diagnosztika |             | [Azure hálózati biztonsági csoport elemzés](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Helyreállítási tárolók         | Microsoft.RecoveryServices/vaults       |             |             | [Az Azure Recovery Services Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Szolgáltatások keresése         | Microsoft.Search/searchServices         | Diagnosztika | Diagnosztika | |
| Service Bus-névtér   | Microsoft.ServiceBus/namespaces         | Diagnosztika | Diagnosztika | [Service Bus Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric Analytics (előzetes verzió)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnosztika | [Az Azure SQL elemzés (előzetes verzió)](log-analytics-azure-sql.md) |
| Storage                 |                                         |             | Szkript      | [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuális gépek        | Microsoft.Compute/virtualMachines       | Mellék   | Mellék <br> Diagnosztika  | |
| Virtuális gépek méretezési csoportok | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnosztika | |
| Kiszolgáló-webfarmok        | Microsoft.Web/serverfarms               |             | Diagnosztika | |
| Webhelyek               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnosztika | [Az Azure Web Apps Analytics (előzetes verzió)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Figyelés Azure virtuális gépeken (Linux és Windows rendszerekhez), azt javasoljuk, telepíti a [napló Analytics Virtuálisgép-bővítmény](log-analytics-azure-vm-extension.md). Az ügynök biztosít a virtuális gépekről gyűjtött insights. Is használhatja a bővítményt a virtuálisgép-méretezési készlet.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Az Azure diagnostics közvetlen szolgáltatáshoz
Sok Azure-erőforrások tud írni a diagnosztikai naplók és metrikákat közvetlenül a Naplóelemzési, és ez az előnyben részesített módja analitikai adatok gyűjtése. Az Azure diagnostics használatakor adatot ír azonnal Naplóelemzési, és nincs szükség az első az adatok írása tárolási.

Azure-erőforrások, amely támogatja a [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview.md) küldhet a naplók és a metrikák közvetlenül Naplóelemzési.

> [!NOTE]
> A többdimenziós metrikák elküldése a Log Analyticsbe diagnosztikai beállításokon keresztül jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Azonban a metrika egy jelenik meg az összes bejövő üzenet összes diagnosztikai beállítások keresztül exportálásakor várólisták a központ.
>
>

* Az elérhető mérőszámok leírását, [támogatott Azure-figyelő metrikák](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* A naplók leírását, [szolgáltatások és a séma támogatja a diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Diagnosztika engedélyezése a PowerShell-lel
A November 2016 (v2.3.0) kell, vagy később kiadása [Azure PowerShell](/powershell/azure/overview).

A következő PowerShell-példa bemutatja, hogyan használható [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) amely lehetővé teszi a hálózati biztonsági csoport. Ugyanezt a megközelítést akkor működik, ha minden támogatott erőforrás - beállítása `$resourceId` az erőforrás diagnosztikai az engedélyezni kívánt erőforrás-azonosítóra.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>A Resource Manager-sablonok diagnosztika engedélyezése

Erőforrás diagnosztika engedélyezése, ha létrejön, és a diagnosztika küldött és a Naplóelemzési munkaterületet használja az alábbihoz hasonló sablont. Ebben a példában az Automation-fiók, de minden támogatott erőforrástípusai működik szolgál.

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

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Az Azure diagnostics tárolási, majd Naplóelemzési

A naplógyűjtés időtartamát az egyes erőforrások belül, akkor lehet elküldeni a naplókat az Azure storage és a naplók olvasását tárolásból Naplóelemzési konfigurálja.

Naplóelemzési is ezt a módszert használja a diagnosztika gyűjteni a következő erőforrások és a naplók az Azure storage:

| Erőforrás | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Működési események <br> Megbízható szereplő esemény <br> Megbízható szolgáltatás esemény |
| Virtuális gépek |Linux rendszernaplójába bejegyzett <br> Windows-esemény <br> IIS-napló <br> Windows ETWEvent |
| Webes szerepkörök <br> Feldolgozói szerepkörök |Linux rendszernaplójába bejegyzett <br> Windows-esemény <br> IIS-napló <br> Windows ETWEvent |

> [!NOTE]
> Van szó, a tárolásért és a tranzakciók, amikor a diagnosztika küld egy tárfiókba és mikor Naplóelemzési az adatokat olvas a tárfiók a Azure megszokott adatforgalmi díjakat.
>
>

Lásd: [blob-tároló használja az IIS és a tábla tárolási események](log-analytics-azure-storage-iis-table.md) tudhat meg többet hogyan Naplóelemzési be ezeket a naplókat.

## <a name="connectors-for-azure-services"></a>Az Azure szolgáltatások összekötők

Nincs összekötőként szolgál az Application Insights részére, amely lehetővé teszi a Naplóelemzési küldendő Application Insights által gyűjtött adatokat.

További információ a [Application Insights-összekötő](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Az adatgyűjtésre és feladási adatok szolgáltatáshoz parancsfájlok

Az Azure-szolgáltatásokat, amelyek nem teszik lehetővé a közvetlen naplók és a metrikák küldeni a Log Analytics egy Azure Automation-parancsfájl segítségével gyűjteni a napló és a metrikák. A parancsfájl is elküldheti az adatokat Naplóelemzési használatával a [adatgyűjtő API](log-analytics-data-collector-api.md)

Az Azure-sablon alapján gyűjteménye van [példák Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) adatokat gyűjteni a szolgáltatások, és küldje el azt szolgáltatáshoz.

## <a name="next-steps"></a>További lépések

* [A blob storage használata az IIS és a tábla tárolási események](log-analytics-azure-storage-iis-table.md) a naplók olvasását, Azure szolgáltatási adott írási diagnosztika a table storage vagy a blob-tároló írni az IIS-naplókba.
* [Megoldások](log-analytics-add-solutions.md) betekintést az adatokba, így.
* [Használja a keresési lekérdezések](log-analytics-log-searches.md) az adatok elemzésére.
