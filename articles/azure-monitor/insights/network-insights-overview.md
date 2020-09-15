---
title: Azure Monitor hálózatok számára (előzetes verzió)
description: A hálózati Azure Monitor gyors áttekintése, amely az összes telepített hálózati erőforrás állapotának és metrikáinak átfogó áttekintését teszi lehetővé konfiguráció nélkül.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 5a85efc5de0d99904da568c97672adf40ce8a414
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088918"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor hálózatok számára (előzetes verzió)
A hálózati Azure Monitor a konfiguráció nélkül átfogó képet nyújt az összes telepített hálózati erőforrás állapotáról és mérőszámáról. A speciális keresési funkció segítséget nyújt az olyan erőforrás-függőségek azonosításában, amelyek lehetővé teszik olyan forgatókönyvek használatát, mint például a webhelyét üzemeltető erőforrások azonosítása a szolgáltatott webhely neve alapján.

A hálózatok Azure Monitor **áttekintése** lap egy könnyed módszert biztosít a hálózati erőforrások leltárának megjelenítéséhez a Resource Health és a riasztások mellett. Négy kulcsfontosságú funkcionális területre oszlik:

- Keresés és szűrés
- Resource Health és mérőszámok
- Riasztások 
- Függőség nézet

![Áttekintő lap](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Keresés és szűrés
Az erőforrás állapota és riasztások nézet a szűrők, például az **előfizetés**, az **erőforráscsoport** és az **erőforrástípus**alapján testreszabható. A keresőmező lehetővé teszi az erőforrás-tulajdonságok közötti keresés lehetőségét.

A keresőmező az erőforrások és a kapcsolódó erőforrások keresésére használható. Egy nyilvános IP-cím például egy Application Gatewayhoz van társítva. A nyilvános IP-címek DNS-nevének keresésekor a rendszer a nyilvános és a hozzá tartozó Application Gateway is azonosítja.

![A képernyőkép a DNS-név keresésének eredményét jeleníti meg.](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health és metrika
Az egyes csempék egy erőforrástípust jelölnek, és az összes, a Resource Health állapottal együtt kiválasztott előfizetésben üzembe helyezett példányok számát adja meg. Az alábbi példában a 105 ER és a VPN-kapcsolatok üzembe helyezése, 103 kifogástalan, és 2 nem érhető el.

![Erőforrás állapota](media/network-insights-overview/resource-health.png)

A két nem elérhető ER és VPN kapcsolatra kattintva metrikai nézetet indít el. 

![Metrika nézet](media/network-insights-overview/metric-view.png)

A rács nézet minden elemére rákattinthat. Kattintson a Health (állapot) ikonra az adott kapcsolatok erőforrás-állapotának átirányításához. Kattintson a riasztások elemre, hogy átirányítsa a riasztások és metrikák lapra az adott kapcsolatban. 

## <a name="alerts"></a>Riasztások
A jobb oldali **riasztások** rács a kiválasztott erőforrásokhoz generált összes riasztást megjeleníti az összes előfizetésben. Kattintson a riasztások száma gombra a részletes riasztások oldal megtekintéséhez.

## <a name="dependency-view"></a>Függőség nézet
A **függőség** nézet segít megjeleníteni az erőforrás konfigurálásának módját. A függőségi nézet mostantól Application Gateway, a virtuális WAN és a Load Balancer esetében is támogatott. Application Gateway esetén például a függőség nézet a metrikák rács nézetében a Application Gateway erőforrás nevére kattintva érhető el. Ez a virtuális WAN-ra és a Load Balancer is vonatkozik. 

![Application Gateway nézet](media/network-insights-overview/application-gateway.png)

A Application Gateway **függőségi** nézete egyszerűsített képet nyújt arról, hogy az előtér-IP-címek hogyan kapcsolódnak a figyelőkhöz, a szabályokhoz és a backend-készlethez. A csatlakozó élek színkódoltak, és további részleteket biztosítanak a háttér-készlet állapota alapján. A nézet az összes kapcsolódó háttér-készlet, például a virtuálisgép-méretezési csoport és a virtuálisgép-példányok Application Gateway metrikáinak és metrikáinak részletes áttekintését is tartalmazza.

![Függőség nézet](media/network-insights-overview/dependency-view.png)

A függőségi gráf lehetővé teszi az egyszerű navigációt a konfigurációs beállításokhoz. Kattintson a jobb gombbal a háttér-készletre a más funkciók eléréséhez. Ha például a háttér-készlet egy virtuális gép, akkor közvetlenül hozzáférhet a virtuális gépekkel kapcsolatos megállapításokhoz és a Network Watcher kapcsolattal a kapcsolódási problémák elhárításához és azonosításához.

![Függőség Nézet menü](media/network-insights-overview/dependency-view-menu.png)

A függőség nézet keresési és szűrési sávján keresztül könnyedén kereshet a gráfon. Például a *AppGWTestRule* keresése az alábbi példában leszűkíti a grafikus nézetet a *AppGWTestRule*-n keresztül csatlakozó összes csomópontra. 

![Példa keresése](media/network-insights-overview/search-example.png)

A különböző szűrők segítségével leszűkítheti az adott elérési utat és állapotot. Válassza például *a csak a nem megfelelő állapotú* elemet **az állapot legördülő menüjéből** az összes olyan szegély megjelenítéséhez, ahol az állapot állapota *sérült*.

Kattintson a **részletes mérőszám nézetre** egy előre konfigurált munkafüzet elindításához az Application Gateway, az összes háttér-készlet erőforrásai és az előtér-IP-címek részletes mérőszámával. 

## <a name="next-steps"></a>Következő lépések 

- További információ a hálózati monitorozásról: Mi az az [Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
