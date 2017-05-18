---
title: "SaaS a dobozban (Azure SQL Database-t használó minta SaaS-alkalmazás) | Microsoft Docs"
description: "SaaS-alkalmazások készítése az SQL Database használatával"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>A Wingtip Tickets Platform (WTP) minta SaaS-alkalmazás bemutatása

A Wingtip Tickets Platform (WTP) SaaS-alkalmazás egy több-bérlős mintaalkalmazás, amely bemutatja az SQL Database egyedülálló előnyeit. Az alkalmazás bérlőnkénti adatbázis SaaS-alkalmazásmintát használ több bérlő kiszolgálásához. A WTP alkalmazás az Azure SQL Database SaaS-forgatókönyveket lehetővé tevő funkcióinak, például az SaaS-tervezési és -kezelési mintáknak a bemutatására lett tervezve. A gyors telepítés érdekében [a WTP alkalmazás üzembe helyezéséhez kevesebb, mint öt percre van szükség](sql-database-saas-tutorial.md)!

A WTP alkalmazás telepítése után ismerje meg a kezdeti üzembe helyezésre épülő [oktatóanyagok gyűjteményét](#sql-database-saas-tutorials). Minden oktatóanyagban az SaaS-alkalmazásokban implementált szokásos feladatok vannak a középpontban. A feladatok megvalósítása az SQL Database beépített funkcióit kihasználó SaaS-mintákat követve történik. A leírt minták magukban foglalják új bérlők üzembe helyezését, a bérlői adatbázisok visszaállítását, elosztott lekérdezések futtatását az összes bérlőn és sémaváltozások bevezetését az összes bérlői adatbázison. Minden oktatóprogram tartalmaz újrafelhasználható szkripteket részletes leírásokkal, amelyek jelentősen egyszerűsítik ugyanazoknak a SaaS-felügyeleti mintáknak a megértését és megvalósítását az alkalmazásaiban.

Bár a WTP alkalmazás meglehetősen befejezett és kötelező mintaalkalmazásként, fontos a mag SaaS-mintákra koncentrálni, mert azok az adatréteggel kapcsolatosak. Más szavakkal, koncentráljon az adatrétegre, és ne elemezze túl magát az alkalmazást. A mag SaaS-minták végrehajtásának megértése kulcsfontosságú ezeknek a mintáknak az alkalmazásaiban való megvalósítása szempontjából, ami során meg kell fontolnia a sajátos céges követelmények miatt szükséges minden módosítást.



## <a name="application-architecture"></a>Alkalmazásarchitektúra

A WTP alkalmazás a bérlőnkénti adatbázis modellt használja, és Rugalmas SQL-készletek alkalmaz a hatékonyság növelésére.
Bérlői katalógus használata a felügyelet és az összekapcsolhatóság kiépítésére.
Integrált alkalmazás-, készlet- és adatbázis-figyelés és riasztás (OMS).
Több-bérlős séma- és referenciaadat-kezelés (rugalmas adatbázis-feladatok).
Több-bérlős lekérdezés, operatív elemzés (rugalmas lekérdezés).
Földrajzilag elosztott adatok használata a megnövelt hatókör érdekében.
Skálázható Business continuity Single-tenant recovery (PITR) DR (geo-visszaállítás, georeplikáció, auto-DR) bérlő önkiszolgáló kezelése (felügyeleti API-kkal) PITR a saját maga által okozott hurokból való helyreállításhoz.

A mag Wingtip alkalmazás három minta bérlővel rendelkező készletet használ és egy katalógus-adatbázist.

![WTP-architektúra](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>SQL Database WTP SaaS-oktatóanyagok

A következő oktatóanyagok a [Wingtip Tickets Platform SaaS alkalmazásmintájának](sql-database-saas-tutorial.md) kezdeti üzemelő példányára épülnek:

| Terület | Leírás | Szkript helye |
|:--|:--|:--|
|[Bérlők kiépítése és katalogizálása oktatóanyag](sql-database-saas-tutorial-provision-and-catalog.md)| Új bérlők kiépítése és regisztrálása a katalógusban | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Teljesítmény figyelése és kezelése oktatóanyag](sql-database-saas-tutorial-performance-monitoring.md)| Adatbázis és a készlet teljesítményének figyelése és kezelése | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Egyetlen bérlő visszaállítása – oktatóanyag](sql-database-saas-tutorial-restore-single-tenant.md)| Bérlői adatbázisok visszaállítása | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Bérlői sémák kezelése – oktatóanyag](sql-database-saas-tutorial-schema-management.md)| Lekérdezések végrehajtása az összes bérlőn  | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Alkalmi elemzések futtatása – oktatóanyag](sql-database-saas-tutorial-adhoc-analytics.md) | Alkalmi elemzések adatbázisának létrehozása és lekérdezések futtatása az összes bérlőn  | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Kezelés a Log Analytics szolgáltatásban (OMS) oktatóanyag](sql-database-saas-tutorial-log-analytics.md) | A Log Analytics konfigurálása és megismerése | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Bérlői elemzések futtatása – oktatóanyag](sql-database-saas-tutorial-tenant-analytics.md) | Bérlői elemzések lekérdezéseinek beállítása és futtatása | [A githubon található szkriptek](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub-tárban érhetők el. A parancsfájlok a [Tanulási modulok mappában](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) találhatók. Töltse le a **Tanulási modulok** mappát a helyi számítógépére, a mappaszerkezetének megőrzésével.

## <a name="working-with-the-wtp-powershell-scripts"></a>A WTP PowerShell-szkriptek használata

A WTP alkalmazással való munkavégzés előnyei a megadott szkriptek elemzéséből és a különböző SaaS-minták megvalósítási módjának vizsgálatából származnak.

A megadott szkriptek és modulok megtekintéséhez és a jobb megértésük érdekében a rajtuk való végiglépkedéshez használja a [Windows PowerShell ISE-t](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Mivel a *Demo-* előtaggal rendelkező szkriptek többsége a végrehajtás előtt módosítható változókat tartalmaz, a PowerShell ISE használata leegyszerűsíti az ezekkel a szkriptekkel végzett munkát.

A WTP alkalmazás minden egyes üzemelő példányánál található egy **UserConfig.psm1** fájl, amely két paramétert tartalmaz az üzembe helyezéskor meghatározott erőforráscsoport és felhasználónév értékeinek beállításához. Üzembe helyezés után szerkessze a **UserConfig.psm1** modult, és állítsa be a _ResourceGroupName_ és a _Name_ paramétereket. Ezeket az értékeket használják más szkriptek, hogy sikeresen futtatni lehessen őket, ezért ajánlott beállítani ezeket az üzembe helyezés befejeződésekor!



### <a name="execute-scripts-by-pressing-f5"></a>A szkripteket az F5 lenyomásával hajtsa végre

Számos szkript a *$PSScriptRoot* paramétert használja a mappák közötti navigáció engedélyezéséhez, és ennek a változónak az ellenőrzése csak akkor történik meg, ha a szkriptet az **F5** lenyomásával hajtja végre.  A kijelölés és a kijelöltek futtatása (**F8**) hibákat eredményezhet, ezért a WTP-szkriptek futtatásakor nyomja le az **F5** billentyűt.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>A megvalósítás vizsgálatához lépjen végig a szkripteken

A szkriptek vizsgálatának tényleges értéke abban áll, hogy végig kell lépkedni rajtuk, hogy láthassa, milyen műveleteket végeznek. Tekintse meg az első szintű _Demo-_ szkripteket, amelyek könnyen olvasható, magas szintű munkafolyamatot biztosítanak, amelyek bemutatják a feladat elvégzéséhez szükséges lépéseket. Elemezze mélyebben az egyes hívásokat, hogy láthassa a különböző SaaS-minták megvalósítási részleteit.

Tippek a [PowerShell-szkriptek hibáinak kereséséhez](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) és a velük való munkavégzéshez:

* Nyissa meg és konfigurálja a demo- előtagú szkripteket a PowerShell ISE-ben.
* Hajtsa végre vagy folytassa az **F5** billentyű lenyomásával. Az **F8** használata nem javasolt, mert a *$PSScriptRoot* kiértékelése nem történik meg a szkript kijelöléseinek futtatásakor.
* Töréspontok elhelyezéséhez kattintson egy sorra, vagy válasszon ki egyet, és nyomja le az **F9** billentyűt.
* Függvény- vagy szkripthívás átlépéséhez használja az **F10** billentyűt.
* Függvény- vagy szkripthívásba az **F11** billentyűvel léphet.
* A jelenlegi függvény- vagy szkripthívásból a **Shift + F11** billentyűkkel léphet ki.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Adatbázisséma vizsgálatát és SQL-lekérdezések végrehajtása SSMS használatával

Használja az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) alkalmazást a WTP-kiszolgálókhoz és -adatbázisokhoz való kapcsolódásra és azok tallózására.

A WTP mintaalkalmazásnak kezdetben két SQL Database-kiszolgáló áll a rendelkezésére, amelyekhez kapcsolódhat – a *tenants1* kiszolgáló és a *katalógus* kiszolgáló:


1. Nyissa meg az *SSMS* alkalmazást, és kapcsolódjon a *tenants1-&lt;User&gt;.database.windows.net* kiszolgálóhoz.
2. Kattintson a **Kapcsolódás** > **Adatbázismotor...** :

   ![katalóguskiszolgáló elemre](media/sql-database-wtp-overview/connect.png)

1. Bemutató hitelesítő adatai: bejelentkezés = *fejlesztő*, jelszó =*P@ssword1*

   ![kapcsolat](media\sql-database-wtp-overview\tenants1-connect.png)

1. Ismételje meg a 2. és 3. lépést, és kapcsolódjon a *catalog-&lt;User&gt;.database.windows.net* kiszolgálóhoz.

Sikeres kapcsolódás után meg kell jelennie mindkét kiszolgálónak. A kiépített bérlők számától függően megjelenhet több vagy kevesebb adatbázis:

![Object Explorer](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Következő lépések

[A Wingtip Tickets SaaS-mintaalkalmazás telepítése](sql-database-saas-tutorial.md)
