---
title: Egy Azure SQL-adatbázist egy több-bérlős SaaS-alkalmazás visszaállításához |} A Microsoft Docs
description: Ismerje meg, hogyan adatok véletlen törlését követően egy egybérlős SQL-adatbázis visszaállítása
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 4059b0f979e7e6856905f1759129167d62d7b5f5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274428"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>A bérlőnkénti adatbázis SaaS-alkalmazásokkal egyetlen bérlő visszaállítása

A bérlőnkénti adatbázis modellt megkönnyíti az egyetlen bérlő visszaállítása egy korábbi időpontra időben anélkül, hogy befolyásolná a többi bérlőtől.

Ebből az oktatóanyagból megtudhatja, adat-helyreállítási két mintát:

> [!div class="checklist"]
> * Adatbázis visszaállítása egy párhuzamos adatbázisba (egymás mellett).
> * Teljesülnek, és cserélje le a meglévő adatbázis adatbázis visszaállítása.

|||
|:--|:--|
| Párhuzamos adatbázisba történő visszaállítása | Ez a minta olyan feladatokhoz, mint a felülvizsgálatot, naplózással és megfelelőségi használható, hogy a bérlő vizsgálja meg saját adatainak egy korábbi időpontra. A bérlő aktuális adatbázis online és a változatlan marad. |
| A hely visszaállítása | Ez a minta általában állíthatók helyre egy bérlőt egy korábbi időpontra, egy bérlő véletlenül törli vagy megsérülnek adatok után. Az eredeti adatbázis offline állapotú venni és a visszaállított adatbázis helyére. |
|||

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és megismerése a Wingtip SaaS-alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információkért lásd: [Azure PowerShell használatának első lépései](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>A SaaS-bérlő visszaállítási minták bemutatása

Nincsenek egy adott bérlő adatok visszaállításához két egyszerű mintát. Bérlői adatbázisok elkülönülnek egymástól, mert egyetlen bérlő visszaállítása nem befolyásolja a semmilyen más bérlővel adatok. Az Azure SQL Database pont a-időponthoz kötött visszaállítás (PITR) szolgáltatás mindkét minták használatban van. PITR mindig létrehoz egy új adatbázist.

* **Párhuzamos visszaállítási**: Az első mintájában mellett a jelenlegi adatbázis a bérlő új párhuzamos adatbázis jön létre. A bérlő majd a visszaállított adatbázis való csak olvasási hozzáféréssel kap. A visszaállított adatok azok áttekinthetők és aktuális adatokat értékeket felülírására potenciálisan használható. Fontos az alkalmazástervező, hogyan a bérlő fér hozzá a visszaállított adatbázis és rendelkezésre milyen helyreállítási lehetőségek meghatározása. Egyszerűen lehetővé teszi a bérlő, tekintse át az adatokat egy korábbi időpontra való minden egyes esetekben szükséges lehet.

* **A hely visszaállítása**: A második minta akkor hasznos, ha az adatok elvesztésének vagy sérült állapotba kerül, és a bérlő egy korábbi időpontra vissza szeretné. A bérlő offline állapotú használatban van, amíg az adatbázis helyreállítása. Az eredeti adatbázis törlődik, és a visszaállított adatbázis neve. Az eredeti adatbázist a biztonsági mentési láncolata marad elérhető a törlés után, így visszaállíthatja az adatbázist egy korábbi időpontra történik, ha szükséges.

Ha az adatbázis használ [aktív georeplikáció](sql-database-active-geo-replication.md) és visszaállítása párhuzamosan, javasoljuk, hogy a szükséges adatokat a visszaállított másolatból az eredeti adatbázisba másolja. Ha lecseréli az eredeti adatbázist a visszaállítottra, meg kell konfigurálnia és szinkronizálnia a georeplikáció.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazásszkriptek beolvasása

A Wingtip Tickets SaaS több-Bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Töltse le és feloldása a Wingtip Tickets SaaS-parancsfájlok lépéseiért lásd: a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Előkészületek

Ha egy adatbázis jön létre, 10 – 15 percet, mielőtt érhető el az első teljes biztonsági másolatból történő visszaállítását is igénybe vehet. Ha éppen most telepítette az alkalmazást, szüksége lehet Várjon néhány percet, mielőtt megpróbálná ebben a forgatókönyvben.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>A bérlői adatok véletlen törlését szimulálása

Ezek a helyreállítási forgatókönyvek bemutatása érdekében először "véletlenül" töröljön egy eseményt egy, a bérlői adatbázisok. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Nyissa meg az események alkalmazás áttekintheti az aktuális események

1. Nyissa meg az Eseményközpontot (http://events.wtp.&lt; felhasználó&gt;. trafficmanager.net), és válassza ki **Contoso Concert Hall**.

   ![Eseményközpont](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Görgessen az események, és jegyezze fel az utolsó esemény a listában.

   ![Legutóbbi esemény jelenik meg.](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>Az utolsó esemény "Véletlenül" törlése

1. A PowerShell ISE-ben nyissa meg a... \\Tanulási modulok\\üzletmenet-folytonossági és vészhelyreállítási\\RestoreTenant\\*Demo-RestoreTenant.ps1*, és állítsa be a következő értéket:

   * **$DemoScenario** = **1**, *törlése (a nem jegyeladásokkal) utolsó esemény*.
2. Nyomja le az F5 billentyűt a szkript futtatásához és az utolsó esemény törlése. A következő megerősítő üzenetet jelenik meg:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. A Contoso események lap nyílik meg. Görgessen lefelé, és győződjön meg arról, hogy az esemény szűnt. Ha az esemény továbbra is szerepel a listában, válassza ki a **frissítése** , és ellenőrizze, hogy felment.
   ![Legutóbbi esemény eltávolítása](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Az éles adatbázisban párhuzamosan bérlői adatbázis visszaállítása

Ebben a gyakorlatban a Contoso Concert Hall adatbázis visszaállítása egy időben az esemény törlése előtti. Ebben a forgatókönyvben azt feltételezi, hogy szeretné-e ellenőrizni a törölt adatokat egy párhuzamos adatbázisban.

 A *Restore-TenantInParallel.ps1* a szkript létrehoz egy párhuzamos bérlői adatbázist *ContosoConcertHall\_régi*, a párhuzamos katalógusbejegyzés. Ez a minta a visszaállítás olyan kisebb adatvesztés utáni helyreállításhoz. Is használhatja ezt a mintát, ha meg szeretné tekinteni a megfelelőségi és naplózási célokra adatok. Ez az ajánlott módszer használata esetén [aktív georeplikáció](sql-database-active-geo-replication.md).

1. Végezze el a [véletlenül törli az adatokat egy bérlő szimulálása](#simulate-a-tenant-accidentally-deleting-data) szakaszban.
2. A PowerShell ISE-ben nyissa meg a... \\Tanulási modulok\\üzletmenet-folytonossági és vészhelyreállítási\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
3. Állítsa be **$DemoScenario** = **2**, *párhuzamos visszaállítási bérlői*.
4. A szkript futtatásához nyomja le az F5.

Az parancsfájl visszaállítja a bérlői adatbázis egy időben az esemény törlése előtt. Az adatbázis helyreállítása egy új adatbázist a _ContosoConcertHall\_régi_. A katalógus metaadat, hogy megtalálható-e a visszaállított adatbázis törlődik, és majd az adatbázis bekerül a katalógusba értékekből összeállított kulcs használatával a *ContosoConcertHall\_régi* nevét.

A bemutató-parancsfájl az új bérlői adatbázis az események lapot a böngészőben nyílik meg. Megjegyzés: az URL-címből ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , hogy ezen a lapon látható-e adatokat a visszaállított adatbázisból ahol *_old* adnak hozzá a neve.

Az események a böngészőben, hogy ellenőrizze, hogy az esemény törölve az előző szakaszban felsorolt görgessen vissza lett állítva.

A visszaállított bérlőjéhez egy további bérlői, saját eseményeket alkalmazással is közzéteheti valószínű, hogy hogyan adja meg a visszaállított adatok bérlői hozzáférést lehet. A visszaállítási minta bemutatására szolgál. Általában csak olvasható hozzáférést biztosít a régi adatok, és a visszaállított adatbázis megőrzése egy adott időszakban. A mintában a visszaállított bérlői bejegyzés után törölheti a futó végzett a _távolítsa el a bérlő visszaállítása_ forgatókönyv.

1. Állítsa be **$DemoScenario** = **4**, *távolítsa el a bérlő visszaállítása*.
2. A szkript futtatásához nyomja le az F5.
3. A *ContosoConcertHall\_régi* bejegyzés törlődik a katalógusból. Zárja be az események lapot a böngészőben a bérlőhöz.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Egy bérlő helyen, és cserélje le a meglévő bérlői adatbázis visszaállítása

Ebben a gyakorlatban a Contoso Concert Hall bérlő egy pontot állítja vissza az esemény törlése előtti. A *Restore-TenantInPlace* szkript visszaállít egy bérlői adatbázist egy új adatbázist, és törli az eredeti. A visszaállítási minta a legalkalmasabb, helyreállítás, az súlyos adatvesztést, és a bérlő előfordulhat, hogy jelentős adatvesztés alkalmazkodnia kell.

1. A PowerShell ISE-ben nyissa meg a **Demo-RestoreTenant.ps1** fájlt.
2. Állítsa be **$DemoScenario** = **5**, *visszaállítási bérlői helyen*.
3. A szkript futtatásához nyomja le az F5.

A parancsfájl visszaállítja Önnek a bérlői adatbázis pontra, mielőtt az esemény törölve lett. Először a Contoso Concert Hall bérlő offline állapotú ahhoz, hogy tovább tart. Ezt követően párhuzamos létrehozott adatbázisok esetében a visszaállítási pontból történő helyreállításával. A visszaállított adatbázis nevű időbélyeg, hogy az adatbázis neve nem lehet azonos a meglévő bérlői adatbázis nevére. Ezután a régi bérlői adatbázis törlődik, és a rendszer átnevezi a visszaállított adatbázis az eredeti adatbázis neve. Végül Contoso Concert Hall online állapotúra a visszaállított adatbázisból az alkalmazás hozzáférésének engedélyezéséhez.

Sikeresen visszaállította a az adatbázis egy időben előtt az eseményt törölték. Ha a **események** lap megnyitásakor, győződjön meg arról, hogy az utolsó esemény helyreállt.

Miután visszaállította az adatbázis, előtt érhető el az első teljes biztonsági mentés visszaállítása újra egy másik 10 – 15 percet vesz igénybe.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis visszaállítása egy párhuzamos adatbázisba (egymás mellett).
> * Egy helyen adatbázis visszaállítása.

Próbálja ki a [kezelés bérlői adatbázis-séma](saas-tenancy-schema-management.md) oktatóanyag.

## <a name="additional-resources"></a>További források

* [A Wingtip SaaS-alkalmazás létrehozásához, további oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md)
* [Ismerje meg az SQL-adatbázisok biztonsági mentése](sql-database-automated-backups.md)
