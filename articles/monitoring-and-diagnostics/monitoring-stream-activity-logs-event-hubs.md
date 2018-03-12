---
title: "Az Event hubs Azure tevékenységnapló adatfolyam |} Microsoft Docs"
description: "Útmutató az Azure tevékenységnapló Event hubs adatfolyamként történő küldéséhez."
author: johnkemnetz
manager: orenr
editor: 
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
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
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
Ha a napló-profilja már létezik, először távolítsa el a profilt.

1. Használjon `Get-AzureRmLogProfile` azonosítását, ha a napló-profil létezik.
2. Ha igen, használjon `Remove-AzureRmLogProfile` távolítsa el.
3. Használjon `Set-AzureRmLogProfile` profil létrehozása:

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

A Service Bus Szabályazonosító egy ilyen formátumú karakterláncot: `{service bus resource ID}/authorizationrules/{key name}`. 

### <a name="via-azure-cli"></a>Via Azure CLI
Ha a napló-profilja már létezik, először távolítsa el a profilt.

1. Használjon `azure insights logprofile list` azonosítását, ha a napló-profil létezik.
2. Ha igen, használjon `azure insights logprofile delete` távolítsa el.
3. Használjon `azure insights logprofile add` profil létrehozása:

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

A Service Bus Szabályazonosító egy ilyen formátumú karakterláncot: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="consume-the-log-data-from-event-hubs"></a>A naplózási adatokat az Event Hubs
A műveletnapló sémáját érhető el [előfizetés figyelése az Azure tevékenységnapló](monitoring-overview-activity-logs.md). Minden esemény van nevezett JSON-blobok tömbje *rekordok*.

## <a name="next-steps"></a>További lépések
* [A műveletnapló tárfiókba archiválása](monitoring-archive-activity-log.md)
* [Olvassa el az Azure tevékenységnapló áttekintése](monitoring-overview-activity-logs.md)
* [Tevékenységnapló esemény alapján riasztás beállítása](insights-auditlog-to-webhook-email.md)

