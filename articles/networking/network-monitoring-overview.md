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
ms.openlocfilehash: 66e6b49dd3e3c7d0f2cbd7547b68733e1d4959ce
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="network-monitoring-solutions"></a>A hálózatfigyelési megoldások 

Az Azure-megoldások olyan gazdagépet, a hálózati eszközök figyelése kínál. Azure-megoldások és hálózati kapcsolatot, ExpressRoute-Kapcsolatcsoportok állapotának figyelésére, és a felhő hálózati forgalom elemzése segédprogramok rendelkezik.

## <a name="network-performance-monitor-npm"></a>Hálózati teljesítmény figyelése (NPM)

Hálózati teljesítmény figyelése (NPM) képességeket, amelyek részesíti előnyben a hálózaton, a hálózati kapcsolatot az alkalmazások állapotának figyelése és hálózatára teljesítményének betekintést nyújt a csomagok. NPM felhőalapú és hibrid hálózatfigyelési megoldás, amely figyeli a közötti kapcsolatot biztosít:
 
* A felhő-telepítések és a helyszíni helyek
* Több adatközpontok és a fiókirodák számára
* Küldetési kritikus Többrétegű alkalmazások és micro-szolgáltatások
* Felhasználói helyek és a webes alkalmazás (HTTP/HTTPs) 

## <a name="performance-monitor"></a>Teljesítménymonitorozás

Teljesítményfigyelő NPM részét képezi, és a hálózat a felhőhöz, hibrid és a helyszíni környezetek figyelése. Távoli fiókirodai és a fiókirodák, a tárolási helyek, a adatközpontok és a felhők közötti hálózati kapcsolat figyelheti. Mielőtt a felhasználók panaszkodnak mert hálózati problémák észlelését. A főbb előnyei a következők:

* Veszteséget és késéseket figyelése különböző alhálózatokon és értesítések beállítása
* Összes elérési utat (például a redundáns elérési utak) a hálózat figyelése
* Átmeneti és időpontban hálózati problémák, nehezen replikálása
* Határozza meg a hálózaton, a teljesítmény csökkenését felelős az adott szegmens
* A hálózati SNMP szükségessége nélkül állapotának figyelésére

![NPM topológia térkép](./media/network-monitoring-overview/npm-topology-map.png) 

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

## <a name="service-endpoint-monitor"></a>Szolgáltatási végpont-figyelő

Szolgáltatásvégpont figyeléssel, mostantól reachability alkalmazások tesztelése és a helyszíni, a vivőjel hálózatok és a felhő/titkos adatközpontok között szűk keresztmetszetek észleléséhez.

* Végpontok közötti hálózati kapcsolat alkalmazások figyelése
* Alkalmazások kézbesítéséhez kivizsgált hálózati teljesítményt, pontos helyét a felhasználó és az alkalmazás között az útvonalon teljesítménycsökkenést észlelése
* Teszt alkalmazás reachability világszerte több felhasználó helyekről
* Határozza meg a hálózati késés és a csomag veszteséget az üzletági és SaaS-alkalmazásokhoz
* Határozza meg a hálózaton, gyenge alkalmazásteljesítmény okozó interaktív területek
* Az Office 365 alkalmazásra, beépített tesztek a Microsoft Office 365, Dynamics 365, a Skype vállalati verzió és más Microsoft-szolgáltatásokban elérhetőség figyelésére

További információkért tekintse át a következő cikkeket:

* [Hálózati Teljesítményfigyelő Szolgáltatásvégpontok figyelés konfigurálása](https://aka.ms/applicationconnectivitymonitorguide)
* [blogbejegyzés](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Forgalmi elemzések
Forgalom Analytics egy felhőalapú megoldás, amely a felhasználó-és alkalmazás betekintést nyújt a felhőalapú hálózatokhoz. NSG Flow naplók elemzése a betekintést:

* A hálózatok közötti Azure és az Internet, nyilvános felhő régiók, Vnetek és alhálózatok közötti forgalmat
* Alkalmazások és a hálózaton, nincs szükség szimatolók vagy dedikált folyamata adatgyűjtő készülékek protokollok
* Felső talkers, chatty alkalmazások, virtuális gép beszélgetések a felhőben, a forgalom csatlakozási pontokhoz
* Források és a célhelyek között Vnetek, közötti kritikus fontosságú üzleti szolgáltatások és alkalmazások közötti kapcsolatok forgalom
* Biztonság – rosszindulatú forgalmat, a portokat nyissa meg az internethez, az alkalmazások és a virtuális gépek Internet-hozzáférés megkísérlése...

Forgalom Analytics vontatón végrehajthatóként adatokat, hogy a segítségével a szervezet hálózati tevékenységet, biztonságos alkalmazásokhoz és adatokhoz, naplózási munkaterhelés teljesítményének optimalizálásához, és megfelelő maradnak.

![Földrajzi-társítási ábrázoló forgalom régiók között](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Kapcsolódó hivatkozások:
* [Blogbejegyzés](https://aka.ms/trafficanalytics), [dokumentáció](https://aka.ms/trafficanalyticsdocs), [– gyakori kérdések](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-elemzés
DNS-rendszergazdák számára készült, ez a megoldás gyűjti, elemzi és biztonsága, DNS-naplók hibához kapcsolódó elemzések műveletek és a teljesítmény.  Néhány olyan funkciója, a következők:

* Ügyfelek, rosszindulatú tartományokhoz megoldásának azonosítója
* Elavult erőforrásrekordok azonosítása
* Gyakran lekérdezett tartománynevek és a talkative DNS-ügyfelek láthatósága
* A kérelem terhelés, a DNS-kiszolgálók láthatósága
* A dinamikus DNS-regisztrációs hibák figyelése

![DNS Analytics Dashboard](./media/network-monitoring-overview/dns-analytics-overview.png) 

Kapcsolódó hivatkozások:
* [Blogbejegyzés](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentáció](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="next-steps"></a>További lépések

* [Konfigurálja a hálózati teljesítmény figyelése](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)
