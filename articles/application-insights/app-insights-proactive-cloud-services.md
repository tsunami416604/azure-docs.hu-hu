---
title: Az Azure Cloud Services használata az Azure Diagnostics-integráció az Azure Application insights szolgáltatással kapcsolatos problémák a riasztás |} A Microsoft Docs
description: Figyelő a problémák, mint az indítási hibák, a szoftverleállások és a szerepkör újraindítása hurkok az Azure Cloud Services az Azure Application insights segítségével
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: c51c3fcde95f53565ac50a0ccef1d0ac8b348382
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52728072"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Problémák az Azure Cloud Services használata az Azure diagnostics-integráció az Azure Application Insights-riasztás

Ebben a cikkben azt azt ismerteti, hogyan állíthatja be a riasztási szabályok, amelyek problémáinak figyelése, az indítási hibák, a szoftverleállások és a szerepkör újraindítása hurkok az Azure Cloud Services (webes és feldolgozói szerepkörök).

Ebben a cikkben leírt módszer alapján az [az Application insights szolgáltatással az Azure Diagnostics-integráció](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/), és a legutóbb kiadott [Naplóriasztások az Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) képesség.

## <a name="define-a-base-query"></a>Alap lekérdezés definiálása

Első lépésként meghatározunk egy alap lekérdezést, amely a Windows Eseménynapló eseményeinek átveszi a Windows Azure csatorna, amelyek rögzítve lesznek a nyomkövetési bejegyzésként az Application insightsba.
Ezeket a rekordokat számos különböző probléma észlelése az Azure Cloud Servicesben, például a hibák, futásidejű hibák is használható, és újrahasznosítását a hurkok.

> [!NOTE]
> Az alábbi alapszintű lekérdezést ellenőrzi a problémákat a 30 perces egy olyan időkeretet, és feltételezi, hogy a telemetria rekordok feldolgozására 10 perc késése. Ezeket az alapértelmezett értékeket is konfigurálható, ahogyan szeretné.

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

## <a name="check-for-specific-event-ids"></a>Adott eseményazonosító-ellenőrzés

A Windows Eseménynapló eseményeinek beolvasása, után konkrét problémák ellenőrzésével azok megfelelő esemény Azonosítóját és az üzenet tulajdonságait (lásd az alábbi példákat) észlelhető.
Egyszerűen össze egy, a lekérdezések az alábbi, és a használt összevont lekérdezést, a riasztási szabály meghatározásakor a alap lekérdezés felett.

> [!NOTE]
> Az alábbi példákban a probléma érzékeli, ha több mint három eseménynek az elemzett időszakon találhatók. Ez az alapértelmezett beállítható úgy, hogy a riasztási szabály az érzékenység módosítása.

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

A navigációs menü belül az Application Insights-erőforrást, lépjen a **riasztások**, majd válassza ki **Új riasztási szabály**.

![Szabály létrehozása képernyőképe](./media/app-insights-proactive-cloud-services/001.png)

Az a **szabály létrehozása** ablak alatt a **riasztási feltétel megadása** területén kattintson a **adja meg a feltételeket**, majd válassza ki **egyéni naplóbeli keresés**.

![Képernyőkép a riasztás feltétele feltételek megadása](./media/app-insights-proactive-cloud-services/002.png)

Az a **keresési lekérdezés** mezőbe illessze be az egyesített lekérdezés előkészített az előző lépésben.

Ezt követően a **küszöbérték** mezőbe, majd állítsa az értékét 0-ra. Előfordulhat, hogy igény szerint módosíthatja a **időszak** és a gyakoriság **mezők**.
Kattintson a **Done** (Kész) gombra.

![Képernyőkép a jel logikájának lekérdezés konfigurálása](./media/app-insights-proactive-cloud-services/003.png)

Alatt a **riasztás részleteinek megadása** területén adjon meg egy **neve** és **leírás** a riasztási szabályt, és a set annak **súlyossági**.
Emellett győződjön meg arról, hogy a **engedélyezése a szabály létrehozásakor** gomb értékre van állítva **Igen**.

![Képernyőfelvétel: a riasztás részletei](./media/app-insights-proactive-cloud-services/004.png)

Alatt a **műveleti csoport megadása** szakaszban kiválaszthatja, hogy egy meglévő **műveletcsoport** , vagy hozzon létre egy újat.
A művelet csoportot tartalmazhat több művelet a különféle fenyegetési típusokat választhatja.

![Műveletcsoport képernyőképe](./media/app-insights-proactive-cloud-services/005.png)

Miután meghatározta a műveletcsoport, hagyja jóvá a módosításokat, és kattintson a **riasztási szabály létrehozása**.

## <a name="next-steps"></a>További lépések

További információk automatikus észlelése:

[Rendellenes hibák](app-insights-proactive-failure-diagnostics.md)
[memóriavesztés](app-insights-proactive-potential-memory-leak.md)
[teljesítmény-anomáliák](app-insights-proactive-performance-diagnostics.md)

