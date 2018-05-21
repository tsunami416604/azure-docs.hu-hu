---
title: Az Azure alkalmazás irányítópult – áttekintés |} Microsoft Docs
description: Alkalmazások figyelése az Azure Application insights szolgáltatással és az áttekintő irányítópulthoz funkciókkal.
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
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="application-insights-overview-dashboard-preview"></a>Application Insights áttekintő irányítópulthoz (előzetes verzió)

Az Application Insights mindig nyújtott gyors és:-a-áttekintő értékeléséhez, az alkalmazás állapotának és teljesítményének a összefoglaló áttekintés ablaktábláján. Az új előzetes áttekintő irányítópulthoz gyorsabb rugalmasabb élményt nyújt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hogyan tesztelheti ki az új felület?

 Az Application insightsban: _áttekintése_, jelölje be _ahhoz, hogy az alapértelmezett élmény próbálkozzon meg az új áttekintése_.

![Előzetes áttekintése](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

Megnyílik az új alapértelmezett áttekintő irányítópulthoz:

![A betekintő áttekintése](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>Jobb teljesítmény

Tartomány időbeállítást egyszerűsített, egy kattintással egyszerű felületet.

![Időtartomány](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

A teljes teljesítmény nagy mértékben nőtt. Minden KPI csempe dinamikus frissítéséhez alapértelmezett kapcsolódik a megfelelő Application Insights szolgáltatás. Például a sikertelen kérelmek kiválasztásával megnyílik a _hibák_ panelen:

![Meghibásodások](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Alkalmazás irányítópultja

Alkalmazás irányítópult kihasználja a meglévő irányítópult technológia az alkalmazás állapotának és teljesítményének a teljes mértékben testreszabható egytáblás áttekintése Azure-ban.

Az alapértelmezett irányítópult válassza eléréséhez _alkalmazás irányítópult_ bal felső sarokban.

![Irányítópult-nézet](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Ha az első alkalommal az irányítópult eléréséhez ez fog megjelenni az alapértelmezett nézet:

![Irányítópult-nézet](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Hálózati adaptere esetében megtarthatja az alapértelmezett nézet tetszés szerint azt, amíg azt is megteheti, és törölje az irányítópultról legjobb a csapat az igényeinek.

> [!NOTE]
> Az Application Insights-erőforráshoz hozzáféréssel rendelkező összes felhasználó azonos alkalmazás-irányítópult élmény megosztani. Egy felhasználó által végrehajtott módosítások módosítani fogja a nézet az összes felhasználó számára.

Lépjen vissza az Áttekintés felületet csak válasszon:

![Áttekintés gomb](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>További lépések

- [Tölcsérek](usage-funnels.md)
- [Megőrzés](app-insights-usage-retention.md)
- [Felhasználói folyamatok](app-insights-usage-flows.md)
- [Irányítópultok](app-insights-dashboards.md)
