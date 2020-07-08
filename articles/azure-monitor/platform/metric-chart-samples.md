---
title: Példa Azure Monitor metrikus diagramra
description: Tudnivalók a Azure Monitor adatainak megjelenítéséről.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 00935762a5e19ec47074021aff59992fd3b801bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797452"
---
# <a name="metric-chart-examples"></a>Metrikus diagram – példák 

Az Azure platform [több mint ezer mérőszámot](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)kínál, amelyek közül sok dimenziót tartalmaz. A [dimenzió szűrők](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)használatával a [felosztás](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), a diagramtípus szabályozása és a diagram beállításainak módosítása lehetővé teszi, hogy hatékony diagnosztikai nézeteket és irányítópultokat hozzon létre, amelyek betekintést nyújtanak az infrastruktúra és az alkalmazások állapotára. Ez a cikk néhány példát mutat be a [Metrikaböngésző](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) használatával felépíthető diagramokra, és ismerteti az egyes diagramok konfigurálásához szükséges lépéseket.

Szeretné megosztani a nagyszerű diagramok példáit a világgal? Járuljon hozzá ehhez az oldalhoz a GitHubon, és ossza meg a saját diagramon megjelenő példákat!

## <a name="website-cpu-utilization-by-server-instances"></a>Webhely CPU-kihasználtsága kiszolgálói példányok szerint

Ez a diagram azt mutatja, hogy egy App Service PROCESSZORa az elfogadható tartományon belül van-e, és a példányok lebontásával megállapítja, hogy a terhelés megfelelően van-e elosztva. A diagramon láthatja, hogy az alkalmazás egy adott kiszolgálói példányon fut a 6. előtt, majd egy másik példány hozzáadásával felskálázást végez.

![A CPU átlagos százalékos arányának diagramja kiszolgálópéldány szerint](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hogyan konfigurálható a diagram?

Válassza ki a App Service erőforrást, és keresse meg a **CPU százalékos** metrikáját. Ezután kattintson a **felosztás alkalmazása** elemre, és válassza ki a **példány** dimenziót.

## <a name="application-availability-by-region"></a>Alkalmazások elérhetősége régiónként

Megtekintheti az alkalmazás elérhetőségét régiónként, és meghatározhatja, hogy mely földrajzi helyeknél van probléma. Ez a diagram a Application Insights rendelkezésre állási metrikáját mutatja be. Láthatja, hogy a figyelt alkalmazás nem jelent problémát az USA keleti adatközpontjának rendelkezésre állása miatt, de az USA nyugati régiójában lévő, részlegesen rendelkezésre álló probléma tapasztalható, és Kelet-Ázsia.

![Helyek közötti átlagos elérhetőség diagramja](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hogyan konfigurálható a diagram?

Először be kell kapcsolni [Application Insights rendelkezésre állásának](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) figyelését a webhelyén. Ezt követően válassza ki Application Insights erőforrását, és válassza ki a rendelkezésre állási metrikát. Alkalmazzon felosztást a **futtatási hely** dimenzión.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>A Storage-fiók tranzakcióinak mennyisége API-név szerint

A Storage-fiók erőforrása túlzott mennyiségű tranzakciót tapasztal. A tranzakciók metrikájának használatával meghatározhatja, hogy melyik API felelős a felesleges terhelésért. Figyelje meg, hogy az alábbi diagram a szűrés és a felosztás során ugyanazzal a dimenzióval (API-névvel) van konfigurálva, hogy szűkítse a nézetet csak a kamat API-hívásokra:

![API-tranzakciók oszlopdiagram](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hogyan konfigurálható a diagram?

A metrika-választóban válassza ki a Storage-fiókot és a **tranzakciók** metrikáját. Diagram típusának váltása **oszlopdiagram**. Kattintson a **felosztás alkalmazása** elemre, és válassza a dimenzió **API neve**elemet. Ezután kattintson a **szűrő hozzáadása** lehetőségre, majd válassza ki az **API-név** dimenziót újra. A szűrő párbeszédpanelen válassza ki a diagramon ábrázolni kívánt API-kat.

## <a name="next-steps"></a>További lépések

* Tudnivalók a Azure Monitor- [munkafüzetek](../../azure-monitor/platform/workbooks-overview.md) használatáról
* További információ a [metrika-kezelőről](metrics-charts.md)
