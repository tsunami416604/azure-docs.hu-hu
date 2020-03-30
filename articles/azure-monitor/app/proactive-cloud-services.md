---
title: Riasztás az Azure Cloud Services problémáiról az Azure Diagnostics integráció és az Azure Application Insights használatával | Microsoft dokumentumok
description: Az Azure Application Insights segítségével figyelheti az olyan problémákat, mint az indítási hibák, az összeomlások és a szerepkör-újrahasznosítási hurkok az Azure Cloud Servicesben
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 997c5e063c4181a597520e60e2a7669401b9677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669743"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Riasztás az Azure Cloud Services problémáiról az Azure-diagnosztikai integráció és az Azure Application Insights használatával

Ebben a cikkben bemutatjuk, hogyan állíthat be riasztási szabályokat, amelyek figyelik az olyan problémákat, mint az indítási hibák, az összeomlások és a szerepkör-újrahasznosítási hurkok az Azure Cloud Servicesben (webes és feldolgozói szerepkörök).

A cikkben ismertetett módszer az [Azure Diagnostics integrációaz Application Insights,](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)és a nemrég kiadott [naplóriasztások az Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) képesség.

## <a name="define-a-base-query"></a>Alaplekérdezés definiálása

A kezdéshez egy alaplekérdezést határozunk meg, amely lekéri a Windows Eseménynapló-eseményeket a Windows Azure-csatornáról, amelyek az Application Insightsban nyomkövetési rekordként kerülnek rögzítésre.
Ezek a rekordok az Azure Cloud Services számos probléma észlelésére használhatók, például indítási hibák, futásidejű hibák és újrahasznosítási hurkok.

> [!NOTE]
> Az alábbi alaplekérdezés 30 perces időablakban ellenőrzi a problémákat, és 10 perces késést feltételez a telemetriai rekordok betöltésében. Ezek az alapértelmezések beállíthatók, ahogy jónak látja.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Adott eseményazonosítók ellenőrzése

A Windows eseménynapló eseményeinek beolvasása után bizonyos problémák észlelhetők a megfelelő eseményazonosítóés üzenettulajdonságok ellenőrzésével (lásd az alábbi példákat).
Egyszerűen kombinálja a fenti alaplekérdezést az alábbi lekérdezések egyikével, és használja ezt a kombinált lekérdezést a naplóriasztási szabály meghatározásakor.

> [!NOTE]
> Az alábbi példákban a rendszer egy problémát észlel, ha az elemzett időablakban háromnál több eseményt talál. Ez az alapértelmezett beállítás a riasztási szabály érzékenységének módosítására használható.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Riasztás létrehozása

Az Application Insights-erőforrás navigációs menüjében nyissa meg a **Riasztások**lehetőséget, és válassza az **Új riasztási szabály**lehetőséget.

![Képernyőkép: Szabály létrehozása](./media/proactive-cloud-services/001.png)

A **Szabály létrehozása** ablakfigyelmeztetés **megadása csoportban** kattintson a **Feltételek hozzáadása**gombra, majd az **Egyéni naplókeresés gombra.**

![Képernyőkép a riasztás feltételfeltételeinek meghatározásáról](./media/proactive-cloud-services/002.png)

A **Keresés lekérdezés** mezőbe illessze be az előző lépésben elkészített kombinált lekérdezést.

Ezután folytassa a **Küszöbérték** mezővel, és állítsa az értékét 0-ra. A **Pont** és gyakoriság **mezőket**tetszés szerint módosíthatja.
Kattintson a **Done** (Kész) gombra.

![Képernyőkép a jellogikai lekérdezés konfigurálásáról](./media/proactive-cloud-services/003.png)

A **Riasztás részleteinek megadása** csoportban adjon meg egy **nevet** és **leírást** a riasztási szabályhoz, és állítsa be a **súlyosságát.**
Győződjön meg arról is, hogy a **Létrehozáskor szabály engedélyezése** gomb beállítása **Igen**.

![Képernyőkép–riasztás részletei](./media/proactive-cloud-services/004.png)

A **Műveletcsoport definiálása** csoportban kijelölhet egy meglévő **műveletcsoportot,** vagy létrehozhat egy újat.
Dönthet úgy, hogy a műveletcsoport több különböző típusú műveletet tartalmaz.

![Képernyőkép műveletcsoport](./media/proactive-cloud-services/005.png)

Miután definiálta a Művelet csoportot, erősítse meg a módosításokat, és kattintson a **Figyelmeztetési szabály létrehozása gombra.**

## <a name="next-steps"></a>Következő lépések

További információ az automatikus észlelésről:

[A hiba anomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md)
[memóriaszivárgás](../../azure-monitor/app/proactive-potential-memory-leak.md)
[teljesítményanomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md)

