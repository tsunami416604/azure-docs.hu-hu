---
title: A több-bérlős alkalmazások által használt Azure SQL adatbázis új bérlők kiépítése |} Microsoft Docs
description: Megtudhatja, hogyan szeretnék telepíteni, és a katalógus egy több-bérlős Szolgáltatottszoftver-alkalmazás az Azure SQL Database az új bérlők
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 1accc672e396c5a9405369654f9bc4f8463c9afc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Útmutató új bérlők ellátásához, majd regisztrálja őket a katalógusban

Ebben az oktatóanyagban elsajátíthatja kiépítése és a Szolgáltatottszoftver-minták a katalógus. Azt is megtudhatja, hogyan azokat a Wingtip jegyek SaaS-adatbázis egy bérlő alkalmazás implementálva van. Hozzon létre és új bérlő adatbázisok inicializálása, és regisztrálja őket az alkalmazáskatalógusban bérlő. A katalógus egy adatbázis több bérlő a SaaS-alkalmazás és az adataik közötti leképezést. A katalógus fontos szerepet utasította a alkalmazás és a felügyeleti kérések a megfelelő adatbázishoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Egy új bérlőt kiépítéséhez.
> * A kötegelt további bérlő kiépítéséhez.


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek Szolgáltatottszoftver-adatbázis egy bérlő alkalmazás telepítve van. A telepítés öt percen belül, lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS-adatbázis egy bérlő alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>A Szolgáltatottszoftver-katalógus mintát bemutatása

Egy adatbázis biztonsági több-bérlős SaaS-alkalmazáshoz fontos tudni, hogy az egyes bérlők számára adatokat tároló. A Szolgáltatottszoftver-katalógus mintában a katalógus adatbázist használja ahhoz, hogy az egyes bérlők és az adatbázis, amely tárolja az adatokat közötti leképezést. Ebben a mintában alkalmazandó, amikor a bérlő adatok több adatbázis között van elosztva.

Mindegyik bérlő azonosít egy kulcsot a katalógus, amely az adatbázis helye van hozzárendelve. A Wingtip jegyek alkalmazásban a kulcs formátuma a kivonatát, a bérlő nevét. Ez a séma lehetővé teszi, hogy az alkalmazás a bérlő neve, az alkalmazás URL-cím szerepel a kulcs létrehozásához. Más bérlői kulcs rendszerek is használható.  

A katalógus lehetővé teszi, hogy a nevét vagy az adatbázis módosítására gyakorolt minimális hatás mellett az alkalmazás helyét. Egy adatbázis több-bérlős modell ezt a lehetőséget is biztosítja a bérlők adatbázisok közötti áthelyezése. A katalógus is segítségével adja meg, hogy a bérlő vagy az adatbázis offline karbantartás vagy egyéb műveleteket. Ez a lehetőség van írja le a [visszaállítási egybérlős oktatóanyag](saas-dbpertenant-restore-single-tenant.md).

A katalógus is tárolhatja további bérlő vagy az adatbázis-metaadatok, például a séma verziója, a service-csomag vagy a szolgáltatásiszint-szerződések a bérlők számára biztosított. A katalógus más információ, amely lehetővé teszi, hogy a felügyeleti alkalmazás, ügyfél-támogatási vagy DevOps tárolhatja. 

Túl a SaaS-alkalmazáshoz a katalógus adatbázis eszközök használatával engedélyezheti. A Wingtip jegyek SaaS adatbázis / bérlői mintában a katalógus segítségével engedélyezhető a több-bérlős lekérdezés, amely van írja le a [alkalmi jelentéskészítési oktatóanyag](saas-tenancy-cross-tenant-reporting.md). Adatbázisok közötti feladatkezelés van írja le a [séma felügyeleti](saas-tenancy-schema-management.md) és [analytics bérlői](saas-tenancy-tenant-analytics.md) oktatóanyagok. 

A Wingtip jegyek Szolgáltatottszoftver-mintákban a katalógus Shard felügyeleti szolgáltatásai segítségével történik a [Elastic Database ügyféloldali kódtárának (EDCL)](sql-database-elastic-database-client-library.md). A EDCL Java és a .NET-keretrendszer érhető el. A EDCL lehetővé teszi az alkalmazás létrehozására, kezelésére és használja egy adatbázis biztonsági shard leképezését. 

A szilánkok térkép szilánkok (adatbázisok) és a kulcsok (bérlőkkel) és a szilánkok leképezése listáját tartalmazza. EDCL funkciók bérlői létrehozni a bejegyzéseket a shard térkép kiépítése során használatosak. Ezek van futási időben alkalmazások használják a megfelelő adatbázishoz való kapcsolódáshoz. EDCL gyorsítótárazza a katalógus-adatbázis felé irányuló forgalom minimalizálása és felgyorsíthatja az alkalmazás kapcsolódási adatait. 

> [!IMPORTANT]
> A hozzárendelési adatok érhető el a katalógus-adatbázisban, de *nem szerkesztése*. A rugalmas adatbázis ügyfél könyvtár API-k használatával csak leképezési adatok szerkesztése Az adatbázis közvetlen módosítása a hozzárendelési adatok kockázatok, a katalógus rongál, és nem támogatott.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>A Szolgáltatottszoftver-üzembe helyezési minta bemutatása

Ha egy új bérlőt ad hozzá SaaS-alkalmazás egyetlen-bérlő adatbázis modellt használ, el kell juttatnia új bérlő adatbázis. Az adatbázist kell létrehozni a megfelelő helyet és a szolgáltatási rétegben. Azt is inicializálni kell a megfelelő sémáját, és a referenciaadatoknál. És szerepelnie kell a megfelelő bérlői kulcs alatt a katalógusban. 

Az adatbázis kiépítésére különböző megközelítések is alkalmazhatók. SQL-parancsfájlok végrehajtása, egy bacpac telepítéséhez, vagy egy sablon adatbázis másolása. 

A séma-kezelés stratégiájának részeként kell adatbázis létrehozását. Meg kell győződnie arról, hogy új adatbázist, a legújabb séma törlődnek. Ez a követelmény van írja le a [séma felügyeleti oktatóanyag](saas-tenancy-schema-management.md). 

A Wingtip jegyek adatbázis / bérlői alkalmazás új bérlők látja el a sablon nevű adatbázis másolása _basetenantdb_, amely a katalógus kiszolgálón van telepítve. Kiépítés integrálhatják az alkalmazás-előfizetési élmény részeként. Azt is támogatnak a kapcsolat nélküli parancsfájlok segítségével. Ez az oktatóanyag ismerteti, kiépítés PowerShell használatával. 

Kiépítés parancsfájlok másolása a _basetenantdb_ adatbázis új bérlő adatbázis létrehozására a rugalmas készletekben található. A parancsfájlok majd inicializálása bérlői-specifikus adatait. az adatbázis, és regisztrálja a katalógus shard leképezésben. Bérlői adatbázisok a következők a bérlő neve alapuló nevet kapnak. Az elnevezési sémája nem egy minta kritikus része. A katalógus bármely elnevezési is használható, az adatbázis neve képezi le a bérlői kulcs. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek Szolgáltatottszoftver-parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Részletes útmutató a kiépítéshez és katalógusba vételhez

Ha szeretné megtudni, hogyan a Wingtip jegyek alkalmazás valósít meg új bérlő kiépítésének, töréspont, és kövesse a munkafolyamat, amíg a bérlő ellátásához.

1. A PowerShell ISE megnyitása... \\Tanulási modulok\\ProvisionAndCatalog\\_bemutató-ProvisionAndCatalog.ps1_ és állítsa be a következő paraméterekkel:

   * **$TenantName** = az új helyszín neve (például *Bushwillow Blues*).
   * **$VenueType** = az előre definiált helyszínére típusok egyikét: _kékek, classicalmusic, tánc, jazz, judo, racing, többcélú motor, opera, rockmusic, foci_.
   * **$DemoScenario** = **1**, *kiépíteni egy egybérlős*.

2. Töréspont hozzáadásához a kurzor elhelyezése bárhol, amely szerint a sor *új bérlői "*. Nyomja le az F9.

   ![Töréspont](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. A parancsfájl futtatásához nyomja le az F5 billentyűt.

4. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az F11 lépjen be a kódját.

   ![Hibakeresés](media/saas-dbpertenant-provision-and-catalog/debug.png)



A parancsfájl végrehajtása nyomkövetési használatával a **Debug** menü elemeit. Nyomja le az F10 és F11 keresztül vagy a hívott függvényekké. lépés. PowerShell-parancsfájlok hibakereső kapcsolatban további információkért lásd: [kezelése és a PowerShell-parancsfájlok hibakeresési tippeket](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nem kell explicit módon hajtsa végre a munkafolyamat. Ismerteti a parancsfájlok hibakeresése.

* **A CatalogAndDatabaseManagement.psm1 modul importálása.** A katalógus és a bérlői szintű absztrakció keresztül biztosít a [Shard felügyeleti](sql-database-elastic-scale-shard-map-management.md) funkciók. Ez a modul magában foglalja a katalógus mintát jelentős részét, és érdemes feltárása.
* **A SubscriptionManagement.psm1 modul importálása.** Az Azure próbál bejelentkezni, és kiválasztja a használni kívánt Azure-előfizetés funkciók tartalmaz.
* **Ezzel a konfigurációs adatokat.** F11 használatával a Get-konfigurációs lépést, és tekintse meg, hogyan az alkalmazások konfigurációja van megadva. Erőforrás és más alkalmazás-specifikus értékeket az itt megadott. Ezek az értékek nem megváltoztatni, amíg meg nem ismeri a parancsfájlok.
* **A katalógus objektum beolvasása.** Lépjen be a Get-katalógus, amely a magasabb szintű parancsfájlban composes és a katalógus objektumot használt. Ez a funkció a Shard felügyeleti funkciók rendszerből importált **AzureShardManagement.psm1**. A katalógus objektum a következő elemekből áll:

   * a szabványos stem és a felhasználói név használatával összeállított $catalogServerFullyQualifiedName: _katalógus -\<felhasználói\>. database.windows .net_.
   * $catalogDatabaseName – a *tenantcatalog* konfigurációból származik.
   * $shardMapManager – ez az objektum a katalógus-adatbázisból van inicializálva.
   * $shardMap – ez az objektum a katalógus-adatbázisban található _tenantcatalog_ szilánkleképezésből van inicializálva. A katalógus objektum áll, és adott vissza. A magasabb szintű parancsfájl használatban van.
* **Az új bérlőkulcs kiszámításához.** A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrizze, hogy a bérlői kulcs már létezik-e.** A katalógus be van jelölve, ellenőrizze, hogy a kulcs érhető el.
* **A bérlői adatbázis kiépítése a New-TenantDatabase használatával történik.** Lépjen be, hogy ki van építve az adatbázis használatával F11 segítségével egy [Azure Resource Manager sablon](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Az adatbázis neve a bérlő nevéből jön létre, hogy egyértelmű legyen, melyik szilánk melyik bérlőhöz tartozik. Más adatbázis elnevezési konvenciókat is használhatja. A Resource Manager-sablon sablon adatbázis másolása egy bérlő adatbázist hoz létre (_baseTenantDB_) a katalógus-kiszolgálón. Alternatív megoldásként hozzon létre egy adatbázist, és inicializálnia egy bacpac importálásával. Vagy egy inicializálási parancsfájlja hajthat végre egy jól ismert helyről.

    A Resource Manager-sablon a ...\Learning Modules\Common\ mappában van: *tenantdatabasecopytemplate.json*

* **A bérlői adatbázis további inicializálva van.** A helyszínére (bérlői) nevét és típusát helyszínére kerülnek. Más inicializálási itt is végezhet.

* **A bérlői adatbázis regisztrálva van a katalógusban.** Regisztrálva van *Add-TenantDatabaseToCatalog* a bérlői kulcs használatával. F11 segítségével lépjen be a részletek:

    * A katalógus-adatbázis bekerül a szilánkleképezésbe (az ismert adatbázisok listájába).
    * Létrejön a kulcsérték és a szilánk társítása.
    * A bérlő (a helyszínére neve) kapcsolatos további metaadatokat hozzáadódik a bérlők tábla a katalógusban. A bérlők tábla nem a Shard felügyeleti séma része, és azt nem telepítette a EDCL. A következő táblázat bemutatja, hogyan a katalógus-adatbázis is terjeszthető támogatásához további alkalmazásspecifikus adatait.


Kiépítés befejezése után végrehajtási visszatér az eredeti *bemutató-ProvisionAndCatalog* parancsfájl. A **események** lap megnyitása a böngészőben az új bérlőhöz.

   ![Események lap](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Egy kötegben, a bérlő kiépítése

Ebben a gyakorlatban egy kötegelt 17 bérlő látja el. Azt javasoljuk, hogy a bérlő kötegelt kiépítése többi Wingtip jegyek SaaS adatbázis / bérlői oktatóanyag elindítása előtt. Nincsenek legfeljebb pár adatbázisok történő együttműködésre.

1. A PowerShell ISE megnyitása... \\Tanulási modulok\\ProvisionAndCatalog\\*bemutató-ProvisionAndCatalog.ps1*. Módosítsa a *$DemoScenario* 3 paramétert:

   * **$DemoScenario** = **3**, *kiépíteni a bérlő köteg*.
2. A parancsfájl futtatásához nyomja le az F5 billentyűt.

A szkript üzembe helyezi a további bérlők kötegét. Használja az [Azure Resource Manager sablon](../azure-resource-manager/resource-manager-template-walkthrough.md) , amely szabályozza a kötegelt, és delegálja a kiépítés minden adatbázis-sablonhoz társított. A sablonok ily módon való alkalmazása lehetővé teszi, hogy az Azure Resource Manager közvetítse a szkriptnek a kiépítési folyamatot. A sablonok kiépítése párhuzamos és leíró újrapróbálkozások, adatbázisok, szükség esetén. A parancsfájl az idempotent, ezért ha nem sikerül, vagy bármilyen okból leáll, futtassa újra.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Ellenőrizze a kötegelt a sikeresen telepített bérlő

* Az a [Azure-portálon](https://portal.azure.com), keresse meg a kiszolgálók listáján, és nyissa meg a *tenants1* kiszolgáló. Válassza ki **SQL-adatbázisok**, és győződjön meg arról, hogy a köteg 17 további adatbázisok most a listában.

   ![Az adatbázisok listája](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Más nem szerepel ebben az oktatóanyagban létesítési minták:

**Adatbázisok előre kiépítése**: előre üzembe helyezési minta kihasználja az, hogy az adatbázisok rugalmas készlethez hozzáadása nem kapcsolódik további költség. Számlázási van, a rugalmas készlet, nem az adatbázisokat. Üresjárati adatbázisok nincsenek erőforrások felhasználását. A készlet és lefoglalása azokat szükség esetén előre létesítési adatbázisok hozzáadása a bérlők idő csökkentheti. Előzetes kiosztása adatbázisok száma puffer megfelelő-e a kiépítési várható sebesség tartása szükség szerint módosítható.

**Automatikus kiépítés**: automatikus kialakítási mintában a létesítési szolgáltatás kiépítését kiszolgálók, a készletek és az adatbázisokat, szükség esetén automatikusan. Ha azt szeretné, a előzetes kiépítése az adatbázisok rugalmas készletek is felvehet. Adatbázisok szerelni és törlése, ha a rugalmas készletek hézagok meghatározása érdekében is ki kell tölteni a létesítési szolgáltatás. Lehet, hogy az ilyen egy szolgáltatás egyszerűek vagy összetettek, például a különböző földrajzi létesíteni, valamint beállítja a vész-helyreállítási georeplikáció kezelése. 

Az Automatikus kiépítés mintájú egy ügyfélalkalmazás vagy parancsfájl létesítési kérelmet küld a vizsgálatnál annak a várólistára a létesítési szolgáltatás feldolgozni. A szolgáltatás befejezési meghatározásához majd kérdezi le. Kiépítés előtti használata esetén kéréseket gyorsan kezeli. A szolgáltatás a háttérben helyettesítő adatbázis látja el.


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Egy új bérlőt kiépítéséhez.
> * A kötegelt további bérlő kiépítéséhez.
> * Lépjen be a bérlők kiépítés, és regisztrálja őket a katalógusba részleteit.

Próbálja meg a [teljesítmény figyelési oktatóanyag](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS-adatbázis egy bérlő alkalmazás létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
* [A Windows PowerShell ISE parancsfájlok hibakeresése](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
