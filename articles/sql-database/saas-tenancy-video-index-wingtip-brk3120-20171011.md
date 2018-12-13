---
title: Indexelt videó, Azure SaaS SQL-alkalmazás |} A Microsoft Docs
description: Ez a cikk különféle idő pontok indexeli az SaaS-DB bérlős app Tervező, a 2017. október 11 tárolt Ignite konferenciáról videó 81 percek alatt. Áttérhet az Önt érdeklő részére. Legalább 3 minták ismertetik. Azure-szolgáltatások, amelyek megkönnyítik a fejlesztését és kezelését ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib
manager: craigg
ms.date: 05/14/2018
ms.openlocfilehash: ec3f286791a3a2c7a3bebd84ba17a11571e389f8
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163973"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>A videó indexelt, és jegyzetelve lettek volna, több-bérlős SaaS-alkalmazás az Azure SQL Database használatával

Ez a cikk egy 81 perces videó bérlős SaaS-modelleket és minták az idő helyek be annotált index. Ez a cikk lehetővé teszi, hogy Ugrás vissza, vagy a videó mely részében előre lehetőséggel. A videó az Azure SQL Database több-bérlős adatbázis-alkalmazásoknál fő kialakítási lehetőségeit ismerteti. A videó problémáira bemutatóinkon, forgatókönyvek felügyeleti szabályzat, és esetenként részletesebben élmény figyelmeztet írásos dokumentációnk lehet, mint tartalmazza.

A videó írásos dokumentációban található a következő helyen található információk felerősíti: 
- *Fogalmi:* [Több-bérlős SaaS-adatbázis bérlős minták][saas-concept-design-patterns-563e]
- *Az oktatóanyagok:* [A Wingtip Tickets SaaS-alkalmazás][saas-how-welcome-wingtip-app-679t]

A videó és a cikkek ismertetik, számos fázisai a több-bérlős alkalmazás létrehozását az Azure SQL Database a felhőben. Speciális funkciók az Azure SQL Database egyszerűbbé kifejlesztése és megvalósítása a több-bérlős alkalmazások, amelyek is könnyebben kezelhető és megbízhatóan nagy teljesítményt nyújtva.

Rendszeresen frissítjük az írásos dokumentációt. A videó nem szerkeszthetők vagy frissíteni, így idővel további, a részletesség is elavulttá válnak.



## <a name="sequence-of-38-time-indexed-screenshots"></a>38 idő indexelt képernyőképek sorrendje

Ez a szakasz indexeli elérhetetlennek 81 video 38 hozzászólások időzónáját. Minden egyes alkalommal index egy Képernyőkép a videót, és egyes esetekben további információkat tartalmazó van feliratozva.

Minden egyes alkalommal index formátumban van *ó*. Például a második indexelt időzónáját feliratú **munkamenet célok**, becsült időt helyét kezdődik **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompakt mutató indexelt videó idő helyek

Az alábbi címek a cikk későbbi részében megfelelő megjegyzésekkel ellátott szakasz mutató hivatkozásokat:

- [1. **(Indítás)**  Üdvözlő diába 0:00:03](#anchor-image-wtip-min00001)
- [2. Munkamenet-célkitűzések 0:03:11](#anchor-image-wtip-min00311)
- [3. Napirend, 0:04:17](#anchor-image-wtip-min00417)
- [4. Több-bérlős web app alkalmazásban 0:05:05](#anchor-image-wtip-min00505)
- [5. Alkalmazás webes űrlap művelet, 0:05:55](#anchor-image-wtip-min00555)
- [6. Bérlőnkénti költség (skálázást, elkülönített helyreállítási), 0:09:31](#anchor-image-wtip-min00931)
- [7. Adatbázis-modellek a több-bérlős: és hátrányai, 0:11:59](#anchor-image-wtip-min01159)
- [8. A hibrid modell veheti igénybe az MT/ST, 0 előnyei: 13:01](#anchor-image-wtip-min01301)
- [9. Egybérlős és több-bérlős: és hátrányai, 0:16:44](#anchor-image-wtip-min01644)
- [10. Címkészlet, költséghatékony kiszámíthatatlan számítási feladatok esetén 0:19:36](#anchor-image-wtip-min01936)
- [11. Bérlőnkénti adatbázis és a hibrid ST/MT, 0 bemutató: 20:08](#anchor-image-wtip-min02008)
- [12. Élő bemutató Dojo, 0 alkalmazás űrlap: 20:29](#anchor-image-wtip-min02029)
- [13. Nem üzemel, 0 az adatbázis és a MYOB: 28:54](#anchor-image-wtip-min02854)
- [14. MYOB rugalmas készlet használati például 0:29:40](#anchor-image-wtip-min02940)
- [15. A MYOB és más független szoftverszállítók, 0 tanulási: 31:36](#anchor-image-wtip-min03136)
- [16. Minták összeállítása be E2E SaaS-környezetben, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonical hibrid több-bérlős SaaS-alkalmazás, 0:47:33](#anchor-image-wtip-min04733)
- [18. A Wingtip SaaS mintaalkalmazást, 0:48:10](#anchor-image-wtip-min04810)
- [19. Forgatókönyvek és minták az oktatóanyagok, 0 vizsgálhatók: 49:10](#anchor-image-wtip-min04910)
- [20. Bemutató oktatóanyagokat és a GitHub-tárházat, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub-adattárat a Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. A minták, 0 felfedezése: 56:20](#anchor-image-wtip-min05620)
- [23. Bérlők és bevezetési, 0 üzembe helyezése: 57:44](#anchor-image-wtip-min05744)
- [24. Bérlők és az alkalmazás kapcsolat, 0 üzembe helyezése: 58:58](#anchor-image-wtip-min05858)
- [25. Bemutató felügyeleti parancsfájlok kiépítése egyetlen új bérlő, 0:59:43](#anchor-image-wtip-min05943)
- [26. Kiépítéssel és katalogizálással, 1:00:02: PowerShell-lel](#anchor-image-wtip-min10002)
- [27. A T-SQL SELECT * a TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Előre nem látható a bérlői terhelések, 1:04:36 kezelése](#anchor-image-wtip-min10436)
- [29. Rugalmas készlet figyelése, 1:06:39](#anchor-image-wtip-min10639)
- [30. Betöltés és a teljesítmény figyelését, 1:09:42](#anchor-image-wtip-min10942)
- [31. Nagy mennyiségű, 1:10:33 séma kezelése](#anchor-image-wtip-min11033)
- [32. Elosztott lekérdezés több bérlős adatbázisban, 1:12:21](#anchor-image-wtip-min11221)
- [33. A jegy létrehozásakor, 1:12:32 bemutató](#anchor-image-wtip-min11232)
- [34. SSMS alkalmi elemzések, 1:12:46](#anchor-image-wtip-min11246)
- [35. Az SQL dw-vel, 1:16:32 bérlői adatok kibontása](#anchor-image-wtip-min11632)
- [36. Napi értékesítés terjesztési, 1:16:48-grafikon](#anchor-image-wtip-min11648)
- [37. Belefoglalni, és hívja meg a műveletet, 1:19:52](#anchor-image-wtip-min11952)
- [38. További információ, 1:20:42 erőforrások](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Jegyzettel ellátott indexelési idő helyeket a videóban

Bármilyen képernyőkép képre kattintva megnyílik a pontos idő helyre a videóban.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Indítás)*  Üdvözlő diába 0:00:01

*A MYOB tanulás: Tervezési minták az Azure SQL Database - BRK3120 SaaS-alkalmazásokhoz*

[![Üdvözöljük dia][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Cím: A MYOB tanulás: Tervezési minták az Azure SQL Database SaaS-alkalmazásokhoz
- Bill.Gibson@microsoft.com
- Fő Programmenedzsere, az Azure SQL Database
- A Microsoft Ignite munkamenet BRK3120, Orlandóban, FL USA, 2017. október/11.


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Munkamenet-célkitűzések 0:01:53
[![Munkamenet-célkitűzések][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatív példamodellek a több-bérlős alkalmazások, és hátrányai.
- SaaS-minták fejlesztési, felügyeleti és erőforrás-költségek csökkentése érdekében.
- Egy mintaalkalmazás + parancsfájlokat.
- PaaS szolgáltatások + SaaS-minták győződjön meg arról, az SQL Database rugalmasan méretezhető, költséghatékony adatplatform a több-bérlős SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Napirend, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Több-bérlős web app alkalmazásban 0:05:00
[![A Wingtip SaaS-alkalmazás: Több-bérlős webalkalmazások][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Alkalmazás webes űrlap művelet, 0:05:39
[![Alkalmazás webes űrlap művelet][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Bérlőnkénti költség (skálázást, elkülönített helyreállítási), 0:06:58
[![Bérlőnkénti költség, méretezhető, elkülönítést, a helyreállítási][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Adatbázis-modellek a több-bérlős: és hátrányai, 0:09:52
[![Adatbázis-modellek a több-bérlős: és hátrányai][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. A hibrid modell veheti igénybe az MT/ST, 0 előnyei: 12:29
[![A hibrid modell veheti igénybe az MT/ST előnyei][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Egybérlős és több-bérlős: és hátrányai, 0:13:11
[![Egybérlős és több-bérlős: és hátrányai][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Címkészlet, költséghatékony kiszámíthatatlan számítási feladatok esetén 0:17:49
[![Címkészlet, költséghatékony kiszámíthatatlan számítási feladatokhoz][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Bérlőnkénti adatbázis és a hibrid ST/MT, 0 bemutató: 19:59
[![Bérlőnkénti adatbázis és a hibrid ST/MT bemutatója][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Élő bemutató Dojo, 0 alkalmazás űrlap: 20:10
[![Az élő alkalmazás űrlap Dojo megjelenítése][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. Nem üzemel, 0 az adatbázis és a MYOB: 25-ös: 06
[![MYOB, és nem üzemel az adatbázis][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB rugalmas készlet használati például 0:29:30
[![Példa a MYOB rugalmas készlet használatra][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. A MYOB és más független szoftverszállítók, 0 tanulási: 31:25-kor
[![A MYOB és más független szoftverszállítók tanulás][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Minták összeállítása be E2E SaaS-környezetben, 0:31:42
[![Minták összeállítása be E2E SaaS-környezetben][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonical hibrid több-bérlős SaaS-alkalmazás, 0:46:04
[![Canonical hibrid több-bérlős SaaS-alkalmazás][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. A Wingtip SaaS mintaalkalmazást, 0:48:01
[![A Wingtip SaaS-mintaalkalmazás][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Forgatókönyvek és minták az oktatóanyagok, 0 vizsgálhatók: 49:00
[![Forgatókönyvek és minták megvizsgálta a oktatóanyagok][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Bemutató oktatóanyagokat és a GitHub-tárházat, 0:50:12
[![Bemutató oktatóanyagokat és a GitHub-adattár][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub-adattárat a Microsoft/WingtipSaaS, 0:50:32
[![GitHub-adattárat a Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. A minták, 0 felfedezése: 56:15
[![A minták felfedezése][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Bérlők és bevezetési, 0 üzembe helyezése: 56:19
[![Bérlők és az előkészítési üzembe helyezése][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Bérlők és az alkalmazás kapcsolat, 0 üzembe helyezése: 57:52
[![Bérlők és az alkalmazás kapcsolat kiépítése][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Bemutató felügyeleti parancsfájlok kiépítése egyetlen új bérlő, 0:59:36
[![Bemutató felügyeleti parancsfájlok egyetlen új bérlő kiépítése][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell-lel kiépítéssel és katalogizálással, 0:59:56
[![Kiépítéssel és katalogizálással PowerShell-lel][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![A T-SQL SELECT * a TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Előre nem látható a bérlői terhelések, 1:03:34 kezelése
[![Előre nem látható a bérlői terhelések kezelése][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Rugalmas készlet figyelése, 1:06:32
[![Rugalmas készlet figyelése][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. . És a teljesítmény figyelését, 1:09:37 betöltése
[![Terhelés létrehozása és az alkalmazásteljesítmény-figyelés][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Séma a felügyeletet, 1:09:40
[![Séma kezelése, ipari méretekben][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Több bérlős adatbázisban, 1:11:18 elosztott lekérdezés
[![Elosztott lekérdezés több bérlős adatbázisban][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. A jegy létrehozásakor, 1:12:28 bemutató
[![A jegy létrehozásakor bemutató][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS alkalmi elemzések, 1:12:35
[![SSMS alkalmi elemzések][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Bérlő adatokat nyerhet ki az SQL dw-vel, 1:15:46
[![Bérlő adatokat nyerhet ki az SQL DW-be][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Napi értékesítés terjesztési, 1:16:38-grafikon
[![Napi értékesítés terjesztésipont-grafikon][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Belefoglalni, és hívja meg a műveletet, 1:17:43
[![Belefoglalni, és hívja meg a műveletet][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. További információ, 1:20:35-erőforrások
[![További információk erőforrások][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogbejegyzésében találhat, 2017. május 22.][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Fogalmi:* [Több-bérlős SaaS-adatbázis bérlős minták][saas-concept-design-patterns-563e]

- *Az oktatóanyagok:* [A Wingtip Tickets SaaS-alkalmazás][saas-how-welcome-wingtip-app-679t]

- GitHub-adattárak a Wingtip Tickets SaaS-bérlős alkalmazás változatban érhetők el:
    - [GitHub-adattárat a – önálló alkalmazásmodell][github-wingtip-standaloneapp].
    - [GitHub-adattárat a - DB / bérlő modell][github-wingtip-dbpertenant].
    - [GitHub-adattárat a – több-Bérlős DB modell][github-wingtip-multitenantdb].





## <a name="next-steps"></a>További lépések

- [Első oktatóanyag][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Üdvözöljük dia"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Munkamenet-célkitűzéseket."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Napirend."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "A Wingtip SaaS-alkalmazás: Több-bérlős webalkalmazások"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Alkalmazás webes űrlap művelet"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Bérlőnkénti költség, méretezhető, elkülönítést, a helyreállítási"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Adatbázis-modellek a több-bérlős: és hátrányai"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "A hibrid modell veheti igénybe az MT/ST előnyei"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Egybérlős és több-bérlős: és hátrányai"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Címkészlet, költséghatékony kiszámíthatatlan számítási feladatokhoz"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Bérlőnkénti adatbázis és a hibrid ST/MT bemutatója"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Az élő alkalmazás űrlap Dojo megjelenítése"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB, és nem üzemel az adatbázis"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Példa a MYOB rugalmas készlet használatra"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "A MYOB és más független szoftverszállítók tanulás"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Minták összeállítása be E2E SaaS-környezetben"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hibrid több-bérlős SaaS-alkalmazás"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "A Wingtip SaaS-mintaalkalmazás"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Forgatókönyvek és minták megvizsgálta a oktatóanyagok"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Bemutató oktatóanyagokat és a GitHub-adattár"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub-adattárat a Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "A minták felfedezése"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Bérlők és az előkészítési üzembe helyezése"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Bérlők és az alkalmazás kapcsolat kiépítése"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Bemutató felügyeleti parancsfájlok egyetlen új bérlő kiépítése"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Kiépítéssel és katalogizálással PowerShell-lel"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "A T-SQL SELECT * a TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Előre nem látható a bérlői terhelések kezelése"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Rugalmas készlet figyelése"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Terhelés létrehozása és az alkalmazásteljesítmény-figyelés"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Séma kezelése, ipari méretekben"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Elosztott lekérdezés több bérlős adatbázisban"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "A jegy létrehozásakor bemutató"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS alkalmi elemzések"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Bérlő adatokat nyerhet ki az SQL DW-be"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Napi értékesítés terjesztésipont-grafikon"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Belefoglalni, és hívja meg a műveletet"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "További információk erőforrások"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

