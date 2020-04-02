---
title: Bérlőnkénti adatbázis SaaS-oktatóanyag
description: Telepítse és fedezze fel a Wingtip jegyek SaaS több-bérlős alkalmazást, amely bemutatja a bérlőnkénti adatbázis-mintát és más SaaS-mintákat az Azure SQL Database használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3182daa4ebf3becc824b600d1e487e12b875b275
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529658"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Több-bérlős SaaS-alkalmazás üzembe helyezése és feltárása, amely a bérlőnkénti adatbázis-mintát használja az SQL Database-rel

Ebben az oktatóanyagban üzembe helyezheti és felfedezheti a Wingtip jegyek SaaS-adatbázis-bérlőnkénti alkalmazást (Wingtip). Az alkalmazás egy bérlőnkénti adatbázis-mintát használ több bérlő adatainak tárolására. Az alkalmazás célja, hogy bemutassa az Azure SQL Database olyan funkcióit, amelyek leegyszerűsítik az SaaS-forgatókönyvek engedélyezését.

Öt perccel azután, hogy az **Üzembe helyezés az Azure-ba lehetőséget választja,** több-bérlős SaaS-alkalmazással rendelkezik. Az alkalmazás tartalmaz egy SQL-adatbázist, amely a felhőben fut. Az alkalmazás három mintabérlővel van telepítve, mindegyik saját adatbázissal. Az összes adatbázis egy SQL rugalmas készletbe van telepítve. Az alkalmazás telepítve van az Azure-előfizetésben. Teljes hozzáféréssel rendelkezik az alkalmazás egyes összetevőinek felfedezéséhez és az azokkal való munkához. Az alkalmazás C# forráskód és a felügyeleti parancsfájlok érhetők el a [WingtipTicketsSaaS-DbPerTenant GitHub tárház.][github-wingtip-dpt]

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - A Wingtip SaaS-alkalmazás telepítése.
> - Hol lehet beszerezni az alkalmazás forráskódját és felügyeleti parancsfájljait.
> - Az alkalmazást kiszolgálókról, készletekről és adatbázisokról.
> - Hogyan vannak leképezve a bérlők az adataikhoz a *katalógusban?*
> - Új bérlő kiépítése.
> - A bérlői tevékenység figyelése az alkalmazásban.

Egy [sor kapcsolódó oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) kínál, hogy vizsgálja meg a különböző SaaS tervezési és kezelési minták. Az oktatóanyagok a kezdeti telepítésen túl raknak ki. Az oktatóanyagok használatakor megvizsgálhatja a megadott parancsfájlokat, hogy lássa, hogyan valósítják meg a különböző SaaS-mintákat. A parancsfájlok bemutatják, hogy az SQL Database szolgáltatásai hogyan egyszerűsítik az SaaS-alkalmazások fejlesztését.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez győződjön meg arról, hogy az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>A Wingtip Tickets SaaS alkalmazás üzembe helyezése

### <a name="plan-the-names"></a>Tervezze meg a neveket

Ebben a szakaszban megadhat egy felhasználói értéket, amely biztosítja, hogy az erőforrásnevek globálisan egyediek legyenek. Az erőforráscsoport nevét is megadja, amely az alkalmazás központi telepítése által létrehozott összes erőforrást tartalmazza. Egy Ann Finley nevű fiktív személy számára a következőket javasoljuk:

- **Felhasználó**: *Az af1* Ann Finley monogramjából és egy számjegyéből áll. Ha másodszor is telepíti az alkalmazást, használjon másik értéket. Erre példa az af2.
- **Erőforráscsoport**: *wingtip-dpt-af1* azt jelzi, hogy ez a bérlőnkénti adatbázis-alkalmazás. Az af1 felhasználónév hozzáfűzésével korreláljon az erőforráscsoport nevével a benne lévő erőforrások nevével.

Válasszák ki a nevüket, és írják le őket.

### <a name="steps"></a>Lépések

1. A Wingtip Tickets SaaS-adatbázis-bérlőnkénti telepítési sablon megnyitásához az Azure Portalon válassza a **Telepítés az Azure-ba**lehetőséget.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Adja meg a sablonban a szükséges paraméterek értékeit.

    > [!IMPORTANT]
    > Egyes hitelesítési és kiszolgálói tűzfalak szándékosan nem biztonságosak demonstrációs célokra. Azt javasoljuk, hogy hozzon létre egy új erőforráscsoportot. Ne használjon meglévő erőforráscsoportokat, kiszolgálókat vagy készleteket. Ne használja ezt az alkalmazást, parancsfájlokat vagy üzembe helyezett erőforrásokat éles környezetben. Törölje ezt az erőforráscsoportot, ha befejezte az alkalmazást a kapcsolódó számlázás leállításához.

    - **Erőforráscsoport**: Válassza **az Új létrehozása**lehetőséget, és adja meg az erőforráscsoporthoz korábban kiválasztott egyedi nevet.
    - **Hely**: Válasszon egy helyet a legördülő listából.
    - **Felhasználó**: Használja a korábban kiválasztott felhasználónév-értéket.

1. Az alkalmazás üzembe helyezése.

    a. Válassza ki, hogy elfogadja a feltételeket.

    b. Válassza a **Beszerzés** lehetőséget.

1. A telepítés állapotának figyeléséhez válassza az **Értesítések** (a keresőmező jobb oldalán található csengő ikon) lehetőséget. A Wingtip Tickets SaaS alkalmazás üzembe helyezése körülbelül öt percet vesz igénybe.

   ![A telepítés sikerült](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>A Wingtip Tickets management parancsfájlok letöltése és tiltásának feloldása

Amíg az alkalmazás telepíti, töltse le a forráskódot és a felügyeleti parancsfájlokat.

> [!IMPORTANT]
> Előfordulhat, hogy a Windows blokkolja a végrehajtható tartalmakat (parancsfájlokat és DL-eket), ha a windows .zip fájlokat külső forrásból tölti le és bontja ki. A parancsfájlok kibontása előtt kövesse a .zip fájl blokkolásának feloldásához szükséges lépéseket. A letiltás letiltása biztosítja, hogy a parancsfájlok futhassanak.

1. Tallózással keresse meg a [WingtipTicketsSaaS-DbPerTenant GitHub-tárházát.][github-wingtip-dpt]
1. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
1. Válassza **a Zip letöltése**lehetőséget, majd mentse a fájlt.
1. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-master.zip** fájlra, majd válassza **a Tulajdonságok parancsot.**
1. Az **Általános** lapon válassza az Alkalmazás **tiltásának** > **feloldása**lehetőséget.
1. Válassza **az OK**gombot, és bontsa ki a fájlokat

Szkriptek találhatók a ... \\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules mappa.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>A központi telepítés felhasználói konfigurációs fájljának frissítése

A parancsfájlok futtatása előtt frissítse az erőforráscsoportot és a felhasználói értékeket a Felhasználói konfiguráció fájlban. Állítsa be ezeket a változókat a központi telepítés során használt értékekre.

1. A PowerShell ISE,open ... \\Tanulási modulok\\**UserConfig.psm1**
1. Frissítse **a ResourceGroupName** és **a Name értéket** a központi telepítés adott értékeivel (csak a 10. és 11. sorban).
1. Mentse a módosításokat.

Ezekre az értékekre szinte minden parancsfájl hivatkozik.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás bemutatja az eseményeket fogadó helyszíneket. A helyszínek között koncerttermek, jazz klubok és sportklubok is szerepelnek. A Wingtip jegyekben a helyszínek bérlőként vannak regisztrálva. Mivel a bérlő ad egy helyszín egy egyszerű módja annak, hogy sorolja események és eladni jegyeket ügyfeleiknek. Minden helyszín kap egy személyre szabott weboldalt, hogy felsorolják az eseményeket és eladják a jegyeket.

Az alkalmazáson belül minden bérlő kap egy SQL-adatbázis titulált egy SQL rugalmas készletbe.

A központi **Eseményközpont** lap a központi telepítésben szereplő bérlőkre mutató hivatkozások listáját tartalmazza.

1. Az URL-cím segítségével nyissa meg az http://events.wingtip-dpt.&ltEseményközpontot a webböngészőben: ;user&gt;.trafficmanager.net. Helyettesítse &lt;a felhasználót&gt; a központi telepítés felhasználói értékével.

    ![Eseményközpont](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Válaszd a **Fabrikam Jazz Club** lehetőséget az Események központban.

    ![Események](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Wingtip alkalmazás az [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) segítségével szabályozza a bejövő kérelmek elosztását. Egy adott bérlő eseményoldalának eléréséhez használt URL-cím a következő formátumot használja:

- http://events.wingtip-dpt.&lt;felhasználó&gt;.trafficmanager.net/fabrikamjazzclub

    Az előző formátum egyes részeit az alábbi táblázat ismerteti.

    | URL-rész        | Leírás       |
    | :-------------- | :---------------- |
    | események.wingtip-dpt | A Wingtip alkalmazás események részei.<br /><br /> *-dpt* megkülönbözteti az *adatbázis-per-bérlő* végrehajtása Wingtip jegyek más megvalósítások. Ilyenek például a bérlőnkénti *egyetlen* alkalmazás (*-sa*) vagy *a több-bérlős adatbázis* (*-mt)* implementációk. |
    | . * &lt;felhasználó&gt;* | *af1* a példában. |
    | trafficmanager.net/. | Traffic Manager, alap URL. |
    | fabrikamjazzclub | Azonosítja a Fabrikam Jazz Club nevű bérlőt. |
    | &nbsp; | &nbsp; |

- A bérlő nevét az események alkalmazás elemzi az URL-címből.
- A bérlő neve kulcs létrehozásához használható.
- A kulcs a katalógus eléréséhez a bérlő ialista helyének megszerzéséhez használható.
  - A katalógus a *shard térképkezelés*használatával valósítva meg.
- Az Eseményközpont kiterjesztett metaadatokat használ a katalógusban az egyes bérlők eseménylistájának url-címei létrehozásához.

Éles környezetben általában létrehoz egy CNAME DNS-rekordot, amely [*a vállalati internetes tartományt*](../traffic-manager/traffic-manager-point-internet-domain.md) a Traffic Manager DNS-nevére irányíthatja.

> [!NOTE]
> Lehet, hogy nem azonnal nyilvánvaló, hogy mi a használata a forgalomkezelő van ebben a tutorial. Az oktatósorozat célja, hogy bemutassa azokat a mintákat, amelyek képesek kezelni egy összetett éles környezet méretét. Ebben az esetben például több webalkalmazást kellene terjeszteni a világ minden táján, az adatbázisokkal együtt elhelyezve, és a példányok közötti útvonalhoz forgalomkezelőre van szükség.
Egy másik oktatókészlet, amely a traffic manager használatát szemlélteti, a [geo-visszaállítás](saas-dbpertenant-dr-geo-restore.md) és a [georeplikációs](saas-dbpertenant-dr-geo-replication.md) oktatóanyagok. Ezekben az oktatóanyagokban a Traffic Manager segítségével válthat a SaaS-alkalmazás helyreállítási példányára regionális leállás esetén.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítve van, tegyük működőképesnek.

A *Demo-LoadGenerator* PowerShell parancsfájl elindítja a számítási feladatok, amely fut az összes bérlői adatbázisok. Számos SaaS-alkalmazás valós terhelése szórványos és kiszámíthatatlan. Az ilyen típusú terhelés szimulálásához a generátor minden bérlőn véletlenszerű tüskékkel vagy tevékenységsorozatokkal rendelkező terhelést hoz létre. A sorozatok véletlenszerű időközönként történnek. A terhelési minta megjelenése néhány percet vesz igénybe. Hagyja, hogy a generátor legalább három vagy négy percig működne, mielőtt figyelemmel kíséri a terhelést.

1. A PowerShell ISE-ben nyissa meg a ... \\Tanulási modulok\\\\Segédprogramok*Demo-LoadGenerator.ps1* script.
2. Nyomja meg az F5 gombot a parancsfájl futtatásához és a terhelésgenerátor elindításához. Hagyja meg az alapértelmezett paraméterértékeket.
3. Jelentkezzen be Az Azure-fiókjába, és szükség esetén válassza ki a használni kívánt előfizetést.

A betöltés-generátor parancsfájl elindítja a háttérfeladatot a katalógusban lévő minden adatbázishoz, majd leáll. Ha újrafuttatja a betöltési generátor parancsfájlt, leállítja a háttérfeladatokat, amelyek futnak, mielőtt újakat indítana.

### <a name="monitor-the-background-jobs"></a>A háttérfeladatok figyelése

Ha a háttérfeladatokat szeretné szabályozni és figyelni, használja a következő parancsmagokat:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 műveletek

*Demo-LoadGenerator.ps1* utánozza az aktív munkaterhelés az ügyfél tranzakciók. A következő lépések a *Demo-LoadGenerator.ps1* által kezdeményezett műveletek sorrendjét ismertetik:

1. *Demo-LoadGenerator.ps1* elindítja *LoadGenerator.ps1* az előtérben.

    - Mindkét .ps1 fájl a Tanulási modulok\\segédprogramjai\\mappában tárolódik.

2. *LoadGenerator.ps1* hurkok a katalógusban lévő összes bérlői adatbázisban.

3. *A LoadGenerator.ps1* minden bérlői adatbázishoz elindítja a háttérben futó PowerShell-feladatot:

    - Alapértelmezés szerint a háttérfeladatok 120 percig futnak.
    - Minden feladat processzoralapú terhelést okoz egy bérlői adatbázison *a sp_CpuLoadGenerator*végrehajtásával. A terhelés intenzitása és időtartama afüggőtől függ. `$DemoScenario`
    - *sp_CpuLoadGenerator* egy SQL SELECT utasítás körül, amely nagy processzorterhelést okoz. A SELECT problémái közötti időintervallum a paraméterértékektől függően változik, hogy szabályozható CPU-terhelést hozzon létre. A terhelési szintek és intervallumok véletlenszerűen kerülnek felosztásra, hogy valósághűbb terheléseket szimuláljanak.
    - Ez az .sql fájl a *WingtipTenantDB\\\\dbo StoredProcedures\\*alatt található.

4. Ha `$OneTime = $false`a terhelésgenerátor elindítja a háttérfeladatokat, majd folytatja a futtatást. 10 másodpercenként figyeli a kiépített új bérlőket. Ha beállítja, `$OneTime = $true`a LoadGenerator elindítja a háttérfeladatokat, majd leáll az előtérben. Ehhez az oktatóanyaghoz hagyja a . `$OneTime = $false`

   A Ctrl-C vagy a Stop Operation Ctrl-Break billentyűkombinációt használja a terhelésgenerátor leállításához vagy újraindításához.

   Ha a terhelésgenerátor fut az előtérben, használjon másik PowerShell ISE-példányt más PowerShell-parancsfájlok futtatásához.

&nbsp;

Mielőtt folytatna a következő szakasz, hagyja a terhelésgenerátor fut a feladat-meghívási állapotban.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

A kezdeti központi telepítés három mintabérlőt hoz létre. Most hozzon létre egy másik bérlőt, hogy az üzembe helyezett alkalmazásra gyakorolt hatás. A Wingtip alkalmazásban az új bérlők kiépítéséhez szükséges munkafolyamatot a Kiépítés és katalógus [oktatóanyag ismerteti.](saas-dbpertenant-provision-and-catalog.md) Ebben a fázisban hozzon létre egy új bérlőt, amely kevesebb mint egy percet vesz igénybe.

1. Nyisson meg egy új PowerShell ISE-t.
2. Nyit... \\Tanulási modulok\Provision and\\Catalog*Demo-ProvisionAndCatalog.ps1*.
3. A parancsfájl futtatásához nyomja le az F5 billentyűt. Egyelőre hagyja meg az alapértelmezett értékeket.

   > [!NOTE]
   > Sok Wingtip SaaS parancsfájl *$PSScriptRoot* a mappák böngészésére használja a függvények más parancsfájlokban való hívásához. Ez a változó csak akkor kerül kiértékelésre, ha a teljes parancsfájl végrehajtása az F5 billentyű lenyomásával történik.Az F8-as kijelölés kiemelése és futtatása hibákat eredményezhet. A parancsfájlok futtatásához nyomja le az F5 billentyűt.

Az új bérlői adatbázis:

- SQL rugalmas készletben létrehozott.
- Inicializálva.
- Regisztrálva van a katalógusban.

Sikeres üzembe építés után az új bérlő *Események* helye megjelenik a böngészőben.

![Új bérlő](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Frissítse az Eseményközpontot, hogy az új bérlő megjelenjen a listában.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Most, hogy megkezdte a bérlők gyűjteményének terhelését, tekintse meg az üzembe helyezett erőforrások egy részét.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az SQL-kiszolgálók listáját. Ezután nyissa meg a **&lt;katalógus-dpt- USER&gt; ** szervert.
    - A katalóguskiszolgáló két adatbázist tartalmaz, **a tenantcatalog-ot** és **a basetenantdb-t** (egy sablonadatbázist, amelyet az új bérlők létrehozásához másol).

   ![Adatbázisok](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Lépjen vissza az SQL-kiszolgálók listájára.

3. Nyissa meg a **bérlői&lt;adatbázisokat tartalmazó tenants1-dpt- USER&gt; ** kiszolgálót.

4. Lásd a következő elemeket:

    - Minden bérlői adatbázis egy **rugalmas standard** adatbázis egy 50 eDTU-szabványkészletben.
    - A Red Maple Racing adatbázis a bérlői adatbázis, amelyet korábban kiépített.

   ![Kiszolgáló adatbázisokkal](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

Miután *LoadGenerator.ps1* fut néhány percig, elegendő adatnak kell rendelkezésre állnia, hogy elkezd látszó-on néhány felügyeleti képességek. Ezek a képességek készletekbe és adatbázisokba vannak beépítve.

Tallózással keresse meg a kiszolgáló **bérlők1-dpt-felhasználó&lt;&gt;**, és válassza **a Készlet1** a készlet erőforrás-kihasználtsága megtekintéséhez. A következő diagramokon a terhelésgenerátor egy órán át futott.

   ![Figyelő készlet](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Az első diagram, feliratú **Erőforrás-kihasználtság**, mutatja készlet eDTU kihasználtsága.
- A második diagram a készlet öt legaktívabb adatbázisának eDTU-kihasználtságát mutatja.

A két diagram azt mutatja, hogy a rugalmas készletek és az SQL-adatbázis kiválóan alkalmas a kiszámíthatatlan SaaS-alkalmazás számítási feladatok. A diagramok azt mutatják, hogy négy adatbázis mindegyike 40 eDTU-ra van feltörve, és mégis az összes adatbázist kényelmesen támogatja egy 50 eDTU-készlet. Az 50 eDTU-készlet még nagyobb számítási feladatokat is támogat. Ha az adatbázisok egyetlen adatbázisként vannak kiépítve, mindegyiknek S2 (50 DTU) kell lennie a sorozatok támogatásához. Négy egyetlen S2-adatbázis költsége közel háromszorosa a készlet árának. Valós helyzetekben az SQL Database-ügyfelek legfeljebb 500 adatbázist futtatnak 200 eDTU-készletben. További információt a [Teljesítményfigyelés oktatóanyaga című témakörben talál.](saas-dbpertenant-performance-monitoring.md)

## <a name="additional-resources"></a>További források

- További információkért tekintse meg [a Wingtip Tickets SaaS adatbázis-bérlőalkalmazásra épülő további oktatóanyagokat.](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- A rugalmas készletekről a [Mi az Azure SQL rugalmas készlete.](sql-database-elastic-pool.md)
- A rugalmas feladatokról a [Scaled-out felhőadatbázisok kezelése című témakörben olvashat.](elastic-jobs-overview.md)
- A több-bérlős SaaS-alkalmazásokról a [több-bérlős SaaS-alkalmazások tervezési mintái című](saas-tenancy-app-design-patterns.md)témakörben olvashat.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - A Wingtip Tickets SaaS alkalmazás telepítése.
> - Az alkalmazást kiszolgálókról, készletekről és adatbázisokról.
> - Hogyan vannak leképezve a bérlők az adataikhoz a *katalógusban?*
> - Új bérlők kiépítése.
> - A készlet kihasználtságának megtekintése a bérlői tevékenység figyeléséhez.
> - Mintaforrások törlése a kapcsolódó számlázás leállításához.

Ezután próbálja ki a [kiépítés és katalógus oktatóanyag](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
