---
title: Egy több-bérlős alkalmazásban, amely az Azure SQL Database új bérlők kiépítése |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database több-bérlős SaaS-alkalmazásokban az új bérlők kiépítése és katalógusba'
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: aa884f2df76c20d3119022069179b08ba2f2a6b7
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565213"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Ismerje meg, hogyan új bérlők kiépítése és regisztrálása a katalógusban

Ebben az oktatóanyagban elsajátíthatja, hogyan kiépítése és katalógusba SaaS-minták. Emellett megismerjük, hogyan azok van megvalósítva a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazásban. Hozzon létre és inicializálja az új bérlői adatbázisokat, és regisztrálja őket az alkalmazáskatalógusban bérlő. A katalógus egy adatbázis, amely a bérlői a SaaS-alkalmazás és az adataik közötti leképezéseket kezeli. A katalógus fontos szerepet az alkalmazás és a megfelelő adatbázishoz felügyeleti kérések irányítja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése.
> * További bérlők kötegelt kiépítése.


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás telepítve van. Tekintse meg az üzembe helyezés kevesebb mint öt perc alatt [üzembe helyezés és Fedezze fel a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS-katalógusmintában bemutatása

Egy adatbázis-alapú több-bérlős SaaS-alkalmazásban fontos tudni, hogy minden bérlő adatainak tárolására. Az SaaS-katalógusmintában a katalógus-adatbázis szolgál ahhoz, hogy minden bérlő, és az adatbázis, amely tárolja az adatokat közötti leképezést. Ez a minta jelentősége, hogy a bérlői adatok több adatbázis között vannak.

Mindegyik bérlő azonosít egy kulcsot a katalógus, amely az adatbázis helye van hozzárendelve. A Wingtip Tickets alkalmazás képződik a kulcs a bérlő neve kivonatát. Ez a séma lehetővé teszi az alkalmazásnak, hogy a bérlő nevéből, hogy az alkalmazás URL-cím szerepel a kulcs létrehozásakor. Más bérlői kulcs sémák is használható.  

A katalógus lehetővé teszi a nevét vagy módosítható az alkalmazás csak minimális hatással van az adatbázis helyét. Egy több-bérlős adatbázis modellben ezt a funkciót is biztosítja a bérlői adatbázisok közötti áthelyezése. A katalógus is segítségével adja meg, hogy egy bérlő vagy az adatbázis offline állapotban van a karbantartás vagy egyéb műveleteket. Ez a funkció akkor írja le a [visszaállítási egybérlős oktatóanyag](saas-dbpertenant-restore-single-tenant.md).

A katalógus is tárolhatja további bérlő vagy az adatbázis metaadatait, például a sémaverzió, a szolgáltatáscsomag vagy a SLA-k a bérlők számára érhető el. A katalógus tárolhat, amely lehetővé teszi az Alkalmazáskezelés, ügyfél-támogatási vagy a DevOps más információkat. 

A SaaS-alkalmazás túl a katalógus adatbáziseszközöket is engedélyezheti. A Wingtip Tickets SaaS bérlőnkénti adatbázis mintában a katalógus segítségével engedélyezhető a több-bérlős lekérdezés, amely van írja le a [alkalmi jelentéskészítő oktatóanyag](saas-tenancy-cross-tenant-reporting.md). Adatbázisközi feladatok kezelése a rendszer megvizsgálta a [Sémakezelés](saas-tenancy-schema-management.md) és [bérlői analitikák](saas-tenancy-tenant-analytics.md) oktatóanyagok. 

A Wingtip Tickets SaaS-mintákban a katalógus Szilánkkezelési funkcióját segítségével történik a [Elastic Database ügyfélkódtár (EDCL)](sql-database-elastic-database-client-library.md). Az EDCL a Java és a .NET-keretrendszer érhető el. Az EDCL lehetővé teszi, hogy az alkalmazás létrehozása, kezelése és használata egy adatbázis-alapú horizontális skálázási térképet. 

Horizontálispartíció-térkép szegmensek (adatbázisok) és a kulcsok (bérlők) és a szegmensek közötti listáját tartalmazza. Az EDCL függvények a bérlő kiépítésének a bejegyzéseket létrehozni a szegmenstérkép során használatosak. Használhatók futási időben az alkalmazások a megfelelő adatbázishoz. Az EDCL gyorsítótárazza a katalógus-adatbázis a forgalom csökkentése érdekében, és gyorsítsa fel az alkalmazás való kapcsolódáshoz szükséges adatokat. 

> [!IMPORTANT]
> A társítási adatok érhető el a katalógus-adatbázisban, de *ne módosítsa őket*. Rugalmas adatbázis ügyféloldali kódtár API-k használatával csak leképezési adatok szerkesztése A társítási adatok közvetlen módosítása a katalógus sérülésének kockázatát hordozza magában, és nem támogatott.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Az SaaS-üzembe helyezési minta bemutatása

Amikor egy SaaS-alkalmazás egy egybérlős adatbázismodell használó ad hozzá egy új bérlőt, kiépíteni a egy új bérlői adatbázist. Az adatbázist kell létrehozni a megfelelő hely és a szolgáltatási szint. Azt is inicializálni kell a megfelelő sémát és a referenciaadatok. És regisztrálni kell a megfelelő bérlői kulcs alatt a katalógusban. 

Az adatbázis üzembe helyezésének különböző megközelítések is alkalmazhatók. SQL-szkriptek végrehajtása, üzembe helyezése egy bacpac, vagy egy sablon adatbázis másolása. 

Adatbázis kiépítése a séma-kezelés stratégiájának részeként kell. Győződjön meg arról, hogy új adatbázisokat a legújabb sémával vannak kiépítve. Ezt a követelményt a rendszer megvizsgálta a [séma oktatóanyaggal](saas-tenancy-schema-management.md). 

A Wingtip Tickets bérlőnkénti adatbázis alkalmazás az új bérlők nevű sablon adatbázis másolásával építi ki _basetenantdb_, amelyre telepítve van a kiszolgáló. Kiépítés integrálhatók a bejelentkezési élmény részeként az alkalmazásba. Az is is támogatott lesz offline-parancsfájlok használatával. Ez az oktatóanyag bemutatja, hogy kiépítése a PowerShell használatával. 

Kiépítés-szkriptek másolása a _basetenantdb_ adatbázist, hogy létrehozza az új adatbázist egy rugalmas készletben. A bérlői adatbázis jön létre a bérlői kiszolgálóhoz rendelve a _newtenant_ DNS-alias. Ezt az aliast tartja nyilván a kiszolgáló, amellyel kiépíthetők az új bérlők számára, és frissül, és a egy helyreállítási bérlői kiszolgálóra mutasson a vész-helyreállítási oktatóanyagok ([georestore használatával DR](saas-dbpertenant-dr-geo-restore.md), [DR gyorsítótárakbanhasználatával](saas-dbpertenant-dr-geo-replication.md)). A parancsfájlok majd inicializálni a bérlő-specifikus adatokkal az adatbázist, és regisztrálja azt a katalógus szegmenstérkép. Bérlői adatbázisok a bérlő neve alapuló nevet kapnak. Az elnevezési sémája nem a minta kritikus része. A katalógus leképezi a bérlői kulcs az adatbázis neve, így minden olyan elnevezési konvenciót is használható. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazásszkriptek beolvasása

A Wingtip Tickets SaaS-parancsprogramok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Részletes útmutató a kiépítéshez és katalógusba vételhez

Szeretné megtudni, hogyan valósítja meg a Wingtip Tickets alkalmazás az új bérlőket, adjon hozzá egy töréspontot, és kövesse a munkafolyamat, amíg az új bérlő kiépítéséhez.

1. A PowerShell ISE-ben nyissa meg a... \\Tanulási modulok\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ és állítsa be a következő paraméterekkel:

   * **$TenantName** = az új helyszín neve (például *Bushwillow Blues*).
   * **$VenueType** = az előre beállított helyszíntípusok egyike: _blues, tánc, tánc, jazz, dzsúdó, kerékpárok verseny, többcélú, opera, rockzene, futball_.
   * **$DemoScenario** = **1**, *egyetlen új bérlő kiépítéséhez*.

2. Adjon hozzá egy töréspontot, hogy helyezze a kurzort bárhol a arról, hogy a sor *New-Tenant '*. Nyomja le az F9.

   ![Töréspont](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. A szkript futtatásához nyomja le az F5.

4. Miután a parancsfájl végrehajtása a töréspont leáll, nyomja le az F11 lépjen be a kódot.

   ![Hibakeresés](media/saas-dbpertenant-provision-and-catalog/debug.png)



A szkript végrehajtását nyomkövetési használatával a **Debug** menüpontok. Nyomja le az F10 és F11 vagy belépjen a meghívott függvényeken keresztül. lépésre. Hibakeresés a PowerShell-parancsfájlokkal kapcsolatos további információkért lásd: [tippekkel szolgál az használatához és hibakereséséhez PowerShell-parancsfájlok](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Nem kell explicit módon hajtsa végre ezt a munkafolyamatot. Ismerteti, hogyan hibakeresést a szkripten.

* **Importálja a CatalogAndDatabaseManagement.psm1 modult.** Katalógus- és bérlőszintű absztrakciót biztosít keresztül a [Szilánkkezelési](sql-database-elastic-scale-shard-map-management.md) funkciók. Ez a modul magában foglalja a katalógusminta részét, és ezért érdemes megismerkedni vele.
* **Importálja a SubscriptionManagement.psm1 modult.** Függvények jelentkezik be az Azure-ba, és kiválasztja a használni kívánt Azure-előfizetést tartalmazza.
* **Lekéri a konfigurációs részleteket.** F11 használatával, a Get-konfigurációs lépést, és tekintse meg, hogyan az alkalmazás konfigurációs van megadva. Erőforrásnevek és egyéb alkalmazásspecifikus értékek itt vannak megadva. Ne módosítsa ezeket az értékeket, amíg ki nem ismerte a szkriptet.
* **Lekéri a katalógusobjektumot.** Get-katalógus, amely composes, és adja vissza, amely szolgál katalógusobjektum a felsőbb szintű szkript lép. Ez a funkció fájlból importált szilánkkezelési függvények **AzureShardManagement.psm1**. A katalógusobjektumot a következő elemekből áll:

   * a standard szintű reáltudományi, valamint a felhasználónév használatával összeállított $catalogServerFullyQualifiedName: _catalog -\<felhasználói\>. database.windows .net_.
   * $catalogDatabaseName – a *tenantcatalog* konfigurációból származik.
   * $shardMapManager – ez az objektum a katalógus-adatbázisból van inicializálva.
   * $shardMap – ez az objektum a katalógus-adatbázisban található _tenantcatalog_ szilánkleképezésből van inicializálva. A katalógusobjektumot mikroszolgáltatásokból álló, és a adja vissza. A magasabb szintű szkript használatban van.
* **Az új bérlőkulcs kiszámítása.** A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrizze, hogy ha a bérlői kulcs már létezik.** Győződjön meg arról, hogy a kulcs-e a rendszer ellenőrzi a katalógusban.
* **A bérlői adatbázis kiépítése a New-TenantDatabase használatával történik.** Lépjen be, hogy ki van építve az adatbázis használatával F11 használatával egy [Azure Resource Manager-sablon](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Az adatbázis neve a bérlő nevéből jön létre, hogy egyértelmű legyen, melyik szilánk melyik bérlőhöz tartozik. Más adatbázis elnevezési konvenciókat is használhatja. Resource Manager-sablonnal hoz létre egy bérlői adatbázis egy sablon adatbázis másolásával (_baseTenantDB_) a katalóguskiszolgálón. Alternatív megoldásként hozzon létre egy adatbázist, és inicializálása egy bacpac importálásával. Vagy egy inicializációs szkriptet futtathatja egy jól ismert helyre.

    A Resource Manager-sablon a ...\Learning Modules\Common\ mappában van: *tenantdatabasecopytemplate.json*

* **A bérlői adatbázis további inicializálva van.** A helyszín (bérlő) nevének és a helyszín típusának kerülnek. Emellett a ponton További inicializálások itt teheti meg.

* **A bérlői adatbázis regisztrálása a katalógusban.** Regisztrálva van az *Add-TenantDatabaseToCatalog* a bérlői kulcs használatával. F11 használatával részletek:

    * A katalógus-adatbázis bekerül a szilánkleképezésbe (az ismert adatbázisok listájába).
    * Létrejön a kulcsérték és a szilánk társítása.
    * A bérlő (a helyszín neve) kapcsolatos további metaadatok hozzáadódik a bérlők tábla a katalógusban. A bérlők tábla nem a Szilánkkezelési séma része, és azt nem telepítette az EDCL. A következő táblázat bemutatja, hogyan a katalógus-adatbázis támogatására, további alkalmazásspecifikus adatait is kiterjeszthető.


Kiépítés befejezése után a végrehajtás visszatér az eredeti *Demo-ProvisionAndCatalog* parancsfájlt. A **események** lap, melyen az új bérlő a böngészőben.

   ![Események lap](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegelt kiépítése

Ebben a gyakorlatban 17 bérlők egy kötegét építi ki. Azt javasoljuk, hogy más Wingtip Tickets SaaS bérlőnkénti adatbázis oktatóanyagok megkezdése előtt a bérlők kötegelt kiépítése. Nincsenek dolgozhat több mint néhány adatbázist.

1. A PowerShell ISE-ben nyissa meg a... \\Tanulási modulok\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Módosítsa a *$DemoScenario* 3 paramétert:

   * **$DemoScenario** = **3**, *bérlők kötegelt kiépítése*.
2. A szkript futtatásához nyomja le az F5.

A szkript üzembe helyezi a további bérlők kötegét. Használja az [Azure Resource Manager-sablon](../azure-resource-manager/resource-manager-template-walkthrough.md) , amely a köteget, és delegálja a egy hivatkozott sablonnak az egyes adatbázisok kiépítésére. A sablonok ily módon való alkalmazása lehetővé teszi, hogy az Azure Resource Manager közvetítse a szkriptnek a kiépítési folyamatot. A sablonok párhuzamos és leíró újrapróbálkozások,-adatbázisok építhet ki, ha szükséges. A parancsfájl idempotens, ezért ha nem sikerül vagy bármilyen okból leáll, futtassa újra.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Győződjön meg arról, hogy sikeresen üzembe helyezett kiépített bérlők

* Az a [az Azure portal](https://portal.azure.com), keresse meg a kiszolgálók listáját, és nyissa meg a *tenants1* kiszolgáló. Válassza ki **SQL-adatbázisok**, és ellenőrizze, hogy a köteg 17 további adatbázisok most már a listában.

   ![Adatbázisok listája](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Ebben az oktatóanyagban nem szereplő egyéb kiépítési minták:

**Az adatbázisokat előzetesen kiépíti**: Az előzetesen kiépített mintája kihasználja a tény, hogy a rugalmas készletben található adatbázisok hozzáadása nem kapcsolódik további költség. A számlázás a rugalmas készlet, nem az adatbázisokra van. A tétlen adatbázisoknak nincs erőforrás. Előzetesen kiépített adatbázisokat a készlet és lefoglalása őket, amikor szükséges csökkentheti az időt adja hozzá a bérlők számára. Tartsa a várható kiépítési aránynak megfelelő puffer igény szerint módosítani lehet az előzetesen kiépített adatbázisok száma.

**Automatikus kiépítés**: Az automatikus kiépítési mintában az eszközkiépítési szolgáltatás kiosztja az kiszolgálók, készletek és adatbázisok automatikusan, szükség szerint. Ha azt szeretné, megadhatja a rugalmas készletekben található adatbázisok előzetes kiépítése. Adatbázisok szerelni és törlése, ha a rugalmas készletekben is ki kell tölteni a kiépítési szolgáltatás által. Ilyen szolgáltatás lehet egyszerű vagy összetett, például egyszerre több földrajzi és vész-helyreállítási georeplikáció beállításához. 

Az automatikus kiépítési mintában egy ügyfélalkalmazás vagy szkript elküld egy kiépítési kérést egy üzenetsorba a kiépítési szolgáltatás általi feldolgozásának. Ezután a szolgáltatás elvégzésével kérdezi le. Előzetes kiépítését használata esetén kéréseket gyorsan kezeli. A szolgáltatás látja el a háttérben egy helyettesítő adatbázist.


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése.
> * További bérlők kötegelt kiépítése.
> * Lépés bérlők kiépítése és a katalógusban való regisztrálásukra vonatkozó részletek.

Próbálja ki a [teljesítményfigyelési oktatóanyag](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amely a Wingtip Tickets SaaS bérlőnkénti adatbázis-alkalmazás létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
* [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
