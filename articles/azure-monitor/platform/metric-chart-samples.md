---
title: Példa az Azure Monitor metrikadiagramjára
description: Ismerje meg az Azure Monitor-adatok megjelenítését.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1aaeb853a67b36a21a09db57e015029d10cd0c36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660870"
---
# <a name="metric-chart-examples"></a>Példák a metrikadiagramra 

Az Azure platform [több mint ezer metrikát](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)kínál, amelyek közül sok dimenzióval rendelkezik. Dimenziószűrők [dimension filters](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)használatával felosztással, [splitting](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)diagramtípus vezérlésével és a diagrambeállítások módosításával hatékony diagnosztikai nézeteket és irányítópultokat hozhat létre, amelyek betekintést nyújtanak az infrastruktúra és az alkalmazások állapotába. Ez a cikk néhány példát mutat be a mérőszámokkal konfigurálható [diagramokra,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) és ismerteti az egyes diagramok konfigurálásához szükséges lépéseket.

Szeretné megosztani a nagy grafikonok példák a világgal? Járuljon hozzá ezen az oldalon a GitHubon, és ossza meg saját diagrampéldáit itt!

## <a name="website-cpu-utilization-by-server-instances"></a>Webhely CPU-kihasználtsága kiszolgálópéldányok szerint

Ez a diagram azt mutatja, hogy egy App Service CPU-ja az elfogadható tartományon belül volt-e, és példányonként bontja-e le annak megállapítására, hogy a terhelés megfelelően volt-e elosztva. A diagramon láthatja, hogy az alkalmazás egyetlen kiszolgálópéldányon futott 6:00 óra előtt, majd egy másik példány hozzáadásával felskálázott.

![Átlagos processzorszázalék-diagram kiszolgálópéldányonként](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hogyan kell beállítani ezt a diagramot?

Válassza ki az App Service-erőforrást, és keresse meg a **CPU százalékos** mérőszámát. Ezután kattintson a **Felosztás alkalmazása gombra,** és válassza ki a **Példány** dimenziót.

## <a name="application-availability-by-region"></a>Alkalmazás elérhetősége régiónként

Tekintse meg az alkalmazás rendelkezésre állását régiónként, hogy azonosíthassa, mely földrajzi helyekkel vannak problémák. Ez a diagram az Application Insights rendelkezésre állási metrikáját mutatja. Láthatja, hogy a figyelt alkalmazásnak nincs problémája az USA keleti részének adatközpontból való rendelkezésre állással, de részleges rendelkezésre állási problémát tapasztal az USA nyugati részéről és Kelet-Ázsiából.

![Átlagos rendelkezésre állás diagramja helyek szerint](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hogyan kell beállítani ezt a diagramot?

Először be kell kapcsolnia az [Application Insights rendelkezésre állási](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) figyelését a webhelyén. Ezt követően válassza ki az Application Insights erőforrást, és válassza ki az elérhetőségi metrikát. Felosztás alkalmazása a **Futtatás i.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Tárfiók-tranzakciók kötete API-név szerint

A tárfiók-erőforrás a tranzakciók többletmennyiséget tapasztal. A tranzakciók metrikája segítségével azonosíthatja, hogy melyik API felelős a többletterhelésért. Figyelje meg, hogy a következő diagram van konfigurálva az azonos dimenzió (API-név) a szűrés és felosztása, hogy szűkítse a nézetet, hogy csak az API-hívások az érdeklődésre számot tartó:

![API-tranzakciók sávdiagramja](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hogyan kell beállítani ezt a diagramot?

A metrikaválasztóban válassza ki a tárfiókot és a **Tranzakciók mutatót.** Váltás a diagramtípusból **sávdiagramra**. Kattintson **a Felosztás alkalmazása gombra,** és válassza ki a dimenzió **API-nevét.** Ezután kattintson a **Szűrő hozzáadása elemre,** és ismét válassza ki az **API-név dimenziót.** A szűrőpárbeszédpanelen jelölje ki a diagramon ábrázolni kívánt API-kat.

## <a name="next-steps"></a>További lépések

* További információ az Azure [Monitor-munkafüzetekről](../../azure-monitor/app/usage-workbooks.md)
* További információ a [Mérőszám-kezelőről](metrics-charts.md)
