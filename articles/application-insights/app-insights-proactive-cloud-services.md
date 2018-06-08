---
title: Azure Cloud Services használata az Azure Diagnostics integráció az Azure Application insights szolgáltatással kapcsolatos riasztás |} Microsoft Docs
description: A figyelő kapcsolatos problémák, mint az indítási hibák, az összeomlásokat és a szerepkör Lomtár hurkok az Azure Application insights szolgáltatással Azure Cloud Services
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: harelbr; mbullwin
ms.openlocfilehash: 18817fd84a86a72d379f96973b71658f2cdf4afd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851269"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Azure Cloud Services használata az Azure diagnostics integráció az Azure Application insights szolgáltatással kapcsolatos riasztás

Ebben a cikkben azt azt ismerteti, hogyan állíthat be riasztási szabályok figyelő kapcsolatos problémák, például indítási hibák, az összeomlásokat és a szerepkör Lomtár hurkok Azure Cloud Services (webes és feldolgozói szerepkörök).

Ebben a cikkben leírt módszer alapján a [Azure Diagnostics integráció az Application insights szolgáltatással](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/), és a legutóbb kiadott [Application Insights riasztásainak naplózási](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) funkció.

## <a name="define-a-base-query"></a>Alap lekérdezés megadása

A kezdéshez azt határozza meg, amely a Windows Eseménynapló eseményeinek átveszi a Windows Azure csatorna alap lekérdezés, amely az Application Insights nyomkövetési rekord, a rendszer rögzíti.
Ezeket a rekordokat is használható számos különböző probléma az Azure Felhőszolgáltatások, például Indítás meghibásodása, futásidejű hibák, és hasznosítsa újra a hurkok.

> [!NOTE]
> Az alábbi alapszintű lekérdezést egy olyan időkeretet, 30 perc kapcsolatos hibákat keres, és azt feltételezi, hogy egy 10 perc várakozási ideje a választásával dolgozhat fel a telemetriai adatokat rögzíti. Ezeket az alapértelmezett értékeket konfigurálhatja, ahogyan szeretné.

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

## <a name="check-for-specific-event-ids"></a>Bizonyos eseményazonosítókat ellenőrzése

A Windows Eseménynapló eseményeinek beolvasása, után konkrét problémák azzal, azok megfelelő esemény azonosítója és az üzenet tulajdonságait (lásd az alábbi példákat) észlelhető.
A kiinduló lekérdezés fent valamelyik, a lekérdezések alábbi és használt lekérdezés együttesen, a napló riasztási szabály definiálásakor egyszerűen össze.

> [!NOTE]
> A következő példa egy probléma észlelni fogja Ha több mint három események az elemzett időszak alatt találhatók. Ez az alapértelmezett beállítható úgy, hogy a riasztási szabály érzékenységének módosítása.

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

A navigációs menü belül az Application Insights-erőforrást, Ugrás **riasztások**, majd válassza ki **Új riasztási szabály**.

![Szabály létrehozása képernyőképe](./media/app-insights-proactive-cloud-services/001.png)

A a **létrehozás szabály** ablakban, a a **riasztási feltétel megadása** területen kattintson a **adja meg a feltételeket**, majd válassza ki **egyéni napló keresés**.

![Képernyőkép a riasztás feltétele feltételek megadása](./media/app-insights-proactive-cloud-services/002.png)

Az a **keresési lekérdezés** mezőbe illessze be a kombinált előkészített lekérdezés az előző lépésben.

Ezt követően a **küszöbérték** mezőbe, majd állítsa be az értékét 0-ra. Előfordulhat, hogy finomhangolására a **időszak** és gyakoriságát **mezők**.
Kattintson a **Done** (Kész) gombra.

![Képernyőkép a jel logika lekérdezés konfigurálása](./media/app-insights-proactive-cloud-services/003.png)

A a **határozza meg a riasztás részleteinek** területen adjon meg egy **neve** és **leírása** a riasztási szabályt, és állítsa a **súlyossági**.
Emellett győződjön meg arról, hogy a **engedélyezése a szabály létrehozásakor** beállított **Igen**.

![Képernyőfelvétel a riasztás részletei](./media/app-insights-proactive-cloud-services/004.png)

Alatt a **Define művelet csoport** területen válasszon egy meglévő **művelet csoport** vagy hozzon létre egy újat.
Választhatja a művelet csoport több, különféle műveleteket tartalmaz.

![Képernyőfelvétel a művelet csoport](./media/app-insights-proactive-cloud-services/005.png)

Miután meghatározta a művelet csoport, ellenőrizze a módosításokat, majd kattintson **riasztási szabály létrehozása**.

## <a name="next-steps"></a>További lépések

További tudnivalók automatikusan észlelni:

[Hiba rendellenességeket](app-insights-proactive-failure-diagnostics.md)
[memóriavesztés](app-insights-proactive-potential-memory-leak.md)
[teljesítményanomáliákat](app-insights-proactive-performance-diagnostics.md)

