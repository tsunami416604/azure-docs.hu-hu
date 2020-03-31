---
title: Azure-figyelő hálózatokhoz (előzetes verzió)
description: Az Azure Monitor for Network gyors áttekintése, amely átfogó képet nyújt az összes üzembe helyezett hálózati erőforrás állapotáról és metrikáiról konfiguráció nélkül.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654868"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure-figyelő hálózatokhoz (előzetes verzió)
Az Azure Monitor for Network átfogó képet nyújt az összes üzembe helyezett hálózati erőforrás állapotáról és metrikákról, konfiguráció nélkül. A speciális keresési funkció segít azonosítani az erőforrás-függőségeket, lehetővé téve a forgatókönyveket, például a webhelyet üzemeltető erőforrások azonosítását a hosztolt webhely nevének egyszerű keresésével.

Az Azure Monitor hálózatok **áttekintése** lap egy egyszerű módja annak, hogy vizualizálja a hálózati erőforrások leltárát, valamint az erőforrások állapotát és riasztásokat. Négy fő funkcionális területre oszlik:

- Keresés és szűrés
- Erőforrás állapota és metrikák
- Riasztások 
- Függőségi nézet

![Áttekintő lap](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Keresés és szűrés
Az erőforrás állapota és a riasztások nézet testreszabható olyan szűrőkkel, mint **az Előfizetés**, **az Erőforráscsoport** és **az Erőforrástípus**. A keresőmező lehetővé teszi az erőforrás-tulajdonságok on keresztüli keresést.

A keresőmező segítségével erőforrásokat és a kapcsolódó erőforrásokat kereshet. Például egy nyilvános IP-cím van társítva egy alkalmazásátjáróhoz. A nyilvános IP-dns-név keresése azonosítja a nyilvános IP-címet és a kapcsolódó Alkalmazásátjárót is.

![Keresés](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Erőforrás állapota és metrikája
Minden csempe egy erőforrástípust jelöl, az összes előfizetésben telepített példányok számával az erőforrás állapotával együtt. Az alábbi példában 105 ER és VPN-kapcsolatok üzembe helyezett, 103 kifogástalan állapotú, és 2 nem érhető el.

![Erőforrás állapota](media/network-insights-overview/resource-health.png)

A két nem elérhető ER- és VPN-kapcsolatra kattintva elindít egy metrikanézetet. 

![Metrikus nézet](media/network-insights-overview/metric-view.png)

A rácsnézet minden elemére kattinthat. Kattintson az Egészség ikonra az adott kapcsolat erőforrás-állapotára való átirányításához. Kattintson a Riasztások gombra a riasztásokra, illetve a mérőszámok oldalára való átirányításához az adott kapcsolathoz. 

## <a name="alerts"></a>Riasztások
A jobb oldali **Riasztások** rács a kiválasztott erőforrásokhoz az összes előfizetésben létrehozott összes riasztást tekinti meg. Kattintson a riasztásszámít, hogy keresse meg a részletes riasztások oldalon.

## <a name="dependency-view"></a>Függőségi nézet
A **Függőség** nézet segít az erőforrás konfigurálásának megjelenítésében. Jelenleg a függőségi nézet csak az Application Gateway esetén támogatott. A függőségi nézet az Application Gateway erőforrás nevére kattintva érhető el a metrikarács nézetből.

![Alkalmazásátjáró nézet](media/network-insights-overview/application-gateway.png)

Az Application Gateway **függőségi** nézete egyszerűsített nézetet biztosít arról, hogy az előtér-IP-k hogyan kapcsolódnak a figyelőkhöz, szabályokhoz és háttérkészlethez. A csatlakozó élek színkódoltak, és további részleteket nyújtanak a háttérkészlet állapota alapján. A nézet is részletes áttekintést nyújt az Application Gateway metrikák és metrikák az összes kapcsolódó háttér-készletek, például a VMSS és a virtuális gép példányok.

![Függőségi nézet](media/network-insights-overview/dependency-view.png)

A függőségi grafikon lehetővé teszi a könnyű navigációt a konfigurációs beállításokhoz. Kattintson a jobb gombbal a háttérkészletre, hogy hozzáférjen más funkciókhoz. Ha például a háttérkészlet egy virtuális gép, akkor közvetlenül elérheti a VM Insights és a Network Watcher kapcsolat hibaelhárítási kapcsolat azonosítása kapcsolódási problémák.

![Függőségi nézet menü](media/network-insights-overview/dependency-view-menu.png)

A függőségi nézet keresési és szűrősávja könnyedén kereshet a diagramon. Ha például *az AppGWTestRule kifejezésre* keres az alábbi példában, akkor az *AppGWTestRule-n*keresztül csatlakoztatott összes csomópontra leszűkíti a grafikus nézetet. 

![Példa keresési](media/network-insights-overview/search-example.png)

A különböző szűrők segítségével leszűkítheti egy adott elérési útra és állapotra. Ha például csak az **Állapot** legördülő *menüben* csak az Nem kifogástalan állapot lehetőséget választja, az összes olyan él megjelenítéséhez, ahol az állapot *nem kifogástalan.*

Kattintson a **Részletes metrikanézetre** egy előre konfigurált munkafüzet elindításához, amely részletes metrikákat tartalmaz az alkalmazásátjáróhoz, az összes háttérkészlet-erőforrásokhoz és az előtér-IP-címzéshez. 

## <a name="next-steps"></a>További lépések 

- További információ a hálózati figyelésről a Mi az [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
