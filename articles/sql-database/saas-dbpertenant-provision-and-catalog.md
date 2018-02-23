---
title: "A több-bérlős alkalmazások által használt Azure SQL adatbázis új bérlők kiépítése |} Microsoft Docs"
description: "Megtudhatja, hogyan szeretnék telepíteni, és a katalógus egy Azure SQL Database több-bérlős SaaS-alkalmazás az új bérlők"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 79b3743054f73914c6755a3c9b102b613b1944f2
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Útmutató új bérlők ellátásához, majd regisztrálja őket a katalógusban

Ebben az oktatóanyagban elsajátíthatja a kiépítés és a katalógus Szolgáltatottszoftver-minták, és hogyan valósulnak bérlői alkalmazásonként Wingtip jegyek SaaS adatbázisban. Kell létrehozni és inicializálni új bérlő adatbázisok, és regisztrálja őket az alkalmazáskatalógusban bérlői. A katalógus egy adatbázis több bérlő a SaaS-alkalmazás és az adataik közötti leképezést. A katalógus arra utasíthatja a alkalmazás és a felügyeleti kérések a megfelelő adatbázis fontos szerepet játszik.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése
> * További bérlők kötegelt kiépítése


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis bérlői alkalmazásonkénti van telepítve. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonként](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Az SaaS-katalógusminta ismertetése

Egy adatbázis biztonsági több-bérlős SaaS-alkalmazáshoz fontos tudni, hogy az egyes bérlők számára adatokat tároló. A Szolgáltatottszoftver-katalógus mintában a katalógus adatbázist használja ahhoz, hogy az egyes bérlők és az adatbázis, amely tárolja az adatokat közötti leképezést. Ebben a mintában alkalmazandó, amikor a bérlő adatok több adatbázis között van elosztva.

Mindegyik bérlő azonosít egy kulcsot a katalógus, amely az adatbázis helye van hozzárendelve. A Wingtip jegyek alkalmazásban a kulcs formátuma a kivonatát, a bérlő nevét. Ez lehetővé teszi az alkalmazásnak, hogy a bérlő neve, az alkalmazás URL-cím szerepel a kulcs létrehozásához. Más bérlői kulcs rendszerek is használható.  

A katalógus lehetővé teszi, hogy a nevét vagy az adatbázis módosítására gyakorolt minimális hatás mellett az alkalmazás helyét.  Egy adatbázis több-bérlős modell ez is biztosítja az "" a bérlő közötti áthelyezése adatbázisok.  A katalógus jelzi, hogy a bérlő vagy az adatbázis offline karbantartás vagy egyéb műveleteket is használható. Ez az írja le a [egybérlős oktatóanyag visszaállítása](saas-dbpertenant-restore-single-tenant.md).

A katalógus további bérlő vagy az adatbázis metaadatait, például a sémaverzió, a service-csomag vagy az SLA-k számára a bérlők számára, valamint egyéb adatait, amely lehetővé teszi a felügyeleti alkalmazás, ügyfél-támogatási vagy devops is tárolhatja.  

Túl a SaaS-alkalmazáshoz a katalógus adatbázis eszközök használatával engedélyezheti.  A Wingtip jegyek Szolgáltatottszoftver-adatbázisban egy bérlő minta a katalógus segítségével engedélyezhető a több-bérlős lekérdezés írja le a [ad hoc jelentések oktatóanyag](saas-tenancy-cross-tenant-reporting.md). Adatbázisok közötti feladatkezelés van írja le a [séma felügyeleti](saas-tenancy-schema-management.md) és [analytics bérlői](saas-tenancy-tenant-analytics.md) oktatóanyagok. 

A Wingtip jegyek Szolgáltatottszoftver-mintákban a katalógus segítségével van megvalósítva a Shard kezelési funkciókat a [rugalmas adatbázis ügyfél könyvtár (EDCL)](sql-database-elastic-database-client-library.md). A EDCL érhető el a Java és a .net keretrendszer. A EDCL lehetővé teszi az alkalmazás létrehozására, kezelésére és használja egy adatbázis biztonsági shard leképezését. A szilánkok térkép szilánkok (adatbázisok) és a kulcsok (bérlőkkel) és a szilánkok leképezése listáját tartalmazza.  EDCL funkciók során használatosak bérlő kiépítésének létrehozni a bejegyzéseket a shard térkép, és a futási időben alkalmazások a megfelelő adatbázishoz való kapcsolódáshoz. EDCL gyorsítótárazza a katalógus-adatbázis felé irányuló forgalom minimalizálása és felgyorsíthatja az alkalmazás kapcsolódási adatait.  

> [!IMPORTANT]
> A társítási adatok hozzáférhetők a katalógus-adatbázisban, de *ne módosítsa őket*! A társítási adatokat kizárólag az Elastic Database-ügyfélkódtár API-jaival szerkessze. A társítási adatok közvetlen módosítása a katalógus sérülésének kockázatát hordozza magában, ezért nem támogatott.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>A Szolgáltatottszoftver-üzembe helyezési minta bemutatása

Ha ki kell építenie a bevezetési single-bérlő adatbázis modellt használ, egy új bérlő adatbázis SaaS-alkalmazás egy új bérlő.  Az adatbázist kell létrehozni a megfelelő helyet és a szolgáltatási rétegben, megfelelő séma- és referenciainformációkat adatokkal, vagy a katalógus alatt az megfelelő bérlőkulcs regisztrációja.  

Az adatbázis kiépítésére különböző szempontok is használható, amelyek magukban foglalják az SQL-parancsfájlok végrehajtása, központi telepítése egy bacpac vagy sablon adatbázis másolása.  

Adatbázis-kiépítés kell lennie a felügyeleti győződjön meg arról, hogy új adatbázist, a legújabb séma kiépített séma stratégiájának a részét. Ez a követelmény van írja le a [séma felügyeleti oktatóanyag](saas-tenancy-schema-management.md).  

A bérlői alkalmazásonkénti Wingtip jegyek adatbázis új bérlők kiépítését nevű sablon adatbázis másolása _basetenantdb_, a kiszolgáló a telepített.  Kiépítés is kell integrálni az alkalmazás-előfizetési élmény részeként, és/vagy támogatott offline állapotba a parancsfájlok segítségével. Ez az oktatóanyag azt ismerteti, kiépítése a PowerShell használatával. Kiépítés parancsfájlok másolása a _basetenantdb_ -adatbázist hozzon létre egy új bérlő adatbázis rugalmas készlethez, majd inicializálni az adatbázis bérlői vonatkozó információval, és regisztrálja a katalógus shard leképezésben.  Bérlői adatbázisok a bérlő neve alapuló nevet kapnak, de ez elnevezési sémát része nem kritikus – minta, a katalógus bármely elnevezési használható adatbázisnév van leképezve a bérlői kulcs. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek Szolgáltatottszoftver-parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Részletes útmutató a kiépítéshez és katalógusba vételhez

Szeretné megtudni, hogyan a Wingtip jegyek alkalmazás valósít meg új bérlő kiépítésének, adja hozzá a töréspont és a lépés a munkafolyamaton keresztül egy bérlő kiépítése során:

1. A _PowerShell ISE_, nyissa meg... \\Tanulási modulok\\ProvisionAndCatalog\\_bemutató-ProvisionAndCatalog.ps1_ és állítsa be a következő paraméterekkel:
   * **$TenantName** = az új helyszín neve (például *Bushwillow Blues*).
   * **$VenueType** = az előre definiált helyszínére típusok egyikét: _kékek, classicalmusic, tánc, jazz, judo, racing, többcélú motor, opera, rockmusic, foci_.
   * **$DemoScenario** = **1**található *kiépíteni egy egybérlős*.

1. Adja hozzá a töréspont tegyen a kurzort, bárhol, amely szerint a sorban: *új bérlői "*, és nyomja le az ENTER **F9**.

   ![töréspont](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. A parancsfájl nyomja le az futtatásához **F5**.

1. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.

   ![Hibakeresés](media/saas-dbpertenant-provision-and-catalog/debug.png)



Nyomon követni a parancsfájl végrehajtása használatával a **Debug** menüpontok - **F10** és **F11** lépéssel keresztül vagy a hívott függvényekké. PowerShell-parancsfájlok hibakereső kapcsolatban további információkért lásd: [kezelése és a PowerShell-parancsfájlok hibakeresési tippeket](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


A következők nem kifejezetten a követendő lépések, de annak magyarázatát, a munkafolyamat a parancsprogram-hibakeresés során lépéseit:

1. **Importálja a SubscriptionManagement.psm1 modult**, amely az Azure-bejelentkezésre és a használt Azure-előfizetés kiválasztására szolgáló függvényeket tartalmaz.
1. **Importálja a CatalogAndDatabaseManagement.psm1 modult**, amely katalógus- és bérlőszintű absztrakciót biztosít a [szilánkkezelési](sql-database-elastic-scale-shard-map-management.md) függvényekhez. Ez a modul magában foglalja a katalógus mintát jelentős részét, és érdemes feltárása.
1. **Lekéri a konfigurációs részleteket**. Lépjen be a Get-konfigurációra (F11), és tekintse meg, hogyan az alkalmazások konfigurációja van megadva. Erőforrás és más alkalmazás-specifikus értékeket az itt megadott, de ne változtassa meg ezeket az értékeket, amíg nem ismeri a parancsfájlok.
1. **Lekéri a katalógusobjektumot**. Lépjen be a Get-katalógus, amely a magasabb szintű parancsfájlban composes és a katalógus objektumot használt.  Ez a funkció a Shard felügyeleti funkciók rendszerből importált **AzureShardManagement.psm1**. A katalógus objektum a következő elemekből áll:
   * $catalogServerFullyQualifiedName összeállított a szabványos stem és a felhasználói név: _katalógus -\<felhasználói\>. database.windows .net_.
   * $catalogDatabaseName – a *tenantcatalog* konfigurációból származik.
   * $shardMapManager – ez az objektum a katalógus-adatbázisból van inicializálva.
   * $shardMap – ez az objektum a katalógus-adatbázisban található _tenantcatalog_ szilánkleképezésből van inicializálva.
   A katalógusobjektumot a felsőbb szintű szkript állítja össze, adja vissza és használja.
1. **Kiszámítja az új bérlőkulcsot**. A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
1. **Ellenőrzi, hogy a bérlőkulcs létezik-e már**. A rendszer ellenőrzi a katalógusban, hogy a kulcs használható-e.
1. **A bérlői adatbázis kiépítése a New-TenantDatabase használatával történik.** Használjon **F11** lépést, és hogy az adatbázis van-e használó kiépítve egy [Azure Resource Manager sablon](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Az adatbázisnév összeállított a bérlő neve abba, hogy törölje a mely shard mely a bérlőhöz tartozik, bár egyéb adatbázis elnevezési konvenciókat is használható.
    A Resource Manager-sablon sablon adatbázis másolása egy bérlő adatbázist hoz létre (_baseTenantDB_) a katalógus-kiszolgálón. Alternatív megoldásként meg lehetett adatbázis létrehozása és inicializálnia egy bacpac importálásával, vagy hajtsa végre egy inicializálási parancsfájlja jól ismert helyről.

    A Resource Manager-sablon a ...\Learning Modules\Common\ mappában van: *tenantdatabasecopytemplate.json*

1. A bérlői adatbázis létrehozása után azt is majd **helyszínére (bérlői) nevét és a helyszínére típusú inicializálva**. Ezen a ponton további inicializálások is elvégezhetők.

1. A **bérlői adatbázis regisztrálva van a katalógus** rendelkező *Add-TenantDatabaseToCatalog* a bérlői kulcs használatával. Nyomja le az **F11** billentyűt a részletek megtekintéséhez:

    * A katalógus-adatbázis bekerül a szilánkleképezésbe (az ismert adatbázisok listájába).
    * Létrejön a kulcsérték és a szilánk társítása.
    * A bérlő (a helyszínére neve) kapcsolatos további meta adatok kerülnek a *bérlők* tábla a katalógusban.  A bérlők tábla nincs az ShardManagement-séma része, és a EDCL szerint nincs telepítve.  A következő táblázat bemutatja, hogyan a katalógus-adatbázis is terjeszthető támogatásához további alkalmazásspecifikus adatait.   


Kiépítés befejezése után végrehajtási visszatér az eredeti *bemutató-ProvisionAndCatalog* parancsprogramot, megnyílik a **események** lapot a böngészőben új bérlő számára:

   ![események](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Egy kötegben, a bérlő kiépítése

Ebben a gyakorlatban egy kötegelt 17 bérlő látja el. A kötegelt bérlő kiépítése indítása bérlői oktatóanyagok más Wingtip jegyek SaaS adatbázishoz, így nincs használható legfeljebb pár adatbázisok előtt ajánlott.

1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\ProvisionAndCatalog\\*bemutató-ProvisionAndCatalog.ps1* , és módosítsa a *$DemoScenario* 3 paramétert:
   * **$DemoScenario** = **3**található *kiépíteni a bérlő köteg*.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A szkript üzembe helyezi a további bérlők kötegét. Használ egy [Azure Resource Manager sablon](../azure-resource-manager/resource-manager-template-walkthrough.md) , amely meghatározza, hogy a kötegelt és amely minden adatbázis-sablonhoz társított kiépítés delegálja. A sablonok ily módon való alkalmazása lehetővé teszi, hogy az Azure Resource Manager közvetítse a szkriptnek a kiépítési folyamatot. A sablonok rendelkezés adatbázisok párhuzamos, és ha szükséges, kezeli az újrapróbálkozások. A parancsfájl az idempotent, ezért ha nem sikerül, vagy bármilyen okból leáll, futtassa újra.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>A sikeresen kiépített bérlők kötegének ellenőrzése

* Az a [Azure-portálon](https://portal.azure.com), keresse meg a kiszolgálók listáján, és nyissa meg a *tenants1* kiszolgáló. Kattintson a **SQL-adatbázisok** , és ellenőrizze a kötegelt 17 további adatbázisok most a listában:

   ![adatbázislista](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Az ebben az oktatóanyagban nem ismertetett egyéb kiépítési minták közé tartoznak a következők:

**Adatbázisok előzetes kiépítése.** Az előre üzembe helyezési minta kihasználja az, hogy nem adja hozzá az adatbázisok rugalmas készlethez kapcsolódik további költség. Számlázási a rugalmas készlet, nem az adatbázisokat, és inaktív adatbázisok nem erőforrást. Előzetes kiépítése adatbázis készletben, és lefoglalása őket, amikor szükséges, a bérlő bevezetési idő jelentősen csökkenthető. Előzetes kiosztása adatbázisok száma puffer megfelelő-e a kiépítési várható sebesség tartása szükség szerint módosítható.

**Automatikus kiépítés.** Az automatikus átadásának mintának, a létesítési szolgáltatás rendelkezések kiszolgáló, készletek és adatbázisok automatikusan szükséges – például a rugalmas készletek előre létesítési adatbázisok Ha szükséges. És adatbázisok való üzembe helyezése és törlése, ha a rugalmas készletek hézagok meghatározása érdekében a létesítési szolgáltatás is ki kell tölteni. Ilyen szolgáltatás lehet egyszerű vagy összetett – például létesíteni, több földrajzi kezelése és vész-helyreállítási georeplikáció beállítása sikerült. Automatikus kiépítés mintájú egy ügyfélalkalmazás vagy parancsfájl kérést létesítési annak a várólistára küld a létesítési szolgáltatás feldolgozni, és majd kérdezze le a befejezési meghatározni a szolgáltatás. Kiépítés előtti használata esetén kérelmek volna kell kezelni a gyors, a szolgáltatás a háttérben helyettesítő adatbázis kiépítés.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése
> * További bérlők kötegelt kiépítése
> * Lépjen be a bérlők kiépítés, és regisztrálja őket a katalógusba részleteit

Próbálja meg a [teljesítmény figyelési oktatóanyag](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis bérlői alkalmazásonként épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
* [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
