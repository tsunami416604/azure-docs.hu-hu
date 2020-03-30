---
title: Azure Application Insights áttekintése irányítópult | Microsoft dokumentumok
description: Az Azure Application Insights és az Overview Dashboard funkcióval figyelheti az alkalmazásokat.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669896"
---
# <a name="application-insights-overview-dashboard"></a>Az Application Insights – áttekintés irányítópult

Az Application Insights mindig biztosított egy összefoglaló áttekintő ablaktáblát, amely lehetővé teszi az alkalmazás állapotának és teljesítményének gyors, egy pillantással történő felmérését. Az új áttekintő irányítópult gyorsabb, rugalmasabb élményt nyújt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hogyan tesztelhetem az új élményt?

Az új áttekintő irányítópult alapértelmezés szerint elindul:

![Áttekintő betekintő ablaktábla](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Jobb teljesítmény

Az időtartomány kiválasztása egyszerű, egykattintásos felületre lett egyszerűsítve.

![Időtartomány](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Az általános teljesítmény jelentősen nőtt. Egy kattintással hozzáférhet olyan népszerű funkciókhoz, mint a **Keresés** és **az Elemzés.** Minden alapértelmezett dinamikusan frissítő KPI csempe betekintést nyújt a megfelelő Application Insights funkciókat. Ha többet szeretne megtudni a sikertelen kérelmekről, válassza a **Hibák** lehetőséget a **Vizsgálat** fejléc alatt:

![Hibák](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Alkalmazás irányítópultja

Az alkalmazás-irányítópult az Azure-on belül meglévő irányítópult-technológiát használja ki, hogy teljes mértékben testre szabható egyablakos nézetet biztosítson az alkalmazás állapotáról és teljesítményéről.

Az alapértelmezett irányítópult eléréséhez válassza az _Alkalmazás-irányítópultot_ a bal felső sarokban.

![Irányítópult nézet](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Ha ez az első alkalom, hogy hozzáfér az irányítópulthoz, akkor egy alapértelmezett nézetet indít el:

![Irányítópult nézet](./media/overview-dashboard/0001-dashboard.png)

Megtarthatja az alapértelmezett nézetet, ha tetszik. Az irányítópultot is hozzáadhatja és törölheti, hogy a legjobban megfeleljen a csapat igényeinek.

> [!NOTE]
> Az Application Insights-erőforráshoz hozzáféréssel rendelkező összes felhasználó ugyanazt az alkalmazás-irányítópult-élményt tapasztalja. Az egy felhasználó által végrehajtott módosítások az összes felhasználó nézetét módosítják.

Ha vissza szeretne navigálni az áttekintő élményhez, válassza a következőket:

![Áttekintés gomb](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a **Csempebeállítások konfigurálása** lehetőséget választja, és 31 napot meghaladó egyéni időtartományt állít be, az irányítópult nem jelenik meg 31 napnál hosszabb adatmegőrzésután, még akkor sem, ha az alapértelmezett adatmegőrzés 90 nap. A viselkedésre jelenleg nincs kerülő megoldás.

## <a name="next-steps"></a>További lépések

- [Tölcsérek](../../azure-monitor/app/usage-funnels.md)
- [Megőrzés](../../azure-monitor/app/usage-retention.md)
- [Felhasználói folyamatok](../../azure-monitor/app/usage-flows.md)
