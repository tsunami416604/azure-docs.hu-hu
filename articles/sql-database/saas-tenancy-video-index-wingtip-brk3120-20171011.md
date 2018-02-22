---
title: "Videó indexelt, Azure SaaS SQL-alkalmazás |} Microsoft Docs"
description: "Ez a cikk különböző időpontban indexeli a 81 kapcsolatos SaaS DB bérleti app kialakításának köszönhetően az ignite-on konferencia 2017. október 11 tartani a videó percben. Kihagyhatja azokat, amelyek az Önt érdeklő részére. Legalább 3 mintákat ismerteti. Azure-szolgáltatások, amelyek megkönnyítik a fejlesztői és felügyeleti ismerteti."
ms.custom: 
ms.date: 12/06/2017
ms.prod: 
ms.service: sql-database
ms.reviewer: billgib
ms.suite: 
ms.technology:
- database-engine
ms.tgt_pltfrm: 
ms.topic: article
author: MightyPen
ms.author: genemi
manager: craigg
ms.workload: Inactive
ms.openlocfilehash: 5e8d5467e676ee178b77c02e387bdfd2c54e6071
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="video-indexed-and-annotated-for-mulit-tenant-saas-app-using-azure-sql-database"></a>Videó indexelt és jegyzetelve mulit-bérlő SaaS-alkalmazás Azure SQL Database használata

Ez a cikk egy megjegyzésekkel ellátott index egy 81 perces videót SaaS bérleti modellek és minták idő helyeinek. Ez a cikk lehetővé teszi Ugrás vissza, vagy a videót, amely mely részében előre érdekel meg. A videó az Azure SQL Database egy több-bérlős adatbázis-alkalmazás fő kialakítási lehetőségeit ismerteti. A videó bemutatók, a felügyeleti kód forgatókönyvek és esetenként részletesebb tájékoztatást élmény írásbeli dokumentációban lehet, mint tartalmazza.

A videó felerősíti írásbeli dokumentációban, a következő címen található információkat: 
- *Ismertető:* [több-bérlős Szolgáltatottszoftver-adatbázis bérleti minták][saas-concept-design-patterns-563e]
- *Oktatóanyag:* [a Wingtip jegyek SaaS-alkalmazáshoz][saas-how-welcome-wingtip-app-679t]

A videó és a cikkek ismertetik, egy több-bérlős alkalmazás létrehozása az Azure SQL Database felhőbeli számos fázisból. Az Azure SQL Database speciális funkciók révén könnyebben kifejlesztése és megvalósítása a több-bérlős-alkalmazásokat is könnyebben kezelhető, és megbízhatóan performant.

Rendszeresen frissítjük írásbeli dokumentációban. A videó nem szerkeszthető, vagy frissíteni, így végül további, a részletesség is elavulttá válnak.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Feladatütemezési a 38 idő indexelt képernyőfelvételek

Ez a szakasz indexeli a 81 perces videót teljes 38 beszélgetéseket idő helyét. Minden alkalommal index van feliratozva a videó látható egy képernyőkép, és egyes esetekben további információt.

Minden alkalommal indexe formátumban *: mm: SS*. Például a második indexelt idő helyét, címkével **munkamenet célok**, kezdődik, az a megközelítőleges időpont, amikor helyét **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>A videó indexelt idő helyekre kompakt hivatkozások

Az alábbi címek a cikk későbbi részében megfelelő megjegyzésekkel ellátott szakasz hivatkozásokat:

- [1. **(Kezdő)**  Üdvözlő menüpontban húzással állíthatja be, 0:00:03](#anchor-image-wtip-min00001)
- [2. Munkamenet céljait, 0:03:11](#anchor-image-wtip-min00311)
- [3. Napirend, 0:04:17](#anchor-image-wtip-min00417)
- [4. Több-bérlős web app alkalmazásban 0:05:05](#anchor-image-wtip-min00505)
- [5. A művelet 0 alkalmazás webes űrlap: 05:55](#anchor-image-wtip-min00555)
- [6. Egy bérlői költségek (méretezési, elkülönítés, helyreállítási), 0:09:31](#anchor-image-wtip-min00931)
- [7. Adatbázis-modellek a több-bérlős: előnyei és hátrányai, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hibrid modell átmenetet alkot a fő Célkiszolgáló/ST, 0 előnyei: 13:01.](#anchor-image-wtip-min01301)
- [9. Single-bérlő és a több-bérlős: előnyei és hátrányai, 0:16:44](#anchor-image-wtip-min01644)
- [10. Készletek költséghatékony, előre nem látható a munkaterhelések, 0:19:36](#anchor-image-wtip-min01936)
- [11. Adatbázis-/-bérlő és a hibrid ST/fő Célkiszolgáló, 0 bemutató: 20:08](#anchor-image-wtip-min02008)
- [12. Az élő app űrlapot Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB és nem a láthatáron, 0 a DBA: 28:54](#anchor-image-wtip-min02854)
- [14. MYOB rugalmas készlet példa, 0:29:40](#anchor-image-wtip-min02940)
- [15. Tanulási MYOB és egyéb ISV-k, 0:31:36](#anchor-image-wtip-min03136)
- [16. Minták összeállítása a E2E Szolgáltatottszoftver-forgatókönyvek, 0:43:15](#anchor-image-wtip-min04315)
- [17. Kanonikus hibrid több-bérlős Szolgáltatottszoftver-alkalmazáshoz, 0:47:33](#anchor-image-wtip-min04733)
- [18. A Wingtip SaaS mintaalkalmazást, 0:48:10](#anchor-image-wtip-min04810)
- [19. Forgatókönyvek és mintázatokat írja le az oktatóanyagok 0:49:10](#anchor-image-wtip-min04910)
- [20. Oktatóanyagok és a Github-tárházban, 0 bemutató: 50:18](#anchor-image-wtip-min05018)
- [21. Github repo Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. A minták, a 0 felfedezése: 56:20](#anchor-image-wtip-min05620)
- [23. Bérlők és bevezetési, 0-kiépítés: 57:44](#anchor-image-wtip-min05744)
- [24. Bérlők és az alkalmazás kapcsolat, 0-kiépítés: 58:58](#anchor-image-wtip-min05858)
- [25. Bemutató felügyeleti parancsfájlok egy egybérlős, 0-kiépítés: 59:43](#anchor-image-wtip-min05943)
- [26. Kiépítés és a katalógus, 1:00:02 PowerShell](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Előre nem látható a bérlői terhelések, 1:04:36 kezelése](#anchor-image-wtip-min10436)
- [29. A rugalmas készlet figyelése, 1:06:39](#anchor-image-wtip-min10639)
- [30. Létrehozás és teljesítményfigyelési feladatokat, 1:09:42 betöltése](#anchor-image-wtip-min10942)
- [31. Séma felügyelet, 1:10:33](#anchor-image-wtip-min11033)
- [32. Az elosztott lekérdezés bérlői az adatbázisok, 1:12:21 közötti](#anchor-image-wtip-min11221)
- [33. A jegy létrehozás, 1:12:32 bemutató](#anchor-image-wtip-min11232)
- [34. SSMS ad hoc elemzés, 1:12:46](#anchor-image-wtip-min11246)
- [35. Az SQL DW, 1:16:32 bérlői adatok kibontása](#anchor-image-wtip-min11632)
- [36. Napi pénztári terjesztési, 1:16:48 számának függvényében ábrázoló grafikon](#anchor-image-wtip-min11648)
- [37. Burkolja, és hívja meg a műveletet, 1:19:52](#anchor-image-wtip-min11952)
- [38. További információt, 1:20:42 erőforrások](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Megjegyzésekkel ellátott indexelési idő helyeket a videó

Képernyőkép képet kattintva megnyitná a pontos idő helyét a videó.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Kezdő)*  Üdvözlő menüpontban húzással állíthatja be, 0:00:01.

*A MYOB Learning: a kialakítási minták az SaaS-alkalmazásokhoz az Azure SQL Database - BRK3120*

[![Üdvözli a menüpontban húzással állíthatja be][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Cím: Tanulási a MYOB: kialakítási az SaaS-alkalmazásokhoz az Azure SQL Database-minták
- Bill.Gibson@microsoft.com
- Fő Programvezető, az Azure SQL-adatbázis
- Microsoft Ignite munkamenet BRK3120, székesfehérvári, FL USA, 2017. októberi vagy 11


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Munkamenet céljait, 0:01:53
[![Munkamenet-célkitűzések][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatív modellek több-bérlős alkalmazásokhoz, az előnyei és hátrányai.
- SaaS-minták fejlesztési, felügyeleti és erőforrás-költségek csökkentése érdekében.
- Egy mintaalkalmazás + parancsfájlok.
- PaaS szolgáltatások + Szolgáltatottszoftver-minták létrehozása SQL-adatbázis egy jól skálázható, költséghatékony adatplatform több-bérlős Szolgáltatottszoftver.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Több-bérlős web app alkalmazásban 0:05:00
[![SaaS-alkalmazás Wingtip: több-bérlős webalkalmazás][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. A művelet 0 alkalmazás webes űrlap: 05:39
[![Alkalmazás webes űrlap művelet:][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Egy bérlői költségek (méretezési, elkülönítés, helyreállítási), 0:06:58
[![Egy bérlői költség, a méretezés, a elkülönítési, a helyreállítási][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Adatbázis-modellek a több-bérlős: előnyei és hátrányai, 0:09:52
[![Adatbázis-modellek a több-bérlős: előnyei és hátrányai][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hibrid modell átmenetet alkot a fő Célkiszolgáló/ST, 0 előnyei: 12:29
[![Hibrid modell átmenetet alkot a fő Célkiszolgáló/ST előnyei][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Single-bérlő és a több-bérlős: előnyei és hátrányai, 0:13:11
[![Single-bérlő és a több-bérlős: előnyei és hátrányai][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Készletek költséghatékony, előre nem látható a munkaterhelések, 0:17:49
[![Készletek költséghatékony, előre nem látható munkaterhelések.][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Adatbázis-/-bérlő és a hibrid ST/fő Célkiszolgáló, 0 bemutató: 19:59
[![Adatbázis-/-bérlő és a hibrid ST/MT bemutató][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Az élő app űrlapot Dojo, 0:20:10
[![Az élő app űrlap Dojo megjelenítése][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB és nem a láthatáron, 0 a DBA: 25:06
[![MYOB és nem a láthatáron Kiszolgálóhoz][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB rugalmas készlet példa, 0:29:30
[![MYOB rugalmas készlet használat – példa][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Tanulási MYOB és egyéb ISV-k, 0:31:25
[![Learning MYOB és egyéb ISV-k][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Minták összeállítása a E2E Szolgáltatottszoftver-forgatókönyvek, 0:31:42
[![Minták összeállítása a E2E Szolgáltatottszoftver-forgatókönyvek][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Kanonikus hibrid több-bérlős Szolgáltatottszoftver-alkalmazáshoz, 0:46:04
[![Canonical hibrid több-bérlős SaaS-alkalmazás][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. A Wingtip SaaS mintaalkalmazást, 0:48:01.
[![A Wingtip SaaS mintaalkalmazás][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Forgatókönyvek és mintázatokat írja le az oktatóanyagok 0:49:00
[![Forgatókönyvek és mintázatokat írja le az oktatóanyagok][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Oktatóanyagok és a Github-tárházban, 0 bemutató: 50:12
[![Bemutató oktatóanyagok és a Github-tárház][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Github repo Microsoft/WingtipSaaS, 0:50:32
[![Github repo Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. A minták, a 0 felfedezése: 56:15
[![A minták felfedezése][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Bérlők és bevezetési, 0-kiépítés: 56:19
[![Bérlők és bevezetési kiépítése][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Bérlők és az alkalmazás kapcsolat, 0-kiépítés: 57:52
[![Bérlők és az alkalmazás kapcsolat kiépítése][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Bemutató felügyeleti parancsfájlok egy egybérlős, 0-kiépítés: 59:36
[![Kiépítés egyetlen bérlői felügyeleti parancsfájlok bemutatója][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. Kiépítés és a katalógus, 0 PowerShell: 59:56
[![Kiépítés és a katalógus PowerShell][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * a TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Előre nem látható a bérlői terhelések, 1:03:34 kezelése
[![Előre nem látható a bérlői terhelések kezelése][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. A rugalmas készlet figyelése, 1:06:32
[![A rugalmas készlet figyelése][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Létrehozás és a teljesítmény figyelése, 1:09:37 betöltése
[![Betöltési és a teljesítmény figyelése][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Séma felügyelet, 1:09:40
[![Séma felügyelet][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Az elosztott lekérdezés bérlői az adatbázisok, 1:11:18 közötti
[![Az elosztott lekérdezés bérlői az adatbázisok közötti][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. A jegy létrehozás, 1:12:28 bemutató
[![A jegy létrehozásának bemutató][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS ad hoc elemzés, 1:12:35
[![SSMS ad hoc elemzés][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Bérlői adatok kinyerése érdekében az SQL DW, 1:15:46
[![Az SQL DW bérlői adatok kibontása][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Napi pénztári terjesztési, 1:16:38 számának függvényében ábrázoló grafikon
[![Napi pénztári terjesztési számának függvényében ábrázoló grafikon][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Burkolja, és hívja meg a műveletet, 1:17:43
[![Burkolja és művelet hívása][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. További információt, 1:20:35 erőforrások
[![További információt az erőforrásokhoz][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogbejegyzés, 2017. május 22.][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Ismertető:* [több-bérlős Szolgáltatottszoftver-adatbázis bérleti minták][saas-concept-design-patterns-563e]

- *Oktatóanyag:* [a Wingtip jegyek SaaS-alkalmazáshoz][saas-how-welcome-wingtip-app-679t]

- Github-adattárak változatban is elkészíti a a Wingtip jegyek SaaS-bérlőhöz alkalmazáshoz:
    - [A - önálló alkalmazásmodell Github-tárház][github-wingtip-standaloneapp].
    - [Github-tárház - adatbázis egy bérlő modell számára][github-wingtip-dbpertenant].
    - [Github-tárház - adatbázis több-Bérlős modell számára][github-wingtip-multitenantdb].





## <a name="next-steps"></a>További lépések

- [Első oktatóanyagot cikk][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.pngÜdvözli a menüpontban húzással állíthatja be ""

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Munkamenet-célkitűzések."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agenda."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.pngSaaS-alkalmazás Wingtip: több-bérlős webalkalmazás ""

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.pngAlkalmazás webes űrlap művelet: ""

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.pngEgy bérlői költség, a méretezés, a elkülönítési, a helyreállítási ""

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.pngAdatbázis-modellek a több-bérlős: előnyei és hátrányai ""

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.pngHibrid modell átmenetet alkot a fő Célkiszolgáló/ST előnyei ""

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.pngSingle-bérlő és a több-bérlős: előnyei és hátrányai ""

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.pngKészletek költséghatékony, előre nem látható munkaterhelések. ""

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.pngAdatbázis-/-bérlő és a hibrid ST/MT bemutató ""

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.pngAz élő app űrlap Dojo megjelenítése ""

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.pngMYOB és nem a láthatáron Kiszolgálóhoz ""

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.pngMYOB rugalmas készlet használat – példa ""

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.pngLearning MYOB és egyéb ISV-k ""

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.pngMinták összeállítása a E2E Szolgáltatottszoftver-forgatókönyvek ""

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.pngCanonical hibrid több-bérlős SaaS-alkalmazás ""

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.pngA Wingtip SaaS mintaalkalmazás ""

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.pngForgatókönyvek és mintázatokat írja le az oktatóanyagok ""

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Oktatóanyagok és a Github-tárház bemutató"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Github repo Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.pngA minták felfedezése ""

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.pngBérlők és bevezetési kiépítése ""

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.pngBérlők és az alkalmazás kapcsolat kiépítése ""

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.pngKiépítés egyetlen bérlői felügyeleti parancsfájlok bemutatója ""

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.pngKiépítés és a katalógus PowerShell ""

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.pngT-SQL SELECT * a TenantsExtended ""

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.pngElőre nem látható a bérlői terhelések kezelése ""

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.pngA rugalmas készlet figyelése ""

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.pngBetöltési és a teljesítmény figyelése ""

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.pngSéma felügyelet ""

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.pngAz elosztott lekérdezés bérlői az adatbázisok közötti ""

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.pngA jegy létrehozásának bemutató ""

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.pngSSMS ad hoc elemzés ""

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.pngAz SQL DW bérlői adatok kibontása ""

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.pngNapi pénztári terjesztési számának függvényében ábrázoló grafikon ""

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.pngBurkolja és művelet hívása ""

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.pngTovábbi információt az erőforrásokhoz ""




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

