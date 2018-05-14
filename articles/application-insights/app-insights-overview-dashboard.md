---
title: Alkalmazás irányítópult – áttekintés |} Microsoft Docs
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
ms.openlocfilehash: bccb56ad45d9054a437bf2d85e74a8d81fbc3db1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="overview-dashboard-preview"></a>Áttekintő irányítópulthoz (előzetes verzió)

Az Application Insights mindig nyújtott gyors és:-a-áttekintő értékeléséhez, az alkalmazás állapotának és teljesítményének a összefoglaló áttekintés ablaktábláján. 2018. május 15. Ezzel egy új gyorsabb és rugalmasabb élmény kiadjuk által megtekinthető villámnézetként. 2018. május 29. a rendszerből a klasszikus áttekintése élményt.

## <a name="how-do-i-test-out-the-new-experience"></a>Hogyan tesztelheti ki az új felület?

Május 15-én az új felület megkezdik jelenik meg az Application Insights: _vizsgálat_ > _áttekintése (előzetes verzió)_.

![Előzetes áttekintése](.\media\app-insights-overview-dashboard\01.png)

Megnyílik az új alapértelmezett áttekintő irányítópulthoz:

![A betekintő áttekintése](.\media\app-insights-overview-dashboard\02.png)

## <a name="better-performance"></a>Jobb teljesítmény

Tartomány időbeállítást egyszerűsített, egy kattintással egyszerű felületet.

![Időtartomány](.\media\app-insights-overview-dashboard\04.png)

A teljes teljesítmény nagy mértékben nőtt. Minden KPI csempe dinamikus frissítéséhez alapértelmezett kapcsolódik a megfelelő Application Insights szolgáltatás. Például a sikertelen kérelmek kiválasztásával megnyílik a _hibák_ panelen:

![Meghibásodások](.\media\app-insights-overview-dashboard\03.png)

## <a name="application-dashboard"></a>Alkalmazás irányítópultja

Alkalmazás irányítópult kihasználja a meglévő irányítópult technológia az alkalmazás állapotának és teljesítményének a teljes mértékben testreszabható egytáblás áttekintése Azure-ban.

Az alapértelmezett irányítópult válassza eléréséhez _alkalmazás irányítópult_ bal felső sarokban.

![Irányítópult-nézet](.\media\app-insights-overview-dashboard\0009.png)

Ha az első alkalommal az irányítópult eléréséhez ez fog megjelenni az alapértelmezett nézet:

![Irányítópult-nézet](.\media\app-insights-overview-dashboard\06.png)

Hálózati adaptere esetében megtarthatja az alapértelmezett nézet tetszés szerint azt, amíg azt is megteheti, és törölje az irányítópultról legjobb a csapat az igényeinek.

Lépjen vissza az Áttekintés felületet csak válasszon:

![Áttekintés gomb](.\media\app-insights-overview-dashboard\07.png)

Is van nevű új gomb _PIN-kód részek_.

![Áttekintés gomb](.\media\app-insights-overview-dashboard\008.png)

Ez egy kicsit ismert szolgáltatás replikálja, amely lehetővé teszi, hogy a csempék a régi áttekintése tapasztalatunkat bármelyikét klasszikus áttekintésében _(riasztások, rendelkezésre állási, élő metrikákat, használat, proaktív észlelések és alkalmazás-hozzárendelés)_ és Adja hozzá az egyéni irányítópultok. 

Az alapértelmezett esetén _alkalmazás irányítópult_ már jelentek meg a csempéket. De további egyéni irányítópultok létrehozásakor, vagy ha valaki a csoport törlése a klasszikus csempe, és adja hozzá ismét, szeretné _PIN-kód részek_ biztosít bármely funkció könnyen megtalálhatók a helyen.
