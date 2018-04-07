---
title: Adatbázis-/-bérlő Szolgáltatottszoftver-oktatóanyag – az Azure SQL Database |} Microsoft Docs
description: Központi telepítése, és vizsgálja meg az adatbázis-/-bérlő és más Szolgáltatottszoftver-minták bemutatja az Azure SQL Database segítségével több-bérlős Wingtip jegyek SaaS-alkalmazáshoz.
keywords: sql database-oktatóanyag
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 84e6f2303bcfd6a2c91f29e9e9afdc2b84f990ca
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Központi telepítése, és vizsgálja meg a több-bérlős Szolgáltatottszoftver-alkalmazást, amely az adatbázis-/-bérlő mintát használ, az SQL Database szolgáltatással

Ebben az oktatóanyagban telepíti, és megismerkedhet a Wingtip jegyek SaaS-adatbázis egy bérlő alkalmazás (Wingtip). Az alkalmazás egy adatbázis-/-bérlő mintát használ, több bérlő adatainak tárolásához. Célja, hogy az alkalmazás, SaaS-forgatókönyvek engedélyezése egyszerűsítő szolgáltatásairól az Azure SQL Database megjelenítve.

Öt perc kiválasztása után **az Azure telepítéséhez**, hogy egy több-bérlős SaaS-alkalmazáshoz. Az alkalmazás a felhőben futó SQL-adatbázis tartalmaz. A három minta bérlőkkel, külön-külön adatbázisa telepítik az alkalmazást. Az adatbázisok SQL rugalmas készletbe vannak telepítve. Az alkalmazás telepítve van az Azure-előfizetéshez. Vizsgálatát, és az egyes összetevők az alkalmazás használatához teljes hozzáféréssel rendelkezik. Az alkalmazás C# forráskódjához és a parancsfájlok érhetők el a [WingtipTicketsSaaS-DbPerTenant GitHub-tárház][github-wingtip-dpt].

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Ügyfélszoftverek központi telepítése a Wingtip SaaS-alkalmazáshoz.
> - Honnan szerezhetők be az alkalmazás forráskódjának kódot és kezelésre szolgáló parancsfájlok.
> - A kiszolgálók, a készletek és a adatbázisok, amelyek az alkalmazás alkotják.
> - Hogyan bérlők vannak leképezve az adataikat a *katalógus*.
> - Megtudhatja, hogyan lehet kiépíteni egy új bérlőt.
> - Megtudhatja, hogyan figyelheti a bérlői tevékenységeket az alkalmazásban.

A [az kapcsolódó oktatóanyag-sorozat](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) különböző SaaS tervezési és felügyeleti minták felfedezése kínál. Az oktatóanyagok túl a kezdeti telepítés létrehozása. Az oktatóanyagok használata esetén tekintse meg, hogyan vannak megvalósítva a különböző Szolgáltatottszoftver-minták a megadott parancsfájlok ellenőrizheti. A parancsfájlok bemutatják, hogyan egyszerűsítheti az SQL-adatbázis szolgáltatások a SaaS-alkalmazások fejlesztésével.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, győződjön meg arról, hogy telepítve van az Azure PowerShell. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>A Wingtip jegyek SaaS-alkalmazás központi telepítése

#### <a name="plan-the-names"></a>A nevek megtervezése

Ebben a szakaszban a lépések meg lehet adni, győződjön meg arról, hogy erőforrásnevek használt felhasználói értéket globálisan egyedi. Is adja meg, amely tartalmazza a központi telepítés az alkalmazás által létrehozott összes erőforrást az erőforráscsoport nevét. A Reino Finley nevű fiktív személy a következőket javasoljuk:

- **Felhasználói**: *af1* Reino Finley monogramját továbbá számjegy áll. Ha az alkalmazás telepítése még egyszer, használjon egy másik értéket. Példa: af2.
- **Erőforráscsoport**: *wingtip-dpt-af1* azt jelzi, ez az adatbázis-/-bérlő alkalmazást. A felhasználó nevét af1 az erőforráscsoport neve a benne található erőforrások neveinek összefüggéseket hozzáfűzése.

Most válassza ki a nevét, és írja le. 

#### <a name="steps"></a>Lépések

1. A Wingtip jegyek SaaS adatbázis egy bérlő központi telepítési sablon megnyitásához az Azure portálon, válassza ki a **az Azure telepítéséhez**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Adja meg a szükséges paramétereket a sablonban értékeit.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és a kiszolgáló tűzfal rendszer szándékosan nem biztonságos, bemutatási céllal. Azt javasoljuk, hogy hozzon létre egy új erőforráscsoportot. Ne használjon meglévő erőforráscsoport-sablonok, kiszolgálóknak vagy készletek. Ne használja ezt az alkalmazást, parancsprogramok vagy bármely telepített erőforrások termelési környezetben. Ez az erőforráscsoport törlése, amikor végzett az alkalmazáshoz kapcsolódó számlázási leállítása.

    - **Erőforráscsoport**: válasszon **hozzon létre új**, és korábban adja meg a választott egyedi név ahhoz az erőforráscsoporthoz. 
    - **Hely**: jelöljön ki egy helyet a legördülő listából.
    - **Felhasználói**: használja a korábban kiválasztott felhasználói név-érték.

3. Az alkalmazás telepítéséhez.

    a. Válassza ki a gombra kattintva elfogadja a feltételeket és kikötéseket.

    b. Válassza ki **beszerzési**.

4. Válassza ki a központi telepítés állapotának figyelésére, **értesítések** (a harang ikonra a keresőmezőbe jobbra). A Wingtip jegyek SaaS-alkalmazás telepítése, körülbelül öt percet vesz igénybe.

   ![A telepítés sikerült](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Töltse le és a Wingtip jegyek felügyeleti parancsfájlok feloldása

Amíg az alkalmazás központi telepítését, töltse le a forrás kódot és kezelésre szolgáló parancsfájlok.

> [!IMPORTANT]
> Ha egy .zip fájl külső forrásból letöltött és kibontott végrehajtható tartalma (a parancsfájlok és a DLL-ek) blokkolhatja Windows. Kövesse a .zip fájl feloldása előtt beolvashatja a parancsfájlok. Blokkolásának feloldása biztosítja, hogy a parancsfájlok futtatásának engedélyezése.

1. Keresse meg a [WingtipTicketsSaaS-DbPerTenant GitHub-tárház][github-wingtip-dpt].
2. Válassza ki **Klónozás vagy letöltési**.
3. Válassza ki **töltse le a ZIP-**, majd mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-master.zip** fájlt, és válassza ki **tulajdonságok**.
5. Az a **általános** lapon jelölje be **Unblock** > **alkalmaz**.
6. Válassza ki **OK**, és bontsa ki a fájlokat

Parancsfájlok a találhatók a... \\WingtipTicketsSaaS főkiszolgálós DbPerTenant\\tanulási modulok mappát.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>A központi telepítés a felhasználó-konfigurációs fájl frissítése

Mielőtt futtatja a parancsfájlokat, módosítsa erőforrás csoport- és a felhasználó-konfigurációs fájlban. Ezeket a változókat beállítva a központi telepítése során használt értékekre.

1. A PowerShell ISE megnyitása... \\Tanulási modulok\\**UserConfig.psm1** 
2. Frissítés **ResourceGroupName** és **neve** a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
3. Mentse a módosításokat.

Majdnem minden parancsfájl hivatkozott ezeket az értékeket.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás események üzemeltető helyszínek bővíthető. Helyszínére típusai koncerttermek jazz treff és sportegyesületek. A Wingtip jegyek helyszínek bérlők van regisztrálva. A bérlő folyamatban lehetőséget ad egy helyszínére egyszerűen lista események és jegyek értékesít az ügyfelek. Egyes helyszínekkel lekérdezi egy személyre szabott webhely, az események elemet, és adja a jegyektől el.

Belső az alkalmazásban mindegyik bérlő lekérdezi a rugalmas SQL-készlet helyezett SQL-adatbázis.

Egy központi **események Hub** lapra mutató hivatkozásokat biztosít a környezetben a bérlők számára.

1. Az URL-cím segítségével a böngészőben nyissa meg az események Hub: http://events.wingtip-dpt.&lt; felhasználói&gt;. trafficmanager.net. Helyettesítő &lt;felhasználói&gt; a központi telepítés felhasználói értékkel.

    ![Events Hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Válassza ki **Fabrikam Jazz Club** események központban.

    ![Események](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Wingtip alkalmazás [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) bejövő kérelmek eloszlás. Az URL-cím, az események lapról egy adott bérlő eléréséhez a következő formátumot használja:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    Az előző formátum részei a következő táblázat ismerteti.

    | URL-cím része        | Leírás       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | A Wingtip app események részeit.<br /><br /> *-dpt* különbözteti meg a *adatbázis / bérlői* megvalósítását Wingtip jegyek más esetében. Többek között a *önálló* app / bérlői (*-sa*) vagy *több-bérlős adatbázis* (*- mt*) hitelesítés megvalósításához. |
    | .*&lt;user&gt;* | *af1* példában. |
    | .trafficmanager.net/ | A TRAFFIC Manager alap URL-CÍMÉT. |
    | fabrikamjazzclub | A Fabrikam Jazz Club nevű bérlő azonosítja. |
    | &nbsp; | &nbsp; |

* A bérlő nevét az események alkalmazás által az URL-cím vizsgálata.
* A bérlő neve kulcs létrehozására szolgál.
* A kulcs a katalógus beszerzése a bérlő adatbázis helyének elérésére szolgál.
    - A katalógus segítségével történik *shard térkép felügyeleti*.
* Az események Hub kiterjesztett metaadatok a katalógus URL-címek listája az események lapról összeállítani az egyes bérlők számára használja.

Éles környezetben általában hoz létre egy CNAME DNS-rekord [ *vállalati internetes tartomány pont* ](../traffic-manager/traffic-manager-point-internet-domain.md) a Traffic Manager DNS-nevével.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítve van, lehetővé használatba vétel.

A *bemutató-LoadGenerator* PowerShell-parancsfájl összes bérlői adatbázis fut egy feladat elindul. Sok Szolgáltatottszoftver-alkalmazásoknál valós terhelése szórványos és előre nem látható. Szimulálása a terhelésének típusát, a kódgenerátor véletlenszerű igényeiben jelentkező vagy az egyes bérlők tevékenység felszakadásáig terhelést eredményez. A felszakadásáig fordulhat elő, véletlenszerű időközönként. A betöltési minta bontakozik több percet vesz igénybe. Lehetővé teszik a generátor legalább három vagy négy percig a figyelheti a betöltés előtt futtassa.

1. A PowerShell ISE nyissa meg a... \\Tanulási modulok\\segédprogramok\\*bemutató-LoadGenerator.ps1* parancsfájl.
2. Nyomja le az F5 billentyűt a parancsfájl futtatása, és indítsa el a terhelés generátor. Hagyja meg az alapértelmezett paraméterértékek most.
3. Jelentkezzen be az Azure-fiókjával, és válassza ki az előfizetést szeretné használni, ha szükséges.

A betöltési generátor parancsfájl indít el az egyes adatbázisok háttér feladatot a katalógusban, és majd leáll. Ha Újrafuttatja a terhelés generátor parancsfájl, leállítja a feladatok a háttérben futnak újakat megkezdése előtt.

#### <a name="monitor-the-background-jobs"></a>A feladatok a háttérben figyelése

Ha szeretné szabályozhatja és figyelemmel kísérheti a feladatok a háttérben, használja a következő parancsmagokat:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>A bemutató-LoadGenerator.ps1 műveletek

*A bemutató-LoadGenerator.ps1* utánozza egy aktív felhasználói tranzakciókat munkaterhelését. A következő lépések bemutatják a műveletek sorrendjét, amely *bemutató-LoadGenerator.ps1* indít el:

1. *A bemutató-LoadGenerator.ps1* elindul *LoadGenerator.ps1* az előtérben.

    - Mindkét .ps1 fájlok tárolása a mappák tanulási modulok\\segédprogramok\\.

2. *LoadGenerator.ps1* hurkok használatával a katalógusban az összes bérlői adatbázis.

3. *LoadGenerator.ps1* indít el az egyes bérlői adatbázisok háttér PowerShell feladatot:

    - Alapértelmezés szerint a feladatok a háttérben futnak a 120 perc.
    - A következő futtatásával minden feladat aktiválja az egy tenant adatbázisban a CPU-alapú terhelést *sp_CpuLoadGenerator*. Attól függően változik a intenzitása és a betöltés időtartama `$DemoScenario`. 
    - *sp_CpuLoadGenerator* hurkok körül, amely magas processzorterhelést okoz tartalmazó SQL SELECT utasításhoz. A KIJELÖLÉSHEZ tartozó problémák közötti időközt a paraméterértékek vezérelhető CPU-terhelés létrehozásához függően változik. Terhelésszintek és intervallumok vannak véletlenszerű modell terhelések szimulálásához.
    - A .sql fájlt tárolja a *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Ha `$OneTime = $false`, a betöltés generátor a feladatok a háttérben elindul, és ekkor tovább fut. Bármely új bérlők által telepített figyeli minden 10 másodperc. Ha `$OneTime = $true`, a LoadGenerator elindítja a feladatok a háttérben, és az előtérben futó megszűnik. Ebben az oktatóanyagban hagyja `$OneTime = $false`.

  Használja a Ctrl-C vagy művelet Ctrl-Break leállítása, ha azt szeretné leállítani, vagy indítsa újra a terhelés generátor. 

  Ha nem adja meg a betöltési generátor az előtérben futó, a másik PowerShell ISE-példány segítségével más PowerShell-parancsfájlok futtatása.

&nbsp;

Mielőtt folytatja a következő szakaszban, hagyja a terhelés generátor fut a feladat meghívása állapotban.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

A kezdeti telepítés három minta bérlők hoz létre. Most hozzon létre egy másik bérlő számára a hatás tekintse meg a telepített alkalmazás. A Wingtip alkalmazásban a munkafolyamat új bérlő kiépítésének leírtak a [biztosítása és a katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md). Ebben a fázisban hozzon létre egy új bérlő számára, amely kisebb, mint egy percig tart.

1. Nyisson meg egy új PowerShell ISE.
2. Nyissa meg... \\Modules\Provision és a katalógus\\*bemutató-ProvisionAndCatalog.ps1*.
3. A parancsfájl futtatásához nyomja le az F5 billentyűt. Most, hagyja üresen az alapértelmezett értékeket.

   > [!NOTE]
   > Sok Wingtip Szolgáltatottszoftver-parancsfájlok használata *$PSScriptRoot* lehet tallózni a mappákat függvények hívása más parancsfájlokban. Ez a változó csak akkor, ha a teljes parancsfájl végrehajtása F5 billentyű lenyomásával értékeli. Kiemelése és futtató egy kijelölést F8 hibát okozhat. A parancsfájlok futtatásához nyomja le az F5 billentyűt.

Az új bérlő adatbázis van:

- Rugalmas SQL-készletet létrehozni.
- Inicializálni.
- A katalógusban regisztrált.

Sikeres kiépítése után a *események* a böngészőben megjelenik az új bérlő webhelye.

![Új bérlő](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Frissítse az események hubot ellenőrizze az új tenanthoz szerepelnek a listán.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Most, hogy egy futtatott bérlők gyűjteménye már elkezdte, vizsgáljuk meg a telepített erőforrások.

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az SQL Server-kiszolgálók listáját. Nyissa meg a **katalógus-dpt -&lt;felhasználói&gt; ** kiszolgáló.
    - A kiszolgáló tartalmazza a két adatbázis **tenantcatalog** és **basetenantdb** (sablon adatbázis létrehozására az új bérlők másolt).

   ![Adatbázisok](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Lépjen vissza, ha az SQL Server-kiszolgálók listáját.

3. Nyissa meg a **tenants1-dpt -&lt;felhasználói&gt; ** kiszolgáló, amely a bérlői adatbázisok.

4. Tekintse meg a következő elemek:

    - Minden egyes bérlő adatbázis egy **Standard rugalmas** adatbázis egy standard 50-eDTU-készlet.
    - A piros Maple Racing adatbázisa a korábban kiépített bérlői adatbázis.

   ![Adatbázisok kiszolgáló](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

Miután *LoadGenerator.ps1* futtatása több percig, elég adatoknak elérhetőknek kell lenniük egyes figyelési képességek megtekint indításához. Ezek a képességek beépített készletek és adatbázisokat.

Keresse meg a kiszolgáló **tenants1-dpt -&lt;felhasználói&gt;**, és válassza ki **Pool1** a készlet erőforrás-használat megtekintéséhez. A következő diagramon a terhelés generátor egy óráig futott.

   ![A figyelő készlet](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Az első diagram, címkével **erőforrás-használat**, mutat be pool eDTU-használat.
- A második pedig a készletben jeleníti meg az öt legaktívabb adatbázisok eDTU-kihasználtságot.

A két diagramot bemutatják, hogy rugalmas készletek és SQL-adatbázis előre nem látható SaaS-alkalmazás munkaterhelések megfelelőek legyenek. A diagram megjelenítése, hogy négy adatbázisok a következők minden teljesítménynövelés érdekében akár 40 edtu-k számára, és még az adatbázisok kényelmesen támogatja az egy 50-eDTU-készlet. Az 50-eDTU-készlet még nehezebb munkaterhelések is támogatja. Ha az adatbázisok, önálló adatbázisok törlődnek, minden egyes kell lennie egy S2 (50 DTU) az felszakadásáig támogatásához. Négy önálló S2 adatbázisok költségeinek a készlet ár majdnem három alkalommal. Valós helyzetekben SQL-adatbázis ügyfelek legfeljebb 200 eDTU-készletek 500 adatbázisok futtassa. További információkért lásd: a [teljesítmény figyelési oktatóanyag](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

- További információkért lásd: további [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS-adatbázis egy bérlő alkalmazás létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Rugalmas készletek kapcsolatos további tudnivalókért lásd: [Mi az az Azure SQL rugalmas készlet?](sql-database-elastic-pool.md).
- Rugalmas feladat kapcsolatos további tudnivalókért lásd: [kiterjesztett felhő adatbázisok kezelése](sql-database-elastic-jobs-overview.md).
- Több-bérlős SaaS-alkalmazásokkal kapcsolatos további tudnivalókért lásd: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS-alkalmazáshoz.
> - A kiszolgálók, a készletek és a adatbázisok, amelyek az alkalmazás alkotják.
> - Hogyan bérlők vannak leképezve az adataikat a *katalógus*.
> - Hogyan kell kiépíteni az új bérlők számára.
> - Hogyan bérlői figyelése készlet kihasználtságának megtekintéséhez.
> - Hogyan mintaerőforrásokat leállításához kapcsolódó számlázási törlése.

Ezt követően próbálja meg a [biztosítása és a katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

