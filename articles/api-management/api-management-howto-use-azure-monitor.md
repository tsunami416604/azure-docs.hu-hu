---
title: "A figyelő az Azure API Management figyelő |} Microsoft Docs"
description: "Útmutató: Azure-figyelővel Azure API Management szolgáltatás figyelése."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Figyelő API-kezelés az Azure-figyelő
Az Azure figyelő az Azure-szolgáltatások, amely az összes Azure-erőforrások figyelése egyetlen helyről biztosít. Azure megfigyelővel ábrázolhatja, lekérdezése, továbbítani, archiválására, és a metrikák és a naplók az Azure erőforrások, például az API Management érkező műveletek. 

A következő videó bemutatja, hogyan figyelheti a figyelővel az Azure API Management. Azure-figyelővel kapcsolatos további információkért lásd: [Ismerkedés az Azure-figyelő]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Mérőszámok
Az API Management jelenleg öt metrikák bocsát ki, és vegyen fel több, a jövőben tervezzük. A metrikák kibocsátott percenként, felkínálva a valós idejű információkat az állapot és az API-kat állapotának közelében. Az alábbiakban látható a metrikák összefoglalása:
* Átjáró kérelmek teljes száma: az API-lekérdezések száma az időtartamon belül. 
* Átjáró sikeres kérelmek:, beleértve a 304, 307 és annak minden kisebb, mint 301 (például 200) sikeres HTTP válaszkódot kapott API-kérelmek száma. 
* Sikertelen átjáró kérelmek:, beleértve a 400-as és annak minden nagyobb, mint 500 hibás HTTP válaszkódot kapott API-kérelmek száma.
* Jogosulatlan átjáró kérelmek: érkezett a HTTP válaszkódot, beleértve a 401-es, a 403-as és a 429 API-kérelmek száma. 
* Más átjáró kérelmek: érkezett a HTTP válaszkódot, amelyek nem tartoznak sem a megelőző kategóriák (például 418) API-kérelmek száma.

Az API Management szolgáltatásban metrikák vagy hozzáférési metrikák összes Azure-erőforrások Azure figyelőben érheti el. Metrikák megtekintése az API Management szolgáltatásban:
1. Nyissa meg az Azure-portálon.
2. Nyissa meg az API Management szolgáltatáshoz.
3. Kattintson a **metrikák**.

![Metrikák panel][metrics-blade]

Metrikák használatával kapcsolatos további információkért lásd: [áttekintése a metrikák].

## <a name="activity-logs"></a>Tevékenységnaplók
Tevékenységi naplóit adja meg az API Management-szolgáltatások a végrehajtott műveletek betekintést. Azt korábban hívták "naplófájlok" vagy "működési logs". Tevékenység-naplók segítségével meghatározhatja a "mi, ki, és mikor" az összes írni az API Management szolgáltatásokban végzett műveleteket (PUT, POST, Törlés). 

> [!NOTE]
> Tevékenység naplói nem tartalmazzák (GET) olvasási műveletek vagy a műveletek végre Publisher a klasszikus portálon vagy az eredeti felügyeleti API-k használatával.

Tevékenység naplók elérhetők az API Management szolgáltatásban, és elérni az összes Azure-erőforrások Azure figyelőben naplókat. Naplózza az tevékenységének megtekintéséhez az API Management szolgáltatásban:
1. Nyissa meg az Azure-portálon.
2. Nyissa meg az API Management szolgáltatáshoz.
3. Kattintson a **tevékenységnapló**.

![Tevékenység naplók panel][activity-logs-blade]

Metrikák használatával kapcsolatos további információkért lásd: [tevékenységi naplóit – áttekintés].

## <a name="alerts"></a>Riasztások
A riasztások metrikák és tevékenység naplók alapján konfigurálhatja. Azure a figyelő riasztást tegye a következőket, amikor elindítja a konfigurálását teszi lehetővé:

* E-mail értesítés küldése
* A webhook hívása
* Egy Azure logikai alkalmazás meghívása

A riasztási szabályok konfigurálhatja az API Management szolgáltatásban, vagy az Azure-figyelő. Az API Management azok konfigurálását: 
1. Nyissa meg az Azure-portálon.
2. Nyissa meg az API Management szolgáltatáshoz.
3. Kattintson a **riasztási szabályok**.

![A riasztási szabályok panel][alert-rules-blade]

Riasztások használatával kapcsolatos további információkért lásd: [áttekintése a riasztások].

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Diagnosztikai naplók gazdag információkkal kapcsolatos műveletek és a naplózás és a hibaelhárítási célból fontos hibák. Diagnosztikai naplók eltérnek a tevékenységi naplóit. Tevékenység naplók az Azure-erőforrások a végrehajtott műveletek betekintést. Diagnosztikai naplók Észreveheti az olyan műveletek, hogy az erőforrás végre magát.

Az API Management jelenleg biztosít diagnosztika kapcsolatos egyéni API naplókat (óránkénti kötegelni) igénylése összes bejegyzést, hogy az alábbi szerkezettel:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

Diagnosztikai naplók elérhetők az API Management szolgáltatásban, és hozzáférés az összes Azure-erőforrások Azure figyelőben naplók. Diagnosztikai naplók megtekintése az API Management szolgáltatásban:
1. Nyissa meg az Azure-portálon.
2. Nyissa meg az API Management szolgáltatáshoz.
3. Kattintson a **diagnosztikai naplófájl**.

![Diagnosztikai naplók panel][diagnostic-logs-blade]

Metrikák használatával kapcsolatos további információkért lásd: [diagnosztikai naplók áttekintése].

## <a name="next-step"></a>Következő lépés

* [Ismerkedés az Azure-figyelő]
* [áttekintése a metrikák]
* [tevékenységi naplóit – áttekintés]
* [diagnosztikai naplók áttekintése]
* [áttekintése a riasztások]

[Ismerkedés az Azure-figyelő]: ../monitoring-and-diagnostics/monitoring-get-started.md
[áttekintése a metrikák]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[tevékenységi naplóit – áttekintés]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[diagnosztikai naplók áttekintése]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[áttekintése a riasztások]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
