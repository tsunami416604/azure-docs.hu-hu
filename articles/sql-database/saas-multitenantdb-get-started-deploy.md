---
title: Azure SQL-adatbázist használó, szilánkos több-bérlős adatbázis SaaS-alkalmazás telepítése |} Microsoft Docs
description: Központi telepítése, és vizsgálja meg a szilánkos Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás, bemutatja a Szolgáltatottszoftver-minták Azure SQL adatbázis használatával.
keywords: sql database-oktatóanyag
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: ac53443140b792d01147cdf22b81d0e6658fa429
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646456"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Központi telepítése, és vizsgálja meg a szilánkos több-bérlős alkalmazás által használt Azure SQL adatbázis

Ebben az oktatóanyagban telepíti, és egy példa több-bérlős SaaS-alkalmazáshoz Wingtip jegyek nevű vizsgálatát. A Wingtip jegyek app tervezték, SaaS-forgatókönyvek végrehajtását egyszerűsítő szolgáltatásairól az Azure SQL Database megjelenítve.

Ez a megvalósítás a Wingtip jegyek alkalmazás szilánkos több-bérlős adatbázis mintát alkalmaz. A horizontális van bérlői azonosítóval. Bérlői adatokat egy adott adatbázis a bérlői azonosító értékek alapján történik. 

Ebben a mintában adatbázis lehetővé teszi egy vagy több tenant minden shard vagy az adatbázis tárolja. Azzal, hogy több bérlő megoszthatók az egyes adatbázisok optimalizálható a legalacsonyabb költséget. Vagy azzal, hogy az egyes adatbázisok csak egy bérlő tárolására is optimalizálhatja az elkülönítés. A optimalizálási választott egymástól függetlenül az egyes konkrét bérlők is végezhető. A kiválasztott is végezhető el, amikor a bérlő először tárolja, vagy később bármikor módosíthatja a szem előtt. Az alkalmazás jól mindkét módszer tervezték.

#### <a name="app-deploys-quickly"></a>Gyorsan telepíti a alkalmazás

Az alkalmazás Azure felhőben fut, és az Azure SQL Database. A központi telepítés következő szakasz biztosít a kék **az Azure telepítéséhez** gombra. A gomb megnyomott állapota esetén, az alkalmazás teljes mértékben telepítve van az Azure-előfizetéshez öt percen belül. Együttműködik az egyéni alkalmazás-összetevők teljes hozzáféréssel rendelkezik.

Az alkalmazás központi adatokkal három minta bérlők számára. A bérlők együtt egy több-bérlős adatbázisban tárolódnak.

Bárki, aki a C# és PowerShell forráskód letöltheti a Wingtip jegyekhez [a GitHub-tárházban][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Ismerje meg, az oktatóanyag

> [!div class="checklist"]
> - Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS-alkalmazáshoz.
> - Honnan szerezhetők be az alkalmazás forráskódjához, és a parancsfájlok.
> - A kiszolgálók és adatbázisok, amelyek az alkalmazás alkotják.
> - Hogyan bérlők vannak leképezve az adataikat a *katalógus*.
> - Megtudhatja, hogyan lehet kiépíteni egy új bérlőt.
> - Megtudhatja, hogyan figyelheti a bérlői tevékenységeket az alkalmazásban.

Kapcsolódó oktatóanyag egy sorozat része érhető el, amely a kezdeti telepítés épül. Az oktatóanyagok egy tartományt a SaaS tervezési és felügyeleti minták felfedezése. Ha az oktatóanyagok keresztül dolgozik, hosszúan lépés, hogy hogyan vannak megvalósítva a különböző Szolgáltatottszoftver-minták a megadott parancsfájlokkal.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A legújabb Azure PowerShell telepítve van. További információkért lásd: [Ismerkedés az Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Központi telepítése a Wingtip jegyek alkalmazás

#### <a name="plan-the-names"></a>A nevek megtervezése

Ebben a szakaszban a lépések adjon meg egy *felhasználói* érték, amely gondoskodik arról, hogy erőforrásnevek megkülönböztetik a globálisan egyedi, és nevezze el a *erőforráscsoport* hozta létre a központi telepítés összes erőforrását tartalmazó az alkalmazás. A személy nevű *Reino Finley*, javasoljuk, hogy:
- *Felhasználó:* **af1***(saját monogramja, valamint egy számjegy. Használjon egy másik értéket (pl. af2) Ha telepít központilag az alkalmazást még egyszer.)*
- *Erőforráscsoport:* **wingtip-mt-af1** *(wingtip-mt azt jelzi, ez a szilánkos több-bérlős alkalmazást. A felhasználó nevét af1 fűznek ad eredményül a benne található erőforrások nevét az erőforráscsoport neve.)*

Most válassza ki a nevét, és írja le. 

#### <a name="steps"></a>Lépések

1. Kattintson a következő kék **az Azure telepítéséhez** gombra.
    - Az Azure-portálon nyílik meg a Wingtip jegyek SaaS központi telepítési sablont.

    [![A gomb telepítse az Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Adja meg a szükséges paraméterértékeket a telepítés.

    > [!IMPORTANT]
    > Az ebben a bemutatóban ne használja a már meglévő erőforráscsoport-sablonok, kiszolgálóknak vagy készletek. Válassza a **hozzon létre egy új erőforráscsoportot**. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.
    > Ne használja ezt az alkalmazást, vagy minden olyan erőforrásnál létrehozza, üzemi. Hitelesítés és a kiszolgáló tűzfal beállításait, az egyes funkcióit nem biztonságosak szándékosan lehetővé teszi a bemutató az alkalmazásban.

    - A **erőforráscsoport** – Itt adhatja meg **hozzon létre új**, majd adjon meg egy **neve** az erőforráscsoport (kis-és nagybetűket).
        - Válassza ki a **hely** a legördülő listából.
    - A **felhasználói** -javasoljuk, hogy egy rövid válassza **felhasználói** érték.

1. **Az alkalmazás üzembe helyezése**.

    - Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    - Kattintson a **Purchase** (Vásárlás) gombra.

1. Központi telepítés állapotának figyelésére kattintva **értesítések**, vagyis a harang ikonra a keresőmezőbe jobbra. Telepíti a Wingtip alkalmazást körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Töltse le és a parancsfájlok feloldása

Amíg az alkalmazás telepítése van, töltse le az alkalmazás forrás kód és a felügyeleti parancsfájlok.

> [!NOTE]
> Ha egy zip-fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájl, a következő lépésekkel tiltásának feloldása a .zip fájl kibontása előtt. A zip-fájlt letiltásuk feloldására, biztosíthatja a parancsfájlok futtatásának engedélyezése.

1. Keresse meg a [a WingtipTicketsSaaS-MultiTenantDb GitHub-tárház](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kattintson a **Klónozás vagy letöltési**.
3. Kattintson a **töltse le a ZIP-** , és mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-MultiTenantDb-master.zip** fájlt, és válassza ki **tulajdonságok**.
5. Az a **általános** lapon jelölje be **Unblock**, és kattintson a **alkalmaz**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a találhatók a *... \\WingtipTicketsSaaS főkiszolgálós MultiTenantDb\\tanulási modulok\\*  mappa.

## <a name="update-the-configuration-file-for-this-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Mielőtt futtatná a parancsfájlokat, állítsa be a *erőforráscsoport* és *felhasználói* értékei **UserConfig.psm1**. Ezeket a változókat beállítva a telepítés során megadott azonos értékeket.

1. Nyissa meg... \\Tanulási modulok\\*UserConfig.psm1* a a *PowerShell ISE*.
2. Frissítés *ResourceGroupName* és *neve* a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
3. Mentse a módosításokat.

Ebben a fájlban megadott értékek használják a parancsfájlok, ezért fontos, hogy pontosak. Újratelepíti az alkalmazást, ha a felhasználó- és erőforrás kell válasszon eltérő értéket. Ezután frissítse a UserConfig.psm1 fájlt újból az új értékekkel.

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Wingtip alkalmazásban a bérlők helyszínek. Egy helyszínére lehet energiaoptimalizálást egyszerre hall, egy sport club vagy bármely más helyre, amelyen az eseményeket. Az ügyfelek a Wingtip regisztrálható a helyszínek, és egyes helyszínekkel létrejön egy bérlői azonosító. A nyilvános vásárolhatja meg a jegyektől azokhoz az eseményekhez, egyes helyszínekkel listák Wingtip, a jövőbeni események.

Egyes helyszínekkel lekérdezi egy személyre szabott webalkalmazást a listában azok az események és eladásra a jegyektől. Minden webalkalmazás független és elkülönítése a bérlőktől. Belsőleg, az Azure SQL Database, minden az adatok az egyes bérlők számára alapértelmezés szerint egy több-bérlős szilánkos adatbázisban tárolja. Minden adat címkéje a bérlő azonosítója.

Egy központi **események Hub** weblap mutató hivatkozásokat biztosít a bérlők számára az adott környezetben. Az alábbi lépések segítségével tapasztalhat a **események Hub** weblap és egy adott webalkalmazás:

1. Nyissa meg a **események Hub** a böngészőben:
    - http://events.wingtip-mt.&lt; felhasználói&gt;. trafficmanager.net &nbsp; *(csere &lt;felhasználói&gt; a központi telepítés felhasználói értékkel.)*

    ![eseményközpont](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kattintson a **Fabrikam Jazz Club** elemre az **eseményközpontban**.

   ![Események](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A bejövő kérelmek terjesztési szabályozására, a Wingtip alkalmazás használ [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Az események lapról, az egyes bérlők számára a bérlő neve szerepel az URL-CÍMÉT. Minden egyes URL-címet is az adott felhasználó érték. Minden egyes URL-cím obeys látható formátuma a következő lépések segítségével:

- http://events.wingtip-mt.&lt; felhasználói&gt;.trafficmanager.net/*fabrikamjazzclub*

1. Az események alkalmazás elemzi a bérlő nevét az URL-címről. A bérlő neve *fabrikamjazzclub* az előző példa URL-címben.
2. Az alkalmazás ezután csak a bérlő nevét, a katalógus használatával eléréséhez kulcs létrehozásához [shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md).
3. Az alkalmazás a katalógust a kulcs talál, és beszerzi a bérlő adatbázis megfelelő helyét.
4. Az alkalmazás az adatok található, és a bérlőhöz tartozó összes adatot tartalmazó egy adatbázist használ.

#### <a name="events-hub"></a>Események Hub

1. A **események Hub** a bérlők a katalógusban, és azok helyszínek regisztrált sorolja fel.
2. A **események Hub** kiterjesztett metaadatokat használ a katalógus összeállítani az URL-címeket és minden kapcsolódó a bérlő nevének beolvasására.

Éles környezetben általában létrehozhat egy CNAME DNS-rekord [vállalati internetes tartomány pont](../traffic-manager/traffic-manager-point-internet-domain.md) a traffic manager-profil.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítését, most használatba vétel! A *bemutató-LoadGenerator* PowerShell-parancsfájl futtatása az egyes bérlők számára a munkaterhelés kezdődik. Sok Szolgáltatottszoftver-alkalmazásoknál valós terhelése jellemzően szórványos és előre nem látható. Szimulálása a terhelésének típusát, a kódgenerátor összes bérlők elosztott terhelésű hoz létre. A terhelés az egyes bérlők, véletlenszerű időközönként előforduló véletlenszerű felszakadásáig tartalmazza. Ahhoz, hogy a generátor legalább három vagy négy percig figyelése a betöltés előtt futtassa a legjobb merülnek fel, a betöltés minta több percet vesz igénybe.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\segédprogramok\\*bemutató-LoadGenerator.ps1* parancsfájl.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

A *bemutató-LoadGenerator.ps1* parancsfájl megnyitja a terhelés generátor futtató másik PowerShell-munkamenetben. A betöltési generátor ebben a munkamenetben, amely meghívja a létrehozás-betöltési feladatok a háttérben, az egyes bérlők számára egy előtér feladatként fut.

Az előtér-feladat indítása után a feladat meghívása állapotban marad. A feladat később kiépített új tenantokat elindítja a további háttér feladatok.

A PowerShell-munkamenet lezárása leállítja az összes feladat.

Indítsa újra a terhelés generátor munkamenet eltérő értékeket szeretné használni kívánt. Ha igen, zárja be a PowerShell generációs munkamenetet, és futtassa újból a *bemutató-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Egy új bérlőt kiépíteni a szilánkos adatbázis

A kezdeti telepítés három minta bérlők magában foglalja a *Tenants1* adatbázis. Most hozzon létre egy másik bérlőt, és tekintse meg a telepített alkalmazás kifejtett hatását. Ebben a lépésben lenyomja az egyik kulcsról a hozzon létre egy új bérlőt:

1. Nyissa meg... \\Tanulási modulok\\biztosítása és a katalógus\\*bemutató-ProvisionTenants.ps1* a a *PowerShell ISE*.
2. Nyomja le az **F5** (nem **F8**) a parancsfájl futtatásához (hagyja meg az alapértelmezett értékeket most).

   > [!NOTE]
   > A PowerShell-parancsfájlok csak billentyűkombináció lenyomásával futtatnia kell a **F5** kulcs, nem billentyűkombináció lenyomásával **F8** kijelölt része a parancsfájl futtatásához. A probléma **F8** , hogy a *$PSScriptRoot* változót a rendszer nem értékeli ki. Ez a változó mappák megnyitása számos parancsfájlok meghívása egyéb parancsfájlokat vagy modul importálása van szükség.

A piros Maple Racing új bérlő hozzáadódik a *Tenants1* adatbázisról, és a katalógusban regisztrált. Az új bérlőhöz tartozó jegy értékesítési **események** webhely megnyitása a böngészőben:

![Új bérlő](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Frissítse a **események Hub**, és az új tenanthoz most megjelenik a listában.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Egy új bérlőt a saját adatbázis kiépítése

A szilánkos több-bérlős modell lehetővé teszi, hogy egy új bérlő kiépítésének más bérlők tartalmazó adatbázis, vagy saját adatbázis-e. A bérlők a saját adatbázis elkülönített élvez a következő előnyöket biztosítja:
- A bérlő adatbázis teljesítményének hibát okoz a többi bérlő igényekkel nélkül is felügyelhetők.
- Ha szükséges, az adatbázis állítható vissza egy korábbi állapotba időben, mert nincs más bérlők befolyásolhat.

Akkor célszerű használni az ingyenes ügyfelek vagy gazdaság ügyfelek kerüljenek a több-bérlős adatbázisok. Minden premium bérlővel saját dedikált adatbázisba ütemezésbe helyezheti. Ha csak egy bérlő tartalmazó adatbázisok rengeteg hoz létre, kezelheti azokat az összes együttesen erőforrás költségek optimalizálása rugalmas készlethez.

A következő azt kiépítése más bérlőket, a saját adatbázisában most:

1. A... \\Tanulási modulok\\biztosítása és a katalógus\\*bemutató-ProvisionTenants.ps1*, módosítsa *$TenantName* való **fűzfa milyen**, *$VenueType* való **dance** és *$Scenario* való **2**.

2. Nyomja le az **F5** újra futtatni a parancsfájlt.
    - Ez **F5** nyomja le az látja el az új tenanthoz külön adatbázisban. Az adatbázis és a bérlő regisztrált a katalógusban. Majd a böngésző megnyitja a bérlő az események lapról.

   ![Fűzfa milyen események lap](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - A lap alján görgessen. Hiba a szalagcím látható az adatbázis nevét, amely a bérlői adatokat tárolja.

3. Frissítse a **események Hub** és a két új bérlő most megjelenik a listában.

## <a name="explore-the-servers-and-tenant-databases"></a>Megismerkedhet a kiszolgálók és adatbázisok bérlői

Most már úgy tekintünk, a telepített erőforrások:

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az erőforráscsoportok listáját. Nyissa meg az alkalmazás üzembe helyezésekor létrehozott erőforráscsoportot.

   ![erőforráscsoport](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kattintson a **katalógus-mt&lt;felhasználói&gt;**  kiszolgáló. A kiszolgáló nevű két adatbázist tartalmaz *tenantcatalog* és *basetenantdb*. A *basetenantdb* egy üres sablont adatbázis. Másolás hozhat létre új bérlő adatbázist, hogy több bérlő vagy csak egy bérlő használja-e.

   ![katalóguskiszolgáló elemre](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Lépjen vissza az erőforráscsoportot, és válassza ki a *tenants1-mt* kiszolgáló, amely a bérlői adatbázisok.
    - A tenants1 adatbázisa egy több-bérlős az eredeti három bérlők, valamint az első bérlői adott hozzá, amelyek tároló adatbázis. 50 DTU normál adatbázisként van konfigurálva.
    - A **salixsalsa** adatbázis tárolja, az csak bérlői a fűzfa milyen iskolabál helyszínére. Ez egy Standard edition adatbázisként 50 Dtu az alapértelmezés szerint van konfigurálva.

   ![bérlők kiszolgáló](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Az adatbázis teljesítményének figyelése

Ha a terhelés generátor több percig futott, lásd: a figyelési képességek az Azure portál beépített adatbázis elég mérési adat érhető el.

1. Keresse meg a **tenants1-mt&lt;felhasználói&gt;**  kiszolgáló, és kattintson **tenants1** adatbázis, amely négy bérlők benne lévő erőforrás-használat megtekintéséhez. Mindegyik bérlő szórványos nagy terhelés a terhelés generátor a rendszerben:

   ![a figyelő tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   A DTU-kihasználtság diagram szépen mutatja be, hogy egy több-bérlős adatbázis támogathat egy előre nem látható munkaterhelés sok bérlők között. Ebben az esetben a terhelés generátor alkalmazza nagyjából 30 Dtu szórványos terhelést minden bérlőhöz. Ez a betöltés csatlakozás 60 % kihasználtsági 50 DTU-adatbázis. Esetleges időszakos csúcsidőkbe 60 %-nál nagyobb terhelés egyszerre egynél több bérlő alkalmazott okoznak.

2. Keresse meg a **tenants1-mt&lt;felhasználói&gt;**  kiszolgálót, majd kattintson a **salixsalsa** adatbázis. Az erőforrás-használat ezen az adatbázison, amely tartalmazza a csak egy bérlő tekintheti meg.

   ![salixsalsa adatbázis](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

A betöltési generátor hasonló terhelés alkalmazza minden bérlőhöz, függetlenül attól, milyen adatbázis mindegyik bérlő van. A csak egy bérlővel a **salixsalsa** adatbázis, láthatja, hogy az adatbázis sikerült egy sokkal nagyobb terhelés fenntartása mint az adatbázis a több bérlő. 

#### <a name="resource-allocations-vary-by-workload"></a>Erőforrás-hozzárendelések eltérők lehetnek a munkaterhelés szerint

Egy több-bérlős adatbázis lehet szükség további erőforrásokat a jó teljesítmény, mint egyetlen-bérlő adatbázis, de nem minden esetben. Az optimális erőforrások elosztását a függ az adott munkaterhelés jellemzőit a bérlők számára a rendszer.

A betöltési generátor parancsfájl által létrehozott munkaterhelések vannak csak illusztrációs célokat szolgálnak.

## <a name="additional-resources"></a>További források

- Több-bérlős SaaS-alkalmazásokhoz, lásd: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

- Rugalmas készletek kapcsolatos információkért lásd:
    - [Rugalmas készletek kezelése, és több Azure SQL-adatbázisok méretezése](sql-database-elastic-pool.md)
    - [Horizontális felskálázás az Azure SQL Database segítségével](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás.
> - A kiszolgálók és adatbázisok alkotó az alkalmazást.
> - Bérlők vannak leképezve az adataikat a *katalógus*.
> - A több-bérlős adatbázis és a bérlői egyetlen adatbázis új bérlők kiépíteni módjáról.
> - Hogyan bérlői figyelése készlet kihasználtságának megtekintéséhez.
> - Hogyan mintaerőforrásokat leállításához kapcsolódó számlázási törlése.

Most a [biztosítása és a katalógus oktatóanyag](sql-database-saas-tutorial-provision-and-catalog.md).


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Az Azure központi gombra."

