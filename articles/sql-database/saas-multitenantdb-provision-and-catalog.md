---
title: Provision in SaaS több-bérlős
description: Ismerje meg, hogyan építhet iúj bérlők et az Azure SQL Database több-bérlős SaaS-alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
ms.date: 09/24/2018
ms.openlocfilehash: 4ea18ee23d845b2d16209b23de14dc3cd70aaa59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133150"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Új bérlők kiépítése és katalogizálása egy SaaS-alkalmazásban egy szilánkos több-bérlős Azure SQL-adatbázis használatával

Ez a cikk ismerteti az új bérlők kiépítése és katalogizálása, egy *több-bérlős szilánkos adatbázis-modell* vagy minta.

Ez a cikk két fő részből áll:

- Az új bérlők kiépítési és katalogizálásának [koncepcionális megvitatása.](#goto_2_conceptual)

- [Oktatóanyag,](#goto_1_tutorial) amely kiemeli a PowerShell-parancsfájlkódot, amely megvalósítja a kiépítés és a katalogizálás.
  - Az oktatóanyag a Wingtip Jegyek SaaS-alkalmazást használja, amely a több-bérlős szilánkos adatbázis-mintához igazodik.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Adatbázis-minta

Ez a szakasz, valamint még néhány, hogy kövesse, a több-bérlős szilánkos adatbázis-minta fogalmait ismerteti.

Ebben a több-bérlős szilánkos modell, az egyes adatbázisokban lévő táblasémák tartalmaznak egy bérlői kulcsot a bérlői adatokat tároló táblák elsődleges kulcsában. A bérlői kulcs lehetővé teszi, hogy minden egyes adatbázis 0, 1 vagy sok bérlő tárolására. A szilánkos adatbázisok használata megkönnyíti az alkalmazási rendszer számára, hogy nagyon nagy számú bérlőt támogasson. Az egyik bérlő összes adata egy adatbázisban tárolódik. A bérlők nagy száma a sok szilánkos adatbázisok között oszlik meg. A katalógus-adatbázis tárolja az egyes bérlők adatbázishoz való hozzárendelését.

#### <a name="isolation-versus-lower-cost"></a>Elkülönítés az alacsonyabb költségek mellett

A bérlő, amely rendelkezik egy adatbázis önmagában élvezi az elkülönítés előnyeit. A bérlő lehet, hogy az adatbázis-visszaállítás egy korábbi időpontra anélkül, hogy korlátozza a más bérlőkre gyakorolt hatás. Az adatbázis teljesítménye behangolható az egy bérlőoptimalizáláshoz, ismét anélkül, hogy más bérlőkkel kompromisszumot kellene kötnie. A probléma az, hogy az elkülönítés többe kerül, mint az adatbázis más bérlőkkel való megosztása.

Ha egy új bérlő van kiépítve, megoszthatja az adatbázist más bérlőkkel, vagy elhelyezhető a saját új adatbázisába. Később meggondolhatja magát, és áthelyezheti az adatbázist a másik helyzetbe.

Adatbázisok több bérlőés egyetlen bérlők vannak keverve ugyanabban a SaaS-alkalmazásban, költség optimalizálása vagy elkülönítése az egyes bérlők számára.

   ![Szilánkos több-bérlős adatbázis-alkalmazás bérlői katalógussal](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Bérlői katalógus mintája

Ha két vagy több adatbázis, amely mindegyike legalább egy bérlőt tartalmaz, az alkalmazásnak rendelkeznie kell egy módja annak, hogy felderítse, melyik adatbázis tárolja az aktuális érdeklődésre számot tartó bérlőt. A katalógusadatbázis tárolja ezt a leképezést.

#### <a name="tenant-key"></a>Bérlői kulcs

Minden egyes bérlő, a Wingtip alkalmazás származtategy egyedi kulcsot, amely a bérlői kulcs. Az alkalmazás kinyeri a bérlő nevét a weblap URL-címéről. Az alkalmazás kiírja a nevet a kulcs beszerzéséhez. Az alkalmazás a kulni segítségével érheti el a katalógust. A katalógus összeveti a bérlőt tároló adatbázis adatait. Az alkalmazás az adatbázis adatait használja a csatlakozáshoz. Más bérlői kulcssémák is használhatók.

A katalógus használata lehetővé teszi, hogy a bérlői adatbázis nevét vagy helyét módosítani kell a kiépítés után az alkalmazás megszakítása nélkül. Egy több-bérlős adatbázis-modell, a katalógus elhelyezi a bérlő áthelyezése adatbázisok között.

#### <a name="tenant-metadata-beyond-location"></a>Bérlői metaadatok a helyen túl

A katalógus azt is jelezheti, hogy a bérlő karbantartás vagy egyéb műveletek miatt offline állapotban van-e. A katalógus pedig további bérlői vagy adatbázis-metaadatok tárolására is kiterjeszthető, például a következő elemekre:
- Az adatbázis szolgáltatási szintje vagy kiadása.
- Az adatbázisséma verziója.
- A bérlő neve és az SLA (szolgáltatásiszint-szerződés).
- Az alkalmazáskezelés, az ügyfélszolgálat vagy a devops-folyamatok engedélyezéséhez szükséges információk.

A katalógus is használható, hogy a bérlők közötti jelentéskészítés, séma kezelése és az adatok kivonat elemzési célokra.

### <a name="elastic-database-client-library"></a>Rugalmas adatbázis-ügyféltár

A Wingtip,a katalógus a *tenantcatalog* adatbázisban van megvalósítva. A *tenantcatalog* a [rugalmas adatbázis-ügyfélkódtár (EDCL)](sql-database-elastic-database-client-library.md)Shard Management szolgáltatásával jön létre. A tár lehetővé teszi, hogy egy alkalmazás hozzon létre, kezelje és használja az adatbázisban tárolt *szegmenstérképet.* A szegmenstérkép összeveti a bérlői kulcsot a szegmensével, ami azt jelenti, hogy a szilánkos adatbázis.

A bérlői kiépítés során AZ EDCL-függvények alkalmazásokból vagy PowerShell-parancsfájlokból is használhatók a shard térkép bejegyzéseinek létrehozásához. Később az EDCL függvények a megfelelő adatbázishoz való csatlakozáshoz használhatók. Az EDCL gyorsítótárazza a kapcsolatadatait, hogy minimalizálja a katalógusadatbázis forgalmát, és felgyorsítsa a kapcsolódási folyamatot.

> [!IMPORTANT]
> *Ne* szerkeszthesse az adatokat a katalógus adatbázisban közvetlen hozzáféréssel! A közvetlen frissítések nem támogatottak az adatsérülés magas kockázata miatt. Ehelyett csak EDCL API-k használatával szerkesztse a leképezési adatokat.

## <a name="tenant-provisioning-pattern"></a>Bérlői létesítési minta

#### <a name="checklist"></a>Ellenőrzőlista

Ha új bérlőt szeretne létesíteni egy meglévő megosztott adatbázisba, a megosztott adatbázisnak a következő kérdéseket kell feltennie:
- Van elég hely az új bérlőnek?
- Rendelkezik táblák az új bérlő höz szükséges referenciaadatokkal, vagy hozzáadhatóaz adatok?
- Rendelkezik az új bérlő alapséméjének megfelelő változatával?
- A megfelelő földrajzi helyen van, közel az új bérlőhöz?
- Ez a megfelelő szolgáltatási szint az új bérlő?

Ha azt szeretné, hogy az új bérlő a saját adatbázisában legyen elkülönítve, létrehozhatja azt, hogy megfeleljen a bérlő specifikációinak.

A kiépítés befejezése után regisztrálnia kell a bérlőt a katalógusban. Végül a bérlői leképezés hozzáadható a megfelelő szegmensre való hivatkozáshoz.

#### <a name="template-database"></a>Sablonadatbázis

Az adatbázis kiépítése SQL-parancsfájlok végrehajtásával, bacpac telepítésével vagy sablonadatbázis másolásával. A Wingtip-alkalmazások egy sablonadatbázist másolnak új bérlői adatbázisok létrehozásához.

Mint minden alkalmazás, wingtip fog fejlődni az idő múlásával. Időnként a Wingtip-hez módosítani kell az adatbázist. A módosítások a következő elemeket tartalmazhatják:
- Új vagy módosított séma.
- Új vagy módosított referenciaadatok.
- Az optimális alkalmazásteljesítmény biztosítása érdekében rendszeres adatbázis-karbantartási feladatok.

A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. Ahhoz, hogy ezek a módosítások a jövőbeli bérlői adatbázisokban legyenek, be kell építeni őket a kiépítési folyamatba. Ezt a kihívást a [sémakezelési oktatóanyag tovább](saas-tenancy-schema-management.md)vizsgálja.

#### <a name="scripts"></a>Scripts

A bérlői létesítési parancsfájlok ebben az oktatóanyagban támogatja a következő forgatókönyvek mindkét:
- Bérlő kiépítése egy más bérlőkkel megosztott meglévő adatbázisba.
- Bérlő kiépítése a saját adatbázisba.

A bérlői adatok at ezután inicializálják és regisztrálják a katalógusszegmens-térképen. A mintaalkalmazásban a több bérlőt tartalmazó adatbázisok általános nevet kapnak, például *tenants1* vagy *tenants2.* Egyetlen bérlőt tartalmazó adatbázisok a bérlő nevét kapják. A mintában használt konkrét elnevezési konvenciók nem kritikus részét képezik a mintának, mivel a katalógus használata lehetővé teszi, hogy bármilyen név legyen hozzárendelve az adatbázishoz.

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Az oktatóanyag kezdete

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Bérlő kiépítése egy több-bérlős adatbázisba
> * Bérlő kiépítése egy egybérlős adatbázisba
> * Bérlők kötegének kiépítése több- és egybérlős adatbázisokba egyaránt
> * Adatbázis és bérlői hozzárendelés regisztrálása katalógusban

#### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és feltárása című témakört.](saas-multitenantdb-get-started-deploy.md)

- A Wingtip parancsfájlok és a forráskód beszerezni:
    - A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub tárház.
    - A Wingtip parancsfájlok letöltésének és feloldásának lépéseit az [általános útmutatóban](saas-tenancy-wingtip-app-guidance-tips.md) talál.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Bérlő kiépítése más bérlőkkel *megosztott* adatbázisba

Ebben a szakaszban a PowerShell-parancsfájlok által végzett kiépítés főbb műveletek listáját láthatja. Ezután a PowerShell ISE hibakereső segítségével lépkeda szkriptek a műveletek et kódban.

#### <a name="major-actions-of-provisioning"></a>A céltartalékképzés főbb intézkedései

A kiépítési munkafolyamat legfontosabb elemei a következők:

- **Az új bérlői kulcs kiszámítása**: A bérlői kulcs bérlői névből való létrehozásához kivonatoló függvény t használ.
- **Ellenőrizze, hogy a bérlői kulcs már létezik-e**: A katalógus be van jelölve, hogy a kulcs még nincs regisztrálva.
- **Bérlő inicializálása az alapértelmezett bérlői adatbázisban:** A bérlői adatbázis frissül az új bérlői adatok hozzáadásához.
- **Bérlő regisztrálása a katalógusban:** Az új bérlői kulcs és a meglévő bérlők1 adatbázis közötti leképezés hozzáadódik a katalógushoz.
- **Adja hozzá a bérlő nevét egy katalógusbővítmény-táblához:** A helyszín neve hozzáadódik a bérlői táblához a katalógusban.  Ez a kiegészítés azt mutatja, hogy a katalógus adatbázis bővíthető további alkalmazásspecifikus adatok támogatásához.
- **Az új bérlő Események lapjának**megnyitása : A *Bushwillow Blues* események oldala megnyílik a böngészőben.

   ![események](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Hibakereső lépései

Annak megértéséhez, hogy a Wingtip alkalmazás hogyan valósítja meg az új bérlői kiépítést egy megosztott adatbázisban, adjon hozzá egy töréspontot, és lépjen végig a munkafolyamaton:

1. A *PowerShell ISE*, nyitott ... \\Tanulási modulok\\provisiontenants\\*demo-provisiontenants.ps1* és állítsa be a következő paramétereket:
   - **$TenantName** = **Bushwillow Blues**, a neve egy új helyszín.
   - **$VenueType** = **blues**, az egyik előre meghatározott helyszín típusok: blues, klasszikuszene, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci (kisbetűs, nincs tér).
   - **$DemoScenario** = **1**, a bérlő kiépítése egy megosztott adatbázisban más bérlőkkel.

2. Adjon hozzá egy töréspontot úgy, hogy a kurzort a 38-as vonalon, a következő sorban adja meg: *Új-bérlő '*, majd nyomja le **az F9 billentyűt.**

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Futtassa a parancsfájlt az **F5**billentyű lenyomásával.

4. Miután a parancsfájlok végrehajtása leáll a töréspontnál, nyomja le az **F11** billentyűt a kódba való lépéshez.

   ![Debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. A parancsfájl végrehajtásának nyomon követése az **F10** és **F11** **Hibakeresési** menü beállításaival, hogy átlépjen a hívott függvények en.

A PowerShell-parancsfájlok hibakereséséről a [PowerShell-parancsfájlok használatával és hibakeresésével](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)kapcsolatos tippek című témakörben olvashat bővebben.

## <a name="provision-a-tenant-in-its-own-database"></a>Bérlő kiépítése a *saját* adatbázisában

#### <a name="major-actions-of-provisioning"></a>A céltartalékképzés főbb intézkedései

A parancsfájl nyomon követése során a munkafolyamat legfontosabb elemei a következők:

- **Az új bérlői kulcs kiszámítása**: A bérlői kulcs bérlői névből való létrehozásához kivonatoló függvény t használ.
- **Ellenőrizze, hogy a bérlői kulcs már létezik-e**: A katalógus be van jelölve, hogy a kulcs még nincs regisztrálva.
- **Új bérlői adatbázis létrehozása**: Az adatbázis az *basetenantdb* adatbázis Erőforrás-kezelő sablon használatával történő másolásával jön létre.  Az új adatbázis neve a bérlő nevén alapul.
- **Adatbázis hozzáadása a katalógushoz:** Az új bérlői adatbázis shardként van regisztrálva a katalógusban.
- **Bérlő inicializálása az alapértelmezett bérlői adatbázisban:** A bérlői adatbázis frissül az új bérlői adatok hozzáadásához.
- **Bérlő regisztrálása a katalógusban:** Az új bérlőkulcs és a *sequoiasoccer* adatbázis közötti leképezés hozzáadódik a katalógushoz.
- **Bérlő neve hozzáadódik a katalógushoz:** A helyszín neve hozzáadódik a bérlők bővítmény táblához a katalógusban.
- **Az új bérlő Események lapjának**megnyitása : A *Sequoia Soccer* Events oldal megnyílik a böngészőben.

   ![események](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Hibakereső lépései

Most végigvezeti a parancsfájl folyamat létrehozásakor a bérlő saját adatbázisában:

1. Még mindig ... \\Tanulási modulok\\provisiontenants\\*Demo-provisiontenants.ps1* állítsa be a következő paramétereket:
   - **$TenantName** = **Sequoia Soccer**, a neve egy új helyszín.
   - **$VenueType** = **foci**, az egyik előre meghatározott helyszín típusok: blues, klasszikuszene, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci (kisbetűs, nincs tér).
   - **$DemoScenario** = **2**, a bérlő kiépítése a saját adatbázisba.

2. Új töréspont hozzáadásával a kurzort bárhová az 57-es sorba helyezze, a következő sorban: * & &nbsp;$PSScriptRoot\New-TenantAndDatabase '*, majd nyomja le **az F9 billentyűt.**

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Futtassa a parancsfájlt az **F5**billentyű lenyomásával.

4. Miután a parancsfájl végrehajtása leáll a töréspontnál, nyomja le az **F11** billentyűt a kódba való lépéshez.  Az **F10** és **az F11** segítségével lépjen át, és lépjen be a függvényekbe a végrehajtás nyomon követéséhez.

## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegének kiépítése

Ez a gyakorlat 17 bérlőből álló köteget tartalmaz. Javasoljuk, hogy a bérlők ezen kötegét a többi Wingtip Tickets oktatóanyag elindítása előtt üzembe helyezze, így több adatbázissal kell dolgoznia.

1. A *PowerShell ISE*, nyitott ... \\Tanulási modulok\\provisiontenants\\*demo-provisiontenants.ps1* és módosítsa a *$DemoScenario* paraméter 4:
   - **$DemoScenario** = **4**, a bérlők egy kötegének egy megosztott adatbázisba való kiépítéséhez.

2. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>A bérlők üzembe helyezett készletének ellenőrzése

Ebben a szakaszban a bérlők egy közös adatbázisba üzembe helyezett és a saját adatbázisokba üzembe helyezett bérlők vegyesen rendelkezik. Az Azure Portal on-k at a létrehozott adatbázisok vizsgálatához használható. Az [Azure Portalon](https://portal.azure.com)nyissa meg a **\<bérlők1-mt- USER\> ** kiszolgáló böngészve az SQL-kiszolgálók listájában.  Az **SQL-adatbázisok** listájának tartalmaznia kell a megosztott **bérlők1 adatbázist** és a saját adatbázisukban lévő bérlők adatbázisait:

   ![adatbázislista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Míg az Azure Portalon a bérlői adatbázisok, nem teszi lehetővé a bérlők a megosztott *adatbázisban.* A bérlők teljes listája a Wingtip **Események központ** weblapján és a katalógus böngészésével látható.

#### <a name="using-wingtip-tickets-events-hub-page"></a>A Wingtip Jegyek események központ lap használata

Nyissa meg az Események központ lapot a böngészőben (http:events.wingtip-mt.\<USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Katalógusadatbázis használata

A bérlők teljes listája és a megfelelő adatbázis minden elérhető a katalógusban. Egy SQL nézet biztosított, amely a bérlő nevét az adatbázis nevéhez csatlakoztatja. A nézet szépen mutatja a katalógusban tárolt metaadatok kiterjesztésének értékét.
- Az SQL nézet elérhető a tenantcatalog-adatbázisban.
- A bérlő neve a Bérlők táblában van tárolva.
- Az adatbázis neve a Shard Management táblákban tárolódik.

1. Az SQL Server Management Studio (SSMS) rendszerben csatlakozzon a bérlői kiszolgálóhoz a **katalógus-mt.\<USER\>.database.windows.net**, a Login = **developer**, and Password = P **\@ssword1**

    ![SSMS-kapcsolat párbeszédpanel](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Az SSMS objektumkezelőben keresse meg a *tenantcatalog-adatbázis* nézeteit.

3. Kattintson a jobb gombbal a *TenantsExtended* nézetre, és válassza **a Top 1000 sorok kiválasztása parancsot.** Vegye figyelembe a bérlő neve és az adatbázis közötti leképezésa a különböző bérlők számára.

    ![Kiterjesztettbérlők nézet az SSMS-ben](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Ez a szakasz más érdekes kiépítési mintákat tárgyalja.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Adatbázisok előzetes kiépítése rugalmas készletekben

Az előlétesítési minta kihasználja azt a tényt, hogy rugalmas készletek használata esetén a számlázás a készlet nem az adatbázisok. Így adatbázisokat lehet hozzáadni egy rugalmas készlet, mielőtt szükség lenne rájuk, anélkül, hogy többletköltséget. Ez az előzetes visioning jelentősen csökkenti a bérlő adatbázisba való kiépítéséhez szükséges időt. Az előre kiépített adatbázisok száma szükség szerint módosítható a várható kiépítési aránynak megfelelő puffer megtartása érdekében.

#### <a name="auto-provisioning"></a>Automatikus üzembe helyezés

Az automatikus kiépítési mintában egy dedikált létesítési szolgáltatás kiszolgálók, készletek és adatbázisok üzembe helyezéséhez szükség szerint automatikusan használatos. Ez az automatizálás magában foglalja az adatbázisok előzetes kiépítése rugalmas készletekben. És ha az adatbázisok levannak szerelve és törölve, a rugalmas készletekben létrehozott réseket a kiépítési szolgáltatás igény szerint kitöltheti.

Az ilyen típusú automatizált szolgáltatás lehet egyszerű vagy összetett. Például az automatizálás több földrajzi területen is kezelhető, és beállíthat georeplikációt a vész-helyreállítási. Az automatikus kiépítési minta, egy ügyfélalkalmazás vagy parancsfájl küld egy kiépítési kérelmet egy várólistába egy kiépítési szolgáltatás által feldolgozandó. A parancsfájl ezután lekérdezést észleléséhez befejezését. Ha előre kiépítést használ, a kérelmeket gyorsan kezeli, míg a háttérszolgáltatás egy helyettesítő adatbázis kiépítését kezeli.

## <a name="additional-resources"></a>További források

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
- [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egyetlen új bérlő kiépítése egy megosztott több-bérlős adatbázisba és saját adatbázisba
> * További bérlők kötegelt kiépítése
> * A bérlők kiépítésének részleteinek lépése és regisztrálása a katalógusba

Próbálja ki a [Teljesítményfigyelés oktatóanyagát.](saas-multitenantdb-performance-monitoring.md)

