---
title: Szilánkos több-bérlős adatbázis SaaS-alkalmazás üzembe helyezése
description: Telepítse és fedezze fel a szilánkos Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás, amely bemutatja a SaaS-minták az Azure SQL Database használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
ms.date: 10/16/2018
ms.openlocfilehash: 3277318e01362df8fc21ff7ca769aaeb8006abc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827994"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Szilánkos több-bérlős alkalmazás üzembe helyezése és feltárása

Ebben az oktatóanyagban üzembe helyezheti és felfedezheti a Wingtip Jegyek nevű több-bérlős SaaS-alkalmazást. A Wingtip Jegyek alkalmazás célja, hogy bemutassa az Azure SQL Database olyan funkcióit, amelyek egyszerűsítik az SaaS-forgatókönyvek megvalósítását.

A Wingtip Jegyek alkalmazás megvalósítása egy szilánkos több-bérlős adatbázis-mintát használ. A skálázás bérlői azonosító szerint történik. A bérlői adatok egy adott adatbázisba kerülnek a bérlői azonosító értékek szerint. 

Ez az adatbázis-minta lehetővé teszi, hogy tárolja egy vagy több bérlő minden szegmensben vagy adatbázisban. A legalacsonyabb költségekre optimalizálhatja, ha az egyes adatbázisokat több bérlő osztja meg. Vagy optimalizálhatja az elkülönítést azáltal, hogy minden adatbázis tárolja csak egy bérlő. Az optimalizálási választás lehet egymástól függetlenül minden egyes bérlő. A választás lehet, amikor a bérlő először tárolja, vagy később meggondolhatja magát. Az alkalmazás célja, hogy jól működik mindkét irányban.

## <a name="app-deploys-quickly"></a>Az alkalmazás gyorsan telepíthető

Az alkalmazás az Azure-felhőben fut, és az Azure SQL Database-t használja. A telepítési szakasz, amely a következő biztosítja a kék **üzembe helyezés az Azure-ba** gombot. Ha megnyomja a gombot, az alkalmazás öt percen belül teljes mértékben üzembe kerül az Azure-előfizetésben. Teljes hozzáférése van az egyes alkalmazás-összetevőkkel való munkához.

Az alkalmazás három mintabérlő adataival van telepítve. A bérlők egy több-bérlős adatbázisban vannak tárolva.

Bárki letöltheti a C# és a PowerShell forráskódot a Wingtip jegyekhez [a GitHub-tárházból.][link-github-wingtip-multitenantdb-55g]

## <a name="learn-in-this-tutorial"></a>További információ az oktatóanyagban

> [!div class="checklist"]
> - A Wingtip Tickets SaaS alkalmazás telepítése.
> - Hol szerezhető be az alkalmazás forráskódja és a felügyeleti parancsfájlok?
> - Az alkalmazást kiszolgálókés adatbázisok.
> - Hogyan vannak leképezve a bérlők az adataikhoz a *katalógusban?*
> - Új bérlő kiépítése.
> - A bérlői tevékenység figyelése az alkalmazásban.

Egy sor kapcsolódó oktatóanyag áll rendelkezésre, amelyek erre a kezdeti üzembe helyezésre épülnek. Az oktatóanyagok a SaaS tervezési és kezelési mintáinak széles skáláját tárják fel. Amikor az oktatóanyagokon keresztül dolgozik, javasoljuk, hogy lépjen végig a megadott parancsfájlokon, hogy lássa, hogyan valósítják meg a különböző SaaS-mintákat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A legújabb Azure PowerShell telepítve van. További információt az [Azure PowerShell – első lépések.][link-azure-get-started-powershell-41q]

## <a name="deploy-the-wingtip-tickets-app"></a>A Wingtip Tickets alkalmazás üzembe helyezése

### <a name="plan-the-names"></a>Tervezze meg a neveket

Ebben a szakaszban megadhat egy *felhasználói* értéket, amely biztosítja, hogy az erőforrásnevek globálisan egyediek legyenek, és egy nevet az *erőforráscsoportnak,* amely tartalmazza az alkalmazás központi telepítése által létrehozott összes erőforrást. *Ann Finley*nevű személy esetén a következőket javasoljuk:
- *Felhasználó:* **af1**  *(A monogramjuk, plusz egy számjegy. Ha másodszor is telepíti az alkalmazást, használjon másik értéket (pl. af2).*
- *Erőforráscsoport:* **wingtip-mt-af1** *(wingtip-mt azt jelzi, hogy ez a szilánkos több-bérlős alkalmazás. Az af1 felhasználónév hozzáfűzése korrelálja az erőforráscsoport nevét a benne lévő erőforrások nevével.)*

Válasszák ki a nevüket, és írják le őket. 

### <a name="steps"></a>Lépések

1. Kattintson a következő kék **üzembe helyezés az Azure-ba** gombra.
   - Megnyitja az Azure Portalon a Wingtip jegyek SaaS-telepítési sablon.

     [![Az Azure-ba való üzembe helyezés gombja.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Adja meg a központi telepítéshez szükséges paraméterértékeket.

    > [!IMPORTANT]
    > Ehhez az bemutatóhoz ne használjon már meglévő erőforráscsoportokat, kiszolgálókat vagy készleteket. Ehelyett válassza **az Új erőforráscsoport létrehozása**lehetőséget. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.
    > Ne használja ezt az alkalmazást, vagy az általa létrehozott erőforrásokat éles környezetben. A hitelesítés egyes aspektusai és a kiszolgáló tűzfalbeállításai szándékosan nem biztonságosak az alkalmazásban, hogy megkönnyítsék a demonstrációt.

    - Erőforráscsoport esetén – válassza **az Új létrehozása**lehetőséget, majd adja meg az erőforráscsoport **nevét** (a kis- és nagybetűk et. **Resource group**
        - Válasszon **egy Helyet** a legördülő listából.
    - **Felhasználó –** Javasoljuk, hogy válasszon egy rövid **felhasználói** értéket.

1. **Az alkalmazás üzembe helyezése**.

    - Kattintson ide, hogy elfogadja a feltételeket.
    - Kattintson a **Purchase** (Vásárlás) gombra.

1. A telepítés állapotának figyeléséhez kattintson az **Értesítések**gombra, amely a keresőmező jobb oldalán található csengő ikon. A Wingtip alkalmazás üzembe helyezése körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>A felügyeleti parancsfájlok letöltése és tiltásának feloldása

Az alkalmazás telepítése közben töltse le az alkalmazás forráskódját és felügyeleti parancsfájljait.

> [!NOTE]
> Előfordulhat, hogy a Windows blokkolja a végrehajtható tartalmakat (parancsfájlokat, DL-eket), ha a zip fájlokat külső forrásból tölti le és bontja ki. Amikor a parancsfájlokat zip fájlból nyeri ki, a kibontás előtt az alábbi lépésekkel oldja fel a .zip fájl blokkolását. A .zip fájl blokkolásának feloldásával biztosíthatja, hogy a parancsfájlok futhassanak.

1. Tallózással keresse meg [a WingtipTicketsSaaS-MultiTenantDb GitHub tárházát.](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Kattintson **a Klónozás vagy letöltés gombra.**
3. Kattintson **a ZIP letöltése** gombra, és mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-MultiTenantDb-master.zip** fájlra, és válassza **a Tulajdonságok parancsot.**
5. Az **Általános** lapon válassza a **Tiltás feloldása**lehetőséget, majd kattintson az **Alkalmaz**gombra.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\ Modules mappa. \\*

## <a name="update-the-configuration-file-for-this-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

A parancsfájlok futtatása előtt állítsa be az *erőforráscsoportot* és a *felhasználói* értékeket a **UserConfig.psm1 fájlban.** Állítsa be ezeket a változókat a telepítés során beállított értékekre.

1. Nyit... \\A\\*UserConfig.psm1* tanulási modulok a *PowerShell ISE-ben.*
2. Frissítse *a ResourceGroupName* és *a Name értéket* a központi telepítés adott értékeivel (csak a 10. és 11. sorban).
3. Mentse a módosításokat.

Az ebben a fájlban beállított értékeket az összes parancsfájl használja, ezért fontos, hogy pontosak legyenek. Ha újratelepíti az alkalmazást, különböző értékeket kell választania a Felhasználó és az Erőforráscsoport számára. Ezután frissítse újra a UserConfig.psm1 fájlt az új értékekkel.

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Wingtip alkalmazásban a bérlők helyszínek. A helyszín lehet koncertterem, sportklub vagy bármely más, rendezvényeknek otthont adó helyszín. A helyszínek regisztrálnak a Wingtip-ben ügyfélként, és minden helyszínhez létrejön egy bérlői azonosító. Minden helyszín felsorolja a közelgő események Wingtip, így a nyilvánosság jegyeket vásárolni az eseményekre.

Minden helyszín kap egy személyre szabott webalkalmazást, amely felsorolja az eseményeket és jegyeket értékesít. Minden webalkalmazás független és elkülönítve van a többi bérlőtől. Az Azure SQL Database-ben belül minden egyes bérlő adatait alapértelmezés szerint egy szilánkos több-bérlős adatbázis tárolja. Minden adat a bérlőazonosítóval van címkézve.

A központi **Eseményközpont** weblapja az adott központi telepítés bérlőire mutató hivatkozások listáját tartalmazza. Az alábbi lépésekkel tapasztalhatja meg az **Eseményközpont** weblapját és egy adott webalkalmazást:

1. Nyissa meg az **Eseményközpontot** a böngészőben:
   - http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net &nbsp; * &lt;(Cserélje le a felhasználót&gt; a központi telepítés felhasználói értékére.)*

     ![eseményközpont](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kattintson a **Fabrikam Jazz Club** elemre az **eseményközpontban**.

   ![Események](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A bejövő kérelmek elosztásának szabályozásához a Wingtip alkalmazás az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)t használja. Az egyes bérlők eseménylapja tartalmazza a bérlő nevét az URL-címében. Minden URL-cím tartalmazza az adott felhasználói értéket is. Minden URL a következő lépésekkel tartja be a megjelenített formátumot:

- http://events.wingtip-mt.&lt;felhasználó&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Az események alkalmazás elemzi a bérlő nevét az URL-címből. A bérlő neve *fabrikamjazzclub* az előző példa URL-cím.
2. Az alkalmazás ezután kiírja a bérlő nevét, hogy hozzon létre egy kulcsot a katalógus eléréséhez [a szegmenstérkép-kezelés használatával.](sql-database-elastic-scale-shard-map-management.md)
3. Az alkalmazás megkeresi a kulcsot a katalógusban, és beszerzi a bérlő adatbázisának megfelelő helyét.
4. Az alkalmazás a helyadatok segítségével megkeresi és elérheti azt az adatbázist, amely a bérlő összes adatát tartalmazza.

### <a name="events-hub"></a>Eseményközpont

1. Az **Eseményközpont** felsorolja a katalógusban regisztrált összes bérlőt és azok helyszíneit.
2. Az **Eseményközpont** kiterjesztett metaadatokat használ a katalógusban a bérlő nevének lekéréséhez az egyes leképezésekhez társítva az URL-címek létrehozásához.

Éles környezetben általában létrehoz egy CNAME DNS-rekordot, amely [a vállalat internetes tartományát](../traffic-manager/traffic-manager-point-internet-domain.md) a forgalomkezelő profilra irányítja.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítve van, tegyük munkára! A *Demo-LoadGenerator* PowerShell parancsfájl elindítja az egyes bérlők számára futó számítási feladatokat. A valós terhelés sok SaaS-alkalmazások általában szórványos és kiszámíthatatlan. Az ilyen típusú terhelés szimulálásához a generátor az összes bérlő között elosztott terhelést hoz létre. A terhelés véletlenszerű sorozatokat tartalmaz minden egyes bérlővéletlenszerű időközönként előforduló. A terhelési minta megjelenése néhány percet vesz igénybe, ezért a legjobb, ha a generátor legalább három vagy négy percig működik, mielőtt figyeli a terhelést.

1. A *PowerShell ISE-ben*nyissa meg a ... \\Tanulási modulok\\\\Segédprogramok*Demo-LoadGenerator.ps1* script.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

A *Demo-LoadGenerator.ps1* parancsfájl megnyit egy másik PowerShell-munkamenetet, ahol a terhelésgenerátor fut. A terhelésgenerátor ebben a munkamenetben egy előtér-feladatként fut, amely meghívja a háttérterhelés-generálási feladatokat, minden bérlőhöz egyet.

Az előtér-feladat indítása után a feladatmeghívás imázsa továbbra is munkameghívási állapotban marad. A feladat további háttérfeladatokat indít el minden olyan új bérlő számára, amely később ki van építve.

A PowerShell-munkamenet bezárása leállítja az összes feladatot.

Előfordulhat, hogy szeretné újraindítani a terhelésgenerátor munkamenet különböző paraméterértékek használatával. Ha igen, zárja be a PowerShell-létrehozási munkamenetet, majd futtassa újra a *Demo-LoadGenerator.ps1 -t.*

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Új bérlő kiépítése a szilánkos adatbázisba

A kezdeti üzembe helyezés három mintabérlőt tartalmaz a *Tenants1* adatbázisban. Hozzon létre egy másik bérlőt, és figyelje meg az üzembe helyezett alkalmazásra gyakorolt hatásait. Ebben a lépésben egy billentyűt megnyom egy új bérlő létrehozásához:

1. Nyit... \\Tanulási modulok\\kiépítése\\és katalógus*bemutató-provisiontenants.ps1* a *PowerShell ISE.*
2. Nyomja **le az F5** (nem **F8**) billentyűt a parancsfájl futtatásához (hagyja meg az alapértelmezett értékeket egyelőre).

   > [!NOTE]
   > A PowerShell-parancsfájlokat csak az **F5** billentyű lenyomásával kell futtatnia, nem pedig az **F8** billentyű lenyomásával a parancsfájl kijelölt részének futtatásához. A probléma az **F8,** hogy a *$PSScriptRoot* változó nem értékeli ki. Erre a változóra számos parancsfájlnak szüksége van a mappákban való navigáláshoz, más parancsfájlok meghívásához vagy modulok importálásához.

Az új Red Maple Racing bérlő hozzáadódik a *Tenants1* adatbázishoz, és regisztrálva van a katalógusban. Az új bérlő jegyértékesítő **események** webhelye megnyílik a böngészőben:

![Új bérlő](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Frissítse az **Eseményközpontot**, és az új bérlő megjelenik a listában.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Új bérlő kiépítése a saját adatbázisában

A szilánkos több-bérlős modell lehetővé teszi, hogy válassza ki, hogy egy új bérlő egy adatbázisba, amely más bérlők, vagy egy saját adatbázisba. A saját adatbázisában elkülönített bérlő a következő előnyöket élvezi:

- A bérlői adatbázis teljesítménye anélkül kezelhető, hogy más bérlők igényeinek megfelelően kompromisszumot kellene kötni.
- Szükség esetén az adatbázis visszaállítható egy korábbi időpontra, mert más bérlők nem érintettek.

Dönthet úgy, hogy az ingyenes próbaverziós ügyfeleket vagy a gazdaságos ügyfeleket több-bérlős adatbázisokba helyezi. Minden prémium szintű bérlőt a saját dedikált adatbázisába helyezhet. Ha sok olyan adatbázist hoz létre, amelyek csak egy bérlőt tartalmaznak, az erőforrásköltségek optimalizálása érdekében együttesen kezelheti őket egy rugalmas készletben.

Ezután egy másik bérlőt építünk ki, ezúttal a saját adatbázisában:

1. A... \\Tanulási modulok\\kiépítése\\és katalógus*Demo-ProvisionTenants.ps1*, módosítsa *$TenantName* **Salix Salsa**, *$VenueType* **táncolni,** és *$Scenario* **2**.

2. Nyomja le **az F5 billentyűt** a parancsfájl ismételt futtatásához.
    - Ez **az F5** sajtó külön adatbázisban rendelkezéseket hoz az új bérlőről. Az adatbázis és a bérlő regisztrálva van a katalógusban. Ezután a böngésző megnyílik a bérlő Események lapjára.

   ![Salix Salsa események oldal](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Görgessen a lap aljára. Ott a szalagcímben láthatja az adatbázis nevét, amelyben a bérlői adatok tárolása.

3. Frissítse az **Eseményközpontot,** és a két új bérlő megjelenik a listában.

## <a name="explore-the-servers-and-tenant-databases"></a>A kiszolgálók és a bérlői adatbázisok felfedezése

Most megnézzük a felhasznált erőforrások egy részét:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az erőforráscsoportok listáját. Nyissa meg az alkalmazás telepítésekor létrehozott erőforráscsoportot.

   ![erőforráscsoport](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kattintson **a&lt;katalógus-mt felhasználói&gt; ** kiszolgáló ra. A katalóguskiszolgáló két *tenantcatalog* és *basetenantdb*nevű adatbázist tartalmaz. A *basetenantdb* adatbázis egy üres sablonadatbázis. A rendszer másolja egy új bérlői adatbázis létrehozása, hogy használt sok bérlő, vagy csak egy bérlő.

   ![katalóguskiszolgáló elemre](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Lépjen vissza az erőforráscsoporthoz, és válassza ki a *bérlői adatbázisokat tartalmazó bérlő1 mt* kiszolgálót.
    - A tenants1 adatbázis egy több-bérlős adatbázis, amelyben az eredeti három bérlő, valamint az első hozzáadott bérlő tárolják. 50 DTU standard adatbázisként van konfigurálva.
    - A **salixsalsa** adatbázis tartja a Salix Salsa tánc helyszín, mint az egyetlen bérlő. Alapértelmezés szerint 50 DT-vel rendelkező standard kiadású adatbázisként van konfigurálva.

   ![bérlői kiszolgáló](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Az adatbázis teljesítményének figyelése

Ha a terhelésgenerátor már néhány perce fut, elegendő telemetriai adatok érhetők el az Azure Portalba beépített adatbázis-figyelési képességek vizsgálatához.

1. Tallózással keresse meg a **bérlők1 mt&lt;felhasználói&gt; ** kiszolgálót, és kattintson a **bérlők1** elemre a négy bérlővel tartalmazó adatbázis erőforrás-kihasználtságának megtekintéséhez. Minden bérlő a terhelésgenerátor szórványos nagy terhelésének van kitéve:

   ![bérlők figyelése1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   A DTU-kihasználtsági diagram szépen illusztrálja, hogy egy több-bérlős adatbázis támogathatja a kiszámíthatatlan számítási feladatok számos bérlő között. Ebben az esetben a terhelésgenerátor egy szórványos terhelést alkalmaz, amely körülbelül 30 DCO-k minden bérlő. Ez a terhelés egy 50 DTU-adatbázis 60%-os kihasználásának felel meg. A 60%-ot meghaladó csúcsok a terhelés egyidejű alkalmazásával történő terhelés eredménye.

2. Tallózással keresse meg a **bérlők1 mt&lt;felhasználói&gt; ** kiszolgálót, és kattintson a **salixsalsa** adatbázisra. Az erőforrás-kihasználtságot ezen az adatbázison láthatja, amely csak egy bérlőt tartalmaz.

   ![salixsalsa adatbázis](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

A terhelésgenerátor hasonló terhelést alkalmaz az egyes bérlőkre, függetlenül attól, hogy az egyes bérlők melyik adatbázisban vannak. Csak egy bérlő a **salixsalsa** adatbázisban, láthatja, hogy az adatbázis képes fenntartani egy sokkal nagyobb terhelést, mint az adatbázis több bérlővel. 

### <a name="resource-allocations-vary-by-workload"></a>Az erőforrás-allokációk munkaterhelésenként változnak

Előfordulhat, hogy egy több-bérlős adatbázis több erőforrást igényel a jó teljesítmény hez, mint egy egy-bérlős adatbázis, de nem mindig. Az erőforrások optimális elosztása a rendszer bérlőinek adott számítási feladatok jellemzőitől függ.

A terhelésgenerátor-parancsfájl által létrehozott számítási feladatok csak illusztrációs célokat szolgálnak.

## <a name="additional-resources"></a>További források

- A több-bérlős SaaS-alkalmazásokról a [több-bérlős SaaS-alkalmazások tervezési mintái című](saas-tenancy-app-design-patterns.md)témakörben olvashat.

- A rugalmas medencékről az:

  - [Több Azure SQL-adatbázis kezelése és méretezése rugalmas készletek használatával](sql-database-elastic-pool.md)
  - [Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése.
> - Az alkalmazást kiszolgálókról és adatbázisokról.
> - A bérlők le vannak képezve az adataikhoz a *katalógusban.*
> - Új bérlők kiépítése egy több-bérlős adatbázisba és egy-bérlős adatbázisba.
> - A készlet kihasználtságának megtekintése a bérlői tevékenység figyeléséhez.
> - Mintaforrások törlése a kapcsolódó számlázás leállításához.

Most próbálja meg a [kiépítés és katalógus bemutató](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Az Azure-ba való üzembe helyezés gombja."

