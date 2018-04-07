---
title: Az Event hubs Azure tevékenységnapló adatfolyam |} Microsoft Docs
description: Útmutató az Azure tevékenységnapló Event hubs adatfolyamként történő küldéséhez.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 8a599558fc35ca2bf48ce2a5f11ec4978bf10277
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Event hubs Azure tevékenységnapló adatfolyam
Megtekintheti a [Azure tevékenységnapló](monitoring-overview-activity-logs.md) közel valós idejű bármely alkalmazás egyike:

* A beépített használatával **exportálása** lehetőséget a portálon
* Engedélyezi az Azure Service Bus szabály azonosítója az Azure PowerShell-parancsmagok segítségével napló-profilban vagy az Azure parancssori felület

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Mit tehet a tevékenységnapló és az Event Hubs
Kétféleképpen lehet, hogy az adatfolyam-továbbítási funkció használata a műveletnapló:

* **Külső naplózása és telemetriai rendszerek adatfolyamot**: adott idő alatt, az Azure Event Hubs streaming lesz a mechanizmust, amely a tevékenységnapló átadhatja a külső siem-ektől, valamint naplófájl-elemzési megoldásokat.
* **Egy egyéni telemetriai adatokat, illetve a naplózási platform**: Ha már rendelkezik egy egyedi telemetriai platform vagy egy felépítése továbbléphetnek, a magas szinten méretezhető közzétételi-feliratkozási jellege Event Hubs lehetővé teszi, hogy a műveletnapló rugalmasan betöltési. További információkért lásd: [Dan Rosanova videó az Event Hubs egy globális méretű telemetriai platform használatával kapcsolatos](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>A műveletnapló adatfolyamként
A műveletnapló streaming vagy programozott módon, vagy a portálon keresztül is engedélyezheti. Mindkét módszer esetén, válasszon ki egy Event Hubs-névteret és a megosztott elérési házirendet a névtérhez. Az első új tevékenységnapló esemény bekövetkezésekor eseményközpontban insights-logs-operationallogs nevét, hogy a névtér jön létre. 

Ha egy Event Hubs névtér nem rendelkezik, először hozzon létre egyet. Ha korábban folyamatos átviteli tevékenységnapló események ehhez az Event Hubs a névtérhez, a korábban létrehozott eseményközpont fogja használni. 

A megosztott elérési házirend határozza meg az adatfolyam-továbbítási mechanizmus jogosultságai. Jelenleg az Event Hubs streaming van szükség **kezelése**, **küldése**, és **figyelésére** engedélyek. Hozzon létre, vagy módosítsa a megosztott elérési házirendeket az Event Hubs-névtér az Azure portál a **konfigurálása** fülre az Event Hubs-névtérhez. 

A műveletnapló napló profil tartalmazza a streaming frissítéséhez változtatás felhasználó a ListKey engedéllyel kell rendelkeznie, hogy az Event Hubs engedélyezési szabályt. Az Event Hubs névtér nem kell az előfizetést, a rendszer kibocsátó naplók, mert ugyanahhoz az előfizetéshez kell, amíg a beállítás konfigurálása felhasználó hozzáfér megfelelő RBAC mindkét előfizetéshez.

### <a name="via-the-azure-portal"></a>Az Azure-portálon
1. Keresse meg a **tevékenységnapló** a szakasz a **minden szolgáltatás** keresési a portál bal oldalán.
   
   ![Tevékenységnapló kiválasztása a portálon szolgáltatások listája](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Válassza ki a **exportálása** gombra a napló elején.
   
   ![Exportálás gomb a portálon](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Vegye figyelembe, hogy a szűrő beállításait meg kellett alkalmaz az előző nézetben tekinti meg a műveletnapló befolyásolni a exportálási beállításait. Most már csak a szűrés, lásd a tevékenységnapló a portálon keresztül böngészése közben.
3. Válassza ki a területen megjelenő **minden egyes**. Ne válassza az adott régióban.
   
   ![A szakasz exportálása](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Ha bármi más, mint **minden egyes**, lesz, amelyeken a legfontosabb eseményeket fogadni várt. A műveletnapló egy globális (nem regionális) naplófájl, így a legtöbb esemény nincs társítva egy régiót. 
   >

4. Válassza ki **mentése** ezek a beállítások mentéséhez. A beállítások azonnal érvényesek az előfizetéséhez.
5. Ha több előfizetéssel rendelkezik, ismételje meg ezt a műveletet, és minden adat küldése a az azonos eseményközpontba.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Ha a napló-profilja már létezik, először távolítsa el a meglévő napló-profilt, és hozzon létre egy új naplófájl-profilt.

1. Használjon `Get-AzureRmLogProfile` azonosítását, ha a napló-profil létezik.  Ha egy napló profil nem létezik, keresse meg a *neve* tulajdonság.
2. Használjon `Remove-AzureRmLogProfile` értéke a napló-profil a *neve* tulajdonság.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Használjon `Add-AzureRmLogProfile` egy új naplófájl-profil létrehozása:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespaceName/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Via Azure CLI
Ha a napló-profilja már létezik, először távolítsa el a meglévő napló-profilt, és hozzon létre egy új naplófájl-profilt.

1. Használjon `az monitor log-profiles list` azonosítását, ha a napló-profil létezik.
2. Használjon `az monitor log-profiles delete --name "<log profile name>` értéke a napló-profil a *neve* tulajdonság.
3. Használjon `az monitor log-profiles create` egy új naplófájl-profil létrehozása:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>A naplózási adatokat az Event Hubs
A műveletnapló sémáját érhető el [előfizetés figyelése az Azure tevékenységnapló](monitoring-overview-activity-logs.md). Minden esemény van nevezett JSON-blobok tömbje *rekordok*.

## <a name="next-steps"></a>További lépések
* [A műveletnapló tárfiókba archiválása](monitoring-archive-activity-log.md)
* [Olvassa el az Azure tevékenységnapló áttekintése](monitoring-overview-activity-logs.md)
* [Tevékenységnapló esemény alapján riasztás beállítása](insights-auditlog-to-webhook-email.md)

