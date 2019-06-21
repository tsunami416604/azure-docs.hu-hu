---
title: Rendelkezésre állási beállítása az Azure Application insights segítségével |} A Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305000"
---
# <a name="availability-alerts"></a>Rendelkezésre állási riasztás

Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Azt is riasztást küld, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

## <a name="enable-alerts"></a>Riasztások engedélyezése

Riasztások most már automatikusan alapértelmezés szerint engedélyezve vannak, de annak érdekében, hogy teljes mértékben a riasztás konfigurálásához először azt kell először hozzon létre a rendelkezésre állási tesztet.

![Hozzon létre felhasználói élményt](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Az a [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), a riasztási szabály fontossága és értesítési beállításai [Műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **kell** riasztásainak konfigurált. A következő lépések nélkül csak a portálon értesítések fog kapni.

1. A rendelkezésre állási teszt mentése, után a részleteinek lapon kattintson a három épp most módosította a teszt által. Kattintson a "riasztás szerkesztése".

   ![Szerkesztés mentés után](./media/availability-alerts/edit-alert.png)

2. Állítsa be a kívánt súlyossági szintet, a szabály leírását és ennél is fontosabb – a műveletcsoport, amely rendelkezik az értesítési beállításokat szeretné használni a riasztási szabály.

   ![Szerkesztés mentés után](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Riasztás x-en kívül Y helyek jelentéskészítési hibák

Az X Y helyek alapértelmezés szerint engedélyezve van a riasztási szabály kívül a [új egyesített riasztások élmény](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), amikor létrehoz egy új rendelkezésre állási teszt. Kikapcsolhatja a "klasszikus" lehetőség kiválasztásával, vagy letiltani a riasztási szabály kiválasztása.

> [!NOTE]
> A Műveletcsoportok, hogy értesítést kapjon, ha a riasztás aktiválásakor a fenti lépéseket követve konfigurálja. Ebben a lépésben nélkül csak értesítéseket fog kapni a portálon a szabály aktiválásakor.
>

### <a name="alert-on-availability-metrics"></a>Riasztás a rendelkezésre állási metrikák

Használatával a [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), szegmentált összesített rendelkezésre állási riasztás és a teszt időtartamának metrikákat is:

1. Application Insights-erőforrás a metrikák felületen válassza ki és a egy rendelkezésre állási metrika:

    ![Rendelkezésre állási metrikák kiválasztása](./media/availability-alerts/select-metric.png)

2. Lehetőséget a menüből léphet az új felhasználói felület, amelyen kiválaszthatja meghatározott vizsgálatok vagy a riasztási szabály beállítása a riasztásokat konfigurálni. A Műveletcsoportok a riasztási szabály is konfigurálhatja.

### <a name="alert-on-custom-analytics-queries"></a>Riasztás egyéni elemzési lekérdezések

Használatával a [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), is riasztás [egyéni napló lekérdezések](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Az egyéni lekérdezések az minden olyan tetszőleges feltételt, amely segít a legmegbízhatóbb jel rendelkezésre állási problémák is riasztás. Ez akkor is különösen, ha a TrackAvailability SDK-val egyéni rendelkezésre állási eredmények küld. 

> [!Tip]
> A metrikák, a rendelkezésre állási adatokat tartalmaz, előfordulhat, hogy küldjön az TrackAvailability SDK meghívásával egyéni rendelkezésre állási eredményeket. A riasztási egyéni rendelkezésre állási eredmények riasztás metrikák támogatás is használhatja.
>

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárításról szóló cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztek](availability-multistep.md)
* [URL-ping webes tesztek](monitor-web-app-availability.md)

