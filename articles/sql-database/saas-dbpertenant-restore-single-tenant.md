---
title: "A több-bérlős SaaS-alkalmazás az Azure SQL-adatbázis visszaállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan egyetlen bérlők SQL-adatbázis visszaállítása után véletlenül az adatok törlése"
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
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 60788b8a1b417e9bdfbe5ea8424dff9a39f36f2d
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>A több-bérlős SaaS-alkalmazás az egyetlen bérlők Azure SQL adatbázis visszaállítása

A Wingtip SaaS-alkalmazás épül, ahol az egyes bérlők rendelkezik saját adatbázis egy adatbázis-/-bérlős modell használatával. Ez a modell előnye, hogy a rendszer egyszerű önmagában egy egybérlős adatok helyreállítását a többi bérlő befolyásolása nélkül.

Ebben az oktatóanyagban elsajátíthatja, két adatok helyreállítási minták:

> [!div class="checklist"]

> * Egy párhuzamos adatbázisba (párhuzamos-) adatbázis visszaállítása
> * Egy helyen adatbázis visszaállítása


|||
|:--|:--|
| **Visszaállítása bérlői egy korábbi időpontra, egy párhuzamos adatbázisba** | Ebben a mintában a bérlő által használható felülvizsgálati naplózás esetén a megfelelőségi stb. Az eredeti adatbázis online állapotú, és változatlan marad. |
| **Bérlői helyben visszaállítása** | Ebben a mintában a bérlő előzetes pontra visszaállíthatja a idő, miután a bérlő véletlenül törli az adatokat általában használatos. Az eredeti adatbázist offline állapotba, és a visszaállított adatbázis helyett. |
|||

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>A Szolgáltatottszoftver-bérlői visszaállítási mintát bemutatása

A bérlői visszaállítási mintát nincsenek egy adott bérlő adatok visszaállításához két egyszerű mintát. Bérlői adatbázisok el különítve egymástól, mert egy bérlő visszaállítása nem hatást gyakorol semmilyen más bérlővel adatokat.

Az első mintában új adatbázisba visszaállítani adatait. A bérlő majd hozzáférése ehhez az adatbázishoz a termelési adatokkal együtt. Ebben a mintában lehetővé teszi, hogy a bérlői rendszergazda tekintse át az adatok és potenciálisan szelektív felülírja az aktuális adatok értékek használhatók. A Szolgáltatottszoftver-app Designer annak meghatározásához, hogy milyen kifinomult kell lennie az adat-helyreállítási beállítások van. Egyszerűen igényt tekintse át az állapotba, az adott időben adatokat minden egyes esetekben szükséges lehet. Ha az adatbázis használ [georeplikáció](sql-database-geo-replication-overview.md), javasoljuk, hogy a szükséges adatok másolását a visszaállított másolja át a az eredeti adatbázist. Ha az eredeti adatbázis cserélje a visszaállított adatbázis, kell konfigurálnia, majd szinkronizálja újra a georeplikáció.

A második mintát, amely azt feltételezi, hogy a bérlő szenvedett elvesztése vagy sérülése, adatok, az a bérlő éles adatbázis visszaállítása van egy korábbi pontra idő. A visszaállítási helyre mintában a bérlő offline állapotba egy rövid ideig, amíg az adatbázis visszaállítása, és újra online állapotba. Az eredeti adatbázist törölték, de továbbra is visszaállíthatók a ha kell térni a még régebbi pontként időben. Ez a minta egy változata névre történő átnevezése volt az adatbázis törlése helyett, bár az adatbázis átnevezése nem további adatbiztonsági előnyösebb kínál.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Szolgáltatottszoftver-parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-tárház. [Töltse le a Wingtip Szolgáltatottszoftver-parancsfájlok lépéseket](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>A bérlő véletlenül törli az adatok szimulálása

Helyreállítási forgatókönyvekben bemutatásához, szeretnénk *véletlenül* törlése egy bérlő adatbázis bizonyos adatai. Rekordot törölheti, amíg a következő lépésben beállítja a bemutató hogy nem blokkolja lekérni a hivatkozási integritás megsértésének! Bővíti Ezenkívül egyes jegy beszerzési adatokat később is használhatja a *Wingtip SaaS Analytics oktatóanyagok*.

A jegy generátor parancsprogrammal, és hozzon létre további adatokat. A jegy generátor szándékosan nem vásárolja meg a jegyektől az egyes bérlők utolsó eseményekhez.

1. Nyissa meg... \\Tanulási modulok\\segédprogramok\\*bemutató-TicketGenerator.ps1* a a *PowerShell ISE*
1. Nyomja le az **F5** futtassa a parancsfájlt és ügyfelek készítése és értékesítési adatokat jegyet.


### <a name="open-the-events-app-to-review-the-current-events"></a>Nyissa meg az események alkalmazásnak, hogy tekintse át a jelenlegi eseményeket

1. Nyissa meg a *események Hub* (http://events.wtp.&lt; felhasználói&gt;. trafficmanager.net), majd **Contoso energiaoptimalizálást egyszerre Hall**:

   ![eseményközpont](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Görgessen az események listájának, és jegyezze fel az utolsó esemény a listában:

   ![utolsó esemény](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>Futtassa a bemutató forgatókönyvet véletlenül törli a legutóbbi esemény

1. Nyissa meg... \\Tanulási modulok\\az üzletmenet folytonossága és vészhelyreállítás\\RestoreTenant\\*bemutató-RestoreTenant.ps1* a a *PowerShell ISE*, és a következő értéket:
   * **$DemoScenario** = **1**, beállítása **1** -jegy értékesítés nélküli események törlése.
1. Nyomja le az **F5** futtassa a parancsfájlt, és az utolsó esemény törlése. Megjelenik egy megerősítő üzenetet a következőhöz hasonló:

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. A Contoso események lap nyílik meg. Görgessen lefelé, és győződjön meg arról az esemény már nem. Ha az esemény továbbra is szerepel a listában, kattintson a frissítés és győződjön meg arról eltűnt.

   ![utolsó esemény](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Az éles adatbázis párhuzamosan bérlői adatbázis visszaállítása

Ebben a gyakorlatban adatbázisát állítja vissza a Contoso energiaoptimalizálást egyszerre Hall pontra előtti az esemény törölve lett. Az esemény az előző lépésben törlését követően szeretné helyreállítani, és tekintse meg a törölt adatokat. Nem kell visszaállítani a törölt bejegyzés az éles adatbázis, de kell állítani a régi adatbázis más üzleti okokból a régi adatok eléréséhez.

 A *visszaállítási-TenantInParallel.ps1* parancsfájl adatbázis és a párhuzamos katalógusbejegyzés mindkét nevű hoz létre egy párhuzamos bérlői *ContosoConcertHall\_régi*. Ebben a mintában a visszaállítás olyan kisebb adatvesztés helyreállítás vagy megfelelőségi és naplózási helyreállítási forgatókönyveit. Ez egyben az ajánlott módszer használata esetén [georeplikáció](sql-database-geo-replication-overview.md).

1. Fejezze be a [véletlenül törli az adatokat a felhasználó szimulálása](#simulate-a-tenant-accidentally-deleting-data) szakasz.
1. Nyissa meg... \\Tanulási modulok\\az üzletmenet folytonossága és vészhelyreállítás\\RestoreTenant\\_bemutató-RestoreTenant.ps1_ a a *PowerShell ISE*.
1. Állítsa be **$DemoScenario** = **2**, állítsa ezt a beállítást **2** való *párhuzamos visszaállítási bérlői*.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A parancsfájl adatbázisát állítja vissza a bérlő (a párhuzamos adatbázis) pontra időben az előző szakaszban esemény törlése előtt. Ez létrehoz egy második adatbázist, eltávolítja a meglévő katalógus metaadatokat, hogy létezik az adatbázisban, és az adatbázis hozzáadása a katalógus a *ContosoConcertHall\_régi* bejegyzés.

A bemutató-parancsfájl az események lapot a böngészőben nyílik meg. Megjegyzés: az URL-címről: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , hogy ez látható a visszaállított adatbázis ahol *_old* hozzáadódik a neve.

Görgessen a böngészőt annak ellenőrzéséhez, hogy visszaállította az esemény törölve az előző szakaszban felsorolt események.

Vegye figyelembe, hogy, egy további bérlői, keresse meg a jegyektől, hogy a saját események alkalmazással nem valószínű, hogy hogyan szeretné biztosít egy bérlői hozzáférést kell, hogy csökkenjen a visszaállított bérlő visszaállította az adatokat, de könnyen mutatja be a visszaállítási mintát szolgál.

A valóságban ugyanúgy valószínűleg csak tartsa meg a visszaállított adatbázis számára meghatározott ideig. A visszaállított bérlői bejegyzés törlése után meghívásával elkészült a *Remove-RestoredTenant.ps1* parancsfájl.

1. Állítsa be **$DemoScenario** való **4** jelölje be a *vissza eltávolítása bérlői* forgatókönyv.
1. **Végrehajtás** **használatával** **F5**
1. A *ContosoConcertHall\_régi* bejegyzés törlődik a katalógusból. Lépjen tovább, és zárja be az események lapról ennél a bérlőnél a böngészőben.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>A bérlő helyen, a mag cseréje a meglévő bérlő adatbázis visszaállítása

Ebben a gyakorlatban a Contoso energiaoptimalizálást egyszerre Hall bérlő visszaállítja az a pont előtti az esemény törölve lett. A *visszaállítási-TenantInPlace* parancsfájl az aktuális bérlőhöz adatbázis visszaállítja az mutat egy korábbi időpontra időben új adatbázist, és törli az eredeti adatbázist. Ebben a mintában a visszaállítás olyan helyreállítására súlyos adatsérülés, mivel előfordulhat, hogy a bérlő olyan jelentős adatvesztés.

1. Nyissa meg **bemutató-RestoreTenant.ps1** a PowerShell ISE fájl
1. Állítsa be **$DemoScenario** való **5** jelölje be a *állítsa vissza a bérlő helye forgatókönyvben*.
1. Végrehajtás használatával **F5**.

A parancsfájl adatbázisát állítja vissza a bérlő pontra öt perc elteltével az esemény törölve lett. Ennek érdekében első véve a Contoso energiaoptimalizálást egyszerre Hall bérlő offline, így nincsenek további frissítések az adatokhoz. Ezt követően egy párhuzamos adatbázis hozta létre a visszaállítási pontról visszaállítása és annak érdekében, hogy az adatbázis neve nem ütközik a meglévő adatbázis bérlő nevét az időbélyegzőnek nevű. Ezután a régi bérlői adatbázisa törlődik, és a visszaállított adatbázis átnevezése az eredeti adatbázis neve. Végezetül Contoso energiaoptimalizálást egyszerre Hall online állapotba a visszaállított adatbázis alkalmazás hozzá.

Sikeresen visszaállította a az adatbázis pontra előtti az esemény törölve lett. Az események lap megnyílik, ezért győződjön meg róla, az utolsó esemény vissza lett állítva.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Egy párhuzamos adatbázisba (párhuzamos-) adatbázis visszaállítása
> * Egy helyen adatbázis visszaállítása

[Bérlői adatbázisséma kezelése](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md)
* [További tudnivalók az SQL-adatbázis biztonsági mentése](sql-database-automated-backups.md)
