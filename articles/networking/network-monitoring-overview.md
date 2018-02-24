---
title: "Hálózati figyelő a Naplóelemzési vonatkozó |} Microsoft Docs"
description: "A hálózatfigyelési megoldások, beleértve az NPM segítségével kezeli a felhő, a helyszíni és hibrid környezetekben hálózatok áttekintése."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6793bd8d2c561554213e9fe645aab018dba7a925
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="network-monitoring-solutions"></a>A hálózatfigyelési megoldások 

Az Azure-megoldások olyan gazdagépet, a hálózati eszközök figyelése kínál. Azure-megoldások és hálózati kapcsolatot, ExpressRoute-Kapcsolatcsoportok állapotának figyelésére, és a felhő hálózati forgalom elemzése segédprogramok rendelkezik.

## <a name="network-performance-monitor-npm"></a>Hálózati teljesítmény figyelése (NPM)

Hálózati teljesítmény figyelése (NPM) képességeket, amelyek részesíti előnyben a hálózaton, a hálózati kapcsolatot az alkalmazások állapotának figyelése és hálózatára teljesítményének betekintést nyújt a csomagok. NPM felhőalapú és hibrid hálózatfigyelési megoldás, amely figyeli a közötti kapcsolatot biztosít:
 
* A felhő-telepítések és a helyszíni helyek
* Több adatközpontok és a fiókirodák számára
* Küldetési kritikus Többrétegű alkalmazások és micro-szolgáltatások
* Felhasználói helyek és a webes alkalmazás (HTTP/HTTPs) 

## <a name="performance-monitor"></a>Teljesítményfigyelő

Teljesítményfigyelő NPM részét képezi, és a hálózat a felhőhöz, hibrid és a helyszíni környezetek figyelése. Távoli fiókirodai és a fiókirodák, a tárolási helyek, a adatközpontok és a felhők közötti hálózati kapcsolat figyelheti. Mielőtt a felhasználók panaszkodnak mert hálózati problémák észlelését. A főbb előnyei a következők:

* Veszteséget és késéseket figyelése különböző alhálózatokon és értesítések beállítása
* Összes elérési utat (például a redundáns elérési utak) a hálózat figyelése
* Átmeneti és időpontban hálózati problémák, nehezen replikálása
* Határozza meg a hálózaton, a teljesítmény csökkenését felelős az adott szegmens
* A hálózati SNMP szükségessége nélkül állapotának figyelésére

További információkért tekintse meg a következő cikkeket:

* [A Naplóelemzési hálózati teljesítményt figyelő megoldás konfigurálása](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Használati esetek](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Termékfrissítéseket: [. február 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [2017. augusztus](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Az ExpressRoute-figyelő

Az ExpressRoute NPM kínál a magánhálózati társviszony-létesítési kapcsolatok átfogó ExpressRoute megfigyelését. Figyelheti E2E kapcsolat és a fiókirodák és az Azure között ExpressRoute keresztül. A főbb funkciók a következők:

* Az Ön előfizetéséhez rendelve ER kapcsolatok automatikus észlelését
* A hálózati topológiát a helyszíni a felhőalapú alkalmazások észlelése
* Kapacitástervezési, memóriakihasználtságának elemzése
* Figyelés és riasztás elsődleges és másodlagos elérési utakon található
* Észleli a kapcsolatot a Vnetek teljesítménycsökkenése

További információkért tekintse át a következő cikkeket:

* [Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)
* [blogbejegyzés](https://aka.ms/NPMExRmonitorGA)

## <a name="next-steps"></a>További lépések

* [Konfigurálja a hálózati teljesítmény figyelése](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)