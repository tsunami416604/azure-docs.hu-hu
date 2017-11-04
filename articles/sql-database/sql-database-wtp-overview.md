---
title: "Az Azure SQL adatbázis több-bérlős app példa - Wingtip SaaS |} Microsoft Docs"
description: "Ismerje meg, amely használja az Azure SQL Database, a Wingtip SaaS példa több-bérlős mintaalkalmazás használatával"
keywords: "sql database-oktatóanyag"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 46c9a3eadc2c23959b4d08649c6c0215d44b493e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Egy SQL-adatbázis több-bérlős SaaS app példa bemutatása

A *Wingtip SaaS* alkalmazás egy több-bérlős mintaalkalmazást, azt mutatja be, az SQL-adatbázis egyedülálló előnyeit. Az alkalmazás bérlőnkénti adatbázis SaaS-alkalmazásmintát használ több bérlő kiszolgálásához. Célja, hogy az alkalmazást, az Azure SQL Database funkcióit, amelyek lehetővé teszik a Szolgáltatottszoftver-forgatókönyvek, köztük a több SaaS tervezési és felügyeleti minták megjelenítve. Gyorsan karban lehessen működik és elérhető, a Wingtip SaaS-alkalmazás telepíti, kevesebb mint öt perc alatt!

Alkalmazás forrás kódot és kezelésre szolgáló parancsfájlok érhetők el a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-tárház. A parancsfájlok futtatásához [töltse le a tanulási modulok mappa](#download-and-unblock-the-wingtip-saas-scripts) a helyi számítógépen.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL adatbázis Wingtip SaaS oktatóprogramok

Miután telepíti az alkalmazást, megismerkedhet a következő oktatóprogramot kínál, amelyek a kezdeti telepítés épül. Ezek az oktatóanyagok közös SQL-adatbázis, az SQL Data Warehouse és más Azure-szolgáltatások beépített funkciók előnyeit Szolgáltatottszoftver-minták felfedezése. Oktatóanyagok PowerShell-parancsfájlok, a részletes leírást, amely jelentősen egyszerűsítheti a megismeréséhez, és az azonos SaaS felügyeleti minták valósít meg az alkalmazások közé tartoznak.


| Oktatóanyag | Leírás |
|:--|:--|
|[Központi telepítése, és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](sql-database-saas-tutorial.md)| **KEZDJE ITT!** Központi telepítése, és vizsgálja meg a Wingtip SaaS-alkalmazás az Azure-előfizetéshez. |
|[Kiépítés és a katalógus bérlők](sql-database-saas-tutorial-provision-and-catalog.md)| Ismerje meg, hogyan az alkalmazás a bérlők katalógus adatbázis használatával csatlakozik, és hogy a katalógus hogyan leképezi a bérlők adataikat. |
|[Megfigyelés és kezelés teljesítmény](sql-database-saas-tutorial-performance-monitoring.md)| Útmutató: az SQL-adatbázis felügyeleti funkcióinak használatát, és riasztások beállítását, ha a teljesítmény-küszöbérték túllépése. |
|[A figyelőt Naplóelemzés (OMS)](sql-database-saas-tutorial-log-analytics.md) | Használata [Naplóelemzési](../log-analytics/log-analytics-overview.md) nagy mennyiségű erőforrást, figyelheti több készletet között. |
|[Egy egybérlős visszaállítása](sql-database-saas-tutorial-restore-single-tenant.md)| Megtudhatja, hogyan bérlői adatbázis visszaállítása korábbi pontra időben. Párhuzamos adatbázisba, online, így a meglévő bérlő adatbázis lépéseket is szerepelnek. |
|[Bérlői séma kezelése](sql-database-saas-tutorial-schema-management.md)| Tudnivalók a séma frissítése, és frissíti a referenciaadatok, minden Wingtip Szolgáltatottszoftver-bérlők között. |
|[Ad hoc analytics futtatása](sql-database-saas-tutorial-adhoc-analytics.md) | Hozzon létre egy ad hoc elemzés és valós idejű elosztott lekérdezések futtatása egyetlen bérlő számára.  |
|[Futtassa a bérlő elemzés](sql-database-saas-tutorial-tenant-analytics.md) | Bontsa ki a bérlői adatforgalom az egy analytics adatbázis vagy a data warehouse-bA offline elemzési lekérdezések futtatását. |



## <a name="application-architecture"></a>Alkalmazásarchitektúra

A Wingtip SaaS-alkalmazás az adatbázis-/-bérlős modell, és használja az SQL rugalmas készletek hatékonyságának maximalizálása. A létrehozásához és a leképezés bérlők számára az adatokat a katalógus-adatbázist használja. A core Wingtip SaaS-alkalmazás, egy címkészlet, amely három minta bérlők, valamint a katalógus-adatbázis használja. A bővítmények megnyilvánulhat oktatóanyagok a kezdeti telepítés Wingtip SaaS számos befejezése elemzési adatbázis bevezetésével adatbázisok közötti séma felügyeleti stb.


![A Wingtip Szolgáltatottszoftver-architektúra](media/sql-database-wtp-overview/app-architecture.png)


Az oktatóanyagok keresztül haladó, és az alkalmazás dolgozik, miközben fontos a Szolgáltatottszoftver-minták összpontosítson, mivel az adatréteg kapcsolódnak. Más szavakkal, koncentráljon az adatrétegre, és ne elemezze túl magát az alkalmazást. Ezek SaaS végrehajtásának megértése mintázatok, kulcs valósít meg ezeket a mintákat a alkalmazások, figyelembe véve a szükséges módosításokat a saját speciális üzleti igényeinek.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Töltse le és a Wingtip Szolgáltatottszoftver-parancsfájlok feloldása

Ha egy zip-fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájl, ***tiltásának feloldása a .zip fájl kibontása előtt az alábbi lépésekkel***. Ez biztosítja, hogy a parancsfájlok futtatásának engedélyezése.

1. Keresse meg a [a Wingtip SaaS github-tárház](https://github.com/Microsoft/WingtipSaaS).
1. Kattintson a **Klónozás vagy letöltési**.
1. Kattintson a **töltse le a ZIP-** , és mentse a fájlt.
1. Kattintson a jobb gombbal a **WingtipSaaS-master.zip** fájlt, és válassza ki **tulajdonságok**.
1. Az a **általános** lapon jelölje be **Unblock**.
1. Kattintson az **OK** gombra.
1. Bontsa ki a fájlokat.

Parancsfájlok a találhatók a *... \\WingtipSaaS-főkiszolgáló\\tanulási modulok* mappát.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>A Wingtip SaaS PowerShell-parancsfájlok használata

A legtöbbet hozhatja ki a minta alaposabban tanulmányozhatja a megadott parancsfájlok kell. Töréspontokat használja, és a parancsfájlok vizsgálata folyamatban van a különböző Szolgáltatottszoftver-minták megvalósított hogyan részleteit lépéseit. Könnyen végighaladhat a megadott parancsfájlok és a modulok legjobb megértéséhez, ajánlott használata a [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Szerkessze a **UserConfig.psm1** fájl központi telepítése során beállított erőforrás csoportot és felhasználót értékét:

1. Nyissa meg a *PowerShell ISE* és betöltése... \\Tanulási modulok\\*UserConfig.psm1* 
1. Frissítés *ResourceGroupName* és *neve* a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
1. A módosítások mentése!

Ezek az értékek beállítás itt egyszerűen révén nem minden parancsfájlban a központi telepítési tartományspecifikus értékeinek frissítéséhez.

### <a name="execute-scripts-by-pressing-f5"></a>A szkripteket az F5 lenyomásával hajtsa végre

Több parancsfájlok használata *$PSScriptRoot* navigáljon a mappákat, és *$PSScriptRoot* csak értékeli a parancsfájlok billentyűkombináció lenyomásával végrehajtásakor **F5**.  Kiemelése és futtató egy kijelölést (**F8**) is hibákat eredményez, így nyomja le az **F5** Ha a parancsfájlok futtatásához.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>A megvalósítás vizsgálatához lépjen végig a szkripteken

A legjobb módszer megértéséhez a parancsfájlok által számukra látni enged ezek lépésenkénti végrehajtás van. Tekintse meg a megadott **bemutató -** parancsfájlok, amelyek jelenleg egy könnyen kövesse az általános munkafolyamat. A **bemutató -** parancsfájlok szemlélteti a lépéseket minden feladatnak, így állítson be töréspontokat és elemezze szükséges mélyebb be az egyes hívások tekintse meg a különböző Szolgáltatottszoftver-minták megvalósítási részleteit.

Tippek az fel, és lépjen az PowerShell-parancsfájlokkal:

* Nyissa meg **bemutató -** a PowerShell ISE parancsfájlok.
* Hajtható végre, vagy folytassa a **F5** (használatával **F8** nem ajánlott, mert *$PSScriptRoot* nem kerül kiértékelésre beállításokat egy parancsfájl futtatásakor).
* Töréspontok elhelyezéséhez kattintson egy sorra, vagy válasszon ki egyet, és nyomja le az **F9** billentyűt.
* Függvény- vagy szkripthívás átlépéséhez használja az **F10** billentyűt.
* Függvény- vagy szkripthívásba az **F11** billentyűvel léphet.
* A jelenlegi függvény- vagy szkripthívásból a **Shift + F11** billentyűkkel léphet ki.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Adatbázisséma vizsgálatát és SQL-lekérdezések végrehajtása SSMS használatával

Használjon [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) csatlakozzanak, és keresse meg az alkalmazás-kiszolgálókat és adatbázisokat.

A központi telepítés először van csatlakozni - két SQL adatbázis-kiszolgálók a *tenants1 -&lt;felhasználói&gt;*  kiszolgáló, és a *katalógus -&lt;felhasználói&gt;*  kiszolgáló. Ahhoz, hogy a sikeres bemutató kapcsolatot, mindkét kiszolgáló rendelkezik egy [tűzfalszabály](sql-database-firewall-configure.md) így minden IP-címen keresztül.


1. Nyissa meg az *SSMS* alkalmazást, és kapcsolódjon a *tenants1-&lt;User&gt;.database.windows.net* kiszolgálóhoz.
1. Kattintson a **Kapcsolódás** > **Adatbázismotor...** :

   ![katalóguskiszolgáló elemre](media/sql-database-wtp-overview/connect.png)

1. Bemutató hitelesítő adatai: bejelentkezés = *fejlesztő*, jelszó =*P@ssword1*

   ![kapcsolat](media\sql-database-wtp-overview\tenants1-connect.png)

1. Ismételje meg a 2. és 3. lépést, és kapcsolódjon a *catalog-&lt;User&gt;.database.windows.net* kiszolgálóhoz.

Sikeres kapcsolódás után meg kell jelennie mindkét kiszolgálónak. Az adatbázisok listája, a korábban kiépített bérlők függően eltérőek lehetnek:

![Object Explorer](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Következő lépések

[A Wingtip SaaS-alkalmazás központi telepítése](sql-database-saas-tutorial.md)
