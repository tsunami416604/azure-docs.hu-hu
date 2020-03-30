---
title: Gyakori riasztási séma az Azure figyelőriasztásaihoz
description: A gyakori riasztási séma ismertetése, használatának és engedélyezésének okai
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249047"
---
# <a name="common-alert-schema"></a>Gyakori riasztási séma

Ez a cikk ismerteti, mi a közös riasztási séma, a használat előnyeit, és hogyan engedélyezheti azt.

## <a name="what-is-the-common-alert-schema"></a>Mi a gyakori riasztási séma?

A közös riasztási séma szabványosítja a riasztási értesítések felhasználási élményét az Azure-ban ma. A mai azure-beli három riasztástípus (metrika, napló és tevékenységnapló) korábban saját e-mail-sablonokkal, webhook-sémákkal stb. A közös riasztási séma most már riasztási értesítéseket kaphat egy konzisztens sémával.

Minden riasztási példány leírja **az érintett erőforrást** és **a riasztás okát,** és ezeket a példányokat a közös séma a következő szakaszokban ismerteti:
* **Essentials**: **Szabványosított mezők**készlete, amelyek minden riasztástípusban közösek, és leírják, hogy a riasztás **milyen erőforráson** van, valamint további gyakori riasztási metaadatok (például súlyosság vagy leírás). 
* **Riasztási környezet**: A **riasztás okát**leíró mezők csoportja , a **riasztás típusától függően**változó mezőkkel. Például egy metrika riasztás volna mezők, például a metrika neve és metrika értéke a riasztási környezetben, míg egy tevékenységnapló riasztás volna információt az esemény, amely létrehozta a riasztást. 

Az ügyfelektől hallott tipikus integrációs forgatókönyvek közé tartozik a riasztási példány útvonala az érintett csapathoz valamilyen pivot (például erőforráscsoport) alapján, amely után a felelős csapat elkezd dolgozni rajta. A közös riasztási séma, szabványosított útválasztási logika között riasztási típusok kihasználva az alapvető mezőket, így a környezeti mezők, mint az érintett csapatok, hogy vizsgálja tovább.

Ez azt jelenti, hogy potenciálisan kevesebb integrációval rendelkezhet, így a kezelésük és karbantartásuk folyamata _sokkal_ egyszerűbb feladat. Emellett a jövőbeli riasztási hasznos tartalom bővítési (például testreszabás, diagnosztikai bővítés stb.) csak a közös sémában jelennek meg.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Milyen fejlesztéseket hoz a gyakori riasztási séma?

A közös riasztási séma elsősorban a riasztási értesítésekben nyilvánul meg. A megjelenő fejlesztések az alábbiakban találhatók:

| Műveletek | Erősítés|
|:---|:---|
| SMS | Egységes SMS-sablon az összes riasztástípushoz. |
| E-mail | Konzisztens és részletes e-mail sablon, amely lehetővé teszi a problémák egyszerű diagnosztizálását egy pillantással. A portálon és az érintett erőforráson lévő riasztási példányra mutató beágyazott mélyhivatkozások biztosítják, hogy gyorsan betud ugrani a javítási folyamatba. |
| Webhook/Logic App/Azure függvény/Automation Runbook | Konzisztens JSON-struktúra az összes riasztási típushoz, amely lehetővé teszi az integrációk egyszerű kiépítését a különböző riasztási típusok között. |

Az új séma is lehetővé teszi a gazdagabb riasztási felhasználási élményt az Azure Portalon és az Azure mobilalkalmazásban a közeljövőben. 

[További információ a webhookok/logikai alkalmazások/Azure Functions/Automation Runbookok sémadefinícióiról.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> A következő műveletek nem támogatják a közös riasztási sémát: ITSM-összekötő.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Hogyan engedélyezhetem a közös riasztási sémát?

A gyakori riasztási sémát a műveletcsoportokon keresztül, a portálon és a REST API-n keresztül is letilthatja vagy letilthatja. Az új sémára való váltáshoz kapcsoló műveletszinten létezik. Például külön kell választania egy e-mail műveletet és egy webhook-műveletet.

> [!NOTE]
> 1. A következő riasztási típusok alapértelmezés szerint támogatják a közös sémát (nincs szükség opt-in)
>     * Intelligens észlelési riasztások
> 1. A következő riasztástípusok jelenleg nem támogatják a közös sémát:
>     * Az Azure Monitor által létrehozott riasztások [virtuális gépekhez](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Az [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) által létrehozott riasztások

### <a name="through-the-azure-portal"></a>Az Azure portalon keresztül

![Gyakori riasztási séma beválasztása](media/alerts-common-schema/portal-opt-in.png)

1. Nyisson meg egy műveletcsoportban lévő meglévő vagy új műveletet. 
1. Válassza az "Igen" lehetőséget a kapcsolóhoz a közös riasztási séma engedélyezéséhez az ábrán látható módon.

### <a name="through-the-action-groups-rest-api"></a>A MŰVELETcsoportok REST API-n keresztül

A [műveletcsoportok API-t](https://docs.microsoft.com/rest/api/monitor/actiongroups) is használhatja a közös riasztási sémára való feliratkozáshoz. Rest API-hívás [létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) közben beállíthatja a "useCommonAlertSchema" jelzőt "true" (feliratkozás) vagy "false" (leiratkozás) a következő műveletek bármelyikéhez – email/webhook/logic app/Azure Function/automation runbook.

A REST API létrehozása vagy [frissítése](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) érdekében a következő kéréstörzs például a következő:

* A "John Doe e-mailje" e-mail-művelet gyakori riasztási sémájának engedélyezése
* A "Jane Smith e-mailje" e-mail művelet általános riasztási sémájának letiltása
* A "Minta webhook" webhook-művelet közös riasztási séma engedélyezése

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>További lépések

- [Webhooks/Logic Apps/Azure Functions/Automation Runbookok gyakori riasztási sémadefiníciói.](https://aka.ms/commonAlertSchemaDefinitions)
- [Ismerje meg, hogyan hozhat létre egy logikai alkalmazást, amely a közös riasztási sémát használja az összes riasztás kezeléséhez.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



