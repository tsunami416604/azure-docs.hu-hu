---
title: Az Azure Application Insights áttekintő irányítópult |} A Microsoft Docs
description: Alkalmazások figyelése az Azure Application Insights és irányítópultjának megnyitására funkciókat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: d1823779f8a8070149811e2349fc9f4281072d38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497152"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights áttekintő irányítópult

Az Application Insights mindig biztosított gyors és a egy pillantással értékeléséhez, az alkalmazás állapotát és teljesítményét egy összegző áttekintés ablaktábláján. Az új áttekintő irányítópult gyorsabb, rugalmasabb élményt nyújt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hogyan az új lehetőségek kipróbálásához?

Az új áttekintő irányítópult mostantól alapértelmezés szerint elindul:

![Áttekintő kép panel](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Jobb teljesítmény érdekében

Időtartomány kijelölésének egyszerűsített, egy egyszerű, egykattintásos felületen.

![Időtartomány](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Általános teljesítménye jelentősen nőtt. Népszerű szolgáltatások, mint az egykattintásos hozzáférést rendelkezik **keresési** és **Analytics**. Egyes alapértelmezett dinamikus frissítése a KPI-csempe megfelelő Application Insights-funkciók betekintést nyújt. Tudjon meg többet sikertelen kérelmek válassza **hibák** alatt a **vizsgálat** fejléc:

![Hibák](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Alkalmazás irányítópultja

Alkalmazás irányítópult kihasználja a meglévő irányítópult technológia adjon meg egy teljes mértékben testre szabható egyetlen tekintse át az alkalmazás állapotát és teljesítményét az Azure-ban.

Az alapértelmezett irányítópulton válassza el _alkalmazás irányítópult_ a bal felső sarokban.

![Irányítópult nézet](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Ha most először az irányítópulthoz hozzáférő, indítja el az alapértelmezett nézet:

![Irányítópult nézet](./media/overview-dashboard/0001-dashboard.png)

Ha például azt megtarthatja az alapértelmezett nézet. Vagy is hozzáadhat, és a csapat igényeinek legjobban megfelelő, az az irányítópult törlése.

> [!NOTE]
> Application Insights-erőforrás-hozzáféréssel rendelkező összes felhasználó azonos alkalmazás-irányítópult élmény megosztani. Egy felhasználó által végrehajtott módosításokat fog módosítsa a nézet az összes felhasználó számára.

Nyissa meg újra az áttekintő tapasztalatok csak adja meg:

![Áttekintés gombra](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha **csempe beállításainak konfigurálása** és biztosította az irányítópulton nem jelennek meg 31 napos időszak letelte után az adatok, akár 90 napos alapértelmezett adatmegőrzési-31 nap egyéni időtartomány beállítása. Jelenleg nem ez a viselkedés nincs megkerülő megoldás.

## <a name="next-steps"></a>További lépések

- [Tölcsérek](../../azure-monitor/app/usage-funnels.md)
- [Megőrzés](../../azure-monitor/app/usage-retention.md)
- [Felhasználói folyamatok](../../azure-monitor/app/usage-flows.md)