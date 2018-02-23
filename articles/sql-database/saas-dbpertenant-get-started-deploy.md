---
title: "Adatbázis-/-bérlő Szolgáltatottszoftver-oktatóanyag – az Azure SQL Database |} Microsoft Docs"
description: "Központi telepítése, és vizsgálja meg a Wingtip jegyek SaaS-több-bérlős alkalmazás, azt mutatja be, az adatbázis a bérlők és az egyéb Azure SQL Database használata Szolgáltatottszoftver-minták száma."
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: cbe8a04abbf2dada7cc43e57e823c3a41bf83fe7
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Központi telepítése, és vizsgálja meg az adatbázis típusú bérlői mintában használja az Azure SQL Database több-bérlős SaaS-alkalmazás

Ebben az oktatóanyagban telepíteni, és vizsgálja meg a Wingtip jegyek SaaS *bérlőnként adatbázis* alkalmazás (Wingtip). Az alkalmazás típusú bérlői mintában adatbázist használ, több bérlő adatainak tárolásához. Célja, hogy az alkalmazást, az Azure SQL Database engedélyezése Szolgáltatottszoftver-forgatókönyvek egyszerűsítő szolgáltatásairól megjelenítve.

A kék feliratú gombra kattintás után 5 percen **az Azure telepítéséhez**, hogy egy több-bérlős SaaS-alkalmazáshoz. Az alkalmazást egy Azure SQL-adatbázis a Microsoft Azure-felhőben futó tartalmazza. A három minta bérlőkkel, külön-külön adatbázisa telepítik az alkalmazást. Az adatbázisok telepít egy SQL *rugalmas készlet*. Az alkalmazás telepítve van az Azure-előfizetéshez. Vizsgálatát, és az egyes összetevők az alkalmazás használatához teljes hozzáféréssel rendelkezik. Az alkalmazás C# forráskódjához, és a felügyeleti parancsfájlok érhetők el a [WingtipTicketsSaaS-DbPerTenant GitHub-tárház][github-wingtip-dpt].

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Ügyfélszoftverek központi telepítése a Wingtip SaaS-alkalmazáshoz.
> - Honnan szerezhetők be az alkalmazás forráskódjához, és a parancsfájlok.
> - A kiszolgálók, a készletek és a adatbázisok, amelyek az alkalmazás alkotják.
> - Hogyan bérlők vannak leképezve az adataikat a *katalógus*.
> - Megtudhatja, hogyan lehet kiépíteni egy új bérlőt.
> - Megtudhatja, hogyan figyelheti a bérlői tevékenységeket az alkalmazásban.

A [az kapcsolódó oktatóanyag-sorozat](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) különböző SaaS tervezési és felügyeleti minták felfedezése kínál. Az oktatóanyagok túl a kezdeti telepítés létrehozása.
Az oktatóanyagok használata esetén tekintse meg a különböző Szolgáltatottszoftver-minták vizsgálata folyamatban van a megadott parancsfájl által megvalósított hogyan.
A parancsfájlok bemutatják, hogyan egyszerűsítheti az SQL-adatbázis szolgáltatások a SaaS-alkalmazások fejlesztésével.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* Az Azure PowerShell telepítve van. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>A Wingtip jegyek SaaS-alkalmazás központi telepítése

#### <a name="plan-the-names"></a>A nevek megtervezése

Ebben a szakaszban a lépések adjon meg egy *felhasználói* érték, amely gondoskodik arról, hogy erőforrásnevek megkülönböztetik a globálisan egyedi, és nevezze el a *erőforráscsoport* hozta létre a központi telepítés összes erőforrását tartalmazó az alkalmazás. A személy nevű *Reino Finley*, javasoljuk, hogy:
- *Felhasználó:* **af1***(saját monogramja, valamint egy számjegy. Használjon egy másik értéket (pl. af2) Ha telepít központilag az alkalmazást még egyszer.)*
- *Erőforráscsoport:* **wingtip-dpt-af1** *(wingtip-dpt azt jelzi, ez az adatbázis-/-bérlő alkalmazást. A felhasználó nevét af1 fűznek ad eredményül a benne található erőforrások nevét az erőforráscsoport neve.)*

Most válassza ki a nevét, és írja le. 

#### <a name="steps"></a>Lépések

1. Nyissa meg a Wingtip jegyek SaaS adatbázishoz bérlő központi telepítési sablont az Azure portálon, a kék kattintva **az Azure telepítéséhez** gombra.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. A sablonba adja meg a szükséges paramétereket értékeket:

    > [!IMPORTANT]
    > A bemutató céljából bizonyos hitelesítési lépések és kiszolgálói tűzfalak beállítása szándékosan nem biztonságos. Azt javasoljuk, hogy Ön *hozzon létre egy új erőforráscsoportot*. Ne használjon meglévő erőforráscsoport-sablonok, kiszolgálóknak vagy készletek. Ne használja ezt az alkalmazást, parancsprogramok vagy bármely telepített erőforrások termelési környezetben. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.

    - **Erőforráscsoport** – Itt adhatja meg **hozzon létre új** , és adja meg az egyedi **neve** korábban kiválasztott ahhoz az erőforráscsoporthoz. 
    - **Hely** – Itt adhatja meg a **hely** a legördülő listából.
    - **Felhasználói** -a korábban kiválasztott felhasználó nevének értékét használja.

1. Az alkalmazás telepítéséhez.

    - Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    - Kattintson a **Purchase** (Vásárlás) gombra.

1. Központi telepítés állapotának figyelésére kattintva **értesítések**, vagyis a harang ikonra a keresőmezőbe a jobb. A Wingtip jegyek SaaS-alkalmazás telepítése, körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Töltse le és a Wingtip jegyek felügyeleti parancsfájlok feloldása

Amíg az alkalmazás telepítése van, töltse le a forrás kódot és kezelésre szolgáló parancsfájlok.

> [!IMPORTANT]
> Ha egy .zip fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájl, a következő lépésekkel tiltásának feloldása a .zip fájl kibontása előtt. A parancsfájlok futtatásának engedélyezése a letiltásuk feloldására biztosítja.

1. Keresse meg a [WingtipTicketsSaaS-DbPerTenant GitHub-tárház][github-wingtip-dpt].
2. Kattintson a **Klónozás vagy letöltési**.
3. Kattintson a **töltse le a ZIP-**, majd mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-master.zip** fájlt, és válassza ki **tulajdonságok**.
5. Az a **általános** lapon jelölje be **Unblock**, és kattintson a **alkalmaz**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

Parancsfájlok a találhatók a *... \\WingtipTicketsSaaS főkiszolgálós DbPerTenant\\tanulási modulok* mappát.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>A központi telepítés a felhasználó-konfigurációs fájl frissítése

Bármely-parancsfájlok futtatásakor, előtt frissítse a *erőforráscsoport* és *felhasználói* értékei a *felhasználói konfiguráció* fájlt. Ezeket a változókat beállítva a központi telepítése során használt értékekre.

1. Az a **PowerShell ISE**, nyissa meg... \\Tanulási modulok\\**UserConfig.psm1** 
2. Frissítés **ResourceGroupName** és **neve** a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
3. A módosítások mentése!

Majdnem minden parancsfájl hivatkozott ezeket az értékeket.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás események üzemeltető helyszínek bővíthető. Helyszínére típusai koncerttermek jazz treff és sportegyesületek. A Wingtip jegyek helyszínek bérlők van regisztrálva. A bérlő folyamatban lehetőséget ad egy helyszínére egyszerűen lista események és jegyek értékesít az ügyfelek. Egyes helyszínekkel lekérdezi egy személyre szabott webhely, az események elemet, és adjon a jegyektől el.

Belső az alkalmazásban mindegyik bérlő lekérdezi a rugalmas SQL-készlet helyezett SQL-adatbázis.

Egy központi **események Hub** lapra mutató hivatkozásokat biztosít a környezetben a bérlők számára.

1. Nyissa meg a *események Hub* a böngészőben: http://events.wingtip-dpt.&lt;felhasználói&gt;. trafficmanager.net (helyettesítse &lt;felhasználói&gt; a központi telepítés felhasználói érték):

    ![eseményközpont](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Kattintson a **Fabrikam Jazz Club** elemre az *eseményközpontban*.

    ![Események](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Wingtip alkalmazás [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) bejövő kérelmek eloszlás. Az URL-cím, az események lapról egy adott bérlő eléréséhez a következő formátumot használja:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

Az előző formátum részei a következő táblázat ismerteti.

| URL-cím része        | Leírás       |
| :-------------- | :---------------- |
| http://events.wingtip-dpt | A Wingtip app események részeit.<br /><br /> *-dpt* különbözteti meg a *bérlőnként adatbázis* megvalósítását Wingtip jegyek más esetében. Például a *önálló* app / bérlői (*-sa*), vagy *több-bérlős adatbázis* (*- mt*) hitelesítés megvalósításához. |
| .*&lt;user&gt;* | *af1* ebben a példában. |
| .trafficmanager.net/ | Az Azure Traffic Manager alap URL-CÍMÉT. |
| fabrikamjazzclub | A bérlő neve azonosítja *Fabrikam Jazz Club*. |
| &nbsp; | &nbsp; |

1. A bérlő nevét az események alkalmazás által az URL-cím vizsgálata.
2. A bérlő neve kulcs létrehozására szolgál.
3. A kulcs a katalógust és az beszerzése a bérlő adatbázis helyének elérésére szolgál.
    - A katalógus segítségével van megvalósítva *shard térkép felügyeleti*.
4. A *események Hub* kiterjesztett metaadatokat használ a katalógus összeállításához események lap URL-címek listája az egyes bérlők számára.

Éles környezetben általában hoz létre egy CNAME DNS-rekord [ *vállalati internetes tartomány pont* ](../traffic-manager/traffic-manager-point-internet-domain.md) a traffic manager DNS-nevével.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítését, most használatba vétel!
A *bemutató-LoadGenerator* PowerShell-parancsfájl elindítja a munkaterhelés futtatott összes bérlői adatbázis.
Sok Szolgáltatottszoftver-alkalmazásoknál valós terhelése szórványos és előre nem látható.
Szimulálása a terhelésének típusát, a kódgenerátor véletlenszerű igényeiben jelentkező vagy az egyes bérlők tevékenység felszakadásáig terhelést eredményez.
A felszakadásáig fordulhat elő, véletlenszerű időközönként.
A betöltési minta bontakozik több percet vesz igénybe. Ezért érdemes lehetővé teszik a generátor legalább három vagy négy percig figyelése a betöltés előtt futtassa.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\segédprogramok\\*bemutató-LoadGenerator.ps1* parancsfájl.
1. Nyomja le az **F5** futtassa a parancsfájlt, és indítsa el a terhelés generátor. (Hagyja meg az alapértelmezett paraméterértékek most.)
1. Kérni fogja az Azure-fiókjával jelentkezzen be, és ha szükséges, válassza ki az előfizetést szeretne használni.

A betöltési generátor parancsfájl indít el az egyes adatbázisok háttér feladatot a katalógusban, és majd leáll.  Ha Újrafuttatja a terhelés generátor parancsfájl, először leáll bármely feladatok a háttérben futnak újakat megkezdése előtt.

#### <a name="monitor-the-background-jobs"></a>A feladatok a háttérben figyelése

Ha szeretné szabályozhatja és figyelemmel kísérheti a feladatok a háttérben, a következő parancsmagokat is használhatja:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>A bemutató-LoadGenerator.ps1 műveletek

*A bemutató-LoadGenerator.ps1* utánozza egy aktív felhasználói tranzakciókat munkaterhelését. A következő lépések bemutatják a műveletek sorrendjét, amely *bemutató-LoadGenerator.ps1* indít el:

1. *A bemutató-LoadGenerator.ps1* elindul *LoadGenerator.ps1* az előtérben.
    - A mappákban tárolt mindkét .ps1 fájl *tanulási modulok\\segédprogramok\\*.

1. *LoadGenerator.ps1* hurkok használatával a katalógusban az összes bérlői adatbázis.

1. *LoadGenerator.ps1* indít el az egyes bérlői adatbázisok háttér PowerShell feladatot: 
    - Alapértelmezés szerint a feladatok a háttérben futnak, a 120 perc.
    - A következő futtatásával minden feladat aktiválja az egy tenant adatbázisban a CPU-alapú terhelést *sp_CpuLoadGenerator*.  Attól függően változik a intenzitása és a betöltés időtartama `$DemoScenario`. 
    - *sp_CpuLoadGenerator* hurkok körül, amely magas processzorterhelést okoz tartalmazó SQL SELECT utasításhoz. A KIJELÖLÉSHEZ tartozó problémák közötti időközt a paraméterértékek vezérelhető CPU-terhelés létrehozásához függően változik. Terhelésszintek és intervallumok vannak véletlenszerű modell terhelések szimulálásához.
    - A .sql fájlt tárolja a *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Ha `$OneTime = $false`, a betöltés generátor a feladatok a háttérben elindul, és ekkor tovább fut, figyelés által telepített új tenantokat 10 másodpercenként. Ha `$OneTime = $true`, a LoadGenerator elindítja a feladatok a háttérben, és állítsa az előtérben futó le. Ebben az oktatóanyagban hagyja `$OneTime = $false`.

  Használja a Ctrl-C vagy művelet Ctrl-Break leállítása, ha azt szeretné leállítani, vagy indítsa újra a terhelés generátor. 

  Ha nem adja meg a betöltési generátor az előtérben futó, a másik PowerShell ISE-példány segítségével más PowerShell-parancsfájlok futtatása.

&nbsp;

Mielőtt továbblép a következő szakaszban, a betöltés generátor fut a feladat meghívása állapotban hagyja.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

A kezdeti telepítés három minta bérlők hoz létre. Most hozzon létre egy másik bérlő számára a hatás tekintse meg a telepített alkalmazás. A Wingtip alkalmazásban a munkafolyamat új bérlő kiépítésének leírtak a [biztosítása és a katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md). Ebben a fázisban hozzon létre egy új bérlő számára, amely kisebb, mint egy percig tart.

1. Nyisson meg egy új *PowerShell ISE*.
1. Nyissa meg... \\Modules\Provision és a katalógus\\*bemutató-ProvisionAndCatalog.ps1* .
2. A szkriptek futtatásához nyomja le az **F5** billentyűt. (Az alapértelmezett értékeket hagyja a lépést.)

   > [!NOTE]
   > Sok Wingtip Szolgáltatottszoftver-parancsfájlok használata *$PSScriptRoot* mappák függvényeinek meghívása más parancsfájlokban történő megnyitása. Ez a változó csak akkor értékeli ki, ha a teljes parancsfájl végrehajtása billentyűkombináció lenyomásával **F5**.  Kiemelése és futtató egy kijelölést **F8** hibát okozhat. A parancsfájlt, a billentyűkombináció lenyomásával **F5**.

Az új bérlő adatbázis van:

- Rugalmas SQL-készletet létrehozni.
- Inicializálni.
- A katalógusban regisztrált.

Sikeres kiépítése után a *események* a böngészőben megjelenik az új bérlő webhelye:

![Új bérlő](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Frissítse a *események Hub* ahhoz, hogy az új tenanthoz szerepelnek a listán.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Most, hogy egy futtatott bérlők gyűjteménye már elkezdte, vizsgáljuk meg a telepített erőforrások:

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az SQL Server-kiszolgálók listáját, és nyissa meg a **katalógus-dpt -&lt;felhasználói&gt;**  kiszolgáló.
    - A kiszolgáló tartalmazza a két adatbázis **tenantcatalog** és **basetenantdb** (sablon adatbázis létrehozására az új bérlők másolt).

   ![adatbázisok](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Lépjen vissza, ha az SQL Server-kiszolgálók listáját.

3. Nyissa meg a **tenants1-dpt -&lt;felhasználói&gt;**  kiszolgáló, amely a bérlői adatbázisok.

4. Tekintse meg a következő elemek:
    - Minden egyes bérlő adatbázis egy *Standard rugalmas* adatbázis egy standard 50 eDTU-készlet.
    - A *piros Maple Racing* adatbázis, amely a korábban kiépített bérlői adatbázis.

   ![kiszolgáló](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

Miután *LoadGenerator.ps1* futtatása több percig, elég adatoknak elérhetőknek kell lenniük egyes figyelési képességek megtekint indításához. Ezek a képességek beépített készletek és adatbázisokat.

Keresse meg a kiszolgáló **tenants1-dpt -&lt;felhasználói&gt;**, és kattintson a **Pool1** a készlet erőforrás-használat megtekintéséhez. A következő diagramon a terhelés generátor egy óráig futott.

   ![készlet figyelése](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Az első diagram, címkével **erőforrás-használat**, mutat be pool eDTU-használat.
- A második pedig a készletben jeleníti meg az öt legaktívabb adatbázisok eDTU-kihasználtságot.

A két diagramot bemutatják, hogy rugalmas készletek és SQL-adatbázis előre nem látható SaaS-alkalmazás munkaterhelések megfelelőek legyenek.
A diagram megjelenítése, hogy 4 adatbázisok a következők minden teljesítménynövelés érdekében akár 40 edtu-k számára, és még az adatbázisok kényelmesen támogatja az egy 50 eDTU-készlet. Az 50 eDTU-készlet még nehezebb munkaterhelések is támogatja.
Ha azok, önálló adatbázisok volt megadva, az egyes adatbázisok kell lennie egy S2 (50 DTU) az felszakadásáig támogatásához.
4 önálló S2 adatbázisok költségeinek a készlet ár majdnem 3-szor lenne.
Valós helyzetekben SQL-adatbázis ügyfelek legfeljebb 200 eDTU-készletek 500 adatbázisok futtassa.
További információkért lásd a [teljesítményfigyeléssel foglalkozó oktatóanyagot](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

- További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonként létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- A rugalmas készletekkel kapcsolatos tudnivalókért lásd [*a rugalmas Azure SQL-készleteket*](sql-database-elastic-pool.md) ismertető cikket.
- A rugalmas feladatokkal kapcsolatos tudnivalókért lásd a [*horizontálisan felskálázott felhőalapú adatbázisok kezelését*](sql-database-elastic-jobs-overview.md) ismertető cikket.
- A több-bérlős SaaS-alkalmazásokkal kapcsolatos tudnivalókért lásd a [*több-bérlős SaaS-alkalmazások tervezési mintáit*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - A Wingtip jegyek SaaS-alkalmazás központi telepítése
> - Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok
> - Bérlők társítása az adataikhoz a *katalógus* használatával
> - Új bérlők kiépítése
> - A bérlők tevékenységének megfigyelése a készlet kihasználtságának nyomon követésével
> - Mintaerőforrások törlése a kapcsolódó számlázások leállításához

Ezt követően próbálja meg a [biztosítása és a katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

