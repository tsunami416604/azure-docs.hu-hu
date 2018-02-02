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
ms.date: 01/30/2018
ms.author: johnkem
ms.openlocfilehash: c3c7ffe00263b8f76d89aa8d15fe2d502538527d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Event hubs Azure tevékenységnapló adatfolyam
A [ **Azure tevékenységnapló** ](monitoring-overview-activity-logs.md) továbbítható bármely alkalmazás a beépített "Exportálás" beállítás használatával, a portálon, vagy a Service Bus szabály azonosítója az Azure PowerShell-parancsmagok vagy Azure parancssori felület használatával napló profilban engedélyezésével közel valós időben.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Mit tehet a tevékenységnapló és az Event Hubs
Az alábbiakban néhány olyan módon, előfordulhat, hogy az adatfolyam-továbbítási funkció használata a műveletnapló:

* **Külső naplózása és telemetriai rendszerek adatfolyamot** – adott idő alatt, az Event Hubs streaming lesz a mechanizmust, amely a tevékenységnapló átadhatja a külső siem-ektől, valamint naplófájl-elemzési megoldásokat.
* **Egy egyéni telemetriai adatokat, illetve a naplózási platform** – Ha már rendelkezik egy egyedi telemetriai platform vagy csak egy kiválóan méretezhető kiépítésével foglalkozó végezni azok jellegű közzétételi-feliratkozási az Event hubs lehetővé teszi a műveletnapló rugalmasan betöltési. [Az Event Hubs használatával egy globális méretű telemetriai platform itt Dan Rosanova útmutatójában talál.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>A műveletnapló adatfolyamként
A műveletnapló streaming vagy programozott módon, vagy a portálon keresztül is engedélyezheti. Mindkét módszer esetén, válasszon egy Service Bus Namespace és egy megosztott elérési házirendet a névtérhez, és egy Eseményközpontot, hogy a névtér jön létre, az első új tevékenységnapló esemény bekövetkezésekor. Ha nem rendelkezik egy Service Bus Namespace, először hozzon létre egyet. Ha korábban folyamatos átviteli tevékenység a Service Bus Namespace eseményeket, a korábban létrehozott Eseményközpont fogja használni. A megosztott elérési házirend határozza meg az adatfolyam-továbbítási mechanizmus jogosultságai. Jelenleg az Event hubs streaming van szükség **kezelése**, **küldése**, és **figyelésére** engedélyek. Hozzon létre, vagy módosíthatja a Service Bus Namespace Service Bus Namespace megosztott hozzáférési házirendek az Azure portálon az "Beállítása" lapon. A műveletnapló napló profil tartalmazza a streaming frissítése, a felhasználó, a módosítás elvégzése a ListKey engedéllyel kell rendelkeznie a Service Bus engedélyezési szabályt.

A service bus vagy esemény hub névtér nem kell ugyanazt az előfizetést, mint az előfizetés naplók kibocsátó mindaddig, amíg a beállítás konfigurálása felhasználó hozzáfér megfelelő RBAC mindkét előfizetéshez lehet.

### <a name="via-azure-portal"></a>Azure-portálon
1. Keresse meg a **tevékenységnapló** használata az összes szolgáltatás panelen keresse a portál bal oldalán.
   
    ![Tevékenységnapló navigáljon a portálon](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Kattintson a **exportálása** gomb tevékenység napló panel tetején.
   
    ![A portál Exportálás gomb](./media/monitoring-stream-activity-logs-event-hubs/export.png)
3. A megjelenő panelen kiválaszthatja a régiók, amelynek szeretné adatfolyam események és a Service Bus Namespace, amelyben szeretné létrehozni az adatfolyamként történő ezeket az eseményeket egy Eseményközpontba. Válassza ki **minden egyes**.
   
    ![Exportálás tevékenységnapló panel](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)
4. Kattintson a **mentése** ezek a beállítások mentéséhez. A beállítások azonnal érvényesek az előfizetéséhez.
5. Ha több előfizetéssel rendelkezik, ismételje meg ezt a műveletet, és minden adat küldése a az azonos eseményközpontba.

### <a name="via-powershell-cmdlets"></a>PowerShell-parancsmagok használatával
Ha a napló-profilja már létezik, először távolítsa el a profilt.

1. Használjon `Get-AzureRmLogProfile` azonosítását, ha a napló-profil létezik
2. Ha igen, használjon `Remove-AzureRmLogProfile` távolítsa el.
3. Használjon `Set-AzureRmLogProfile` profil létrehozása:

```powershell

Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

```

A Service Bus Szabályazonosító: karakterlánc a következő formátumban: {service bus erőforrás-azonosító} /authorizationrules/ {kulcsnév}, például 

### <a name="via-azure-cli"></a>Via Azure CLI
Ha a napló-profilja már létezik, először távolítsa el a profilt.

1. Használjon `azure insights logprofile list` azonosítását, ha a napló-profil létezik
2. Ha igen, használjon `azure insights logprofile delete` távolítsa el.
3. Használjon `azure insights logprofile add` profil létrehozása:

```azurecli-interactive
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

A Service Bus Szabályazonosító: karakterlánc a következő formátumban: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hogyan Eseményközpontokból származó napló adatokat felhasználó?
[A műveletnapló sémáját érhető el itt](monitoring-overview-activity-logs.md). Minden esemény "rekordok." nevű JSON-blobok tömbje van

## <a name="next-steps"></a>További lépések
* [A műveletnapló tárfiókba archiválása](monitoring-archive-activity-log.md)
* [Olvassa el az Azure tevékenységnapló áttekintése](monitoring-overview-activity-logs.md)
* [Tevékenységnapló esemény alapján riasztás beállítása](insights-auditlog-to-webhook-email.md)

