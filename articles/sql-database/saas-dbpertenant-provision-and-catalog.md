---
title: Új bérlők kiépítése egy több-bérlős alkalmazásban
description: Ismerje meg, hogyan építhet iúj bérlők et az Azure SQL Database több-bérlős SaaS-alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132108"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Ismerje meg, hogyan építhet új bérlőket, és hogyan regisztrálhatja őket a katalógusban

Ebben az oktatóanyagban megtudhatja, hogyan építheti ki és katkandálhatja az SaaS-mintákat. Azt is megtudhatja, hogyan valósítják meg a Wingtip jegyek SaaS-adatbázis-bérlőnként alkalmazás. Új bérlői adatbázisokat hoz létre és inicializál, és regisztrálja őket az alkalmazás bérlői katalógusában. A katalógus egy adatbázis, amely fenntartja a leképezés t a SaaS-alkalmazás számos bérlője és az adatok között. A katalógus fontos szerepet játszik az alkalmazás- és felügyeleti kérelmek nek a megfelelő adatbázisba történő irányításában.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Egyetlen új bérlő kiépítése.
> * További bérlők kötegének kiépítése.


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis-bérlőnként alkalmazás telepítve van. Ha kevesebb mint öt perc alatt szeretné üzembe helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis-bérlőnkénti alkalmazás telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Bevezetés a SaaS katalógus mintába

Egy adatbázis-támogatott több-bérlős SaaS-alkalmazásban fontos tudni, hogy hol tárolja az egyes bérlők adatait. A SaaS-katalógus mintában egy katalógus-adatbázis tartjuk az egyes bérlők és az adatbázis közötti leképezés, amelyben az adatok tárolása. Ez a minta akkor érvényes, ha a bérlői adatok több adatbázis között oszlanak meg.

Minden bérlő azonosítja egy kulcs a katalógusban, amely le van képezve az adatbázis helyét. A Wingtip Jegyek alkalmazásban a kulcs a bérlő nevének kivonatából jön létre. Ez a séma lehetővé teszi, hogy az alkalmazás az alkalmazás URL-címében szereplő bérlői névből hozd létre a kulcsot. Más bérlői kulcssémák is használhatók.

A katalógus lehetővé teszi az adatbázis nevének vagy helyének módosítását, minimális hatással az alkalmazásra. A több-bérlős adatbázis-modell, ez a képesség is alkalmazkodik a bérlő áthelyezése az adatbázisok között. A katalógus is használható annak jelzésére, hogy egy bérlő vagy adatbázis offline karbantartás vagy egyéb műveletek. Ezt a lehetőséget az [egybérlős visszaállítása oktatóanyag](saas-dbpertenant-restore-single-tenant.md)vizsgálja.

A katalógus további bérlői vagy adatbázis-metaadatokat is tárolhat, például a sémaverzióját, a szolgáltatási csomagot vagy a bérlőknek kínált SL-eket. A katalógus más olyan információkat is tárolhat, amelyek lehetővé teszik az alkalmazáskezelést, az ügyfélszolgálatot vagy a DevOps-t.

A SaaS-alkalmazáson túl a katalógus engedélyezheti az adatbázis-eszközöket. A Wingtip jegyek SaaS-adatbázis-bérlőnként minta, a katalógus segítségével lehetővé teszi a több-bérlőlekérdezés, amely az [Ad hoc jelentési oktatóanyag](saas-tenancy-cross-tenant-reporting.md)vizsgálja. Az adatbázisközi feladatkezelést a [sémakezelési](saas-tenancy-schema-management.md) és [a bérlői elemzési](saas-tenancy-tenant-analytics.md) oktatóanyagok ismertetik.

A Wingtip jegyek SaaS-mintákban a katalógus a [Rugalmas adatbázis ügyfélkódtár (EDCL)](sql-database-elastic-database-client-library.md)Shard Management funkcióival valósítható meg. Az EDCL Java és .NET framework nyelven érhető el. Az EDCL lehetővé teszi, hogy egy alkalmazás adatbázis-alapú shard térkép létrehozása, kezelése és használata.

A szegmenstérkép a szegmensek (adatbázisok) listáját, valamint a kulcsok (bérlők) és a szegmensek közötti leképezéslistáját tartalmazza. AZ EDCL-függvények a bérlői kiépítés során használatosak a shard térkép bejegyzéseinek létrehozásához. Az alkalmazások futásidőben használják őket a megfelelő adatbázishoz való csatlakozáshoz. Az EDCL gyorsítótárazza a kapcsolatadatait a katalógusadatbázis forgalmának minimalizálása és az alkalmazás felgyorsítása érdekében.

> [!IMPORTANT]
> A leképezési adatok elérhetők a katalógusadatbázisban, de *nem szerkeszthetik.* A leképezési adatokat csak rugalmas adatbázis-ügyfélügyfél API-k használatával szerkeszti. A leképezési adatok közvetlen kezelése a katalógus sérülését kockáztatja, és nem támogatott.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Bevezetés a SaaS-kiépítési mintába

Amikor egy új bérlőt ad hozzá egy SaaS-alkalmazás, amely egy bérlős adatbázis-modellt használ, ki kell építenie egy új bérlői adatbázist. Az adatbázist a megfelelő hely- és szolgáltatásszinten kell létrehozni. Azt is be kell icializálni a megfelelő séma és a referencia-adatokat. És regisztrálni kell a katalógusban a megfelelő bérlői kulcs alatt.

Az adatbázis-kiépítés különböző megközelítései használhatók. SQL-parancsfájlokat futtathat, bacpac-ot telepíthet, vagy sablonadatbázist másolhat.

Az adatbázis-kiépítésnek a sémakezelési stratégia részét kell, hogy legyen. Győződjön meg arról, hogy az új adatbázisok ki vannak építve a legújabb sémával. Ezt a követelményt a [Sémakezelési oktatóanyag vizsgálja.](saas-tenancy-schema-management.md)

A Wingtip jegyek adatbázis-bérlőalkalmazás új bérlők et rendel el egy _basetenantdb_nevű sablonadatbázis másolásával, amely a katalóguskiszolgálón van telepítve. A kiépítés egy regisztrációs élmény részeként integrálható az alkalmazásba. Azt is támogatni lehet kapcsolat nélküli parancsfájlok használatával. Ez az oktatóanyag a PowerShell használatával történő kiépítést ismerteti.

A kiépítési parancsfájlok másolja a _basetenantdb_ adatbázist egy új bérlői adatbázis létrehozásához egy rugalmas készletben. A bérlői adatbázis jön létre a bérlői kiszolgáló az _újbérlő_ DNS-alias. Ez az alias az új bérlők kiépítéséhez használt kiszolgálóra mutató hivatkozást tart fenn, és frissül, hogy a vész-helyreállítási oktatóanyagokban egy helyreállítási bérlői kiszolgálóra mutasson ([dr georestore ,](saas-dbpertenant-dr-geo-restore.md) [DR georeplikáció használatával).](saas-dbpertenant-dr-geo-replication.md) A parancsfájlok majd inicializálja az adatbázist a bérlő-specifikus információkat, és regisztrálja azt a katalógus shard térképen. A bérlői adatbázisok a bérlő neve alapján kapnak neveket. Ez az elnevezési séma nem kritikus része a minta. A katalógus leképezi a bérlői kulcsot az adatbázis nevéhez, így bármilyen elnevezési konvenció használható.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis-bérlőnkénti alkalmazásparancsfájlok beszereznie

A Wingtip Jegyek SaaS-parancsfájlok és az alkalmazás forráskódja a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárházban érhető el. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Részletes útmutató a kiépítéshez és katalógusba vételhez

Annak megértéséhez, hogy a Wingtip Jegyek alkalmazás hogyan valósítja meg az új bérlői kiépítést, adjon hozzá egy töréspontot, és kövesse a munkafolyamatot, miközben bérlőt létesít.

1. A PowerShell ISE,open ... \\Tanulási modulok\\provisionandcatalog\\_demo-provisionandcatalog.ps1_ és állítsa be a következő paramétereket:

   * **$TenantName** = az új helyszín neve (például *Bushwillow Blues*).
   * **$VenueType** = az egyik előre meghatározott helyszín típusok: _blues, klasszikuszene, tánc, jazz, judo, autóverseny, többcélú, opera, rockmusic, foci_.
   * **$DemoScenario** = **1**, Kiépítése *egyetlen bérlő*.

2. Töréspont hozzáadásához helyezze a kurzort a *"New-Tenant "*. Ezután nyomja le az F9 billentyűt.

   ![Töréspont](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. A parancsfájl futtatásához nyomja le az F5 billentyűt.

4. Miután a parancsfájl végrehajtása leáll a töréspontnál, nyomja le az F11 billentyűt a kódba való lépéshez.

   ![Hibakeresés](media/saas-dbpertenant-provision-and-catalog/debug.png)



A parancsfájl végrehajtásának nyomon követése a **Hibakeresési** menü beállításaival. Nyomja le az F10 és az F11 billentyűt a hívott funkciók léptetéséhez vagy léptetéséhez. A PowerShell-parancsfájlok hibakereséséről a [PowerShell-parancsfájlok használatával és hibakeresésével](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)kapcsolatos tippek című témakörben olvashat bővebben.


Nem kell kifejezetten követnie ezt a munkafolyamatot. Ez elmagyarázza, hogyan kell hibakeresést a forgatókönyvet.

* **Importálja a CatalogAndDatabaseManagement.psm1 modult.** Katalógust és bérlőszintű absztrakciót biztosít a [Shard Management](sql-database-elastic-scale-shard-map-management.md) függvények felett. Ez a modul magában foglalja a katalógus minta nagy részét, és érdemes megvizsgálni.
* **Importálja az SubscriptionManagement.psm1 modult.** Olyan függvényeket tartalmaz, amelyek az Azure-ba való bejelentkezéshez és a dolgozni kívánt Azure-előfizetés kiválasztásához szükségesek.
* **A konfiguráció részleteinek beszerezése.** Lépjen be a Get-Configuration szolgáltatásba az F11 használatával, és nézze meg, hogyan van megadva az alkalmazás konfigurációja. Az erőforrásnevek és más alkalmazásspecifikus értékek itt vannak definiálva. Ne módosítsa ezeket az értékeket, amíg nem ismeri a parancsfájlokat.
* **A katalógusobjektum beszerezni.** Lépjen be a Get-Catalog,amely a magasabb szintű parancsfájlban használt katalógusobjektumot komponálja és adja vissza. Ez a függvény az **AzureShardManagement.psm1-ből**importált Shard Management függvényeket használja. A katalógusobjektum a következő elemekből áll:

   * $catalogServerFullyQualifiedName a szabványos szár és a felhasználónév használatával épül fel: _katalógus-\<felhasználó\>.database.windows .net_.
   * $catalogDatabaseName – a *tenantcatalog* konfigurációból származik.
   * $shardMapManager – ez az objektum a katalógus-adatbázisból van inicializálva.
   * $shardMap – ez az objektum a katalógus-adatbázisban található _tenantcatalog_ szilánkleképezésből van inicializálva. A katalógusobjektum ot összekell adni és visszaadja. A magasabb szintű szkriptben használják.
* **Számítsa ki az új bérlői kulcsot.** A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrizze, hogy a bérlői kulcs létezik-e már.** A katalógus be van jelölve, hogy a kulcs elérhető-e.
* **A bérlői adatbázis kiépítése a New-TenantDatabase használatával történik.** Az F11 segítségével lépjen be az adatbázis kiépítésének módjába egy [Azure Resource Manager-sablon](../azure-resource-manager/resource-manager-template-walkthrough.md)használatával.

    Az adatbázis neve a bérlő nevéből jön létre, hogy egyértelmű legyen, melyik szilánk melyik bérlőhöz tartozik. Más adatbázis-elnevezési konvenciókat is használhat. Az Erőforrás-kezelő sablon egy sablonadatbázis _(baseTenantDB)_ másolásával hoz létre bérlői adatbázist a katalóguskiszolgálón. Alternatív megoldásként létrehozhat egy adatbázist, és inicializálhatja azt egy bacpac importálásával. Vagy végrehajtható egy inicializálási parancsfájlt egy jól ismert helyről.

    Az Erőforrás-kezelő sablon a ...\Learning Modules\Common\ mappában található: *tenantdatabasecopytemplate.json*

* **A bérlői adatbázis további inicializálása.** A helyszín (bérlő) neve és a helyszín típusa hozzáadódik. Itt más inicializálást is elérhet.

* **A bérlői adatbázis regisztrálva van a katalógusban.** Az *Add-TenantDatabaseToCatalog* a bérlői kulcs használatával van regisztrálva. Nyomja le az F11 billentyűt a részletek megtekintéséhez:

    * A katalógus-adatbázis bekerül a szilánkleképezésbe (az ismert adatbázisok listájába).
    * Létrejön a kulcsérték és a szilánk társítása.
    * A bérlő további metaadatai (a helyszín neve) hozzáadódnak a katalógus Bérlők táblájához. A bérlők tábla nem része a Shard Management séma, és nem telepíti az EDCL. Ez a táblázat bemutatja, hogyan bővíthető a katalógusadatbázis további alkalmazásspecifikus adatok támogatásához.


A kiépítés befejezése után a végrehajtás visszatér az eredeti *Demo-ProvisionAndCatalog* parancsfájlhoz. Az **Események** lap megnyílik az új bérlő számára a böngészőben.

   ![Események lap](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegének kiépítése

Ez a gyakorlat 17 bérlőből álló köteget tartalmaz. Azt javasoljuk, hogy a bérlők ezen kötegének kiépítése előtt más Wingtip jegyek SaaS adatbázis-bérlőnként oktatóanyagok. Több, mint néhány adatbázissal kell dolgozni.

1. A PowerShell ISE,open ... \\Tanulási modulok\\provisionandcatalog\\*demo-provisionAndCatalog.ps1*. Módosítsa a *$DemoScenario* paramétert 3-ra:

   * **$DemoScenario** = **3**, Provision egy *tétel bérlők*.
2. A parancsfájl futtatásához nyomja le az F5 billentyűt.

A szkript üzembe helyezi a további bérlők kötegét. Egy [Azure Resource Manager sablont](../azure-resource-manager/resource-manager-template-walkthrough.md) használ, amely szabályozza a köteget, és delegálja az egyes adatbázisok egy csatolt sablon. A sablonok ily módon való alkalmazása lehetővé teszi, hogy az Azure Resource Manager közvetítse a szkriptnek a kiépítési folyamatot. A sablonok párhuzamosan létesítik az adatbázisokat, és szükség esetén kezelik az újrapróbálkozásokat. A parancsfájl idempotens, így ha bármilyen okból meghibásodik vagy leáll, futtassa újra.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>A sikeresen üzembe helyezett bérlők kötegének ellenőrzése

* Az [Azure Portalon](https://portal.azure.com)keresse meg a kiszolgálók listáját, és nyissa meg a *tenants1 kiszolgálót.* Válassza ki az **SQL-adatbázisokat**, és ellenőrizze, hogy a 17 további adatbázis kötege szerepel-e a listában.

   ![Adatbázisok listája](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Az oktatóanyagban nem szereplő egyéb kiépítési minták:

**Előlétesítési adatbázisok:** Az előlétesítési minta kihasználja azt a tényt, hogy a rugalmas készletben lévő adatbázisok nem adnak hozzá többletköltséget. Számlázási a rugalmas készlet, nem az adatbázisok. Az indl adatbázisok nem használnak erőforrásokat. Az adatbázisok előzetes kiépítésével és szükség esetén lefoglalásával csökkentheti a bérlők hozzáadásának idejét. Az előre kiépített adatbázisok száma szükség szerint módosítható a várható kiépítési aránynak megfelelő puffer megtartása érdekében.

**Automatikus kiépítés:** Az automatikus kiépítési mintában a kiépítési szolgáltatás szükség szerint automatikusan kiszolgálja a kiszolgálókat, készleteket és adatbázisokat. Ha szeretné, előzetes kiépítési adatbázisok rugalmas készletek. Ha az adatbázisokat leszerelik és törlik, a kiépítési szolgáltatás kitöltheti a rugalmas készletek hézagait. Egy ilyen szolgáltatás lehet egyszerű vagy összetett, például a több földrajzi területen történő kiépítés kezelése és a vész-helyreállítási georeplikáció beállítása.

Az automatikus kiépítési minta, egy ügyfélalkalmazás vagy parancsfájl küld egy létesítési kérelmet egy várólistába, hogy a kiépítési szolgáltatás által feldolgozandó. Ezután lekérdezi a szolgáltatást a befejezés meghatározásához. Ha előre kiépítést használ, a kérelmek kezelése gyorsan történik. A szolgáltatás a háttérben helyettesítő adatbázist biztosít.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Egyetlen új bérlő kiépítése.
> * További bérlők kötegének kiépítése.
> * Lépjen be a bérlők kiépítésének részleteibe, és regisztrálja őket a katalógusba.

Próbálja ki a [Teljesítményfigyelés oktatóanyagát.](saas-dbpertenant-performance-monitoring.md)

## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amelyek a Wingtip Jegyek SaaS-adatbázis-bérlőnkénti alkalmazásra épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
* [Parancsfájlok hibakeresése a Windows PowerShell ISE-ben](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
