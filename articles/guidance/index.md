---
title: "Azure útmutató | Microsoft Docs"
description: "Ajánlott eljárások és útmutató az Azure-hoz"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Azure útmutató
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A Microsoft mintákkal és gyakorlatokkal foglalkozó csoportja az Azure ügyféltanácsadói csapatának része. Célunk annak elősegítése, hogy a fejlesztők, tervezők és informatikusok sikerrel használják a Microsoft Azure platformot. Kidolgoztunk egy útmutatót, amely bemutatja az Azure felhőalapú megoldások kiépítéséhez ajánlott eljárásait.

## <a name="checklists"></a>Ellenőrzőlisták
Ezek a listák rövid összefoglalásul szolgálnak a rendelkezésre állás és méretezhetőség alapvető szempontjainak áttekintéséhez. 

* [Rendelkezésre állási ellenőrzőlisták][AvailabilityChecklist] 
  
    Összegzi azokat az ajánlott eljárásokat, amelyek a rendelkezésre állás biztosításához szükségesek.
* [Méretezhetőségi ellenőrzőlisták][ScalabilityChecklist]
  
    Összegzi azokat az ajánlott eljárásokat, amelyek a méretezhető szolgáltatások és adatkezelési módszerek tervezéséhez és üzembe helyezéséhez szükségesek.

## <a name="best-practices-articles"></a>Az ajánlott eljárásokra vonatkozó cikkek
Ezek a cikkek részletesen ismertetik a felhőalapú informatikához kapcsolódó fontosabb fogalmakat. 

* [API-tervezés][APIDesign] 
  
    Ismerteti azokat a tervezési problémákat, amelyeket érdemes figyelembe venni a webes API-k tervezésekor.
* [API-implementáció][APIImplementation] 
  
    Ismerteti azokat az ajánlott eljárásokat, amelyek a webes API-k implementálásához és közzétételéhez szükségesek.
* [API biztonsági útmutató](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    A hitelesítéssel és az engedélyezéssel kapcsolatos problémákat ismerteti (például jogkivonattípusok, engedélyezési protokollok, engedélyezési folyamatok és fenyegetéselhárítás).
* [Útmutató az automatikus méretezéshez][AutoscalingGuidance] 
  
    A manuális beavatkozás nélküli méretezési megoldásokra vonatkozó szempontok összegzése.
* [Útmutató a háttérfeladatokhoz][BackgroundJobsGuidance] 
  
    A rendelkezésre álló lehetőségek és az ajánlott eljárások ismertetése a háttérben, az előtérbeli vagy interaktív műveletektől függetlenül elvégzendő feladatok végrehajtásához.
* [Content Delivery Network (CDN) útmutató][CDNGuidance] 
  
    Általános útmutatás és ajánlott eljárások a CDN használatához az alkalmazások terhelésének minimalizálása, valamint a lehető legmagasabb szintű rendelkezésre állás és teljesítmény elérése érdekében.
* [Gyorsítótárazási útmutató][CachingGuidance] 
  
    Annak összefoglalása, hogyan használhatja a gyorsítótárat a rendszer teljesítményének és méretezhetőségének növeléséhez.
* [Adatparticionálási útmutató][DataPartitioningGuidance]
  
    Stratégiák az adatok particionálására a méretezhetőség növeléséhez, az erőforrásokért folytatott versengés kiküszöböléséhez és a teljesítmény optimalizálásához.
* [Figyelési és diagnosztikai útmutató][MonitoringandDiagnosticsGuidance] 
  
    Útmutató annak nyomon követéséhez, hogyan használják a rendszert a felhasználók és milyen mértékű kihasználtság jellemzi az erőforrásokat, illetve a rendszer általános állapotának és teljesítményének megfigyeléséhez.
* [Ajánlott elnevezési konvenciók][naming-conventions] 
  
    Az Azure-erőforrások ajánlott elnevezési konvenciói.
* [Újrapróbálkozásokra vonatkozó általános útmutató][RetryGeneralGuidance] 
  
    Az átmeneti hibák kezelésével kapcsolatos általános fogalmak ismertetése.
* [Újrapróbálkozásokra vonatkozó szolgáltatásspecifikus útmutató][RetryServiceSpecificGuidance]
  
    Több Azure-szolgáltatás újrapróbálkozási funkcióinak összegzése, köztük olyan információkkal, amelyek segítséget nyújtanak a szolgáltatás újrapróbálkozási mechanizmusának használatához, módosításához vagy kibővítéséhez.

## <a name="scenario-guides"></a>Forgatókönyvekre vonatkozó útmutatók
* [Elasticsearch futtatása az Azure-on][elasticsearch] 
  
    Az Elasticsearch egy rugalmasan méretezhető, nyílt forráskódú keresőmotor és adatbázis. A használata olyan helyzetekben jelenthet ideális megoldást, amelyek gyors elemzést igényelnek, és az információkat nagy adatkészletekből kell kikeresni. Ez az útmutató olyan fontos szempontokkal foglalkozik, amelyeket érdemes figyelembe venni egy Elasticsearch-fürt megtervezésekor.
* [Identitáskezelés a több-bérlős alkalmazásokban][identity-multitenant] 
  
    A több-bérlős egy olyan architektúra, ahol egy alkalmazáson több bérlő osztozik, de ezek el vannak különítve egymástól. Ez az útmutató bemutatja, hogyan kezelheti a felhasználói identitásokat egy több-bérlős alkalmazásban, ha az [Azure Active Directoryt][AzureAD] használja a bejelentkezés és hitelesítés kezeléséhez.
* [Big data-megoldások fejlesztése](https://msdn.microsoft.com/library/dn749874.aspx)
  
    Ez az útmutató a HDInsight használatát mutatja be olyan forgatókönyvekben, mint például az iteratív feltárás, az adattárházak, ETL-folyamatok, illetve a meglévő BI rendszerekbe végzett integrálás. Emellett a big data-fogalmak értelmezéséhez, a big data-megoldások tervezéséhez és kialakításához, valamint ezen megoldások megvalósításához is útmutatást nyújt.

## <a name="patterns"></a>Minták
* [Felhőkialakítási minták: Előírásszerű útmutató a felhőalapú alkalmazások architektúrájához](https://msdn.microsoft.com/library/dn568099.aspx)
  
    A felhőkialakítási minták gyűjteménye a kialakítási minták és a hozzájuk kapcsolódó, útmutató témakörök könyvtára. Annak bemutatásával hangsúlyozza ki a minták alkalmazásának előnyeit, hogy az egyes részek hogyan illeszkednek a felhőalapú alkalmazások architektúrájába.
* [A felhőalapú alkalmazások teljesítményének optimalizálása](https://github.com/mspnp/performance-optimization)
  
    Ez az útmutató olyan gyakran használt, nem megfelelően kialakított mintákat mutat be, amelyek akadályozzák az alkalmazások a terhelés szerinti méretezését. Nyolc ilyen nem megfelelően kialakított mintát tartalmaz, valamint egy [teljesítményelemzési ismertetőt](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) és egy útmutatót a [teljesítmény fő mérőszámok alapján történő felméréséhez](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Referenciaarchitektúrák
A referenciaarchitektúrák forgatókönyv szerint vannak elrendezve.
Minden architektúrához talál ajánlott eljárásokat és részletesen ismertetett lépéseket, valamint egy futtatható összetevőt, amely az ajánlásokat foglalja magában.

A referenciaarchitektúrák aktuális könyvtára a következő címen érhető el: [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>A rugalmasságra vonatkozó útmutatás
Ezek a témakörök olyan alkalmazások tervezésének módját ismertetik, amelyekre nincs hatással, ha egy elosztott felhőkörnyezet meghibásodik.   

* [A rugalmasság szempontjainak áttekintése][ResiliencyOvervew]
  
     Megtudhatja, hogyan hozhat létre olyan alkalmazásokat az Azure platformon, amelyek a hibák után helyre tudnak állni, és továbbra is működőképesek maradnak. A megfelelő szintű rugalmasság elérésének strukturált, a tervezéstől a megvalósításig, az üzembe helyezésig és a működtetésig minden szakaszt magában foglaló megközelítését írja le.
* [Rugalmasságra vonatkozó ellenőrzőlista][resiliency-checklist]
  
    Ajánlások ellenőrzőlistája, amely számos lehetséges hibaállapotra segít felkészülni.
* [A hibaállapot elemzése][resiliency-fma] 
  
    A hibaállapot-elemzés (failure mode analysis, FMA) olyan folyamat, amely a lehetséges meghibásodási pontok azonosításával épít be kellő rugalmasságot a rendszerbe. Az FMA-folyamat kiindulópontjaként a cikk tartalmaz egy lehetséges hibaállapotokat és azok kezelését ismertető katalógust. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Dec16_HO1-->


