---
title: "Sok Azure SQL-adatbázisoknál a több-bérlős SaaS-alkalmazás teljesítményének figyelése |} Microsoft Docs"
description: "Megfigyelés és kezelés Azure SQL-adatbázisok és tárolókészletekben: a több-bérlős SaaS-alkalmazás teljesítménye"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: sstein
ms.openlocfilehash: 450a5fc578948db044d9e0bb9db09508b2512aca
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Megfigyelés és kezelés Azure SQL-adatbázisok és tárolókészletekben: a több-bérlős SaaS-alkalmazás teljesítménye

Ebben az oktatóanyagban használt SaaS-alkalmazásokhoz több alapvető teljesítményt felügyeleti lehetőségeket írja. A betöltési generátor segítségével szimulálása tevékenység közötti összes bérlői, beépített figyelési és riasztási szolgáltatásokat az SQL Database és a rugalmas készletek egy.

A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás egyetlen-bérlő adatok modellt használ, ahol az egyes helyszínekkel (bérlői) rendelkezik a saját adatbázis. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. Ezen tipikus adatbázis-felhasználási minta előnyei úgy használhatók ki, hogy a bérlői adatbázisokat rugalmas adatbáziskészletekben helyezik üzembe. A rugalmas készletek optimalizálják a megoldások költségeit azáltal, hogy számos adatbázis között osztják meg az erőforrásokat. Ennél a típusú mintánál fontos az adatbázis és a készleterőforrások felhasználásának figyelése annak biztosítása érdekében, hogy a terhelések egyenletesen oszoljanak meg a készletek közt. Emellett azt is biztosítani kell, hogy az egyes adatbázisok elengedő mennyiségű erőforrással rendelkezzenek, és hogy a készletek ne érjék el a maximális [eDTU](sql-database-what-is-a-dtu.md)-korlátot. Ez az oktatóanyag különböző módszereket ismertet az adatbázisok és készletek figyelésére és kezelésére, valamint a számítási feladatok változásaira adott korrekciós műveletek elvégzésére.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Bevezetés a Szolgáltatottszoftver-teljesítmény felügyeleti minták

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. Több bérlő üzemeltetésekor a rugalmas adatbáziskészletekkel költséghatékonyan oldható meg a kiszámíthatatlan számítási feladatokkal szembesülő adatbáziscsoportok erőforrásainak biztosítása és kezelése. Bizonyos számításifeladat-mintáknál már akár két S3-adatbázist is előnyös lehet készletben kezelni.

![Alkalmazásdiagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Annak érdekében, hogy a teljesítmény elfogadható tartományait belül maradnak címkészletekkel és IP-készletek, az adatbázisokat kell figyelni. A tárolókészlet konfigurációját, és annak biztosítására, hogy a készlet edtu-k a teljes munkaterhelés megfelelő összes adatbázis összesített munkaterhelés igényeihez hangolását. Állítsa be az adatbázisonkénti minimális és maximális eDTU-értékeket az alkalmazás specifikus igényei szerint.

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* Ne kelljen manuálisan figyelemmel kísérni a teljesítményét, hogy a leghatékonyabb **riasztások beállítását, amelyek indul el, ha az adatbázisok és a készletek kóbor kívül normál tartományok**.
* A készletek összesített teljesítményszintjének rövidtávú ingadozásaira válaszul **a készletek eDTU-szintje vertikálisan fel- vagy leskálázható**. Ha az ingadozás rendszeres vagy kiszámítható, akkor **a készlet beállítható úgy, hogy a skálázás automatikusan ütemezve legyen**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszabb távú ingadozásokra vagy az adatbázisok számának változására válaszul **az egyes adatbázisok áthelyezhetők másik készletekbe**.
* A rövid távú növekedése válaszolni *egyedi* adatbázis-terhelésnek **egyes adatbázisok készlet veszi, és az egyes teljesítményszintet hozzárendelt**. A terhelés csökkenésével az adatbázis visszahelyezhető a készletbe. Ha ez ismert előre, adatbázisok áthelyezhető pre-emptively ellenőrizze az adatbázis mindig van a szükséges erőforrásokat, és a készlet más adatbázisok gyakorolt hatás elkerülése érdekében. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. Az SQL Database esetében a figyelés és riasztás rendelkezésre áll az adatbázisokhoz és a készletekhez. A beépített figyelés és riasztás érték erőforrás-specifikus, ezért célszerű a kis mennyiségű erőforrást használ, de nem nem nagyon hasznos, ha sok erőforrást.

Nagy mennyiségű forgatókönyvek, ahol sok erőforrással rendelkező dolgozunk, [Naplóelemzés (OMS)](saas-dbpertenant-log-analytics.md) is használható. Ez a külön Azure szolgáltatás, amely analytics kibocsátott diagnosztikai naplók és a naplóelemzési munkaterület összegyűjtött telemetrikus keresztül. A Naplóelemzési sok szolgáltatásokból telemetriai adatokat gyűjthet, és lekérdezése, és állítson be riasztásokat használható.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-source-code-and-scripts"></a>Az alkalmazás forráskódjához Wingtip jegyek SaaS adatbázis / bérlői és parancsfájlok

A Wingtip jegyek SaaS adatbázis / bérlői parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github-tárház. [Töltse le a Wingtip jegyek SaaS adatbázis / bérlői parancsfájlok lépéseket](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts).

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Noha a készletek használata már két S3-adatbázis esetén is költséghatékony lehet, minél több adatbázis található egy készletben, a költséghatékonyság mértéke annál jobban nő. Annak érdekében, hogy a teljesítményfigyelés és -kezelés nagy léptékben való használatát kellőképpen szemléltetni lehessen, jelen oktatóanyaghoz legalább 20 üzembe helyezett adatbázis szükséges.

Ha már megtörtént egy kötegelt bérlő előzetes oktatóanyag, ugorjon a [szimulálás használati összes bérlői adatbázisok](#simulate-usage-on-all-tenant-databases) szakasz.

1. Az a **PowerShell ISE**, nyissa meg... \\Tanulási modulok\\Teljesítményfigyelő és felügyeleti\\*bemutató-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Válassza a **$DemoScenario** = **1**, **Bérlők kötegelt kiépítése** lehetőséget.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A szkript kevesebb mint öt perc alatt 17 bérlőt helyez üzembe.

A *New-TenantBatch* parancsfájl használ egy beágyazott vagy csatolt [erőforrás-kezelő](../azure-resource-manager/index.md) sablonokat, hozzon létre egy kötegelt bérlő, amely alapértelmezés szerint másolja az adatbázist **basetenantdb** adatbázisok létrehozására az új tenanthoz katalóguskiszolgálóhoz, majd regisztrálja ezeket a katalógusban, és végül a bérlő neve és helyszínére típusú inicializálja. Ez a módszer az alkalmazás látja el egy új bérlőt összhangban. Milyen módosítások történtek *basetenantdb* bármely új bérlők számára kiosztott azt követően is vonatkozik. Tekintse meg a [séma felügyeleti oktatóanyag](saas-tenancy-schema-management.md) , hogyan lehet módosítani séma *meglévő* adatbázisok bérlői (beleértve a *basetenantdb* adatbázis).

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *bemutató-PerformanceMonitoringAndManagement.ps1* parancsfájl, feltéve, hogy a munkaterhelés futtatott összes bérlői adatbázis szimulálja. A terhelés jön létre a rendelkezésre álló terheléselosztási forgatókönyveket egyikének használatával:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitású terhelés létrehozása (kb. 40 DTU) |
| 3 | Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel|
| 4 | Terhelés létrehozása adatbázisonkénti magasabb DTU-löketekkel (kb. 80 DTU)|
| 5 | Normál terhelés létrehozása, valamint magasabb terhelés létrehozása egyetlen bérlő számára (kb. 95 DTU)|
| 6 | Kiegyensúlyozatlan terhelés létrehozása több készlet számára|

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelések szintje (eDTU-ban mérve), időtartama és időköze minden adatbázis esetén más és más, ezzel szimulálva a kiszámíthatatlan bérlői aktivitást.

1. Az a **PowerShell ISE**, nyissa meg... \\Tanulási modulok\\Teljesítményfigyelő és felügyeleti\\*bemutató-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Állítsa be **$DemoScenario** = **2**, *Generate normál intenzitásának terhelés*.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

Wingtip jegyek SaaS adatbázis egy bérlő az SaaS-alkalmazás, és a Szolgáltatottszoftver-alkalmazás valós terhelése jellemzően szórványos és előre nem látható. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Több percig merülnek fel, a betöltés minta van szükség, ezért futtassa a terhelés generátor 3-5 percet, mielőtt megpróbálja a terhelés, az alábbi szakaszok a figyelheti.

> [!IMPORTANT]
> A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben. Hagyja nyitva a *Demo-PerformanceMonitoringAndManagement.ps1* lapot! Ha bezárja a lapot vagy felfüggeszti a gép működését, a terhelésgenerátor leáll. A betöltési generátor marad a *feladat meghívása* állapotba, ahol hoz létre a kódgenerátor végrehajtásának megkezdése után törlődnek új tenantokat terhelése. Használjon *Ctrl-C* leállítására, hívja az új feladatokat, és lépjen ki a parancsfájl. A betöltési generátor továbbra is fut, de csak a meglévő bérlők számára.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>A figyelő erőforrás-használata az Azure portál használatával

A figyelheti az erőforrás-felhasználás által alkalmazott a terhelés, nyissa meg a portálra, ahol a bérlői adatbázisokat tartalmazó készlet:

1. Nyissa meg a [Azure-portálon](https://portal.azure.com) , és keresse meg a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló.
1. Görgessen lefelé, keresse meg a rugalmas készleteket, és kattintson a **Pool1** készletre. Ez a készlet tartalmazza az összes eddig létrehozott bérlői adatbázist.

Figyelje meg a **rugalmas készlet figyelése** és **rugalmas adatbázis-figyelési** diagramokat.

A készlet erőforrás-használat a összesített adatbázis-felhasználást a készletben lévő összes adatbázisok. Az adatbázis-diagram bemutatja az öt hottest adatbázisok:

![Adatbázis-diagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Mivel a készlet túl a legjobb öt további adatbázisok, a készlet kihasználtságát, amelyek nem kerülnek a felső öt adatbázisok diagram tevékenység műveleteit jeleníti meg. További információért kattintson **adatbázis erőforrás-használat**:

![adatbázis erőforrás-használat](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Teljesítményriasztások beállítása a készletben

A készlet, amely elindítja a riasztást állíthat be \>75 %-os kihasználtsága az alábbiak szerint:

1. Nyissa meg *Pool1* (a a *tenants1-dpt -\<felhasználói\>*  kiszolgáló) a a [Azure-portálon](https://portal.azure.com).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrika = eDTU százalékos értéke**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**
   * **Az elmúlt 30 perc idő =**
1. E-mail cím hozzáadása a *további rendszergazda email(s)* mezőbe, majd kattintson a **OK**.

   ![riasztás beállítása](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Foglalt készlet vertikális felskálázása

Ha egy készlet összesített terhelési szintje addig növekszik, hogy teljesen lefoglalja a készletet és 100%-os eDTU-használatot ér el, az hatással van az adatbázisok egyéni teljesítményére, és lelassíthatja a lekérdezések válaszidejét a készletben található összes adatbázisban.

**Rövid távú**, a készlet további erőforrások vertikális felskálázásával, vagy távolítsa el adatbázisokat a készletből (helyezni őket más tárolókészletekben, vagy abból a készlet egy önálló szolgáltatási réteg).

**Hosszabb távon**, a lekérdezések optimalizálja, vagy index használati adatbázis teljesítményének javítása érdekében. Az alkalmazás teljesítményingadozásokra való érzékenységétől függően az ajánlott eljárás a készlet vertikális felskálázása még a 100%-os eDTU-használat elérése előtt. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Foglalt készletet a generátor által létrehozott terhelés növelésével szimulálhat. Ez a kapacitásnövelés gyakrabban, valamint a továbbiakban, az adatbázisokat a készlet összesített terhelésének növekedése a követelmények az egyes adatbázisok megváltoztatása nélkül. A készlet vertikális felskálázása könnyedén elvégezhető a portálon vagy a PowerShellben. A gyakorlat során a Portalt használjuk.

1. Állítsa be a *$DemoScenario* = **3**, _Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel_ értéket, hogy megnövelje a készlet összesített terhelési intenzitását az egyes adatbázisok csúcsterhelési követelményeinek megváltoztatása nélkül.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

1. Ugrás a **Pool1** az Azure portálon.

A nagyobb készlet edtu-k a felső diagram figyelése. Az új magasabb terhelés, indítsa néhány percet vesz igénybe, de gyorsan megtekintheti az a készlet elérte maximális használat megkezdése, és a betöltés steadies be az új mintát, mivel gyorsan tömbparaméter-típust használ a készlet.

1. Kattintson a készlet növelheti, **készlet beállítása** tetején a **Pool1** lap.
1. Módosítsa a **készlet edtu értékének** beállítást **100**. A készlet eDTU-értékének módosítása nem módosítja az adatbázisonkénti beállításokat (ami továbbra is adatbázisonként legfeljebb 50 eDTU). Az adatbázis-beállítások jobb oldalán láthatja a **készlet beállítása** lap.
1. Kattintson a **mentése** küldeni a készlethez méretezésére irányuló kérés.

Lépjen vissza a **Pool1** > **áttekintése** a figyelési diagramok megtekintéséhez. Figyeli a hatását, hogy a készlet biztosítson több erőforrást (bár néhány adatbázisok és a véletlenszerű betöltése nem mindig könnyen kétséget látható, amíg nem futtat egy kis ideig). A diagramok megtekintése közben vegye figyelembe, hogy a felső diagramon látható 100% most 100 eDTU-t jelent, míg az alsó diagramon látható 100% továbbra is 50 eDTU-t, mivel az adatbázisonkénti maximum változatlanul 50 eDTU.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Abban a pillanatban, hogy minden adatbázis készen áll a készlet új eDTU-értékével való engedélyezésre, minden aktív kapcsolat megszakad. Az alkalmazás kódját mindig úgy kell megírni, hogy újrapróbálja a megszakított kapcsolatokat, így újra csatlakozni fog az adatbázishoz a felskálázott készletben.

## <a name="load-balance-between-pools"></a>Készletek közötti terheléselosztás

A készlet felskálázása mellett másik lehetőségként létrehozhat egy második készletet és áthelyezhet abba adatbázisokat, hogy kiegyenlítse a két készlet terhelését. Ehhez az új készletet ugyanazon a kiszolgálón kell létrehozni, amelyen az első is megtalálható.

1. Az a [Azure-portálon](https://portal.azure.com), nyissa meg a **tenants1-dpt -&lt;felhasználói&gt;**  kiszolgáló.
1. Kattintson a **+ új készletet** készlet létrehozása az aktuális kiszolgálón.
1. Az a **rugalmas adatbáziskészlet** sablont:

    1. Állítsa be **neve** való *Pool2*.
    1. A tarifacsomagnál hagyja meg a **Standard készlet** beállítást.
    1. Kattintson a **Készlet beállítása** elemre.
    1. Állítsa be **készlet edtu értékének** való *50 eDTU*.
    1. Kattintson a **adatbázisok hozzáadása** adatbázisok listájának megtekintéséhez azon a kiszolgálón, hozzáadható *Pool2*.
    1. Ezek az új készletbe helyezhető át, és kattintson a bármely 10 adatbázisok kijelölése **válasszon**. Ha már futott a terhelés generátor, a szolgáltatás már tudja, hogy a teljesítmény profil az alapértelmezett 50 eDTU méreténél nagyobb készlet igényel, és azt javasolja, hogy egy 100 eDTU-beállítás kezdve.

    ![A javaslat](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Ebben az oktatóanyagban hagyja meg az alapértelmezett 50 edtu-k, és kattintson a **válasszon** újra.
    1. Válassza ki **OK** az új készlet létrehozása és a kijelölt adatbázisok áthelyezése bele.

A készlet létrehozása és az adatbázisok áthelyezése néhány percet vesz igénybe. Adatbázisok áthelyezése maradjanak az online és elérhető-e teljes mértékben csak a legutóbbi néhány percet, mivel ekkor a nyitott kapcsolatokat be van zárva. Mindaddig, amíg még néhány újrapróbálkozási logika, az ügyfelek majd fognak kapcsolódni az adatbázis az új készletben.

Keresse meg a **Pool2** (a a *tenants1-dpt -\<felhasználói\>*  kiszolgáló) megnyitása a címkészletet, és figyelemmel kísérni a teljesítményét. Ha nem látja, várja meg a kiépítés befejezéséhez új készlet.

Most már megtekintheti, hogy az erőforrás-használat *Pool1* csökkent, és hogy *Pool2* hasonlóan most betöltve.

## <a name="manage-performance-of-a-single-database"></a>Egy önálló adatbázis teljesítményének kezelése

Ha a készletben egy önálló adatbázist tartósan magas terhelés ér, akkor a készlet beállításaitól függően előfordulhat, hogy uralni fogja a készlet erőforrásainak használatát, ami hatással lehet a többi adatbázisra. A tevékenység valószínű, hogy egy kis ideig továbbra is, ha az adatbázis átmenetileg áthelyezhető kívül a készletet. Ez lehetővé teszi, hogy az adatbázis számára a további erőforrások van szüksége, és korlátozása, hogy az adatbázisból.

Ez a gyakorlat a Contoso Concert Hall magas terhelésének a hatását szimulálja, amikor megkezdődik a jegyek árusítása egy népszerű koncertre.

1. Az a **PowerShell ISE**, nyissa meg a... \\ *Bemutató-PerformanceMonitoringAndManagement.ps1* parancsfájl.
1. Állítsa be a **$DemoScenario = 5, Normál terhelés létrehozása, valamint magasabb terhelés létrehozása egyetlen bérlő számára (kb. 95 DTU)** értéket.
1. Állítsa be a **$SingleTenantDatabaseName = contosoconcerthall**értéket.
1. Futtassa a szkriptet az **F5** billentyűvel.


1. Az a [Azure-portálon](https://portal.azure.com), az adatbázisok listájának tallózással keresse meg a *tenants1-dpt -\<felhasználói\>*  kiszolgáló. 
1. Kattintson a **contosoconcerthall** adatbázis.
1. Kattintson a készlet, amely **contosoconcerthall** van. Keresse meg a készlet a **rugalmas adatbáziskészlet** szakasz.

1. Vizsgálja meg a **rugalmas készlet figyelése** diagram, és keresse meg a megnövekedett készlet edtu-k. Egy-két perc után jelentkezik a magas terhelés, aminek következtében a készlet eléri a 100%-os kihasználtságot.
2. Vizsgálja meg a **rugalmas adatbázis-figyelési** megjeleníteni, amelyek az elmúlt egy órában az hottest adatbázisokat jeleníti meg. A *contosoconcerthall* adatbázis hamarosan meg kell jelennie az öt hottest adatbázisok egyikét.
3. **Kattintson a rugalmas adatbázis-figyelési** **diagram** és nyílik meg a **adatbázis erőforrás-használat** lap, ahol figyelheti az adatbázisok bármelyikét. Ez lehetővé teszi a megjelenítési elkülönítése a *contosoconcerthall* adatbázis.
4. Adatbázisok listájának megtekintéséhez kattintson **contosoconcerthall**.
5. Kattintson a **(skála dtu-k) Tarifacsomagot** megnyitásához a **konfigurálása a** lap, amelyen beállíthatja az adatbázis egy önálló teljesítményszint szükséges.
6. Kattintson a **Standard** lapra a Standard csomag skálázási beállításainak megnyitásához.
7. Húzza a **DTU csúszkát** kiválasztásához jobbra **100** dtu-inak száma. Megjegyzés: Ez megfelel a szolgáltatási cél **S3**.
8. Kattintson a **alkalmaz** az adatbázis kilépni a készletet, és lehetővé teszi egy *Standard S3* adatbázis.
9. Skálázás végrehajtása után a contosoconcerthall adatbázis gyakorolt hatás és a rugalmas készlet és az adatbázis panelen Pool1 figyelése.

Miután a nagy terhelés a contosoconcerthall adatbázis enyhül juttassa vissza a azt a költségek csökkentése a készlethez. Ha nem egyértelmű, hogy mikor, amely akkor van végrehajtva beállíthat egy riasztást az adatbázis, amely akkor indul el, amikor az adatbázis-alá csökken a DTU-használatát a készlet maximális. Egy adatbázis készletbe történő áthelyezésének menetét az 5. gyakorlat írja le.

## <a name="other-performance-management-patterns"></a>Egyéb teljesítménykezelési minták

**Preemptív skálázás** a gyakorlatban a fenti ahol megismerte az elkülönített adatbázis méretezése tudtak melyik adatbázis, amelyet meg kíván keresni. Contoso energiaoptimalizálást egyszerre Hall kezelését a közelgő jegy értékesítési Wingtips közölte, ha az adatbázis sikerült kikerült a készlet pre-emptively. Máskülönben valószínűleg egy riasztást kellett volna beállítani a készleten vagy az adatbázison ahhoz, hogy észre lehessen venni, mi történik. Nem jó, ha az ilyen eseményekről úgy szerez tudomást, hogy a készletben található többi bérlő csökkenő teljesítményről panaszkodik. Ha a bérlő meg tudja jósolni, hogy milyen hosszan lesz szüksége további erőforrásokra, beállítható egy Azure Automation-runbook, amely pontosan ütemezi az adatbázis kivételét, majd visszahelyezését a készletbe.

**Bérlők általi önkiszolgáló skálázás** Mivel a skálázási feladat könnyen meghívható a felügyeleti API-n keresztül, így a bérlői adatbázisok méretezésének lehetősége könnyen beépíthető a bérlőoldali alkalmazásba, és felkínálható az SaaS-szolgáltatás egy funkciójaként. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Egy készlet felfelé és lefelé skálázás használati minták megfelelő ütemezés szerint**

Ha az összesített bérlői használat kiszámítható mintákat követ, az Azure Automationnel ütemezni tudja a készletek vertikális fel- és leskálázását. Például egy készletet hétköznapokon este 6 után leskálázhat, reggel 6 előtt pedig felskálázhat, ha tudja, hogy a két időpont között csökken az erőforrásigény.



## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére

[Egyetlen bérlő visszaállítása – oktatóanyag](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis / bérlői alkalmazástelepítés épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Rugalmas SQL-készletek](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) – A Log Analytics beállítását és használatát ismertető oktatóanyag
