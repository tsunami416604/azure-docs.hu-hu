---
title: Rendelkezésre állási riasztások beállítása az Azure Application Insights szolgáltatással | Microsoft dokumentumok
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 5af6aec2267384c37f664522d075bf26c632e7e9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382887"
---
# <a name="availability-alerts"></a>Rendelkezésre állási riasztások

Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Figyelmeztetheti, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

## <a name="enable-alerts"></a>Riasztások engedélyezése

A riasztások alapértelmezés szerint automatikusan engedélyezve vannak, de a riasztás teljes konfigurálásához először létre kell hoznia a rendelkezésre állási tesztet.

![Felhasználói élmény létrehozása](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Az [új egyesített riasztások,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)a riasztási szabály súlyosságát és értesítési beállítások [at műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **at kell konfigurálni** a riasztások at. A következő lépések nélkül csak a portálon keresztüli értesítéseket fog kapni.

1. A rendelkezésre állási teszt mentése után a részletek lapon kattintson a három pontra az imént végzett teszt alapján. Kattintson a "szerkesztési riasztás".

   ![Szerkesztés mentés után](./media/availability-alerts/edit-alert.png)

2. Állítsa be a kívánt súlyossági szintet, a szabály leírását és ami a legfontosabb - azt a műveletcsoportot, amely rendelkezik a riasztási szabályhoz használni kívánt értesítési beállításokkal.

   ![Szerkesztés mentés után](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Az ezen a felületen keresztül létrehozott rendelkezésre állási riasztások állapotalapúak. Ez azt jelenti, hogy ha a riasztási feltételek teljesülnek, egyetlen riasztás jön létre, ha a hely nem érhető el. Ha a hely továbbra is le a következő alkalommal, amikor a riasztási feltételek kiértékelése ez nem hoz létre új riasztást. Tehát, ha a webhely egy órára leállt, és beállított egy e-mail értesítést, akkor csak akkor kap e-mailt, amikor a webhely leállt, és egy későbbi e-mailt, amikor a webhely biztonsági másolatot kapott. Nem kap folyamatos figyelmeztetéseket, amelyek emlékeztetik arra, hogy a webhely még mindig nem érhető el.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Riasztás x-re az Y helyekről hibákat jelent

Az X y-n kívüli helyek riasztási szabály alapértelmezés szerint engedélyezve van az [új egyesített riasztások szolgáltatásban,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)amikor új rendelkezésre állási tesztet hoz létre. Leiratkozhat a "klasszikus" lehetőség kiválasztásával vagy a riasztási szabály letiltásával.

> [!NOTE]
> Állítsa be a műveletcsoportokat úgy, hogy értesítéseket kapjanak, amikor a riasztás aktiválódik a fenti lépések végrehajtásával. E lépés nélkül csak akkor kap a portálon értesítést, amikor a szabály aktiválódik.
>

### <a name="alert-on-availability-metrics"></a>Riasztás a rendelkezésre állási mérőszámokról

Az [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)használatával a szegmentált összesített rendelkezésre állási és tesztidőtartam-mérőszámokra vonatkozó riasztásokra is figyelmeztethet:

1. Válasszon ki egy Application Insights-erőforrást a Metrikák alkalmazásban, és válasszon ki egy elérhetőségi mérőszámot:

    ![Rendelkezésre állási mutatók kiválasztása](./media/availability-alerts/select-metric.png)

2. A riasztások konfigurálása beállítása a menüből az új felületre vezet, ahol kiválaszthatja azokat a teszteket vagy helyeket, amelyeken be állíthat riasztási szabályt. A riasztási szabály műveletcsoportjait itt is konfigurálhatja.

### <a name="alert-on-custom-analytics-queries"></a>Értesítés egyéni elemzési lekérdezésekre

Az [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)használatával riasztást kaphat az [egyéni naplólekérdezésekről.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log) Az egyéni lekérdezések segítségével riasztást bármely tetszőleges feltétel, amely segít a rendelkezésre állási problémák legmegbízhatóbb jelzését kap. Ez akkor is alkalmazható, ha egyéni rendelkezésre állási eredményeket küld a TrackAvailability SDK használatával.

> [!Tip]
> A rendelkezésre állási adatok metrikák tartalmazzák az egyéni rendelkezésre állási eredményeket lehet, hogy a TrackAvailability SDK hívásával. Használhatja a riasztási metrikák támogatása az egyéni rendelkezésre állási eredmények riasztást.
>

## <a name="automate-alerts"></a>Riasztások automatizálása

A folyamat automatizálásához az Azure Resource Manager-sablonokkal tekintse meg a [Metrikariasztás létrehozása az Erőforrás-kezelő sablon dokumentációjával című területet.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztek](availability-multistep.md)
* [Url ping webes tesztek](monitor-web-app-availability.md)
