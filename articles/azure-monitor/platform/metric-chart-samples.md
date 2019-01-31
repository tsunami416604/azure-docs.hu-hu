---
title: Az Azure Monitor metrikadiagram minták
description: További információ az Azure Monitor adatai megjelenítéséhez.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: bbfeb428d38c23955df4497242184499349aecf9
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303104"
---
# <a name="metric-chart-samples"></a>Metrikadiagram minták

Az Azure-platform ajánlatok [Kaliforniában mérőszámok](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), számát, amely rendelkezik a dimenziók. Használatával [szűrők dimenzió](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), alkalmazása [felosztás](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)diagramtípus szabályozása és hozhat létre hatékony diagnosztikai nézetek és állapotának betekintést biztosító irányítópultok diagram beállításainak módosítása az infrastruktúra és alkalmazások. Ez a cikk bemutatja a diagramokat hozhat létre használatával néhány példát [Metrikaböngésző](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) és egyes ezekbe a diagramokba konfigurálásához szükséges lépéseket ismerteti.

Szeretné megosztani a nagyszerű diagramokon példákat az egész világon? Ezen a lapon, a Githubon hozzájárul, és megoszthatja az itt szereplő példák a saját diagram!

## <a name="website-cpu-utilization-by-server-instances"></a>Webhely CPU-kihasználtság kiszolgálópéldányok

Ezen a diagramon látható, ha egy App Service-ben a CPU volt elfogadható tartományon belül-e, és felszámolja szerint határozza meg, hogy a terhelés megfelelően terjesztése-példány. A diagram, amely az alkalmazás Reggel 6 előtt egy önálló kiszolgáló példányt futtat, és a egy másik példány hozzáadásával vertikálisan is láthatja.

![Vonaldiagram kiszolgálópéldány átlagos processzorhasználat (%)](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hogyan lehet a diagram konfigurálásához?

Válassza ki az App Service-erőforrás, és keresse meg a **processzorhasználat** metrikát. Kattintson a **alkalmazni a felosztás** , és válassza ki a **példány** dimenzió.

## <a name="application-availability-by-region"></a>Alkalmazás rendelkezésre állása régiónként

Tekintse meg az alkalmazás rendelkezésre állása régiónként azonosításához milyen földrajzi helyeken problémák merülnek fel. Ezen a diagramon látható az Application Insights rendelkezésre állási metrika. Láthatja, hogy a figyelt alkalmazásban nem jelent problémát, az USA keleti Régiójában adatközpontból rendelkezésre állással rendelkezik, de azt az USA nyugati RÉGIÓJA és Kelet-Ázsia részleges rendelkezésre állási problémát észlelt.

![Átlagos rendelkezésre állás helyek alapján diagramja](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hogyan lehet a diagram konfigurálásához?

Először be kell kapcsolnia [Application Insights rendelkezésre állási](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) figyelési webhelye számára. Ezt követően válassza ki az Application Insights-erőforrást, és válassza ki a rendelkezésre állási metrika. Felosztás a alkalmazni a **Futtatás helye** dimenzió.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>API-név alapján tárolási tranzakciók mennyisége

A tárfiók típusú erőforrást tapasztalható túlzott mennyiségű tranzakciót. A tranzakciók metrika segítségével azonosíthatja, amely API felelős a túlzott terhelés. Figyelje meg, hogy a következő diagram van konfigurálva a ugyanazt a dimenziót (API-név) a szűrés és a felosztás szeretné szűkíteni a nézetet, hogy csak a lényeges API-hívások:

![API-tranzakciókhoz oszlopdiagram](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hogyan lehet a diagram konfigurálásához?

A metrika hatókörbe, válassza ki a tárfiókját, és a **tranzakciók** metrikát. Váltson a diagram típusát **sávdiagram**. Kattintson a **alkalmazni a felosztás** , és válassza ki a dimenzió **API neve**. Kattintson a a **szűrő hozzáadása** , és válassza ki a **API neve** dimenzió még egyszer. A szűrés párbeszédpanelen válassza ki a diagram megjeleníteni kívánt API-k.

## <a name="next-steps"></a>További lépések

* További tudnivalók az Azure Monitor [munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
* Tudjon meg többet [Metrikaböngésző](metrics-charts.md)