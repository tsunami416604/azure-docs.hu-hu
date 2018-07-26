---
title: Az Event hubs Azure tevékenységnapló Stream
description: Útmutató az Azure tevékenységnapló streamelése az Event hubs szolgáltatás.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 7a5372174fcc7cd9552c00c9d283772c9863b815
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257998"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Event hubs Azure tevékenységnapló Stream
Streamelheti a [Azure-tevékenységnapló](monitoring-overview-activity-logs.md) közel valós időben által bármely alkalmazás:

* A beépített használatával **exportálása** lehetőséget a portálon
* Az Azure Service Bus szabály azonosítója az Azure PowerShell-parancsmagok használatával log-profilban vagy az Azure CLI-vel

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Mit tehet a tevékenységnapló és az Event Hubs
Az alábbiakban két módon használható a tevékenységnaplóban a streamelési funkciót:

* **Külső naplózás és a telemetriai rendszer Stream**: idővel az Azure Event Hubs streamelési a mechanizmus, amellyel a Tevékenységnaplót kanálu be külső siem-EK és a log analytics-megoldások válnak.
* **Hozhat létre egy egyéni telemetriát és a naplózás platform**: Ha már rendelkezik egy személyre szabott telemetriai platform vagy szem előtt tartva létrehozására, rugalmasan skálázható közzétételi és előfizetési jellege az Event Hubs lehetővé teszi, hogy rugalmasan a tevékenységnaplóban. További információkért lásd: [Dan Rosanova videó, globális méretű telemetriai platform az Event Hubs használatával kapcsolatban](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>A tevékenységnapló streamelésének engedélyezéséhez
A tevékenységnapló streamelési programozás útján vagy a portálon keresztül is engedélyezheti. Mindkét esetben kiválasztjuk az Event Hubs-névtér és a egy megosztott elérési házirendet a névtér számára. Insights-logs-operationallogs nevet az eseményközpont a névtéren jön létre az első új tevékenységnapló-esemény bekövetkezésekor. 

Ha nem rendelkezik az Event Hubs-névtér, akkor először hozzon létre egyet. Ha korábban streamelt Event Hubs-névtér tevékenységnapló eseményeket, a korábban létrehozott event hubs fogja használni. 

A megosztott elérési házirend határozza meg az engedélyeket, a streamelési mechanizmussal rendelkezik. Még ma, és az Event Hubs streamelési igényel **kezelés**, **küldése**, és **figyelésére** engedélyeket. Létrehozhat vagy megosztott elérési házirendeket az Event Hubs-névtér alatt az Azure Portalon módosíthatja a **konfigurálása** lapon találja az Event Hubs-névtér. 

A tevékenységnapló naplóprofil tartalmazza a folyamatos átviteli frissíti, a felhasználó, aki a módosítás a ListKey engedéllyel kell rendelkeznie az Event Hubs engedélyezési szabályokat. Az Event Hubs-névtér nem kell ugyanabban az előfizetésben kell az előfizetést, amelyhez a naplókat, a kibocsátó, mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő RBAC rendelkezik mindkét előfizetés való hozzáférést, és mindkét előfizetés az ugyanahhoz az AAD-bérlőhöz vannak.

### <a name="via-the-azure-portal"></a>Az Azure Portalon
1. Keresse meg a **tevékenységnapló** használatával a szakasz a **minden szolgáltatás** keresés a portál bal oldalán.
   
   ![Tevékenységnapló a listából a szolgáltatások a portálon](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Válassza ki a **exportálása** gombra a napló elején.
   
   ![Exportálás gomb a portálon](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Ne feledje, hogy a szűrő beállításait az előző nézetben a tevékenységnapló megtekintése során alkalmazott nincs hatással az exportálási beállításait. Most már csak a szűrést, amit lát a tevékenységnaplóban a portálon való böngészés során.
3. A megjelenő szakaszban válassza ki a **minden régióban**. Ne válassza az adott régióban.
   
   ![A szakasz exportálása](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Ha bármi más, **minden régióban**, fog kihagyná kaphat legfontosabb eseményeket. A tevékenységnapló egy globális (nem régióhoz kötött) naplófájl, így a legtöbb kapcsolódóan nem történik meg a hozzájuk társított régió. 
   >

4. Kattintson a **Azure Event Hubs** lehetőséget, és válassza ki, hogy mely naplók az event hubs-névtér kell küldeni, majd kattintson **OK**.
5. Válassza ki **mentése** ezek a beállítások mentéséhez. A beállítások azonnal érvényesek az előfizetéshez.
6. Ha több előfizetéssel rendelkezik, ismételje meg ezt a műveletet, és minden adat küldése az azonos eseményközpontba.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Ha egy napló-profilja már létezik, akkor először távolítsa el a meglévő log-profilt, és hozzon létre egy új naplóprofil.

1. Használat `Get-AzureRmLogProfile` azonosítását, ha egy napló-profil létezik.  Ha egy napló profil létezik, keresse meg a *neve* tulajdonság.
2. Használat `Remove-AzureRmLogProfile` , távolítsa el a napló profilt származó értékkel a *neve* tulajdonság.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Használat `Add-AzureRmLogProfile` egy új naplófájl-profil létrehozása:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Az Azure CLI-n keresztül
Ha egy napló-profilja már létezik, akkor először távolítsa el a meglévő log-profilt, és hozzon létre egy új naplóprofil.

1. Használat `az monitor log-profiles list` azonosítását, ha egy napló-profil létezik.
2. Használat `az monitor log-profiles delete --name "<log profile name>` , távolítsa el a napló profilt származó értékkel a *neve* tulajdonság.
3. Használat `az monitor log-profiles create` egy új naplófájl-profil létrehozása:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Az Event Hubsból a naplóadatokat dolgozhat fel
A tevékenységnapló sémája érhető el a [figyelése az Azure-tevékenységnapló előfizetés](monitoring-overview-activity-logs.md). Minden esemény van egy JSON-blobok nevű tömbben *rekordok*.

## <a name="next-steps"></a>További lépések
* [A tárfiókhoz a tevékenységnapló archiválása](monitoring-archive-activity-log.md)
* [Olvassa el az Azure-tevékenységnapló áttekintése](monitoring-overview-activity-logs.md)
* [Egy tevékenységnapló eseményéhez alapuló riasztás beállítása](insights-auditlog-to-webhook-email.md)

