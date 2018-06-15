---
title: Az Azure Operations Management Suite (OMS) dokumentációja – oktatóanyagok | Microsoft Docs
description: A Microsoft Operations Management Suite (OMS) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében. Ez a cikk azonosítja az OMS különböző szolgáltatásait, és a részletes tartalmukra mutató hivatkozásokat tartalmaz.
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: ''
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23071211"
---
# <a name="what-is-operations-management-suite-oms"></a>Mi az az Operations Management Suite (OMS)?
A Microsoft Operations Management Suite (OMS) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében.  Mivel az OMS felhőalapú szolgáltatás, gyorsan és az infrastruktúra-szolgáltatásokra fordított minimális mértékű befektetéssel üzembe helyezhető.  Az új funkciók bevezetése automatikus, így csökkenthetők a folyamatos karbantartás és frissítés költségei.

Amellett, hogy az OMS önmagában is értékes szolgáltatásokat biztosít, integrálható a System Center összetevőivel, például a System Center Operations Manager termékkel, így a meglévő felügyeleti beruházások kiterjeszthetők a felhőre.  A System Center és az OMS együttműködve teljes hibrid felügyeleti megoldást képes biztosítani.

A következőkben az OMS különböző felhasználási területeinek és az őket megvalósító szolgáltatásoknak a magas szintű leírását olvashatja.  A különböző OMS-összetevők részletes dokumentációjának tanulmányozása előtt az OMS-architektúra vázlatán áttekintheti az egyes összetevők szerepét.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Betekintések és elemzés](media/operations-management-suite-overview/icon-insight-analytics.png) Betekintések és elemzés
A [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) segít az operációs rendszerek és alkalmazások által generált napló- és teljesítményadatokat összegyűjteni, keresni, a köztük lévő összefüggéseket felderíteni, illetve az adatok alapján cselekedni. Ez lehetővé teszi, hogy az integrált keresés és az egyéni irányítópultok segítségével valós idejű üzemeltetési adatokhoz jusson, és azonnal elemezze a különböző számítási feladatokból és a kiszolgálókról származó rekordok millióit, függetlenül attól, hogy fizikailag hol helyezkednek el.

A Log Analytics egyszerűen kiegészíthető olyan megoldásokkal, amelyek meghatározzák a gyűjtendő adatokat, valamint az elemzés logikáját.  A megoldások tartalmazhatnak olyan kiegészítő funkciókat, amelyek automatikusan, minimális konfigurációval, vagy akár konfiguráció nélkül is üzembe helyezhetők az ügynökökön.  Az egyedi megoldások által biztosított elemzési eszközök mellett egyéni keresést is végezhet a teljes adatkészleten az adatok rendszerekkel és alkalmazásokkal fennálló összefüggéseinek feltárásához.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automatizálás és vezérlés](media/operations-management-suite-overview/icon-automation-control.png) Automatizálás és vezérlés
Az Azure Automation az adminisztratív folyamatokat PowerShell-alapú, az Azure-felhőben futó [runbookok](../automation/automation-runbook-types.md) segítségével automatizálja.  A runbookok hozzáférhetnek minden olyan termékhez vagy szolgáltatáshoz, amely PowerShell használatával felügyelhető, így többek között a más felhőkben – például az Amazon Web Servicesben (AWS) – található erőforrásokhoz is.  A runbookok futtathatók a helyi adatközpontban is, és kezelhetők velük a helyi erőforrások.

Az Azure Automation a [PowerShell DSC](../automation/automation-dsc-overview.md) használatával biztosít konfigurációkezelést.  Létrehozhat és kezelhet az Azure szolgáltatásban üzemeltetett DSC-erőforrásokat, és alkalmazhatja őket a felhőbeli és a helyszíni rendszerekre a konfigurációjuk automatikus meghatározásához és kikényszerítéséhez.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Védelem és helyreállítás](media/operations-management-suite-overview/icon-protection-recovery.png) Védelem és vészhelyreállítás
Az [Azure Backup](http://azure.microsoft.com/documentation/services/backup) védelmet biztosít az alkalmazásadatok számára, valamint évekig megőrzi őket minden tőkebefektetés nélkül és minimális működési költségek mellett.  Képes biztonsági másolatot készíteni a fizikai és a virtuális Windows kiszolgálókról, valamint az olyan alkalmazások számítási feladatairól, mint az SQL Server és a SharePoint.  A System Center Data Protection Manager (DPM) is használhatja arra, hogy a redundancia és a hosszú távú tárolás biztosítása érdekében a védett adatokat az Azure szolgáltatásba replikálja.

Az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) a helyszíni Hyper-V virtuális gépek, VMware virtuális gépek és fizikai Windows/Linux kiszolgálók replikálásával, feladatátvételével és helyreállításával segít a vállalatnak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A gépek replikálhatók egy másodlagos adatközpontba, illetve arra is van lehetőség, hogy a gépek Azure szolgáltatásba replikálása révén kiterjessze az adatközpontot. A Site Recovery is egyszerű feladatátvételt és helyreállítási lehetőségeket biztosít a számítási feladatok számára. Integrálható az olyan vészhelyreállítási mechanizmusokkal, mint például az SQL Server AlwaysOn, valamint helyreállítási terveket kínál a több számítógépen rétegzett számítási feladatok egyszerű feladatátvételéhez.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS Biztonság és megfelelőség](media/operations-management-suite-overview/icon-security-compliance.png) Biztonság és megfelelőség
A biztonsági és megfelelőségi funkciók segítenek azonosítani, felmérni és mérsékelni az infrastruktúrát fenyegető biztonsági kockázatokat.  Az OMS ide tartozó funkcióit a Log Analytics szolgáltatáson belüli különböző olyan megoldások valósítják meg, amelyek elemzik az ügynökrendszerekről származó naplóadatokat és konfigurációkat, így segítve Önt a számítási környezet folyamatos védelmében.

* A [biztonsági és auditálási megoldás](oms-security-getting-started.md) a gyanús tevékenységek azonosítása érdekében összegyűjti és elemzi a felügyelt rendszerekről származó biztonsági eseményeket.
* A [kártevőirtó megoldás](../log-analytics/log-analytics-malware.md) jelentést készít a felügyelt rendszereken használt, kártevőkkel szembeni védelem állapotáról.  
* A rendszerfrissítési megoldás elemzi a felügyelt rendszereken elérhető biztonsági és egyéb frissítéseket, így egyszerűen be tudja azonosítani, hogy mely rendszerek igénylik javítás telepítését.

## <a name="next-steps"></a>Következő lépések
* További tudnivalók a [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) szolgáltatásról.
* További tudnivalók az [Azure Automation](../automation/automation-intro.md) szolgáltatásról.
* További tudnivalók az [Azure Backup](http://azure.microsoft.com/documentation/services/backup) szolgáltatásról.
* További tudnivalók az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) szolgáltatásról.

