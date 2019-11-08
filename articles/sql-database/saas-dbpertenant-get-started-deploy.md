---
title: Adatbázis/bérlői SaaS-oktatóanyag
description: A Wingtip tickets SaaS több-bérlős alkalmazás üzembe helyezése és megismerése, amely az adatbázis-bérlői mintát és más SaaS-mintákat mutatja Azure SQL Database használatával.
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
ms.openlocfilehash: 31f712f80ee2492e4bbaec99bd093d46f9d04e6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823997"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Olyan több-bérlős SaaS-alkalmazás üzembe helyezése és feltárása, amely az adatbázis-bérlői mintát használja SQL Database

Ebben az oktatóanyagban üzembe helyezi és felderíti a Wingtip tickets SaaS-adatbázis-bérlői alkalmazást (Wingtip). Az alkalmazás egy adatbázis-bérlői mintát használ a több bérlő által tárolt adattároláshoz. Az alkalmazás úgy van kialakítva, hogy bemutassa a Azure SQL Database funkcióit, amelyek egyszerűbbé teszik az SaaS-forgatókönyvek engedélyezését.

Öt perccel azután, **hogy az üzembe helyezés az Azure**-ba lehetőséget választotta, több-bérlős SaaS-alkalmazással rendelkezik. Az alkalmazás tartalmaz egy felhőben futó SQL-adatbázist. Az alkalmazás három minta Bérlővel van telepítve, amelyek mindegyike saját adatbázissal rendelkezik. Az összes adatbázis üzembe helyezése egy rugalmas SQL-készletbe történik. Az alkalmazás üzembe helyezése az Azure-előfizetésében történik. Teljes hozzáféréssel rendelkezik az alkalmazás egyes összetevőinek megismeréséhez és használatához. Az alkalmazás C# forráskódja és a felügyeleti parancsfájlok a [WingtipTicketsSaaS-DbPerTenant GitHub-][github-wingtip-dpt]tárházban érhetők el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - A Wingtip SaaS-alkalmazás üzembe helyezése.
> - Az alkalmazás forráskódjának és felügyeleti parancsfájljainak beszerzése.
> - Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok ismertetése.
> - Hogyan vannak leképezve a bérlők az *adatkatalógushoz*.
> - Új bérlő kiépítése.
> - A bérlői tevékenység figyelése az alkalmazásban.

A [kapcsolódó oktatóanyagok sorozata](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) számos SaaS-kialakítási és-felügyeleti mintát kínál. Az oktatóanyagok a kezdeti üzemelő példányon túlmutatnak. Az oktatóanyagok használatakor megvizsgálhatja a megadott parancsfájlokat, hogy megtudja, hogyan valósulnak meg a különböző SaaS-minták. A szkriptek bemutatják, hogyan egyszerűsíthető a SQL Database szolgáltatásai az SaaS-alkalmazások fejlesztéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy a Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>A Wingtip tickets SaaS-alkalmazás üzembe helyezése

### <a name="plan-the-names"></a>A nevek megtervezése

A szakasz lépéseiben meg kell adnia egy felhasználói értéket, amely az erőforrásnevek globálisan egyedivé tételére szolgál. Meg kell adnia az erőforráscsoport nevét is, amely tartalmazza az alkalmazás központi telepítése által létrehozott összes erőforrást. Az Ann Finley nevű fiktív személy számára javasoljuk a következőket:

- **Felhasználó**: az *AF1* Anna Finley monogramja, valamint egy számjegy áll a rendelkezésére. Ha Másodszor telepíti az alkalmazást, használjon más értéket. Ilyen például a af2.
- **Erőforráscsoport**: *Wingtip-DPT-AF1* azt jelzi, hogy ez az adatbázis/bérlői alkalmazás. Fűzze hozzá a Felhasználónév AF1 az erőforráscsoport nevének és a benne található erőforrások nevének a megfeleltetéséhez.

Válassza ki a neveket, majd írja le őket.

### <a name="steps"></a>Lépések

1. A Wingtip tickets SaaS adatbázis-bérlői telepítési sablon megnyitásához a Azure Portal válassza az **üzembe helyezés az Azure**-ban lehetőséget.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Adja meg a szükséges paraméterek értékét a sablonban.

    > [!IMPORTANT]
    > Bizonyos hitelesítési és kiszolgálói tűzfalak szándékosan nem biztonságosak a demonstrációs célokra. Javasoljuk, hogy hozzon létre egy új erőforráscsoportot. Ne használjon meglévő erőforráscsoportokat, kiszolgálókat vagy készleteket. Ne használja ezt az alkalmazást, parancsfájlt vagy az éles környezetben üzembe helyezett erőforrásokat. Törölje ezt az erőforráscsoportot, ha befejezte az alkalmazást a kapcsolódó számlázás leállításához.

    - **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget, és adja meg az erőforráscsoport számára korábban kiválasztott egyedi nevet.
    - **Hely**: válasszon ki egy helyet a legördülő listából.
    - **Felhasználó**: használja a korábban kiválasztott Felhasználónév értéket.

1. Telepítse az alkalmazást.

    a. Válassza ki, hogy elfogadja a használati feltételeket.

    b. Válassza a **Beszerzés** lehetőséget.

1. A központi telepítés állapotának figyeléséhez válassza az **értesítések** lehetőséget (a keresőmező jobb oldalán található harang ikon). A Wingtip tickets SaaS-alkalmazás üzembe helyezése körülbelül öt percet vesz igénybe.

   ![Az üzembe helyezés sikerült](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>A Wingtip jegyek kezelési parancsfájljainak letöltése és feloldása

Az alkalmazás üzembe helyezésekor töltse le a forráskódot és a felügyeleti parancsfájlokat.

> [!IMPORTANT]
> Előfordulhat, hogy a Windows letiltja a végrehajtható tartalmat (parancsfájlokat és DLL-eket), ha a. zip-fájlokat külső forrásból tölti le, és kinyeri. A parancsfájlok kibontása előtt kövesse a. zip fájl feloldásának lépéseit. A blokkolás feloldása biztosítja, hogy a parancsfájlok futtatása engedélyezett legyen.

1. Keresse meg a [WingtipTicketsSaaS-DbPerTenant GitHub-][github-wingtip-dpt]tárházat.
1. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
1. Válassza a **zip letöltése**lehetőséget, majd mentse a fájlt.
1. Kattintson a jobb gombbal a **WingtipTicketsSaaS-DbPerTenant-Master. zip** fájlra, majd válassza a **Tulajdonságok parancsot**.
1. Az **általános** lapon válassza a **tiltás feloldása** > **alkalmaz**lehetőséget.
1. Válassza az **OK**, majd a fájlok kibontása lehetőséget.

A parancsfájlok a...\\WingtipTicketsSaaS-DbPerTenant-Master\\learning modules mappában találhatók.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>A központi telepítéshez tartozó felhasználói konfigurációs fájl frissítése

A parancsfájlok futtatása előtt frissítse az erőforráscsoportot és a felhasználói értékeket a felhasználói konfigurációs fájlban. Állítsa be ezeket a változókat az üzembe helyezés során használt értékekre.

1. A PowerShell ISE-ben nyissa meg a...\\learning-modulok\\**userconfig. psm1**
1. Frissítse a **ResourceGroupName** és a **nevet** az üzemelő példány megadott értékeivel (csak a 10. és a 11. sorban).
1. Mentse a módosításokat.

Ezek az értékek szinte minden parancsfájlban hivatkoznak rá.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás az eseményeket üzemeltető helyszíneket mutatja be. A helyszín típusok közé tartoznak a koncert termek, a jazz klubok és a sport klubok. Wingtip-jegyek esetén a helyszínek bérlőként vannak regisztrálva. A bérlők egyszerű lehetőséget biztosítanak az események listázására és a jegyek értékesítésére az ügyfeleknek. Minden helyszín egy személyre szabott webhelyet kap az események listázásához és jegyek eladásához.

Belsőleg az alkalmazásban minden bérlő egy SQL-alapú rugalmas készletbe helyezett SQL-adatbázist kap.

A központi **esemény-központ** oldal a központi telepítésben lévő bérlők hivatkozásainak listáját tartalmazza.

1. Az URL-cím használatával nyissa meg az Events hubot a böngészőben: http://events.wingtip-dpt.&lt; user&gt;.trafficmanager.net. Helyettesítse be &lt;felhasználói&gt; az üzembe helyezés felhasználói értékével.

    ![Events hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Válassza a **Fabrikam Jazz Club** lehetőséget az Events központban.

    ![Események](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A Wingtip alkalmazás az [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) használatával szabályozza a bejövő kérelmek eloszlását. Egy adott bérlő események lapjához való hozzáféréshez használt URL-cím a következő formátumot használja:

- http://events.wingtip-dpt.&lt; felhasználó&gt;. trafficmanager.net/fabrikamjazzclub

    Az előző formátum részeit a következő táblázat ismerteti.

    | URL-cím része        | Leírás       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | A Wingtip alkalmazás eseményeinek részei.<br /><br /> *– a DPT* megkülönbözteti a Wingtip-jegyek *adatbázis-bérlői* implementációját más megvalósításokból. Ilyenek például az *egyetlen* alkalmazás-bérlő ( *-sa*) vagy több- *bérlős adatbázis* ( *-MT*) implementációk. |
    | . *&lt;felhasználói&gt;* | a példában szereplő *AF1* . |
    | . trafficmanager.net/ | Traffic Manager, alap URL-cím. |
    | fabrikamjazzclub | A fabrikam Jazz Club nevű bérlőt azonosítja. |
    | &nbsp; | &nbsp; |

- A bérlő neve az Events alkalmazás URL-címéből lesz értelmezve.
- A bérlő neve kulcs létrehozására szolgál.
- A kulcs használatával érheti el a katalógust, hogy beszerezze a bérlő adatbázisának helyét.
  - A katalógus a szegmenses *hozzárendelések kezelése*használatával valósítható meg.
- Az Events hub kiterjesztett metaadatokat használ a katalógusban az egyes bérlők Eseménynapló-URL-címeinek létrehozásához.

Éles környezetben általában egy CNAME DNS-rekordot hoz létre, amely [*egy vállalati internetes tartományt*](../traffic-manager/traffic-manager-point-internet-domain.md) a Traffic Manager DNS-névre.

> [!NOTE]
> Előfordulhat, hogy nem azonnal nyilvánvaló, hogy milyen a Traffic Manager használata ebben az oktatóanyagban. Az oktatóanyag célja, hogy olyan mintákat mutasson be, amelyek képesek kezelni egy összetett éles környezet méretét. Ilyen esetben például több webalkalmazásra lenne szükség a világ bármely részén, amely az adatbázisokkal közös helyen található, és szüksége lesz a Traffic Managerre, hogy átirányítsa ezeket a példányokat.
Egy másik oktatóanyag, amely a Traffic Manager használatát mutatja be, a [geo-visszaállítási](saas-dbpertenant-dr-geo-restore.md) és a [geo-replikációs](saas-dbpertenant-dr-geo-replication.md) oktatóanyagok. Ezekben az oktatóanyagokban a Traffic Manager használatával lehet áttérni az SaaS-alkalmazás helyreállítási példányára regionális leállás esetén.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy üzembe helyezte az alkalmazást, végezze el a munkát.

A *demo-LoadGenerator PowerShell-* szkript elindít egy munkaterhelést, amely az összes bérlői adatbázison fut. A sok SaaS-alkalmazás valós terhelése szórványos és kiszámíthatatlan. Az ilyen típusú terhelés szimulálása érdekében a generátor véletlenszerűen megjelenő terhelést vagy adattörést eredményez az egyes bérlők esetében. A törések véletlenszerű időközönként történnek. A terhelési minta megjelenése több percet vesz igénybe. A betöltés figyelése előtt legalább három vagy négy percen belül futtassa a létrehozót.

1. A PowerShell ISE-ben nyissa meg a...\\learning modules\\Utilities\\*demo-LoadGenerator. ps1* parancsfájlt.
2. Nyomja le az F5 billentyűt a szkript futtatásához és a Load Generator elindításához. A paraméterek alapértelmezett értékeinek meghagyása most.
3. Jelentkezzen be az Azure-fiókjába, és szükség esetén válassza ki a használni kívánt előfizetést.

A Load Generator szkript elindít egy háttérben futó feladatot a katalógusban található összes adatbázishoz, majd leállítja azt. Ha Újrafuttatja a Load Generator-szkriptet, az leállítja az összes olyan háttérben futó feladatot, amely az új adatbázisok elindítása előtt fut.

### <a name="monitor-the-background-jobs"></a>A háttérben futó feladatok figyelése

Ha szeretné vezérelni és figyelni a háttérben futó feladatokat, használja a következő parancsmagokat:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator. ps1 műveletek

A *demo-LoadGenerator. ps1* a vevői tranzakciók aktív terhelését utánozza. A következő lépések a *demo-LoadGenerator. ps1* által kezdeményezett műveletek sorát ismertetik:

1. Az előtérben a *demo-LoadGenerator. ps1* elindítja az *LoadGenerator. ps1* programot.

    - Mindkét. ps1 fájlt a mappák tanulási modulok\\segédprogramok\\tárolja.

2. A *LoadGenerator. ps1* hurkokat a katalógusban található összes bérlői adatbázison keresztül hajtja végre.

3. A *LoadGenerator. ps1* minden bérlői adatbázishoz elindítja a háttérben futó PowerShell-feladatot:

    - Alapértelmezés szerint a háttérben futó feladatok 120 percet vesznek igénybe.
    - Mindegyik feladattípus CPU-alapú terhelést okoz egy bérlői adatbázison *sp_CpuLoadGenerator*végrehajtásával. A terhelés intenzitása és időtartama a `$DemoScenario`tól függően változhat.
    - *sp_CpuLoadGenerator* hurkok egy olyan SQL SELECT utasítás körül, amely magas CPU-terhelést okoz. A kiválasztott problémák közötti időintervallum változó a paraméter értékei alapján, amelyekkel ellenőrizhető CPU-terhelés hozható létre. A betöltési szintek és az intervallumok véletlenszerűek a reális terhelések szimulálása érdekében.
    - Ezt az. SQL-fájlt a *WingtipTenantDB\\dbo\\StoredProcedures\\* alatt tárolja.

4. Ha `$OneTime = $false`, a Load Generator elindítja a háttérben futó feladatokat, majd folytatja a futtatást. Minden 10 másodpercenként figyeli a kiépített új bérlőket. Ha a `$OneTime = $true` értéket állítja be, a LoadGenerator elindítja a háttérben futó feladatokat, majd leállítja a futtatást az előtérben. Ebben az oktatóanyagban hagyja `$OneTime = $false` értéket.

   Ha szeretné leállítani vagy újraindítani a Load Generatort, használja a CTRL-C vagy a Leállítás művelet Ctrl-Break billentyűkombinációt.

   Ha a Load Generatort az előtérben futtatja, egy másik PowerShell ISE-példánnyal is futtathat más PowerShell-parancsfájlokat.

&nbsp;

Mielőtt továbblépne a következő szakaszra, hagyja a betöltési generátort a feladattípus-hívási állapotban.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

A kezdeti üzembe helyezés három minta bérlőt hoz létre. Most hozzon létre egy másik bérlőt, hogy megtekintse a telepített alkalmazás hatását. A Wingtip alkalmazásban az új bérlők kiépítésére szolgáló munkafolyamatot a [kiépítés és a katalógus oktatóanyagban](saas-dbpertenant-provision-and-catalog.md)ismertetjük. Ebben a fázisban egy új bérlőt hoz létre, amely kevesebb mint egy percet vesz igénybe.

1. Nyisson meg egy új PowerShell ISE-t.
2. Nyissa meg a...\\learning Modules\Provision és Catalog\\*demo-ProvisionAndCatalog. ps1*.
3. A szkript futtatásához nyomja le az F5 billentyűt. Hagyja meg most az alapértelmezett értékeket.

   > [!NOTE]
   > Számos Wingtip SaaS-szkript *$PSScriptRoot* használ a mappák tallózására a függvények más parancsfájlokban való meghívásához. Ezt a változót csak akkor értékeli a rendszer, ha a teljes parancsfájlt az F5 billentyű lenyomásával hajtja végre. Ha kijelöli és futtatja a kijelölést az F8-ban, hibákat okozhat. A parancsfájlok futtatásához nyomja le az F5 billentyűt.

Az új bérlői adatbázis:

- Egy rugalmas SQL-készletben lett létrehozva.
- Inicializálva.
- Regisztrálva van a katalógusban.

A sikeres kiépítés után az új bérlő *eseményeinek* helye megjelenik a böngészőben.

![Új bérlő](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Az Events hub frissítése, hogy az új bérlő megjelenjen a listában.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>A kiszolgálók, készletek és bérlői adatbázisok megismerése

Most, hogy már elindította a betöltést a bérlők gyűjteményéből, nézzük meg a telepített erőforrásokat.

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az SQL-kiszolgálók listáját. Ezután nyissa meg a **Catalog-DPT-&lt;felhasználói&gt;-** kiszolgálót.
    - A katalógus-kiszolgáló két adatbázist, a **tenantcatalog** és a **basetenantdb** (az új bérlők létrehozásához másolt sablon-adatbázist) tartalmaz.

   ![Adatbázisok](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Térjen vissza az SQL-kiszolgálók listájához.

3. Nyissa meg a **tenants1-DPT-&lt;felhasználói&gt;** -kiszolgálót, amely a bérlői adatbázisokat tárolja.

4. Tekintse meg a következő elemeket:

    - Minden bérlői adatbázis egy **rugalmasan szabványos** adatbázis egy 50 eDTU standard készletben.
    - A Red Maple Racing-adatbázis a korábban kiépített bérlői adatbázis.

   ![Kiszolgáló adatbázisokkal](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>A készlet figyelése

A *LoadGenerator. ps1* futtatása után néhány percen belül elegendő mennyiségű adattal kell rendelkeznie, hogy megkeresse a figyelési funkciókat. Ezek a képességek készletekbe és adatbázisokba vannak építve.

Keresse meg a kiszolgáló **tenants1-DPT-&lt;felhasználói&gt;** , és válassza a **Pool1** lehetőséget a készlet erőforrás-kihasználtságának megtekintéséhez. A következő diagramok esetében a Load Generator egy órára futott.

   ![Készlet figyelése](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Az első diagram, a megcímkézett **Erőforrás kihasználtsága**a készlet eDTU kihasználtságát mutatja.
- A második diagram a készletben lévő öt legaktívabb adatbázis eDTU használatát mutatja be.

A két diagram azt szemlélteti, hogy a rugalmas készletek és a SQL Databaseek jól illeszkednek a kiszámíthatatlan SaaS-alkalmazások számítási feladataihoz. A diagramok azt mutatják, hogy a négy adatbázis mind a 40 Edtu, mind pedig az összes adatbázist kényelmesen támogatja egy 50 eDTU-készlet. Az 50-eDTU készlet még súlyosabb számítási feladatokat is támogat. Ha az adatbázisok önálló adatbázisként vannak kiépítve, akkor mindegyiknek S2-nek (50 DTU) kell lennie a törések támogatásához. Négy egyszeri S2-adatbázis díja majdnem háromszor a készlet árát. A valós helyzetekben SQL Database az ügyfelek akár 500 adatbázist is futtathatnak 200 eDTU-készletekben. További információ: [Teljesítményfigyelés – oktatóanyag](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

- További információ: [a Wingtip tickets SaaS-adatbázis/bérlői alkalmazásra épülő további oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- A rugalmas készletekről további információt a [Mi az Azure SQL rugalmas készlet?](sql-database-elastic-pool.md)című témakörben talál.
- A rugalmas feladatokkal kapcsolatos további tudnivalókért tekintse meg a kibővített [felhőalapú adatbázisok kezelése](elastic-jobs-overview.md)című témakört.
- A több-bérlős SaaS-alkalmazásokkal kapcsolatos további információkért lásd: [tervezési minták több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - A Wingtip tickets SaaS-alkalmazás üzembe helyezése.
> - Az alkalmazást alkotó kiszolgálók, készletek és adatbázisok ismertetése.
> - Hogyan vannak leképezve a bérlők az *adatkatalógushoz*.
> - Új bérlők kiépítése.
> - A készlet kihasználtságának megtekintése a bérlői tevékenységek figyeléséhez.
> - Mintavételi erőforrások törlése a kapcsolódó számlázás leállításához.

Ezután próbálja ki a [létesítés és a katalógus oktatóanyagot](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
