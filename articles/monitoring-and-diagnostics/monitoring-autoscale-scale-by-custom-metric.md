---
title: Az Azure-ban egyéni metrika automatikus skálázás
description: Ismerje meg, hogy az erőforrás méretezése által egyéni mértéket az Azure-ban.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 739ef5423f7b1769fa793f0cac5306efa634b781
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262973"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Ismerkedés az automatikus skálázási által egyéni mértéket az Azure-ban
Ez a cikk ismerteti, hogyan bővítse az erőforrás egy egyéni mértéket az Azure portálon.

Az Azure a figyelő automatikus méretezési csak a virtuális gép méretezési készletek (VMSS), a felhőszolgáltatások, az app service-csomagokról és a app service Environment-környezetek vonatkozik. 

# <a name="lets-get-started"></a>Lehetővé teszi, hogy első lépései
Ez a cikk feltételezi, hogy rendelkezik-e egy webalkalmazást az application insights szolgáltatással konfigurált. Ha egy már nem rendelkezik, akkor [Application Insights beállítása az ASP.NET-webhely][1]

- Nyissa meg [Azure-portálon][2]
- Kattintson az Azure-figyelő ikonra a bal oldali navigációs ablaktáblán.
  ![Az Azure-figyelő indítása][3]
- Kattintson az automatikus skálázási beállítás, mely automatikus méretezési nem alkalmazható, az automatikus skálázás aktuális állapotával együtt minden erőforrások ![automatikus méretezési Azure figyelőben felderítése][4]
- Azure-figyelő a "automatikus skálázási" panel megnyitásához, és válasszon ki egy erőforrástípust méretezésére
> Megjegyzés: Az alábbi lépéseket az app service-csomag, amely rendelkezik beállított app insights webes alkalmazáshoz kapcsolódó használja.
- Az erőforrás a skálázási beállítás paneljén láthatja, hogy az aktuális példányszám 1. Kattintson az "Enable automatikus skálázás".
  ![Új webalkalmazás skálázási beállítást][5]
- Adjon meg egy nevet a skálázási beállítást, majd kattintson a "Szabály hozzáadása". A skálázási szabályhoz, amelyet értesítés weblapként jelennek meg a jobb oldali ablaktábla a környezetben. Alapértelmezés szerint állítja a méretezési a példányok száma 1, ha a CPU percetage az erőforrás 70 % meghaladja a beállítást. Módosítani a metrika felső "Application Insights", válassza ki a app insights-erőforrás "Resource" a legördülő, majd válassza ki az egyéni metrika-alapú a kívánja méretezni.
  ![Egyéni metrika szerint][6]
- Hasonló a fenti, a adja hozzá a skálázási szabályhoz, amelyek a méretezhető és csökkentheti a méretezési száma 1 az egyéni metrika esetén is a küszöbérték alá.
  ![A skála cpu alapján][7]
- Állítsa be az Ön korlátok példány. Például ha azt szeretné, attól függően, hogy az egyéni mérték ingadozását 2 – 5 példányai között méretezési, állítsa "minimális" a "2", "maximális", "5" és a "default" és "2"
> Megjegyzés: az erőforrás metrikáit olvasása probléma merül fel, és a jelenlegi kapacitásnál nem éri el az alapértelmezett kapacitásértéket, majd az erőforrás rendelkezésre állásának biztosításához automatikus skálázási fog horizontális felskálázás az alapértelmezett értékre. Ha a jelenlegi kapacitásnál már nagyobb, mint az alapértelmezett kapacitásértéket, az automatikus skálázás nem lesz skálázva a.
- Kattintson a "Mentés"

Gratulálunk! Most már sikeresen megtörtént a beállítás automatikus méretezési méretezni a webalkalmazás egyéni metrika alapján.

> Megjegyzés: A lépéseket alkalmazhatók VMSS vagy a felhőbeli szolgáltatás szerepkör használatába.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
