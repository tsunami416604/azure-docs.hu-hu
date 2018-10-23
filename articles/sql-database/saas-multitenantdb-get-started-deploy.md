---
title: Azure SQL Database használó több-bérlős szilánkokra osztott adatbázis SaaS-alkalmazás üzembe helyezése |} A Microsoft Docs
description: Üzembe helyezése, és ismerje meg a horizontálisan skálázott Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazást, azt mutatja be, SaaS-minták az Azure SQL Database használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, stein
manager: craigg
ms.date: 04/02/2018
ms.openlocfilehash: ff09a5f09393ad642ddb2059b58bd69a17591aff
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352211"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Üzembe helyezése és megismerése a horizontálisan skálázott több-bérlős alkalmazás

Ebben az oktatóanyagban üzembe helyezése és a egy több-bérlős SaaS-mintaalkalmazás Wingtip Tickets nevű megismerése. A Wingtip Tickets alkalmazás az Azure SQL Database SaaS-forgatókönyveket végrehajtásának egyszerűsítő szolgáltatásairól bemutatására lett tervezve.

Ez a megvalósítás a Wingtip Tickets alkalmazás egy több-bérlős szilánkokra osztott adatbázis mintát alkalmaz. A horizontális skálázás a bérlőazonosító. Bérlői adatok egy adott adatbázishoz a bérlői azonosító értékeknek megfelelően vannak. 

Az adatbázis minta lehetővé teszi az egyes szegmensek vagy az adatbázis egy vagy több bérlő tárolja. Mindegyik adatbázis több bérlő közösen sablonkonfigurációkat optimalizálhatja legalacsonyabb költségek mellett. Vagy minden egyes adatbázis, tárolására kizárólag egy bérlővel rendelkező szerint optimalizálhatja az elkülönítést. Optimalizálás tetszőleges minden egyes bérlők egymástól függetlenül lehet tenni. A választott lehet tenni, amikor a bérlő első tárolja, és később is megváltoztatja döntését. Az alkalmazás jól mindkét módszer tervezték.

## <a name="app-deploys-quickly"></a>Alkalmazás üzembe gyorsan

Az alkalmazás az Azure-felhőben fut, és az Azure SQL Database. A következő központi telepítési szakasz biztosít a kék **üzembe helyezés az Azure** gombra. A gomb megnyomásakor az alkalmazás teljes mértékben telepítve van az Azure-előfizetéshez legalább öt perccel. Dolgozunk az egyes alkalmazás-összetevők teljes hozzáféréssel rendelkezik.

Az alkalmazás három minta bérlővel az adatok van telepítve. Egy több-bérlős adatbázissal együtt tárolja a bérlők számára.

A Wingtip Tickets bárki tölthetnek le a C# és PowerShell forráskód [a GitHub-adattár][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Ismerje meg, ez az oktatóanyag

> [!div class="checklist"]
> - A Wingtip Tickets SaaS-alkalmazás telepítésének módjáról.
> - Az alkalmazás forráskódjának és felügyeleti parancsfájlokat helyét.
> - A kiszolgálók és adatbázisok az alkalmazás alkotó.
> - Hogyan bérlők társítása az adataikhoz a *katalógus*.
> - Hogyan lehet új bérlő kiépítése.
> - Annak figyelése a bérlő az alkalmazásban.

Kapcsolódó oktatóanyag egy sorozat része érhető el a kezdeti üzembe helyezésre épülő. Az oktatóanyagok megismerése egy tartományt az SaaS-kialakításokat és felügyeleti mintákat. Az oktatóanyagok használatakor, arra biztatjuk haladjon végig megadott megtekintéséhez, hogyan valósíthatók meg a különböző SaaS-minták.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A legújabb Azure PowerShell telepítve van. További információkért lásd: [Ismerkedés az Azure PowerShell-lel][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Üzembe helyezése a Wingtip Tickets alkalmazás

### <a name="plan-the-names"></a>A nevek megtervezése

Ez a szakasz a lépések adjon meg egy *felhasználói* érték, amely annak biztosítása érdekében, erőforrásnevek globálisan egyedi, és a egy nevet a *erőforráscsoport* központi telepítés által létrehozott összes erőforrást tartalmazó az alkalmazás. Nevű személy *Reino Finley*, javasoljuk, hogy:
- *Felhasználó:* **af1**  *(saját monogramját és egy számjegy. Használjon egy másik értéket (pl. af2) másodszor az alkalmazás telepítésekor.)*
- *Erőforráscsoport:* **wingtip-mt-af1** *(wingtip-mt azt jelzi, hogy ez az a horizontálisan skálázott több-bérlős alkalmazást. A felhasználó neve af1 hozzáfűzése utal. az erőforráscsoport nevét a benne található erőforrást neveinek.)*

Most válassza ki a nevét, és írja le. 

### <a name="steps"></a>Lépések

1. Kattintson a következő kék **üzembe helyezés az Azure** gombra.
    - A Wingtip Tickets SaaS központi telepítési sablont nyílik az Azure Portalon.

    [![A gomb üzembe helyezése az Azure-bA.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Adja meg a szükséges paraméterértékeket a telepítés.

    > [!IMPORTANT]
    > Az ebben a bemutatóban ne használja minden olyan már meglévő erőforráscsoportokat, kiszolgálókat és készletek. Válassza a **hozzon létre egy új erőforráscsoportot**. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.
    > Ne használja az alkalmazást vagy az azzal létrehozott erőforrásokat éles környezetben. Egyes funkcióit, a hitelesítés és a kiszolgálói tűzfal beállításaiban olyan szándékosan nem biztonságos megkönnyítése érdekében az alábbiakban az alkalmazásban.

    - A **erőforráscsoport** – válassza ki **új létrehozása**, és adja meg egy **neve** az erőforráscsoport (megkülönbözteti a kis-és nagybetűket).
        - Válassza ki a **hely** a legördülő listából.
    - A **felhasználói** – javasoljuk, hogy egy rövid válassza **felhasználói** értéket.

1. **Az alkalmazás üzembe helyezése**.

    - Ide kattintva elfogadja a feltételeket és kikötéseket.
    - Kattintson a **Purchase** (Vásárlás) gombra.

1. Üzembe helyezés állapotának figyeléséhez kattintson **értesítések**, azaz a harang ikont jobb oldalán a keresőmezőbe. A Wingtip alkalmazás üzembe helyezése körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Töltse le és a felügyeleti parancsfájlokat feloldása

Az alkalmazás üzembe helyezése közben töltse le az alkalmazás forrás-kód és a felügyeleti parancsprogramokat.

> [!NOTE]
> Végrehajtható tartalom (szkriptek, DLL-ek) Windows előfordulhat, hogy blokkolja, amikor a zip-fájlokat egy külső forrásból letöltődnek és ki kell olvasni. A parancsfájlok kibontása zip-fájlból, az a következő lépések segítségével tiltásának feloldása a .zip fájl kibontása előtt. A blokkolás feloldásának a .zip-fájlt, biztosíthatja a parancsfájlok futtatását engedélyezi.

1. Keresse meg a [a WingtipTicketsSaaS-MultiTenantDb GitHub-adattárat](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kattintson a **Klónozás vagy letöltés**.
3. Kattintson a **ZIP letöltése** és mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-MultiTenantDb-master.zip** fájlt, és válassza ki **tulajdonságok**.
5. Az a **általános** lapon jelölje be **feloldása**, és kattintson a **alkalmaz**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a találhatók a *... \\WingtipTicketsSaaS főkiszolgálóval MultiTenantDb\\tanulási modulok\\*  mappát.

## <a name="update-the-configuration-file-for-this-deployment"></a>Az üzembe helyezés a konfigurációs fájl frissítése

Mielőtt futtatná a szkripteket, állítsa be a *erőforráscsoport* és *felhasználói* lévő értékeknek **UserConfig.psm1**. Ezeket a változókat beállítva üzembe helyezése során megadhatja ugyanazokat az értékeket.

1. Nyissa meg... \\Tanulási modulok\\*UserConfig.psm1* a a *PowerShell ISE-ben*.
2. Frissítés *ResourceGroupName* és *neve* jellemző egyedi értékekkel (a 10-es és 11 csak vonalakat) az üzembe helyezéshez.
3. Mentse a módosításokat.

Ebben a fájlban megadott értékeket használja a parancsfájlok, ezért fontos, hogy pontosak. Az alkalmazás újbóli telepítése, különböző értékeket felhasználók és az erőforráscsoport kell választania. Ezután frissítse a UserConfig.psm1 fájlt újra az új értékekkel.

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Wingtip alkalmazást a bérlők helyszínek. A helyszín concert hall, a sport club vagy bármely más helyre, amelyen az események is lehet. A helyszínek ügyfélként Wingtip regisztrálja, és a egy bérlőazonosító jön létre minden egyes helyszín. Minden egyes helyszín a Wingtip, a közelgő eseményeket sorolja fel, így a nyilvános interneten vásárolhatja meg az események jegyek.

Minden helyszín kap egy testreszabott webappot az események listázása és jegyek értékesítésére. Minden webalkalmazás függetlenül és elkülönítve a többi bérlő. Belsőleg az Azure SQL Database, az egyes bérlők adatai egy több-bérlős horizontálisan skálázott adatbázisban tárolt alapértelmezés szerint minden. Az összes adat a bérlői azonosító van megjelölve.

Egy központi **Eseményközpont** weblapra mutató hivatkozásokat biztosít az adott környezetben a bérlők számára. Kövesse az alábbi lépéseket tapasztalhat a **Eseményközpont** weblapon és a egy egyéni web app:

1. Nyissa meg a **Eseményközpont** a böngészőben:
    - http://events.wingtip-mt.&lt; felhasználó&gt;. trafficmanager.net &nbsp; *(cserélje le &lt;felhasználói&gt; az üzemelő példány felhasználói értékkel.)*

    ![eseményközpont](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kattintson a **Fabrikam Jazz Club** elemre az **eseményközpontban**.

   ![Események](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Bejövő kérelmek elosztását, a Wingtip alkalmazás által használt [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Az események lapról, az egyes bérlők számára az URL-CÍMÉT a bérlő nevét tartalmazza. Minden egyes URL-cím tartalmazza az adott felhasználó értékét is. Minden egyes URL-cím obeys a megjelenített formátum a következő lépésekkel:

- http://events.wingtip-mt.&lt; felhasználó&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Az események alkalmazás kielemzi a az URL-címből. A bérlő neve *fabrikamjazzclub* az előző példában URL-címben.
2. Az alkalmazás ezután kivonatolja a bérlőneve, hozzon létre egy kulcsot egy katalógus használatával eléréséhez [szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md).
3. Az alkalmazás megkeresi a kulcsot a katalógusban, és kéri le a bérlői adatbázis megfelelő helyét.
4. Az alkalmazás használ az adatok keresése és a egy adatbázis, amely tartalmazza az összes adatot a bérlő eléréséhez.

### <a name="events-hub"></a>Eseményközpont

1. A **Eseményközpont** a bérlőket a katalógusban, és a helyszínek regisztrált sorolja fel.
2. A **Eseményközpont** bővebb metaadatokat használ a katalógus lekérni a bérlő nevét társított minden egyes hozhatnak létre az URL-címek hozzárendelését.

Éles környezetben általában létrehozhat egy CNAME DNS-rekord [egy vállalati internetes tartomány](../traffic-manager/traffic-manager-point-internet-domain.md) a traffic manager-profilra.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítve van, állítsuk munkába való együttműködésre! A *Demo-LoadGenerator* PowerShell-szkript elindítja a számítási feladatok futtatása az egyes bérlők számára. A való életből vett terhelés számos SaaS-alkalmazásokkal általában szórványosak és kiszámíthatatlanok. Az ilyen típusú terhelés szimulálásához, a készítő összes bérlő között elosztott terhelésű eredményez. A terhelés, minden bérlő, véletlenszerű időközönként előforduló véletlenszerű adatlöketekkel tartalmazza. Ahhoz, hogy a generátor futtatásához legalább három vagy négy percet, mielőtt a terhelés figyelése a legjobb bontakozik ki, a betöltés minta néhány percet vesz igénybe.

1. Az a *PowerShell ISE-ben*, nyissa meg a... \\Tanulási modulok\\segédprogramok\\*Demo-LoadGenerator.ps1* parancsfájlt.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

A *Demo-LoadGenerator.ps1* parancsfájl megnyílik egy másik PowerShell-munkamenetben, ahol a terhelésgenerátor fut-e. A terhelésgenerátor ebben a munkamenetben, amely meghívja a háttérben futó betöltés generációs feladatok, az egyes bérlők számára az egyik előtérbeli feladatként fut.

Az előtér-feladat indítása után a feladat meghívása állapotban marad. A feladat elindítja a későbbiekben kiépített bérlőre további háttérben futó feladatok.

A PowerShell-munkamenet lezárása leállítja az összes feladat.

Előfordulhat, hogy szeretne indítani a generátor betöltési munkamenet különböző paraméterértékeket. Ha igen, zárja be a PowerShell generation-munkamenetet, és futtassa újból a *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Új bérlő kiépítése a szilánkos adatbázisba

A kezdeti telepítés magában foglalja a három minta bérlővel a *Tenants1* adatbázis. Most hozzon létre egy másik bérlőben, és vizsgálja meg a az üzembe helyezett alkalmazás gyakorolt hatását. Ebben a lépésben lenyom egy billentyűt a hozzon létre egy új bérlőt:

1. Nyissa meg... \\Tanulási modulok\\kiépítéssel és Katalogizálással\\*Demo-ProvisionTenants.ps1* a a *PowerShell ISE-ben*.
2. Nyomja meg **F5** (nem **F8**) a parancsfájl futtatásához (egyelőre módosítsa az alapértelmezett értékeket).

   > [!NOTE]
   > A PowerShell-parancsfájlok csak billentyű lenyomásával futtatnia kell a **F5** billentyű lenyomásával nem kulcs **F8** kiválasztott része a parancsfájl futtatásához. A probléma merült fel **F8** , hogy a *$PSScriptRoot* változót a rendszer nem értékeli ki. Ez a változó számos parancsfájlok mappák navigálhat, más szkriptek meghívása, vagy a modul importálása van szükség.

Az új bérlő Red Maple Racing adnak hozzá a *Tenants1* adatbázis- és a katalógusban regisztrált. Az új bérlő felhasználói jegy értékesítése **események** hely megnyílik a böngészőben:

![Új bérlő](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Frissítse a **Eseményközpont**, és az új bérlő mostantól megjelennek a listában.

## <a name="provision-a-new-tenant-in-its-own-database"></a>A saját adatbázisát egy új bérlő kiépítése

A horizontálisan skálázott több-bérlős modell segítségével döntse el, hogy egy új bérlő kiépítése az adatbázis tartalmazza a többi bérlő, vagy a saját adatbázisát. A bérlő egy elkülönített a saját adatbázisban a következő előnyöket élvezi:

- A bérlői adatbázis teljesítményét a többi bérlő igényekkel rendelkező veszélyeztetheti anélkül is felügyelhetők.
- Ha szükséges, az adatbázis is visszaállítható egy korábbi időpontra időben, mert nincs más bérlők befolyásolhat.

Választhatja azt is, ingyenes próba-ügyfelek vagy gazdaság ügyfelei, több-bérlős adatbázisok üzembe helyezhető. Minden egyes premium-bérlő elhelyezheti a saját dedikált adatbázisba. Ha nagy mennyiségű kizárólag egy bérlővel tartalmazó adatbázisok hoz létre, kezelheti azokat minden együttesen egy rugalmas készletben erőforrás a költségek optimalizálása érdekében.

Ezután azt egy másik új bérlő kiépítéséhez, ezúttal a saját adatbázisban:

1. A... \\Tanulási modulok\\kiépítéssel és Katalogizálással\\*Demo-ProvisionTenants.ps1*, módosítsa *$TenantName* való **fűzfa milyen**, *$VenueType* való **helyettünk** és *$Scenario* való **2**.

2. Nyomja meg **F5** újra futtatni a parancsprogramot.
    - Ez **F5** nyomja le az új bérlő külön adatbázisban építi ki. Az adatbázis és a bérlő regisztrált a katalógusban. Ezután a böngésző megnyitja az események lapról, a bérlő.

   ![Fűzfa milyen események lap](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Görgessen a lap alján. Hiba a szalagcím látható az adatbázis nevét, amely a bérlői adatok tárolja.

3. Frissítse a **Eseményközpont** és a két új bérlők most már megjelenik a listában.

## <a name="explore-the-servers-and-tenant-databases"></a>Ismerje meg a kiszolgálók és a bérlői adatbázisok

Most megnézzük egyes üzembe helyezett erőforrások:

1. Az a [az Azure portal](http://portal.azure.com), tallózással keresse meg a azon erőforráscsoportok listája. Nyissa meg az alkalmazás üzembe helyezésekor létrehozott erőforráscsoportot.

   ![erőforráscsoport](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kattintson a **katalógus-mt&lt;felhasználói&gt;**  kiszolgáló. A kiszolgáló nevű két adatbázist tartalmaz *tenantcatalog* és *basetenantdb*. A *basetenantdb* egy üres sablonnal adatbázis. Másolódik létrehozza az új adatbázist, hogy több bérlő vagy az egyetlen bérlő használják-e.

   ![katalóguskiszolgáló elemre](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Lépjen vissza az erőforráscsoportot, és válassza ki a *tenants1-mt* kiszolgálót, amely a bérlői adatbázisokat tartalmazza.
    - A tenants1-adatbázisa egy több-bérlős adatbázis, amelyben az eredeti három bérlők, valamint az adott hozzá, az első bérlő tárolja. 50 DTU Standard adatbázisként van konfigurálva.
    - A **salixsalsa** adatbázis tárolja a fűzfa milyen helyettünk helyszín, mint az egyetlen bérlő. Van konfigurálva, 50 Dtu-val a Standard edition-adatbázis alapértelmezés szerint.

   ![bérlők kiszolgáló](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Az adatbázis teljesítményének figyelése

Ha a terhelésgenerátor több percig futott, és tekintse meg az adatbázis-figyelési képességek az Azure portal beépített elég telemetriai adat érhető el.

1. Keresse meg a **tenants1-mt&lt;felhasználói&gt;**  kiszolgálót, majd kattintson **tenants1** megtekintéséhez, amely négy bérlővel rendelkezik, az adatbázis erőforrás-felhasználását. Minden bérlő van, a terhelésgenerátor időnkénti nagy mértékű terhelés vonatkoznak:

   ![a figyelő tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   A DTU-kihasználtság diagram szépen mutatja be, hogy egy több-bérlős adatbázis miként támogat egy kiszámíthatatlan mennyiségű számítási feladatot több bérlőre kiterjedő. Ebben az esetben a terhelésgenerátor alkalmazza szórványos terhelést nagyjából 30 dtu-k minden egyes bérlőhöz. Ez a terhelés 60 %-os kihasználtság 50 DTU adatbázis állapotnak felel meg. A maximális szám meghaladja a 60 %-os olyan terhelések egynél több bérlőhöz egyszerre eredményét.

2. Keresse meg a **tenants1-mt&lt;felhasználói&gt;**  kiszolgálót, majd kattintson a **salixsalsa** adatbázis. Láthatja az erőforrás-használat ezen az adatbázison, amely tartalmazza a kizárólag egy bérlővel.

   ![salixsalsa adatbázis](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

A terhelésgenerátor egy hasonló terhelést alkalmaz minden bérlő számára, függetlenül attól, mely az adatbázis minden egyes bérlő szerepel. A kizárólag egy bérlővel rendelkező a **salixsalsa** adatbázis, láthatja, hogy az adatbázis egy sokkal magasabb terhelés, mint az adatbázis több bérlő meghibásodást képes. 

### <a name="resource-allocations-vary-by-workload"></a>Erőforrás-hozzárendelések eltérőek lehetnek a munkaterhelés szerint

Néha egy több-bérlős adatbázisban több erőforrást a jó teljesítmény, mint egy egybérlős adatbázis igényel, de nem mindig. Az erőforrások optimális elosztását függ az adott számítási feladatok jellemzői a bérlők számára a rendszer.

A terhelést létrehozó szkripthez által létrehozott munkaterhelések vannak, csak illusztrációs célokat szolgálnak.

## <a name="additional-resources"></a>További források

- Több-bérlős SaaS-alkalmazások kapcsolatos további információkért lásd: [tervezési minták több-bérlős SaaS-alkalmazások](saas-tenancy-app-design-patterns.md).

- Rugalmas készletek kapcsolatos további információkért lásd:

  - [Rugalmas készletek kezelése és a több Azure SQL-adatbázisok horizontális Súgó](sql-database-elastic-pool.md)
  - [Horizontális felskálázás az Azure SQL Database segítségével](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - Hogyan helyezheti üzembe a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazását.
> - A kiszolgálók és adatbázisok alkotó az alkalmazást.
> - Bérlők társítása az adataikhoz a *katalógus*.
> - Hogyan építheti ki az új bérlőket egy több-bérlős adatbázis és egy egybérlős adatbázis.
> - Hogyan készlet kihasználtságának nyomon követésével figyelheti a bérlői tevékenységeket.
> - Hogyan törlése a kapcsolódó számlázások leállításához.

Most a [kiépítéssel és katalogizálással oktatóanyag](sql-database-saas-tutorial-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Gomb üzembe helyezéséhez az Azure-bA."

