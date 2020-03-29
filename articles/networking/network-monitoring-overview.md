---
title: A hálózatfigyelés az Azure Monitor naplóiban | Microsoft dokumentumok
description: A hálózati figyelési megoldások áttekintése, beleértve az NPM-et is, a hálózatok felhőbeli, helyszíni és hibrid környezetekben való kezeléséhez.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: 2912488286745bf8d2e567d09e445b0a44dc7c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67672178"
---
# <a name="network-monitoring-solutions"></a>Hálózatfigyelési megoldások 

Az Azure számos megoldást kínál a hálózati eszközök figyelésére. Az Azure-nak vannak megoldásai és segédprogramjai a hálózati kapcsolatok, az ExpressRoute-áramkörök állapotának figyelésére és a felhőbeli hálózati forgalom elemzésére.

## <a name="network-performance-monitor-npm"></a>Hálózati teljesítményfigyelő (NPM)

A Hálózati teljesítményfigyelő (Network Performance Monitor) egy olyan képességcsomag, amely a hálózat állapotának, az alkalmazásokhoz való hálózati kapcsolatnak a figyelésére, valamint a hálózat teljesítményébe való betekintést nyújt. Az NPM felhőalapú, és hibrid hálózati figyelési megoldást kínál, amely figyeli a következők közötti kapcsolatot:
 
* Felhőalapú telepítések és helyszíni helyek
* Több adatközpont és fiókiroda
* Kritikus fontosságú többszintű alkalmazások/mikroszolgáltatások
* Felhasználói helyek és webalapú alkalmazások (HTTP/HTTPs) 

A Teljesítményfigyelő, az ExpressRoute-figyelő és a Szolgáltatáskapcsolat-figyelő az NPM-en belül figyeli a funkciókat, és az alábbiakban ismertetjük őket.

## <a name="performance-monitor"></a>Teljesítménymonitorozás

A Teljesítményfigyelő az NPM része, és a felhő-, hibrid és helyszíni környezetek hálózati figyelése. A hálózati kapcsolatokat figyelheti a távoli fiók- és területi irodákban, az üzlethelyeken, az adatközpontokban és a felhőkben. A hálózati problémákat még a felhasználók panasza előtt észlelheti. A legfontosabb előnyök a következők:

* A veszteség és a késés figyelése a különböző alhálózatok között, és riasztások beállítása
* A hálózat összes útvonalának figyelése (beleértve a redundáns elérési utakat is)
* A nehezen replikálható átmeneti és időponthoz elegendő hálózati problémák elhárítása
* A hálózat azon szegmensének meghatározása, amely a leromlott teljesítményért felelős
* A hálózat állapotának figyelése SNMP nélkül

![NPM topológia térkép](./media/network-monitoring-overview/npm-topology-map.png) 

További információt az alábbi cikkekben talál:

* [Hálózati teljesítményfigyelő megoldás konfigurálása az Azure Figyelő naplóiban](../azure-monitor/insights/network-performance-monitor.md) 
* [Használati esetek](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Termékfrissítések:
  * [2017. február](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [2017. augusztus](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute-figyelő

Az ExpressRoute-hoz az NPM átfogó ExpressRoute-figyelést biztosít az Azure Private társviszony-létesítési és microsoftos társviszony-létesítési kapcsolatokhoz. Figyelheti az E2E-kapcsolatot és a teljesítményt a fiókirodák és az Azure között az ExpressRoute-on keresztül. A legfontosabb képességek a következők:

* Az előfizetéshez társított ER-áramkörök automatikus észlelése
* Hálózati topoológia észlelése a helyszíni alkalmazásoktól a felhőalapú alkalmazásokig
* Kapacitástervezés, sávszélesség-kihasználtsági elemzés
* Figyelés és riasztás mind az elsődleges, mind a másodlagos útvonalakon
* Az Azure-szolgáltatásokkal, például az Office 365-tel, a Dynamics 365-tel, a ... expressroute-on keresztül
* A virtuális hálózatokkal való kapcsolat romlásának észlelése

![Régiók közötti forgalmat megjelenítő földrajzi térkép](./media/network-monitoring-overview/expressroute-topology-map.png) 

További információkért tekintse át a következő cikkeket:

* [Network Performance Monitor for ExpressRoute konfigurálása](../expressroute/how-to-npm.md)
* [Blogbejegyzést](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Szolgáltatáskapcsolati monitor

A Szolgáltatáskapcsolat figyelésével most tesztelheti az alkalmazások elérhetőségét, és észlelheti a teljesítménybeli szűk keresztmetszeteket a helyszíni, a szolgáltatói hálózatokon és a felhő-/magánadatközpontokban.

* Az alkalmazások végpontok közötti hálózati kapcsolatának figyelése
* Az alkalmazások kézbesítésének és a hálózati teljesítménynek a korrelálása, a lebomlás pontos helyének észlelése a felhasználó és az alkalmazás közötti útvonal mentén
* Az alkalmazások elérhetőségének tesztelése a világ több felhasználói helyéről
* Határozza meg a hálózati késést és a csomagvesztést az üzletághoz és az SaaS-alkalmazásokhoz
* Határozza meg a hálózati forró pontokat, amelyek az alkalmazás gyenge teljesítményét okozhatják
* Az Office 365-alkalmazások elérhetőségének figyelése a Microsoft Office 365, a Dynamics 365, a Skype Vállalati verzió és más Microsoft-szolgáltatások beépített tesztjeinek használatával

További információkért tekintse át a következő cikkeket:

* [A Hálózati teljesítményfigyelő konfigurálása a szolgáltatásvégpontok figyelésére](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogbejegyzést](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Forgalmi elemzések
A Traffic Analytics egy felhőalapú megoldás, amely betekintést nyújt a felhőhálózatok felhasználói és alkalmazási tevékenységébe. Az NSG flow-naplók elemzése a következőkbe való betekintést nyújt:

* Forgalom áramlik a hálózatok között az Azure és az internet, a nyilvános felhő régiók, VNETs, és alhálózatok
* Alkalmazások és protokollok a hálózaton, anélkül, hogy a szippantás vagy dedikált áramlásgyűjtő készülékek
* Top talkers, beszédes alkalmazások, VM beszélgetések a felhőben, a közlekedési hotspotok
* A VNET-k közötti forgalom forrásai és célállomásai, a kritikus üzleti szolgáltatások és az alkalmazások közötti kapcsolatok
* Biztonság – rosszindulatú forgalom, az internetre nyitott portok, alkalmazások vagy virtuális gépek, amelyek internet-hozzáférést próbálnak elérni...
* Kapacitáskihasználás – segít kiküszöbölni a túl-kiépítés vagy az alulkihasználtság problémáit a VPN-átjárók és egyéb szolgáltatások kihasználtsági tendenciáinak figyelésével

A Traffic Analytics olyan használható információkkal látja el Önt, amelyek segítségével ellenőrizheti a szervezet hálózati tevékenységét, biztonságos alkalmazásokat és adatokat biztosíthat, optimalizálhatja a számítási feladatok teljesítményét és megfelelőséget biztosít.

![Régiók közötti forgalmat megjelenítő földrajzi térkép](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Kapcsolódó linkek:
* [Blogbejegyzés](https://aka.ms/trafficanalytics), [Dokumentáció](https://aka.ms/trafficanalyticsdocs), [GYIK](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-elemzés
A DNS-rendszergazdák számára készült megoldás a DNS-naplókat gyűjti, elemzi és korrelálja a biztonság, a műveletek és a teljesítményhez kapcsolódó elemzések biztosítása érdekében.  Néhány képesség a következő:

* A rosszindulatú tartományokra feloldani próbáló ügyfelek azonosítása
* Elavult erőforrásrekordok azonosítása
* A gyakran lekérdezett tartománynevek és a beszédes DNS-ügyfelek láthatósága
* A DNS-kiszolgálók kérelemterhelésének láthatósága
* Dinamikus DNS-regisztrációs hibák figyelése

![DNS-elemzés irányítópultja](./media/network-monitoring-overview/dns-analytics-overview.png) 

Kapcsolódó linkek:
* [Blogbejegyzés](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentáció](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Egyéb

* [Új árak](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
