---
title: "Azure SQL-adatbázist használó, szilánkos több-bérlős adatbázis SaaS-alkalmazás telepítése |} Microsoft Docs"
description: "Központi telepítése, és vizsgálja meg a szilánkos Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás, bemutatja a Szolgáltatottszoftver-minták Azure SQL adatbázis használatával."
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: cb55bf1f1c7eeb0fc7608aca8d70818b5e3e06c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Központi telepítése, és vizsgálja meg a szilánkos több-bérlős alkalmazás által használt Azure SQL adatbázis

Ebben az oktatóanyagban telepíti, és megismerkedhet SaaS több-bérlős adatbázis mintaalkalmazás Wingtip jegyek nevű. A Wingtip app tervezték, SaaS-forgatókönyvek végrehajtását egyszerűsítő szolgáltatásairól az Azure SQL Database megjelenítve.

Ebben az implementációban a Wingtips szilánkos több-bérlős adatbázis mintát alkalmaz. A horizontális van bérlői azonosítóval. Bérlői adatok adott adatbázis a bérlői azonosító értékek alapján történik. Függetlenül attól, hogy hány bérlők bármely adott adatbázis tartalmaz az összes adatbázis rendelkezésre több-bérlős abban az értelemben, hogy a tábla sémái tartalmazzák a bérlő azonosítója. 

Ebben a mintában adatbázis lehetővé teszi egy vagy több tenant minden shard vagy az adatbázis tárolja. Azzal, hogy több bérlő megoszthatók az egyes adatbázisok optimalizálható a legalacsonyabb költséget. Vagy azzal, hogy az egyes adatbázisok csak egy bérlő tárolására is optimalizálhatja az elkülönítés. A optimalizálási választott egymástól függetlenül az egyes konkrét bérlők is végezhető. A kiválasztott is végezhető el, amikor a bérlő először tárolja, vagy később bármikor módosíthatja a szem előtt. Az alkalmazás jól mindkét módszer tervezték.

#### <a name="app-deploys-quickly"></a>Gyorsan telepíti a alkalmazás

A központi telepítés következő szakasz biztosít a **az Azure telepítéséhez** gombra. A gomb megnyomott állapota esetén, a Wingtip alkalmazás teljes mértékben telepítve van egy 5 percen belül. A Wingtip app Azure felhőben fut, és az Azure SQL Database. A Wingtip telepítve van az Azure-előfizetéshez. Együttműködik az egyéni alkalmazás-összetevők teljes hozzáféréssel rendelkezik.

Az alkalmazás központi adatokkal három minta bérlők számára. A bérlők együtt egy több-bérlős adatbázisban tárolódnak.

Bárki, aki a C# és PowerShell forráskód letöltheti a Wingtip jegyekhez [a Github-adattár][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Ismerje meg, az oktatóanyag

> [!div class="checklist"]

> - Ügyfélszoftverek központi telepítése a Wingtip SaaS-alkalmazáshoz.
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

1. Kattintson a következő **az Azure telepítéséhez** gombra.
    - Az Azure-portálon nyílik meg a Wingtip jegyek SaaS központi telepítési sablont. A sablon csak két paramétert: egy új erőforráscsoportot, és a "user" érték, amely megkülönbözteti az alkalmazás más központi telepítésektől a központi telepítés nevét. A következő lépés részletes adatokat biztosít a paraméter értékét.
        - Ne feledje, a pontos értékeket használja, mert később szüksége lesz rájuk a konfigurációs fájl.

    [![A gomb telepítse az Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Adja meg a központi telepítés kötelező paraméter értékeit.

    > [!IMPORTANT]
    > Néhány hitelesítést és a kiszolgáló tűzfal beállításai nem biztonságosak szándékosan a bemutató megkönnyítése érdekében. Válasszon **hozzon létre egy új erőforráscsoportot**, és ne használja a meglévő erőforráscsoport-sablonok, kiszolgálóknak vagy készletek. Ne használja ezt az alkalmazást, vagy minden olyan erőforrásnál létrehozza, üzemi. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.

    Célszerű használni az erőforrás neve csak kisbetűket, számokat és kötőjeleket tartalmazhat.

    - A **erőforráscsoport** – Itt adhatja meg **hozzon létre új**, majd adjon meg egy **neve** az erőforráscsoport (kis-és nagybetűket).
        - Azt javasoljuk, hogy az erőforráscsoport neve szereplő összes betű kisbetűs lehet.
        - Azt javasoljuk, hogy a kötőjel, majd a monogramját számjegy követ hozzáfűzése: például *wingtip-af1*.
        - Válassza ki a **hely** a legördülő listából.
    - A **felhasználói** -javasoljuk, hogy egy rövid válassza **felhasználói** érték, például a saját monogramjával, valamint egy számjegy: például *af1*.

3. **Az alkalmazás üzembe helyezése**.

    - Kattintson a gombra kattintva elfogadja a feltételeket és kikötéseket.
    - Kattintson a **Purchase** (Vásárlás) gombra.

4. Központi telepítés állapotának figyelésére kattintva **értesítések**, vagyis a harang ikonra a keresőmezőbe jobbra. Telepíti a Wingtip alkalmazást körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Töltse le és a parancsfájlok feloldása

Amíg az alkalmazás telepítése van, töltse le az alkalmazás forrás kód és a felügyeleti parancsfájlok.

> [!IMPORTANT]
> Ha egy zip-fájl külső forrásból letöltött és kibontott végrehajtható tartalma (parancsfájlok, DLL-ek) blokkolhatja Windows. Ha a parancsfájlok kibontása zip-fájl, a következő lépésekkel tiltásának feloldása a .zip fájl kibontása előtt. A zip-fájlt blokkolásának feloldása biztosíthatja a parancsfájlok futtatásának engedélyezése.

1. Keresse meg a [a WingtipTicketsSaaS-MultiTenantDb github-tárház](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kattintson a **Klónozás vagy letöltési**.
3. Kattintson a **töltse le a ZIP-** , és mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-MultiTenantDb-master.zip** fájlt, és válassza ki **tulajdonságok**.
5. Az a **általános** lapon jelölje be **Unblock**, és kattintson a **alkalmaz**.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a találhatók a *... \\WingtipTicketsSaaS főkiszolgálós MultiTenantDb\\tanulási modulok\\*  mappa.

## <a name="update-the-configuration-file-for-this-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

Mielőtt futtatná a parancsfájlokat, állítsa be a *erőforráscsoport* és *felhasználói* értékei **UserConfig.psm1**. Ezeket a változókat beállítva a telepítés során megadott azonos értékeket.

1. Nyissa meg a \\Learning Modules\\*UserConfig.psm1* fájlt a *PowerShell ISE*-ben.
2. Frissítés *ResourceGroupName* és *neve* a központi telepítés (sorok 10-es és 11 csak) az adott értékkel.
3. Mentse a módosításokat.

Ebben a fájlban megadott értékek a parancsfájlok használják, ezért fontos, hogy pontosak. Ha újratelepíti az alkalmazást, győződjön meg arról, a különböző erőforrás csoportot és felhasználót értéket választ. Módosítsa a UserConfig az új értékekkel.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás különböző helyszíneket mutat be, például koncerttermeket, jazzklubokat és sportklubokat, amelyek különböző eseményeknek adnak otthont. Helyszínek események listában, és értékesítés jegyek egyszerűen regisztrálni a Wingtip platform, az ügyfelek (vagy bérlők). Minden helyszín kap egy testreszabott webappot, amelyen keresztül a többi bérlőtől függetlenül és elkülönítve listázhatják és kezelhetik a rendezvényeket, illetve értékesíthetik a jegyeket. A színfalak mindegyik bérlő adatokat alapértelmezés szerint a szilánkos több-bérlős adatbázis tárolja.

Egy központi **események Hub** mutató hivatkozásokat biztosít a bérlők a megadott központi telepítés.

1. Nyissa meg a *események Hub* a böngészőben:
    - http://events.Wingtip-MT.&lt;felhasználói&gt;. trafficmanager.net &nbsp; *(a központi telepítés felhasználói érték lecserélése.)*

    ![eseményközpont](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kattintson a **Fabrikam Jazz Club** elemre az *eseményközpontban*.

   ![Események](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

A bejövő kérelem, az alkalmazás által használt eloszlás [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Az események lapok, amelyek bérlői-specifikus, az URL-címben a bérlő nevét tartalmazza. Az URL-címeket is tartalmazzák az adott felhasználó érték, és ezt a formátumot követi:

- http://events.Wingtip-MT.&lt;felhasználói&gt;.trafficmanager.net/*fabrikamjazzclub*
 
Az események alkalmazás elemzi a bérlő nevét az URL-címről, és kivonatolják azt egy katalógus használata eléréséhez kulcs létrehozásához [shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md). A katalógus van leképezve a kulcsot a bérlő adatbázis helye. A **események Hub** a bérlők a katalógusban regisztrált sorolja fel. A **események Hub** kiterjesztett metaadatokat használ a katalógus összeállítani az URL-címeket és minden kapcsolódó a bérlő nevének beolvasására.

Éles környezetben, akkor általában kell létrehoznia a DNS CNAME-rekord [vállalati internetes tartomány pont](../traffic-manager/traffic-manager-point-internet-domain.md) a traffic manager-profil.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy az alkalmazás telepítését, most használatba vétel! A *bemutató-LoadGenerator* PowerShell-parancsfájl futtatása az egyes bérlők számára a munkaterhelés kezdődik. Sok Szolgáltatottszoftver-alkalmazásoknál valós terhelése jellemzően szórványos és előre nem látható. Szimulálása a terhelésének típusát, a kódgenerátor összes bérlők elosztott terhelésű hoz létre. A terhelés az egyes bérlők, véletlenszerű időközönként előforduló véletlenszerű felszakadásáig tartalmazza. Ahhoz, hogy a generátor legalább három vagy négy percig figyelése a betöltés előtt futtassa a legjobb merülnek fel, a betöltés minta több percet vesz igénybe.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\segédprogramok\\*bemutató-LoadGenerator.ps1* parancsfájl.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

> [!IMPORTANT]
> A *bemutató-LoadGenerator.ps1* parancsfájl megnyitja a terhelés generátor futtató másik PowerShell-munkamenetben. A betöltési generátor ebben a munkamenetben, amely meghívja a létrehozás-betöltési feladatok a háttérben, az egyes bérlők számára egy előtér feladatként fut.

Az előtér-feladat indítása után a feladat meghívása állapotban marad. A feladat később kiépített új tenantokat elindítja a további háttér feladatok.

A PowerShell-munkamenet lezárása leállítja az összes feladat.

Indítsa újra a terhelés generátor munkamenet eltérő értékeket szeretné használni kívánt. Ha igen, zárja be a PowerShell generációs munkamenetet, és futtassa újból a *bemutató-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Egy új bérlőt kiépíteni a szilánkos adatbázis

A kezdeti telepítés három minta bérlők magában foglalja a *Tenants1* adatbázis. Hozzon létre egy másik bérlői megtekintéséhez, hogy ez hatással van a telepített alkalmazás. Ebben a lépésben gyorsan létrehozhat egy új bérlőt.

1. Nyissa meg... \\Modules\ProvisionTenants tanulási\\*bemutató-ProvisionTenants.ps1* a a *PowerShell ISE*.
2. Nyomja le az **F5** a parancsfájl futtatásához (hagyja meg az alapértelmezett értékeket most).

   > [!NOTE]
   > Sok Wingtip jegyek Szolgáltatottszoftver-parancsfájlok használata *$PSScriptRoot* mappák, a navigációs engedélyezéséhez vagy más parancsfájlok meghívni vagy modulok importálásához. Ez a változó csak akkor, ha a parancsfájl végrehajtása egész billentyűkombináció lenyomásával történik **F5**.  Kiemelése és futtató egy kijelölést (**F8**) is hibákat eredményez, így nyomja le az **F5** Ha a parancsfájlok futtatásához.

A piros Maple Racing új bérlő hozzáadódik a *Tenants1* adatbázisról, és a katalógusban regisztrált. Az új bérlőhöz tartozó jegy értékesítési *események* webhely megnyitása a böngészőben:

![Új bérlő](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Frissítse a *események Hub* és az új tenanthoz most megjelenik a listában.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Egy új bérlőt a saját adatbázis kiépítése

A szilánkos több-bérlős modell lehetővé teszi, hogy válassza ki, hogy egy adatbázist egy új bérlő kiépítésének, hogy tartalmaz-e más bérlők, vagy saját adatbázisban a bérlő kiépítésének. A bérlő az adataik az adatokat a többi bérlő nem rendelkezik a saját előnyei. Az elkülönítési függetlenül a többi bérlő bérlőre teljesítményének kezelését teszi lehetővé. Azt is könnyebben állítsa vissza az adatokat a elkülönített bérlői egy korábbi időpontra. Akkor célszerű használni az ingyenes vagy rendszeres ügyfeleket egy több-bérlős adatbázis és a prémium szintű ügyfelek be az egyes adatbázisokat. Ha nagy mennyiségű, hogy minden egyes tartalmaznak csak egy bérlő adatbázisokat hoz létre, kezelheti azokat összes együttesen egy rugalmas készletben erőforrás költségek optimalizálása érdekében.  

Most azt kiépítése más bérlőket, ezúttal a saját adatbázisában.

1. A... \\Tanulási modulok\\ProvisionTenants\\*bemutató-ProvisionTenants.ps1*, módosítsa *$TenantName* való **fűzfa milyen**,  *$VenueType* való **dance** és *$Scenario* való **2**.

2. Nyomja le az **F5** újra futtatni a parancsfájlt.
    - Az F5 billentyűt nyomja meg az új tenanthoz külön adatbázisban látja el. Az adatbázis és a bérlő regisztrált a katalógusban. Majd a böngésző megnyitja a bérlő az események lapról.

   ![Fűzfa milyen események lap](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - A lap alján görgessen. Hiba a szalagcím látható az adatbázis nevét, amely a bérlői adatokat tárolja.

3. Az események központ frissítése, és a két új bérlő most megjelenik a listában.



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

Ha a terhelés generátor több percig futott, keressen a figyelési képességek a portálba épített adatbázis néhány elég mérési adat érhető el.

1. Keresse meg a **tenants1-mt&lt;felhasználói&gt;**  kiszolgáló, és kattintson **tenants1** adatbázis, amely négy bérlők benne lévő erőforrás-használat megtekintéséhez. Mindegyik bérlő szórványos nagy terhelés a terhelés generátor a rendszerben:

   ![a figyelő tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   A DTU-kihasználtság diagram szépen mutatja be, hogy egy több-bérlős adatbázis támogathat egy előre nem látható munkaterhelés sok bérlők között. Ebben az esetben a terhelés generátor alkalmazza nagyjából 30 Dtu szórványos terhelést minden bérlőhöz. Ez a betöltés csatlakozás 60 % kihasználtsági 50 DTU-adatbázis. Esetleges időszakos csúcsidőkbe 60 %-nál nagyobb terhelés egyszerre egynél több bérlő alkalmazott okoznak.

2. Keresse meg a **tenants1-mt&lt;felhasználói&gt;**  kiszolgálót, majd kattintson a **salixsalsa** adatbázis ezen az adatbázison, amely csak egy bérlő tartalmazza az erőforrás-használat megtekintéséhez.

   ![salixsalsa adatbázis](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

A betöltési generátor hasonló terhelés alkalmazza minden bérlőhöz, függetlenül attól, milyen adatbázis mindegyik bérlő van. A csak egy bérlővel a **salixsalsa** adatbázis, láthatja, hogy az adatbázis sikerült egy sokkal nagyobb terhelés fenntartása mint az adatbázis a több bérlő. 

> [!NOTE]
> A betöltési készítő által létrehozott terhelések vannak Szemléltetés céljából.  Az erőforrások vannak rendelve a több-bérlős és a bérlői egyetlen, és egy több-bérlős adatbázisban tárolhatja bérlő számait a munkaterhelések tényleges minták az alkalmazás függ.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]

> - A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás központi telepítése
> - A kiszolgálók és adatbázisok, az alkalmazás alkotó
> - Bérlők társítása az adataikhoz a *katalógus* használatával
> - A több-bérlős adatbázis és a bérlői egyetlen adatbázis új bérlők kiépíteni módjáról.
> - A bérlők tevékenységének megfigyelése a készlet kihasználtságának nyomon követésével
> - Mintaerőforrások törlése a kapcsolódó számlázások leállításához

Most a [rendelkezés bérlők oktatóanyag](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>További források

- A több-bérlős SaaS-alkalmazásokkal kapcsolatos tudnivalókért lásd a [*több-bérlős SaaS-alkalmazások tervezési mintáit*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)








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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

