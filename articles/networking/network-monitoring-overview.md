---
title: Kapcsolatos hálózati figyelés a Log Analytics Rendszereben |} A Microsoft Docs
description: Hálózatfigyelési megoldásokkal, mint az NPM segítségével kezeli a felhőbeli, helyszíni és hibrid környezetekben hálózatok áttekintése.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 4107b292a606ea629e7de0c7be66621d699c5f42
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427386"
---
# <a name="network-monitoring-solutions"></a>Hálózati megoldások monitorozása 

Az Azure-megoldások egy gazdagépet a hálózati eszközök figyelése kínál. Az Azure rendelkezik a megoldásokat és segédprogramok használatával a hálózati kapcsolat ExpressRoute-Kapcsolatcsoportok állapotának figyelheti és elemezheti a felhőbeli hálózati forgalmat.

## <a name="network-performance-monitor-npm"></a>A Network Performance Monitor (NPM)

Network Performance monitort (NPM) olyan képességeit, amelyek mindegyike részesíti előnyben a hálózaton, a hálózati kapcsolat és az alkalmazások állapotának figyelése és a hálózat teljesítményét elemezheti. Az NPM felhőalapú és a egy hibrid Hálózatfigyelő megoldás, amely figyeli a közötti kapcsolatot biztosít:
 
* Felhőbeli központi telepítések és a helyszíni helyek
* Az adatközpontok és a fiókirodák
* Mission kritikus Többrétegű alkalmazások/mikroszolgáltatások
* Felhasználó tartózkodási helye és webes alkalmazásokat (HTTP/HTTPs) 

Teljesítményfigyelő, az ExpressRoute-figyelő és a szolgáltatás Kapcsolatfigyelő NPM képességeire figyeli, és az alábbiakban tekintheti át.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

A Teljesítményfigyelő az NPM részét képezi, és hálózati felhőalapú, hibrid és helyszíni környezetek figyelése. Fiókirodai és kirendeltségekben, tárolási helyek, az adatközpontok és felhők közötti hálózati kapcsolat követheti nyomon. Hálózati problémák képes észlelni, mielőtt felhasználói panaszok. A fő előnyei a következők:

* Különböző alhálózatokban és beállított riasztásokkal figyelheti a veszteséget és a késést
* A hálózaton (beleértve a redundáns elérési utak) összes útvonalának figyelése
* Átmeneti és időponthoz – hálózati problémák, amelyeket nehéz replikálni
* A hálózat, teljesítménycsökkenésért felelős konkrét szegmensét meghatározása
* Anélkül monitorozhatja a hálózat állapotát, hogy az SNMP protokollt kellene használnia

![Az NPM-topológia térkép](./media/network-monitoring-overview/npm-topology-map.png) 

További információkért tekintse meg a következő cikkeket:

* [A Network Performance Monitor megoldás konfigurálása a Log Analyticsben](../azure-monitor/insights/network-performance-monitor.md) 
* [Használati esetek](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Termékfrissítések: [2017 február](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [2017. augusztus](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute-figyelő

Az expressroute-hoz az NPM kínál átfogó ExpressRoute-monitorozás az Azure privát társviszony-létesítés és Microsoft társviszony-kapcsolatokat. Figyelheti E2E kapcsolatait és teljesítményét a fiókirodák és az Azure között expressroute-on keresztül. A főbb funkciók a következők:

* Az előfizetéséhez tartozó ER-Kapcsolatcsoportok automatikus felismerése
* Hálózati topológia a helyszíni, felhőalapú alkalmazások észlelése
* Kapacitás megtervezése, sávszélesség-kihasználtságának elemzése
* Monitorozási és riasztási az elsődleges és másodlagos elérési utak
* Kapcsolat az Azure figyelési szolgáltatások például az Office 365, Dynamics 365, expressroute-on keresztül
* Virtuális hálózatok kapcsolatának teljesítménycsökkenése észlelése

![Földrajzitérkép megjelenítő forgalom régióban](./media/network-monitoring-overview/expressroute-topology-map.png) 

További információkért tekintse át a következő cikkeket:

* [Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)
* [Blogbejegyzés](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Szolgáltatás-összekapcsolhatósági monitor

Figyelési szolgáltatás kapcsolata, most tesztelheti az alkalmazások lemezekről és a teljesítmény szűk észleléséhez a helyszíni, a szolgáltató hálózatok és a felhő/titkos adatközpontok.

* Végpontok közötti hálózati alkalmazások hálózati kapcsolatának figyelése
* Hálózati teljesítmény az alkalmazásszolgáltatást összekapcsolását, pontos helyét a felhasználó és az alkalmazás között az elérési úton elhelyezkedő teljesítménycsökkenés észlelését
* Teszt alkalmazás elérhetőség világszerte több felhasználó helyről
* Határozza meg a hálózati késés és a csomag adatveszteség a sor üzleti és SaaS-alkalmazásokhoz
* A hálózaton, a gyenge alkalmazásteljesítményt okozó hotspotok meghatározása
* Elérhetőség beépített tesztek a Microsoft Office 365, Dynamics 365, a Skype és egyéb Microsoft-szolgáltatásokat használó, Office 365-alkalmazások figyelése

További információkért tekintse át a következő cikkeket:

* [A Network Performance Monitor konfigurálása Szolgáltatásvégpontok figyeléshez](https://aka.ms/applicationconnectivitymonitorguide)
* [Blogbejegyzés](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Forgalmi elemzések
A TRAFFIC Analytics egy felhőalapú megoldás, amely a felhőbeli hálózatok felhasználói és alkalmazástevékenységekbe betekintést nyújt. Hálózati biztonsági csoportok naplóinak elemzése, amelyek betekintést nyújtanak:

* A hálózatok között az Azure és az Internet, nyilvános felhő-régiók, virtuális hálózatok és alhálózatok közötti forgalom
* Alkalmazások és a hálózaton, szimatolók vagy dedikált folyamat gyűjtő berendezéseket kellene protokollok
* Fő kapcsolattartók, a forgalmas alkalmazások, a virtuális gép beszélgetések a felhőben, a legaktívabb
* Forrásaként és céljaként a forgalom virtuális hálózataiban, kritikus fontosságú üzleti szolgáltatásokat és alkalmazásokat között helyek közötti kapcsolatok
* Biztonság – rosszindulatú forgalom, a portokat nyissa meg az internethez, alkalmazások és virtuális gépek Internet-hozzáférés kísérlet...
* Kapacitás kihasználtsága - megakadályozhatja, hogy túlzott kiosztása vagy alulkihasználtságának problémák figyelésével VPN-átjárók és más szolgáltatások kihasználtsági trendek

A TRAFFIC Analytics programcsomagok döntéstámogató információkat, hogy segítséget nyújt a szervezet hálózati tevékenység, biztonságos alkalmazások és adatok naplózása, számítási feladatok teljesítményének optimalizálásához, és megfelelnek.

![Földrajzitérkép megjelenítő forgalom régióban](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Kapcsolódó hivatkozások:
* [Blogbejegyzés](https://aka.ms/trafficanalytics), [dokumentáció](https://aka.ms/trafficanalyticsdocs), [– gyakori kérdések](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-elemzés
DNS-rendszergazdák számára készült, ez a megoldás gyűjti, elemzi és hátterében a DNS-naplókat biztonsági, a műveletek és a teljesítménnyel kapcsolatos információkat.  Funkciói a következők:

* Azok az ügyfelek feloldják rosszindulatú tartományokat, azonosítása
* Elavult erőforrásrekordok azonosítása
* Gyakran lekérdezett tartománynevek és beszédes DNS-ügyfelek
* A kérelmek által generált terhelést a DNS-kiszolgálókon láthatósága
* Dinamikus DNS-regisztrációs hibák figyelése

![DNS Analytics Dashboard](./media/network-monitoring-overview/dns-analytics-overview.png) 

Kapcsolódó hivatkozások:
* [Blogbejegyzés](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentációja](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Egyéb rendelkezések

* [Új díjszabás](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
