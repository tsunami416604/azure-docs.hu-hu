---
title: SaaS SQL alkalmazás – videó
description: Ez a cikk a SaaS DB bérlői alkalmazás tervezéséről szóló 81 perces videónk különböző időpontjait indexeli a 2017. Átugorhatod azt a részt, ami érdekel. Legalább 3 mintát írunk le. A fejlesztést és felügyeletet leegyszerűsítő Azure-funkciók leírása.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 1ee8f2fff958045f652b72358ab928f82920fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067617"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Több-bérlős SaaS-alkalmazáshoz indexelt és jegyzetekkel elnevezett videó az Azure SQL Database használatával

Ez a cikk egy jegyzetekkel elnemírt index egy 81 perces videó saas-bérlői modellekről vagy mintákról. Ez a cikk lehetővé teszi, hogy előre vagy hátra ugorjon a videóban, hogy melyik rész érdekli Önt. A videó ismerteti a főbb tervezési lehetőségek egy több-bérlős adatbázis-alkalmazás az Azure SQL Database.The video explain the major design options for a multi-tenant database application on Azure SQL Database. A videó tartalmaz demókat, a felügyeleti kód forgatókönyvét, és időnként a tapasztalatok alapján részletesebb tájékoztatást, mint az írásos dokumentációnkban.

A videó felerősíti az információkat az írásos dokumentációban, amely a következő helyen található: 
- *Fogalmi:* [Több-bérlős SaaS-adatbázis bérleti minták][saas-concept-design-patterns-563e]
- *Oktatóanyagok:* [A Wingtip jegyek SaaS alkalmazás][saas-how-welcome-wingtip-app-679t]

A videó és a cikkek ismertetik a több-bérlős alkalmazás létrehozásának számos fázisát az Azure SQL Database a felhőben. Az Azure SQL Database speciális funkciói megkönnyítik a több-bérlős alkalmazások fejlesztését és megvalósítását, amelyek könnyebben kezelhetők és megbízhatóan működnek.

Rendszeresen frissítjük írásos dokumentációnkat. A videó nem szerkeszthető vagy nem frissül, így végül több részlete elavulttá válhat.



## <a name="sequence-of-38-time-indexed-screenshots"></a>38 időindexelt képernyőkép sorozata

Ez a szakasz 38 vitafórum idejét indexeli a 81 perces videóban. Minden alkalommal, amikor az index et elvan jegyzetelve egy képernyőképa a videóról, és néha további információkkal.

Minden alkalommal, amikor az index *h:mm:ss*formátumú. Például a második indexelt időhely, a **Session objectives**feliratú, **0:03:11**hozzávetőleges időpontban kezdődik.


### <a name="compact-links-to-video-indexed-time-locations"></a>Videóindexelt időhelyekre mutató hivatkozások tömörítése

A cikk későbbi részében a megfelelő jegyzetekkel elnemett szakaszokra mutató hivatkozások a következő címek:

- [1. **(Start)** Üdvözlő dia, 0:00:03](#anchor-image-wtip-min00001)
- [2. Munkamenet céljai, 0:03:11](#anchor-image-wtip-min00311)
- [3. Napirend, 0:04:17](#anchor-image-wtip-min00417)
- [4. Több-bérlős webalkalmazás, 0:05:05](#anchor-image-wtip-min00505)
- [5. App webes űrlap működés közben, 0:05:55](#anchor-image-wtip-min00555)
- [6. Bérlőnkénti költség (méretezés, elkülönítés, helyreállítás), 0:09:31](#anchor-image-wtip-min00931)
- [7. Adatbázis modellek több-bérlős: érvek és ellenérvek, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hibrid modell keverékek előnyeit MT / ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Egybérlős vs több-bérlős: érvek és ellenérvek, 00:16:44](#anchor-image-wtip-min01644)
- [10. A készletek költséghatékonyak a kiszámíthatatlan munkaterhelések esetén, 0:19:36](#anchor-image-wtip-min01936)
- [11. A bérlőnkénti és hibrid ST/MT adatbázis bemutatója, 0:20:08](#anchor-image-wtip-min02008)
- [12. Élő alkalmazás űrlap mutatja Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB és nem a DBA a láthatáron, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB rugalmas készlet használat példa, 0:29:40](#anchor-image-wtip-min02940)
- [15. Tanulás a MYOB-tól és más független szoftverszállítóktól, 0:31:36](#anchor-image-wtip-min03136)
- [16. Minták össze Az E2E SaaS forgatókönyv, 0:43:15](#anchor-image-wtip-min04315)
- [17. Kanonikus hibrid több-bérlős SaaS app, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS minta alkalmazás, 0:48:10](#anchor-image-wtip-min04810)
- [19. Forgatókönyvek és minták feltárt oktatóanyagok, 0:49:10](#anchor-image-wtip-min04910)
- [20. Bemutató bemutató és GitHub tárház, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub repo Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. A minták feltárása, 0:56:20](#anchor-image-wtip-min05620)
- [23. Bérlők kiépítése és bevezetés, 0:57:44](#anchor-image-wtip-min05744)
- [24. Bérlők kiépítése és alkalmazáskapcsolat, 0:58:58](#anchor-image-wtip-min05858)
- [25. Egyetlen bérlő üzembe létesítésének felügyeleti parancsfájljainak bemutatója, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell kiépítése és katalógus, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * From TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Előre nem látható bérlői munkaterhelések kezelése, 1:04:36](#anchor-image-wtip-min10436)
- [29. Elasztikus medence monitoring, 01:06:39](#anchor-image-wtip-min10639)
- [30. Terhelés generálása és teljesítményfigyelés, 1:09:42](#anchor-image-wtip-min10942)
- [31. Sémakezelés méretarányosan, 1:10:33](#anchor-image-wtip-min11033)
- [32. Elosztott lekérdezés a bérlői adatbázisok között, 1:12:21](#anchor-image-wtip-min11221)
- [33. A jegygenerálás bemutatója, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc analitika, 1:12:46](#anchor-image-wtip-min11246)
- [35. Bérlői adatok kinyerése sql DW-be, 1:16:32](#anchor-image-wtip-min11632)
- [36. Grafikon a napi értékesítés forgalmazás, 1:16:48](#anchor-image-wtip-min11648)
- [37. Wrap, és cselekvésre szólítanak fel, 1:19:52](#anchor-image-wtip-min11952)
- [38. Források további információk, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Jegyzetekkal elkészült index időhelyek a videóban

Ha bármelyik képernyőképre kattint, akkor a videó pontos helyére lép.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* Üdvözlő dia, 0:00:01

*Tanulás a MYOB-tól: SaaS-alkalmazások tervezési mintái az Azure SQL Database-ben – BRK3120*

[![Üdvözlő dia][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Cím: Tanulás a MYOB-tól: SaaS-alkalmazások tervezési mintái az Azure SQL Database-ben
- Bill.Gibson@microsoft.com
- Fő programmenedzser, Azure SQL adatbázis
- Microsoft Ignite session BRK3120, Orlando, FL USA, 2017.


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Session célkitűzések, 0:01:53
[![A munkamenet céljai][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatív modellek a több-bérlős alkalmazásokhoz, előnyökkel és hátrányokkal.
- SaaS-minták a fejlesztési, felügyeleti és erőforrás-költségek csökkentése érdekében.
- Mintaalkalmazás + parancsfájlok.
- A PaaS-funkciók + SaaS-minták az SQL Database-t rendkívül skálázható, költséghatékony adatplatformmá teszik a több-bérlős SaaS számára.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Napirend, 0:04:09
[![Napirend][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Több-bérlős webalkalmazás, 0:05:00
[![Wingtip SaaS alkalmazás: Több-bérlős webalkalmazás][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. App webes űrlap működés közben, 0:05:39
[![Alkalmazáswebes űrlap működés közben][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Bérlőnkénti költség (méretezés, elkülönítés, helyreállítás), 0:06:58
[![Bérlőnkénti költség, méretezés, elkülönítés, helyreállítás][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Adatbázis modellek több-bérlős: érvek és ellenérvek, 0:09:52
[![Adatbázis-modellek több-bérlős: érvek és ellenérvek][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hibrid modell keverékek előnyeit MT / ST, 0:12:29
[![A hibrid modell ötvözi az MT/ST előnyeit][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Egybérlős vs több-bérlős: érvek és ellenérvek, 00:13:11
[![Egybérlős vs több-bérlős: érvek és ellenérvek][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. A készletek költséghatékonyak a kiszámíthatatlan munkaterhelések esetén, 0:17:49
[![A készletek költséghatékonyak a kiszámíthatatlan munkaterhelések esetén][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. A bérlőnkénti és hibrid ST/MT adatbázis bemutatója, 0:19:59
[![Adatbázis-bérlőnkénti és hibrid ST/MT bemutatója][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Élő alkalmazás űrlap, amelyen A Dojo látható, 0:20:10
[![A Dojo-t megjelenítő élő alkalmazásűrlap][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB és nem a DBA a láthatáron, 0:25:06
[![MYOB és nem dba a láthatáron][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB rugalmas készlet használat példa, 0:29:30
[![Példa MYOB rugalmas készlet használatára][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Tanulás a MYOB-tól és más független szoftverszállítóktól, 0:31:25
[![Tanulás a MYOB-tól és más független szoftverszállítóktól][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Minták össze Az E2E SaaS forgatókönyv, 0:31:42
[![Minták össze az E2E SaaS forgatókönyv][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Kanonikus hibrid több-bérlős SaaS app, 0:46:04
[![Kanonikus hibrid több-bérlős SaaS-alkalmazás][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS minta alkalmazás, 0:48:01
[![Wingtip SaaS mintaalkalmazás][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Forgatókönyvek és minták feltárt oktatóanyagok, 0:49:00
[![Az oktatóanyagokban feltárt forgatókönyvek és minták][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Bemutató bemutató és GitHub tárház, 0:50:12
[![Bemutató oktatóanyagok és GitHub-tártár][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub repo Microsoft/WingtipSaaS, 0:50:32
[![GitHub-repo Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. A minták feltárása, 0:56:15
[![A minták feltárása][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Bérlők kiépítése és bevezetés, 0:56:19
[![Bérlők kiépítése és bevezetés][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Bérlők kiépítése és alkalmazáskapcsolat, 0:57:52
[![Bérlők és alkalmazáskapcsolat kiépítése][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Egyetlen bérlő talapzatának kezelési parancsfájljainak bemutatója, 0:59:36
[![Egyetlen bérlő üzembe létesítése a felügyeleti parancsfájlok bemutatójának bemutatója][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell kiépítése és katalógus, 0:59:56
[![PowerShell kiépítése és katalógus][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * From TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Előre nem látható bérlői munkaterhelések kezelése, 1:03:34
[![Előre nem látható bérlői munkaterhelések kezelése][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Elasztikus medence monitoring, 01:06:32
[![Rugalmas készletfigyelés][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Terhelés generálása és teljesítményfigyelés, 1:09:37
[![A terhelés generálása és a teljesítmény figyelése][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Sémakezelés méretarányban, 1:09:40
[![Sémakezelés nagy léptékben][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Elosztott lekérdezés a bérlői adatbázisok között, 1:11:18
[![Elosztott lekérdezés a bérlői adatbázisok között][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. A jegygenerálás bemutatója, 1:12:28
[![A jegygenerálás bemutatója][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc analitika, 1:12:35
[![SSMS adhoc analitika][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Bérlői adatok kinyerése sql DW-be, 1:15:46
[![Bérlői adatok kinyerése SQL DW-be][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Grafikon a napi értékesítés forgalmazás, 1:16:38
[![A napi értékesítés eloszlásának grafikonja][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Wrap, és cselekvésre szólítanak fel, 1:17:43
[![Wrap up és cselekvésre való felhívás][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Források további információk, 1:20:35
[![További információk forrásai][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogbejegyzés, 2017.][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Fogalmi:* [Több-bérlős SaaS-adatbázis bérleti minták][saas-concept-design-patterns-563e]

- *Oktatóanyagok:* [A Wingtip jegyek SaaS alkalmazás][saas-how-welcome-wingtip-app-679t]

- GitHub-adattárak a Wingtip Jegyek SaaS-bérlőalkalmazás ízeihez:
    - [GitHub-tárpa a - Önálló alkalmazásmodellhez.][github-wingtip-standaloneapp]
    - [GitHub-tárház - DB bérlőnkéntmodell.][github-wingtip-dbpertenant]
    - [GitHub-tárház hoz - Több-bérlős DB modell.][github-wingtip-multitenantdb]





## <a name="next-steps"></a>További lépések

- [Első oktatóanyag-cikk][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Üdvözlő dia"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Munkamenet-célkitűzések."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Napirend."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS alkalmazás: Több-bérlős webalkalmazás"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Alkalmazáswebes űrlap működés közben"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Bérlőnkénti költség, méretezés, elkülönítés, helyreállítás"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Adatbázis-modellek több-bérlős: érvek és ellenérvek"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "A hibrid modell ötvözi az MT/ST előnyeit"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Egybérlős vs több-bérlős: érvek és ellenérvek"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "A készletek költséghatékonyak a kiszámíthatatlan munkaterhelések esetén"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Adatbázis-bérlőnkénti és hibrid ST/MT bemutatója"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "A Dojo-t megjelenítő élő alkalmazásűrlap"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB és nem dba a láthatáron"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Példa MYOB rugalmas készlet használatára"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Tanulás a MYOB-tól és más független szoftverszállítóktól"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Minták össze az E2E SaaS forgatókönyv"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kanonikus hibrid több-bérlős SaaS-alkalmazás"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS mintaalkalmazás"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Az oktatóanyagokban feltárt forgatókönyvek és minták"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Az oktatóanyagok és a GitHub-tártár bemutatója"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub-repo Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "A minták feltárása"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Bérlők kiépítése és bevezetés"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Bérlők és alkalmazáskapcsolat kiépítése"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Egyetlen bérlő üzembe létesítése a felügyeleti parancsfájlok bemutatójának bemutatója"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell kiépítése és katalógus"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Előre nem látható bérlői munkaterhelések kezelése"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Rugalmas készletfigyelés"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "A terhelés generálása és a teljesítmény figyelése"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Sémakezelés nagy léptékben"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Elosztott lekérdezés a bérlői adatbázisok között"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "A jegygenerálás bemutatója"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS adhoc analitika"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Bérlői adatok kinyerése SQL DW-be"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "A napi értékesítés eloszlásának grafikonja"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Wrap up és cselekvésre való felhívás"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "További információk forrásai"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

