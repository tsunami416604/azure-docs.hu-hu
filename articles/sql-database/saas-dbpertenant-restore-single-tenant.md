---
title: A több-bérlős SaaS-alkalmazás az Azure SQL-adatbázis visszaállítása |} Microsoft Docs
description: 'Útmutató: a egybérlős SQL-adatbázis visszaállítása után véletlenül az adatok törlése'
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: eb7e87934269a5e1ba453e20f6f409a10dfbda5b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Állítsa vissza egy egy bérlőt egy adatbázis-/-bérlő SaaS-alkalmazáshoz

Az adatbázis-/-bérlős modell segítségével egyszerűen visszaállítása egy egybérlős egy korábbi idő nem befolyásolja a többi bérlő.

Ebben az oktatóanyagban elsajátíthatja, két adatok helyreállítási minták:

> [!div class="checklist"]

> * Adatbázis helyreállítása egy párhuzamos adatbázisba (egymás mellett).
> * A mag cseréje a meglévő adatbázis helyben, adatbázis visszaállítása.


|||
|:--|:--|
| Egy párhuzamos adatbázisba | Ez a minta segítségével olyan feladatokhoz, mint tekintse át, a naplózás és a megfelelőség engedélyezése a bérlő az adatok korábbi pontról vizsgálata. A bérlő aktuális adatbázis online állapotú, és változatlan marad. |
| A hely visszaállítása | Ebben a mintában általában egy bérlőt egy korábbi időpontbeli helyreállítása után a bérlő véletlenül törli vagy adatok megsérülnek használatos. Az eredeti adatbázist offline állapotba hozni, és a visszaállított adatbázis helyett. |
|||

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>A Szolgáltatottszoftver-bérlői visszaállítási minták bemutatása

Nincsenek egy adott bérlő adatok visszaállításához két egyszerű mintát. Bérlői adatbázisok el különítve egymástól, mert egy bérlő visszaállítása nem hatást gyakorol semmilyen más bérlővel adatokat. Az Azure SQL Database pontot-idő visszaállítási (PITR) szolgáltatással két minta használatban van. PITR mindig létrehoz egy új adatbázist.   

* **Párhuzamos visszaállítása**: az első mintában új párhuzamos adatbázis mellett az aktuális adatbázisban a bérlő jön létre. A bérlő majd írásvédett való hozzáférése a visszaállított adatbázis. A visszaállított adatok tekintse át, és felülírja a jelenlegi adatértékek potenciálisan használt. Már a Tervező annak meghatározásához, hogy a bérlő hogyan érheti el a visszaállított adatbázis, és milyen helyreállítási lehetőségeket. Egyszerűen lehetővé teszi a bérlő, hogy tekintse át az adatokat egy olyan korábbi időpontban minden egyes esetekben szükséges lehet. 

* **A hely visszaállítása**: A második minta akkor hasznos, ha adatok elvesztésének vagy sérült, és a bérlői szeretne rendelni egy korábbi állapotba való visszaállítását. A bérlő offline állapotú lesz végrehajtva, amíg az adatbázis helyreállítása. Az eredeti adatbázis törlődik, és a rendszer átnevezi a visszaállított adatbázis. Az eredeti adatbázis a biztonsági mentési láncolatát a törlés után elérhető marad, így helyreállíthatja az adatbázis egy korábbi állapotba időben, ha szükséges.

Ha az adatbázis használ [georeplikáció](sql-database-geo-replication-overview.md) és visszaállítása párhuzamosan, azt javasoljuk, bármilyen szükséges adatok másolása a visszaállított másolja át az eredeti adatbázist a. Ha az eredeti adatbázis cserélje a visszaállított adatbázis, kell konfigurálnia, majd szinkronizálja újra a georeplikáció.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-Bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseiért lásd: a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Előkészületek

Amikor egy adatbázist hoz létre, 10 – 15 perc múlva az első teljes biztonsági másolatból történő visszaállítását is igénybe vehet. Ha éppen most telepítette az alkalmazást, akkor várjon néhány percig, mielőtt újból ebben a forgatókönyvben módosítania.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>A bérlő véletlenül törli az adatok szimulálása

Helyreállítási forgatókönyvekben bemutatásához, először "véletlenül" esemény törlése a bérlő adatbázisok egyikében. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Nyissa meg az események alkalmazásnak, hogy tekintse át a jelenlegi eseményeket

1. Nyissa meg az események Hub (http://events.wtp.&lt; felhasználói&gt;. trafficmanager.net), és válassza ki **Contoso energiaoptimalizálást egyszerre Hall**.

   ![Events Hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Görgessen az események listájának, és jegyezze fel az utolsó esemény a listában.

   ![Utolsó esemény jelenik meg](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Az utolsó esemény "Véletlenül" törlése

1. A PowerShell ISE megnyitása... \\Tanulási modulok\\az üzletmenet folytonossága és vészhelyreállítás\\RestoreTenant\\*bemutató-RestoreTenant.ps1*, és a következő értéket:

   * **$DemoScenario** = **1**, *delete utolsó esemény (nincs jegy értékesítési)*.
2. Nyomja le az F5 billentyűt a parancsfájl futtatásához és az utolsó esemény törlése. A következő megerősítő üzenet jelenik meg:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. A Contoso események lap nyílik meg. Görgessen lefelé, és győződjön meg arról, hogy az esemény már nem. Ha az esemény továbbra is szerepel a listán, válassza ki a **frissítése** , és ellenőrizze a állapotba került.

   ![Legutóbbi esemény eltávolítása](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Az éles adatbázis párhuzamosan bérlői adatbázis visszaállítása

Ebben a gyakorlatban adatbázisát állítja vissza a Contoso energiaoptimalizálást egyszerre Hall pontra előtti az esemény törölve lett. Ebben a forgatókönyvben azt feltételezi, hogy szeretné-e megtekinteni a törölt adatokat egy párhuzamos adatbázisban.

 A *visszaállítási-TenantInParallel.ps1* parancsfájl egy párhuzamos bérlői nevű adatbázist hoz létre *ContosoConcertHall\_régi*, a párhuzamos katalógusbejegyzés. Ebben a mintában a visszaállítás olyan kisebb adatvesztés helyreállítására. Is használhatja ezt a mintát Ha meg szeretné tekinteni a megfelelőségi és naplózási célokra adatokat. Ez az ajánlott módszer használata esetén [georeplikáció](sql-database-geo-replication-overview.md).

1. Fejezze be a [véletlenül törli az adatokat a bérlők szimulálása](#simulate-a-tenant-accidentally-deleting-data) szakasz.
2. A PowerShell ISE megnyitása... \\Tanulási modulok\\az üzletmenet folytonossága és vészhelyreállítás\\RestoreTenant\\_bemutató-RestoreTenant.ps1_.
3. Állítsa be **$DemoScenario** = **2**, *párhuzamos visszaállítási bérlői*.
4. A parancsfájl futtatásához nyomja le az F5 billentyűt.

A parancsfájl adatbázisát állítja vissza a bérlő pontra időben az esemény törlése előtt. Az adatbázis helyreállítása nevű új adatbázis _ContosoConcertHall\_régi_. A katalógus metaadat már szerepel a visszaállított adatbázis törlődik, és ezután az adatbázis hozzáadódik a katalógus értékekből összeállított kulcs használatával a *ContosoConcertHall\_régi* nevét.

A bemutató-parancsfájl az új bérlő adatbázishoz az események lapot a böngészőben nyílik meg. Megjegyzés: az URL-címről ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , hogy ezen a lapon látható-e a visszaállított adatbázis ahol *_old* hozzáadódik a neve.

Görgessen az eseményeket, az esemény törölve az előző szakaszban szerepel annak ellenőrzésére, hogy a böngésző vissza lett állítva.

Az ilyen egy további bérlői saját események az alkalmazással, mint a visszaállított bérlő nem valószínű, hogy hogyan kívánja megadni a visszaállított adatok egy bérlő hozzáférést kell. Azt mutatja be a visszaállítási mintát szolgálja ki. Általában a régi adatok csak olvasási hozzáférést, és tartsa meg a visszaállított adatbázis számára meghatározott ideig. A példában a visszaállított bérlői bejegyzés után törölhető végzett a futtatásával a _eltávolítása vissza bérlői_ forgatókönyv.

1. Állítsa be **$DemoScenario** = **4**, *eltávolítása vissza bérlői*.
2. A parancsfájl futtatásához nyomja le az F5 billentyűt.
3. A *ContosoConcertHall\_régi* bejegyzés törlődik a katalógusból. Zárja be az események lapról ennél a bérlőnél a böngészőben.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>A bérlő helyen, a mag cseréje a meglévő bérlő adatbázis visszaállítása

Ebben a gyakorlatban a Contoso energiaoptimalizálást egyszerre Hall bérlő visszaállítása egy pont előtti az esemény törölve lett. A *visszaállítási-TenantInPlace* parancsfájl egy bérlő adatbázis visszaállítása egy új adatbázist, és törli az eredeti. Súlyos adatsérülés helyreállítani a visszaállítási zárolás olyan környezethez a legalkalmasabb, és a bérlői lehet jelentős adatvesztés befogadásához.

1. A PowerShell ISE nyissa meg a **bemutató-RestoreTenant.ps1** fájlt.
2. Állítsa be **$DemoScenario** = **5**, *visszaállítási bérlői helyen*.
3. A parancsfájl futtatásához nyomja le az F5 billentyűt.

A parancsfájl a bérlő adatbázis visszaállítása a pont előtti az esemény törölve lett. A Contoso energiaoptimalizálást egyszerre Hall bérlő offline állapotú további frissítések megelőzése érdekében először vesz igénybe. Ezt követően egy párhuzamos adatbázis állítja vissza a visszaállítási pont létrehozása. A visszaállított adatbázis egy időbélyeg nevű győződjön meg arról, hogy az adatbázis neve nem ütközik a meglévő bérlő adatbázis nevét. Ezután a régi bérlői adatbázisa törlődik, és a visszaállított adatbázis átnevezése az eredeti adatbázis neve. Végezetül Contoso energiaoptimalizálást egyszerre Hall online állapotba a visszaállított adatbázis alkalmazás hozzá.

Sikeresen visszaállította a az adatbázis pontra előtti az esemény törölve lett. Ha a **események** lap megnyitásakor, győződjön meg arról, hogy az utolsó esemény lett visszaállítva.

Az adatbázis helyreállítása után visszaállíthatja őket ismét érhető el az első teljes biztonsági mentés előtt egy másik 10 – 15 percet vesz igénybe. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Adatbázis helyreállítása egy párhuzamos adatbázisba (egymás mellett).
> * Egy helyen adatbázis visszaállítása.

Próbálja meg a [kezelése bérlői adatbázisséma](saas-tenancy-schema-management.md) oktatóanyag.

## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md)
* [További tudnivalók az SQL-adatbázis biztonsági mentése](sql-database-automated-backups.md)
