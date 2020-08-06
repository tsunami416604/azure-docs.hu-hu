---
title: Az Azure Virtual WAN monitorozása Azure Monitor-ismeretekkel
description: Tudnivalók a Virtual WAN figyeléséről Azure Monitor információk használatával
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836071"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor a virtuális WAN (előzetes verzió)

[Azure monitor](../azure-monitor/insights/network-insights-overview.md) a virtuális WAN-ra vonatkozó elemzése lehetővé teszi a felhasználók és a kezelők számára, hogy megtekintsék a virtuális WAN állapotát és állapotát, amely egy automatikusan felderített topológiai Térkép használatával jelenik meg. Az erőforrás állapota és állapota átfedésben van a térképen, hogy a virtuális WAN általános állapotának pillanatkép-nézetét adja meg. Az erőforrás-navigáció a térképen egyetlen kattintással elérhető a virtuális WAN-portál erőforrás-konfigurációs lapjaira.

A virtuális WAN erőforrás-szintű metrikák gyűjtése és bemutatása egy előre csomagolt virtuális WAN-metrikai munkafüzettel történik, amely a virtuális WAN, a hub, az átjáró és a kapcsolat szintjein jeleníti meg a metrikákat. Ebből a cikkből megtudhatja, hogyan használhatja a virtuális WAN Azure Monitor-bepillantást a virtuális WAN-topológiák és-mérőszámok egyetlen helyen történő megtekintéséhez.

> [!NOTE]
> A virtuális WAN-portálon található megállapítások menüpont a kilépési folyamatban van. Habár a virtuális WAN-hoz készült megállapítások menüje folyamatban van, a virtuális WAN-topológia és a metrikák munkafüzete közvetlenül elérhető az Azure-beli hálózatok használatával. További információ: [Azure monitor](../azure-monitor/insights/network-insights-overview.md) elemzések. 
>

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések azt feltételezik, hogy már telepített egy virtuális WAN-t egy vagy több hubhoz. Új virtuális WAN és új központ létrehozásához kövesse az alábbi cikkekben ismertetett lépéseket:

* [Virtuális WAN létrehozása](virtual-wan-site-to-site-portal.md#openvwan)
* [Elosztó létrehozása](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN-topológia megtekintése

A **Azure Portal->virtuális WAN**a bal oldali **figyelés** menüjéből válassza az adatok **(előzetes verzió)** lehetőséget. Ez megjeleníti a **betekintő nézetet**, amely a virtuális WAN-függőségi térképet és a magas szintű mérőszámok mini-munkafüzetet jeleníti meg.

**1. ábra: figyelés – áttekintés menü**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="ábra" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

**Az információk nézetben** megtekintheti az automatikusan felderített virtuális WAN-erőforrásokat, például a hubokat, az átjárókat, a tűzfalakat, a kapcsolatokat és a küllős virtuális hálózatok, a harmadik féltől származó NVA és a végpontok közötti virtuális WAN-ágakat, ahogy az a **2. ábrán**is látható.

Az **erőforrás állapota** és **állapota** színkódolt, és a Térkép erőforrás-ikonjain látható. A virtuális WAN magas szintű mérőszámai, például a hub kapacitása és az átjáró kihasználtsága a jobb oldalon, egy mini-munkafüzeten keresztül jelennek meg.

**2. ábra: az áttekintések nézete**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="ábra" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Függőség nézet

A virtuális WAN **függőségi** nézete megkönnyíti az összes virtuális WAN-erőforrás összekapcsolt nézetének megjelenítését egy sugaras architektúrában.

**3. ábra: a VWAN függőségi nézete**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Függőségi Térkép" lightbox="./media/azure-monitor-insights/dependency-map.png":::

A függőség nézet Térkép a következő erőforrásokat jeleníti meg csatlakoztatott gráfként:

* Virtuális WAN-hubok a különböző Azure-régiók között.
* Küllős virtuális hálózatok, amelyek közvetlenül csatlakoznak a központhoz.
* VPN-és ExpressRoute-fiókirodák és P2S-felhasználók, amelyek a megfelelő ExpressRoute, S2S és P2S kapcsolaton keresztül csatlakoznak az egyes központokhoz, valamint virtuális hálózati átjárókkal.
* Az Azure tűzfalak (beleértve a harmadik féltől származó tűzfalakat is) központilag üzembe helyezhetők (biztonságos központ).
* harmadik féltől származó NVA (hálózati virtuális berendezések), amelyek küllős virtuális hálózatok vannak telepítve.

A függőségi Térkép is megjeleníti a nem közvetlenül csatlakoztatott virtuális hálózatok (VNet, amelyek egy virtuális WAN küllős virtuális hálózatok vannak társítva).

A függőségi Térkép egyszerű navigálást tesz lehetővé az egyes erőforrások konfigurációs beállításaiban. Például a hub-erőforrás fölé húzva megtekintheti az alapszintű erőforrás-konfigurációt, például a hub-régiót és a hub-előtagot. Kattintson a jobb gombbal a hub-erőforrás Azure Portal oldalának eléréséhez.

**4. ábra: az erőforrás-specifikus információk megkeresése**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="erőforrás-információk":::

A függőség nézet keresési és szűrési sávján keresztül könnyedén kereshet a gráfon. A különböző szűrők segítségével leszűkítheti a keresést egy adott elérési útra és állapotba.

**5. ábra: keresés és szűrés**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="keresési és szűrési sáv" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Részletes mérőszámok

A **részletes mérőszámok oldal** megnyitásához válassza a **részletes mérőszámok megtekintése** lehetőséget. A metrikák lap egy olyan irányítópult, amely külön lapokkal van konfigurálva, és hasznos információkat biztosít a virtuális WAN erőforrás-kapacitásáról, teljesítményéről és kihasználtságáról a virtuális WAN szintjén, a hub szintjén és az egyes kapcsolatokon.

**6. ábra: részletes mérőszámok irányítópultja**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="részletes mérőszámok" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a Azure Monitor metrikákkal kapcsolatban, tekintse meg a [Azure monitor mérőszámait](../azure-monitor/platform/data-platform-metrics.md).
* Az összes virtuális WAN-metrika részletes ismertetését lásd: [virtuális WAN-naplók és-metrikák](logs-metrics.md).
