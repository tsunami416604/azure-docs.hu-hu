---
title: Bérlőnkénti adatbázis SaaS-oktatóanyag – Azure SQL Database |} A Microsoft Docs
description: Üzembe helyezése, és ismerje meg a Wingtip Tickets SaaS több-bérlős alkalmazásban, amely bemutatja a bérlőnkénti adatbázis mintáját és más SaaS-minták az Azure SQL Database használatával.
keywords: sql database-oktatóanyag
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 87560f3fb34c281b6802ef5079fd1445caba6db8
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983631"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Üzembe helyezése és megismerése a több-bérlős SaaS-alkalmazás a bérlőnkénti adatbázis mintát használ, az SQL Database szolgáltatással

Ebben az oktatóanyagban üzembe helyezése, és ismerje meg a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás (Wingtip). Az alkalmazás bérlőnkénti adatbázis mintát használ több bérlő az adatok tárolásához. Az alkalmazás az Azure SQL Database SaaS-forgatókönyveket engedélyezése egyszerűsítő szolgáltatásairól bemutatására lett tervezve.

Öt perc alatt kiválasztása után **üzembe helyezés az Azure**, rendelkezik egy több-bérlős SaaS-alkalmazáshoz. Az alkalmazás tartalmaz egy SQL-adatbázis, amely a felhőben. Az alkalmazás három minta bérlővel, mindegyik a saját adatbázissal van telepítve. Összes adatbázist a rendszer üzembe helyezi egy rugalmas SQL-készlet. Az alkalmazást helyezünk üzembe az Azure-előfizetéshez. Megismerése és használata az alkalmazás az egyes összetevők teljes hozzáféréssel rendelkezik. Az alkalmazás C#-forráskódot, és a felügyeleti parancsfájlokat érhető el a [WingtipTicketsSaaS-DbPerTenant GitHub-adattárat][github-wingtip-dpt].

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - A Wingtip SaaS-alkalmazás telepítésének módjáról.
> - Az alkalmazás forráskódjának kódot és kezelésre szolgáló parancsfájlok Letöltés helye.
> - Tudnivalók a kiszolgálók, készletek és adatbázisok alkotó az alkalmazást.
> - Hogyan bérlők társítása az adataikhoz a *katalógus*.
> - Hogyan lehet új bérlő kiépítése.
> - Annak figyelése a bérlő az alkalmazásban.

A [az oktatóanyag-sorozat kapcsolódó](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) böngészhet a különböző SaaS kialakításokat és felügyeleti mintákat biztosít. Az oktatóanyagok meghaladja a kezdeti telepítés hozhat létre. Ha használja az oktatóanyagok, ellenőrizheti a megadott szkriptek hogyan valósíthatók meg a különböző SaaS-minták megtekintéséhez. A parancsfájlok bemutatják, hogyan egyszerűsítheti az az SQL Database funkcióit a SaaS-alkalmazások fejlesztését.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, az Azure PowerShell telepítve van-e. További információkért lásd: [Azure PowerShell használatának első lépései](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>A Wingtip Tickets SaaS-alkalmazás üzembe helyezése

#### <a name="plan-the-names"></a>A nevek megtervezése

Ez a szakasz lépéseit akkor adja meg felhasználói érték, amely segítségével győződjön meg arról, hogy erőforrásnevek globálisan egyedinek kell. Emellett adja meg a központi telepítés az alkalmazás által létrehozott összes erőforrást tartalmazó erőforráscsoport nevét. Reino Finley nevű fiktív személy javasoljuk, hogy:

- **Felhasználói**: *af1* Reino Finley monogramját és egy számjegyet áll. Ha az alkalmazás telepítése még egyszer, használja egy másik értéket. Ez például akkor af2.
- **Erőforráscsoport**: *wingtip-dpt-af1* azt jelzi, hogy ez az a bérlőnkénti adatbázis alkalmazást. Fűzze hozzá a felhasználó neve af1 korrelációját, ha az erőforráscsoport nevét a benne található erőforrást nevére.

Most válassza ki a nevét, és írja le. 

#### <a name="steps"></a>Lépések

1. A Wingtip Tickets SaaS bérlőnkénti adatbázis központi telepítési sablon megnyitásához az Azure Portalon válassza **üzembe helyezés az Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Adja meg a szükséges paramétereket a sablonban értékeket.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és kiszolgálói tűzfalak szándékosan nem biztonságos, bemutatási céllal. Azt javasoljuk, hogy létrehozott egy új erőforráscsoportot. Ne használjon létező erőforráscsoportokat, kiszolgálókat és készletek. Ne használja ezt az alkalmazást, parancsprogramok vagy bármely üzembe helyezett erőforrásokról éles üzemi környezetek részei. Ez az erőforráscsoport törlése, ha elkészült, az alkalmazáshoz kapcsolódó számlázások leállításához.

    - **Erőforráscsoport**: válasszon **új létrehozása**, és az erőforráscsoport korábban adja meg a választott egyedi név. 
    - **Hely**: Válasszon egy helyet a legördülő listából.
    - **Felhasználói**: használja a korábban kiválasztott felhasználói név-érték.

1. Telepítse az alkalmazást.

    a. Válassza ki a használati feltételek elfogadása.

    b. Válassza a **Beszerzés** lehetőséget.

1. Válassza ki a központi telepítési állapotának figyelése, **értesítések** (a harang ikonra a jobb oldalon, a keresőmező). A Wingtip Tickets SaaS-alkalmazás üzembe helyezése körülbelül öt percet vesz igénybe.

   ![A telepítés sikerült](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Töltse le és a felügyeleti Wingtip Tickets szkriptjei feloldása

Amíg az alkalmazás üzembe helyezése, töltse le a forrás kódot és a felügyeleti parancsfájlokat.

> [!IMPORTANT]
> Végrehajtható tartalom (parancsfájlok és DLL-ek) Windows előfordulhat, hogy blokkolja, amikor a .zip kiterjesztésű fájlok külső forrásból letölteni, és ki kell olvasni. Kövesse a lépéseket feloldása a .zip-fájlt, mielőtt kibontja a parancsfájlok. A blokkolás feloldásának biztosítja, hogy a parancsfájlok futtatását engedélyezi.

1. Keresse meg a [WingtipTicketsSaaS-DbPerTenant GitHub-adattárat][github-wingtip-dpt].
1. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
1. Válassza ki **ZIP letöltése**, majd mentse a fájlt.
1. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-master.zip** fájlt, és válassza ki **tulajdonságok**.
1. Az a **általános** lapon jelölje be **feloldása** > **alkalmaz**.
1. Válassza ki **OK**, és bontsa ki a fájlokat

Parancsfájlok a találhatók a... \\WingtipTicketsSaaS főkiszolgálóval DbPerTenant\\tanulási modulok mappát.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>A központi telepítés a felhasználó-konfigurációs fájl frissítése

Mielőtt futtatná a szkripteket, frissítse a erőforrás csoport- és a felhasználó konfigurációs fájlban. Üzembe helyezés során használt értékek ezeket a változókat beállítva.

1. A PowerShell ISE-ben nyissa meg a... \\Tanulási modulok\\**UserConfig.psm1** 
1. Frissítés **ResourceGroupName** és **neve** jellemző egyedi értékekkel (a 10-es és 11 csak vonalakat) az üzembe helyezéshez.
1. Mentse a módosításokat.

Ezek az értékek hivatkozott szinte az összes parancsfájl.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás események üzemeltető helyszínek bemutatja. Helyszíntípusok például koncerttermeket, különböző helyszíneket és sportklubokat által az olimpián. A Wingtip Tickets helyszínek regisztrált bérlők számára. Folyamatban van egy bérlő biztosít egy helyszín egyszerűen rendezvények listázására és jegyek értékesítésére ügyfeleiknek. Minden helyszín kap egy személyre szabott webhely eseményeket listázásához és jegyek értékesítésére.

Belsőleg az alkalmazás egyes bérlők egy SQL-adatbázis üzembe helyezve egy SQL rugalmas készlet lesz.

Egy központi **Eseményközpont** lap mutató hivatkozásokat biztosít a bérlők számára a központi telepítésben.

1. Az URL-cím segítségével a böngészőben nyissa meg az Eseményközpontot: http://events.wingtip-dpt.&lt; felhasználó&gt;. trafficmanager.net. A helyettesítő &lt;felhasználói&gt; az üzemelő példány felhasználói értékkel.

    ![Eseményközpont](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Válassza ki **Fabrikam Jazz Club** a az Eseményközpontot.

    ![Események](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Wingtip alkalmazás használ [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) bejövő kérelmek elosztását. Az URL-cím és a egy adott bérlő az események lapot a következő formátumot használja:

- http://events.wingtip-dpt.&lt; felhasználó&gt;.trafficmanager.net/fabrikamjazzclub

    A fenti formátum részeit mutatjuk be az alábbi táblázat.

    | URL-cím része        | Leírás       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | A Wingtip alkalmazás események részeit.<br /><br /> *-dpt* megkülönbözteti a *bérlőnkénti adatbázis* más esetében a Wingtip Tickets megvalósítását. Példa a *önálló* alkalmazás bérlőnkénti (*-sa*) vagy *több-bérlős adatbázis* (*- mt*) hitelesítés megvalósításához. |
    | .  *&lt;felhasználó&gt;* | *af1* példában. |
    | .trafficmanager.net/ | A TRAFFIC Manager, a kiinduló URL-címe. |
    | fabrikamjazzclub | Azonosítja a Fabrikam Jazz Club nevű bérlőben. |
    | &nbsp; | &nbsp; |

* A bérlő nevét az események alkalmazás elemzi az URL-címből.
* A bérlő neve segítségével hozzon létre egy kulcsot.
* A kulcs segítségével hozzáférhet a katalógushoz beszerzése a bérlői adatbázis helyét.
    - A katalógus használatával valósul *szilánkleképezés-kezelés*.
* Az Eseményközpontot a katalógus kibővített metaadataival használatával hozhat létre az események listája lap URL-címeket az egyes bérlők számára.

Éles környezetben általában hoz létre egy CNAME DNS-rekord [ *egy vállalati internetes tartomány* ](../traffic-manager/traffic-manager-point-internet-domain.md) Traffic Manager DNS-nevével.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítve van, állítsuk munkába való együttműködésre.

A *Demo-LoadGenerator* PowerShell-szkript elindítja szemben az összes bérlői adatbázison futó számítási feladatok. A való életből vett terhelés számos SaaS-alkalmazásokkal szórványosak és kiszámíthatatlanok. Az ilyen típusú terhelés szimulálásához, a generátor véletlenszerű adatforgalmi csúcsokhoz vagy egyes bérlők tevékenység adatlöketekkel terhelést eredményez. A szolgáltatás fordulhat elő, véletlenszerű időközönként. A betöltés minta bontakozik ki néhány percet vesz igénybe. Lehetővé teszik a generátor futtatásához legalább három vagy négy percet, mielőtt a terheléseket figyelni.

1. A PowerShell ISE-ben nyissa meg a... \\Tanulási modulok\\segédprogramok\\*Demo-LoadGenerator.ps1* parancsfájlt.
1. Nyomja le az F5 billentyűt a szkript futtatásához és a terhelésgenerátor indításához. Hagyja meg az alapértelmezett paraméterértékeket most.
1. Jelentkezzen be az Azure-fiókjával, és válassza ki az előfizetést szeretné használni, ha szükséges.

A terhelést létrehozó szkripthez az egyes adatbázisok háttérfeladat elindítja a katalógusban, és megszűnik. Ha újra futtatja a terhelést létrehozó szkripthez, leállítja az bármely a háttérben futó feladatok, amelyek újak megkezdése előtt futtatják.

#### <a name="monitor-the-background-jobs"></a>A háttérben futó feladatok figyelése

Ha azt szeretné, vezérlése és figyelése a háttérben futó feladatok, használja a következő parancsmagokat:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Bemutató – LoadGenerator.ps1 műveletek

*Bemutató – LoadGenerator.ps1* utánozza egy aktív számítási feladatok felhasználói tranzakciókat. Az alábbi lépések bemutatják a műveletek sorrendjét, amely *Demo-LoadGenerator.ps1* kezdeményezi:

1. *Bemutató – LoadGenerator.ps1* elindul *LoadGenerator.ps1* az előtérben történik.

    - Mindkét .ps1 fájlok tárolása a mappák tanulási modulok\\segédprogramok\\.

1. *LoadGenerator.ps1* hurkokat a katalógusban szereplő összes bérlői adatbázison keresztül.

1. *LoadGenerator.ps1* elindít egy háttér-PowerShell feladatot minden bérlői adatbázis számára:

    - Alapértelmezés szerint a háttérben futó feladatok 120 percig futtat.
    - Minden egyes feladat a CPU-alapú terheléselosztást hatására egy bérlői adatbázis végrehajtásával *sp_CpuLoadGenerator*. Attól függően változik a fényerő és a terhelés időtartamát `$DemoScenario`. 
    - *sp_CpuLoadGenerator* hurkok körül, amely magas processzorterhelést okoz tartalmazó SQL SELECT utasításhoz. A kiválasztott problémák közötti időintervallum paraméterértékek vezérelhető CPU-terhelés létrehozása függően változik. Terhelésszintek és időközök vannak kiválasztással realisztikusabb terhelés szimulálásához.
    - Ez .sql fájlt tárolja a *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Ha `$OneTime = $false`, a terhelésgenerátor a háttérben futó feladatok elindul, és ezután továbbra is fusson. Minden olyan új bérlők üzembe helyezett figyeli 10 másodpercenként. Ha `$OneTime = $true`, a LoadGenerator a háttérben futó feladatok elindul, és akkor az az előtérben futó lejár. Ebben az oktatóanyagban hagyja `$OneTime = $false`.

  Ha szeretné leállítani, vagy indítsa újra a terhelésgenerátor, használja a Ctrl-C vagy leállítása műveletet a Ctrl-Break. 

  Ha üresen hagyja a terhelésgenerátort az előtérben futó, használja a PowerShell ISE-t egy másik példány más PowerShell-parancsfájlok futtatásához.

&nbsp;

Mielőtt folytatná a következő szakaszban, hagyja a terhelésgenerátor fut a feladat meghívása állapotban.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

Az első üzembe helyezés létrehoz három mintabérlőt. Most hozzon létre egy másik bérlőben, az üzembe helyezett alkalmazás-azonosítókra gyakorolt hatást. A Wingtip alkalmazás a munkafolyamat új bérlők kiépítése ismertetése a [kiépítéssel és katalogizálással oktatóanyag](saas-dbpertenant-provision-and-catalog.md). Ebben a fázisban hozzon létre egy új bérlő számára, amely kisebb, mint egy percet vesz igénybe.

1. Nyisson meg egy új PowerShell ISE-ben.
1. Nyissa meg... \\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
1. A szkript futtatásához nyomja le az F5. Egyelőre hagyja meg az alapértelmezett értékeket.

   > [!NOTE]
   > Számos Wingtip SaaS-parancsfájlok használata *$PSScriptRoot* tallózni a mappákat funkciók hívja meg a további parancsfájlok. Ez a változó csak akkor, ha a teljes szkript végrehajtása az F5 billentyű lenyomásával lesz kiértékelve. Kiemelés, és a kijelöltek futtatása az F8 hibákat eredményezhet. A szkriptek futtatásához nyomja le az F5.

Az új bérlői adatbázis van:

- Rugalmas SQL-készletet létrehozni.
- Inicializálva.
- A katalógusban regisztrált.

A sikeres kiépítés után a *események* a böngészőben megjelenik az új bérlő helye.

![Új bérlő](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Frissítse az Eseményközpontot, hogy az új bérlő szerepel a listában.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Most, hogy Ön már elkezdte a bérlők ellen, nézzük meg egyes üzembe helyezett erőforrások.

1. Az a [az Azure portal](http://portal.azure.com), tallózással keresse meg az SQL Server-kiszolgálók listája. Nyissa meg a **katalógus-dpt -&lt;felhasználói&gt;**  kiszolgáló.
    - A katalóguskiszolgáló két adatbázist tartalmaz **tenantcatalog** és **basetenantdb** (a sablon adatbázis létrehozása az új bérlők másolt).

   ![Adatbázisok](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Lépjen vissza az SQL Server-kiszolgálók listája.

1. Nyissa meg a **tenants1-dpt -&lt;felhasználói&gt;**  kiszolgálót, amely a bérlői adatbázisokat tartalmazza.

1. Tekintse meg a következő elemek:

    - Mindegyik bérlői adatbázis egy **normál rugalmas** -adatbázis egy 50 – edtu méretű standard készletben.
    - A Red Maple Racing adatbázisa a korábban kiépített bérlő adatbázis.

   ![Adatbázis-kiszolgáló](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

Miután *LoadGenerator.ps1* fut néhány percig, elegendő adat elérhetőnek kell lennie bizonyos figyelési funkciók megtekintésére. Ezek a képességek beépített készletek és adatbázisok.

Tallózással keresse meg a kiszolgáló **tenants1-dpt -&lt;felhasználói&gt;**, és válassza ki **Pool1** a készlet erőforrás-használat megtekintéséhez. A következő diagramok a a terhelésgenerátor egy óráig futott.

   ![Készlet figyelése](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Az első diagram, címkéjű **erőforrás-használat**, mutat be a készlet eDTU kihasználtsága.
- A második diagram az öt legaktívabb adatbázisok eDTU-kihasználtság a készlet jeleníti meg.

A két diagram mutatja be, hogy a rugalmas készletek és az SQL Database kiválóan alkalmas a kiszámíthatatlan SaaS-alkalmazások és szolgáltatások. A diagramok megjelenítése, hogy négy adatbázis egyes tartalékkapacitás akár 40 edtu, és még az összes adatbázis kényelmesen támogatottak az 50-eDTU-készlet. Az 50-edtu-s készlet még nagyobb számítási feladatok támogatására képes. Ha az adatbázisokat önálló adatbázisként van, egyenként kell lennie egy S2 (50 DTU) a szolgáltatás támogatására. Négy különálló S2 adatbázis költsége a készlet árát szinte háromszor. A való életből vett helyzetekben az SQL Database ügyfelei akár 500 adatbázist is 200 eDTU méretű készletekben egyszerre futtatni. További információkért lásd: a [teljesítményfigyelési oktatóanyag](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

- További információkért lásd: további [a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazására készíthet oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Rugalmas készletek kapcsolatos további információkért lásd: [Mi az Azure SQL rugalmas készlet?](sql-database-elastic-pool.md).
- Rugalmas feladatokkal kapcsolatos további információkért lásd: [horizontálisan felskálázott felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md).
- Több-bérlős SaaS-alkalmazásokkal kapcsolatos tudnivalókért lásd: [tervezési minták több-bérlős SaaS-alkalmazások](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - A Wingtip Tickets SaaS-alkalmazás telepítésének módjáról.
> - Tudnivalók a kiszolgálók, készletek és adatbázisok alkotó az alkalmazást.
> - Hogyan bérlők társítása az adataikhoz a *katalógus*.
> - Hogyan építheti ki az új bérlők számára.
> - Hogyan készlet kihasználtságának nyomon követésével figyelheti a bérlői tevékenységeket.
> - Hogyan törlése a kapcsolódó számlázások leállításához.

Ezt követően próbálja meg a [kiépítéssel és katalogizálással oktatóanyag](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

