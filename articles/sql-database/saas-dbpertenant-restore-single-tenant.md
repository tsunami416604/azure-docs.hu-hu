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
ms.openlocfilehash: 46471073f88247510f45d6c4152afa43be6e1aaa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Egy egybérlős SaaS-alkalmazás bérlőnként egy adatbázis visszaállítása

A bérlői modell teszi adatbázishoz is könnyen visszaállítása egy egybérlős egy korábbi idő más bérlők befolyásolása nélkül.

Ebben az oktatóanyagban elsajátíthatja, két adatok helyreállítási minták:

> [!div class="checklist"]

> * Egy párhuzamos adatbázisba (párhuzamos-) adatbázis visszaállítása
> * A mag cseréje a meglévő adatbázis helyben, adatbázis visszaállítása


|||
|:--|:--|
| **Egy párhuzamos adatbázisba** | Ebben a mintában használható tekintse át, a naplózás esetén a megfelelőségi, engedélyezi az adatok korábbi pontról vizsgálata a bérlő stb.  A bérlő aktuális adatbázis online állapotú, és változatlan marad. |
| **A hely visszaállítása** | Ebben a mintában általában egy bérlőt egy korábbi időpontbeli helyreállítása után a bérlő véletlenül törli vagy adatok megsérülnek használatos. Az eredeti adatbázist offline állapotba, és a visszaállított adatbázis helyett. |
|||

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>A Szolgáltatottszoftver-bérlői visszaállítási minták bemutatása

Nincsenek egy adott bérlő adatok visszaállításához két egyszerű mintát. Bérlői adatbázisok el különítve egymástól, mert egy bérlő visszaállítása nem hatást gyakorol semmilyen más bérlővel adatokat.  Az SQL-adatbázis pontot-idő visszaállítási (PITR) szolgáltatással két minta használatban van.  PITR mindig létrehoz egy új adatbázist.   

Az első mintában **párhuzamosan visszaállítása**, egy új párhuzamos adatbázis létrehozása a bérlői aktuális adatbázis mellett. A bérlő majd írásvédett való hozzáférése a visszaállított adatbázis. A visszaállított adatok tekintse át, és felülírja a jelenlegi adatértékek potenciálisan használt. Már a Tervező annak meghatározásához, hogy a bérlő hogyan érheti el a visszaállított adatbázis, és milyen helyreállítási lehetőségeket. Egyszerűen lehetővé teszi a bérlő, hogy tekintse át az adatokat egy olyan korábbi időpontban minden egyes esetekben szükséges lehet. 

A második minta **helyen visszaállítása**, akkor hasznos, ha az adatok elvesztése vagy sérülése, és a bérlői szeretne rendelni egy korábbi állapotba való visszaállítását.  A bérlő offline állapotba kerül, amíg az adatbázis helyreállítása. Az eredeti adatbázis törlődik, és a rendszer átnevezi a visszaállított adatbázis. A törlés, lehetővé téve, állítsa vissza az adatbázist egy korábbi időpontbeli időben szükség után elérhető marad az eredeti adatbázis a biztonsági mentési láncolatát.

Ha használ az adatbázis [georeplikáció](sql-database-geo-replication-overview.md) és visszaállítása párhuzamosan, azt javasoljuk, minden szükséges adatok másolását a visszaállított másolja át a az eredeti adatbázist. Ha az eredeti adatbázis cserélje a visszaállított adatbázis, kell konfigurálnia, majd szinkronizálja újra a georeplikáció.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.

## <a name="before-you-start"></a>Előkészületek

Amikor egy adatbázist hoz létre, 10 – 15 perc múlva az első teljes biztonsági másolatból történő visszaállítását is igénybe vehet.  Ha csak most telepítette az alkalmazás esetleg Várjon pár percet, mielőtt újból ebben a forgatókönyvben.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>A bérlő véletlenül törli az adatok szimulálása

A fenti helyreállítási forgatókönyvek először bemutatásához *"véletlenül"* egy bérlő adatbázis az esemény törlése. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Nyissa meg az események alkalmazásnak, hogy tekintse át a jelenlegi eseményeket

1. Nyissa meg a *események Hub* (http://events.wtp.&lt; felhasználói&gt;. trafficmanager.net), majd **Contoso energiaoptimalizálást egyszerre Hall**:

   ![eseményközpont](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Görgessen az események listájának, és jegyezze fel az utolsó esemény a listában:

   ![utolsó esemény](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Az utolsó esemény "Véletlenül" törlése

1. Nyissa meg... \\Tanulási modulok\\az üzletmenet folytonossága és vészhelyreállítás\\RestoreTenant\\*bemutató-RestoreTenant.ps1* a a *PowerShell ISE*, és a következő értéket:
   * **$DemoScenario** = **1**, törlési utolsó esemény (nincs jegy értékesítési).
1. Nyomja le az **F5** futtassa a parancsfájlt, és az utolsó esemény törlése. A következő megerősítő üzenetet kell megjelennie:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. A Contoso események lap nyílik meg. Görgessen lefelé, és győződjön meg arról az esemény már nem. Ha az esemény továbbra is szerepel a listában, kattintson a frissítés és győződjön meg arról eltűnt.

   ![utolsó esemény](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Az éles adatbázis párhuzamosan bérlői adatbázis visszaállítása

Ebben a gyakorlatban adatbázisát állítja vissza a Contoso energiaoptimalizálást egyszerre Hall pontra előtti az esemény törölve lett. Ebben a forgatókönyvben azt feltételezzük, hogy csak szeretné megtekinteni a törölt adatokat egy párhuzamos adatbázisban.

 A *visszaállítási-TenantInParallel.ps1* parancsfájl egy párhuzamos bérlői nevű adatbázist hoz létre *ContosoConcertHall\_régi*, a párhuzamos katalógusbejegyzés. Ebben a mintában a visszaállítás esetén a kisebb adatvesztés, helyreállítás a legmegfelelőbb, vagy ha meg szeretné tekinteni a megfelelőségi és naplózási célokra adatait. Ez egyben az ajánlott módszer használata esetén [georeplikáció](sql-database-geo-replication-overview.md).

1. Fejezze be a [véletlenül törli az adatokat a bérlők szimulálása](#simulate-a-tenant-accidentally-deleting-data) szakasz.
1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\az üzletmenet folytonossága és vészhelyreállítás\\RestoreTenant\\_bemutató-RestoreTenant.ps1_.
1. Állítsa be **$DemoScenario** = **2**, *párhuzamos visszaállítási bérlői*.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A parancsfájl adatbázisát állítja vissza a bérlő pontra időben az esemény törlése előtt. Az adatbázis helyreállítása új adatbázis nevű _ContosoConcertHall\_régi_. A katalógus metaadat már szerepel a visszaállított adatbázis törlődik, és majd az adatbázis hozzáadódik a katalógus értékekből összeállított kulccsal a *ContosoConcertHall\_régi* nevét.

A bemutató-parancsfájl az új bérlő adatbázishoz az események lapot a böngészőben nyílik meg. Megjegyzés: az URL-címről: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` , hogy ezen a lapon látható a visszaállított adatbázis ahol *_old* hozzáadódik a neve.

Görgessen a böngészőt annak ellenőrzéséhez, hogy visszaállította az esemény törölve az előző szakaszban felsorolt események.

Vegye figyelembe, hogy, egy további bérlői saját események alkalmazással nem valószínű, hogy hogyan szeretné biztosít egy bérlői hozzáférést kell, hogy csökkenjen a visszaállított bérlő visszaállította az adatokat, de mutatja be a visszaállítási mintát szolgál. A valóságban valószínűleg ehhez a régi adatok csak olvasható hozzáférést és tartsa meg a visszaállított adatbázis számára meghatározott ideig. A minta is törli a visszaállított bérlői bejegyzést, miután befejezte az futtatásával a _eltávolítása vissza bérlői_ forgatókönyv.

1. Állítsa be **$DemoScenario** = **4**, *eltávolítása bérlői visszaállítása*
1. **Végrehajtás** **használatával** **F5**
1. A *ContosoConcertHall\_régi* bejegyzés törlődik a katalógusból. Lépjen tovább, és zárja be az események lapról ennél a bérlőnél a böngészőben.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>A bérlő helyen, a mag cseréje a meglévő bérlő adatbázis visszaállítása

Ebben a gyakorlatban a Contoso energiaoptimalizálást egyszerre Hall bérlő visszaállítása egy pont előtti az esemény törölve lett. A *visszaállítási-TenantInPlace* parancsfájl egy bérlő adatbázis visszaállítása egy új adatbázist, és törli az eredeti.  Visszaállítás zárolás, előfordulhat, hogy jelentős adatvesztés, amelyek a bérlő olyan súlyos adatsérülés helyreállítás a legmegfelelőbb.

1. Nyissa meg **bemutató-RestoreTenant.ps1** a PowerShell ISE fájl
1. Állítsa be **$DemoScenario** = **5**, *visszaállítási bérlői helyen*.
1. Végrehajtás használatával **F5**.

A parancsfájl a bérlő adatbázis visszaállítása a pont előtti az esemény törölve lett. Először vesz igénybe a Contoso energiaoptimalizálást egyszerre Hall bérlő offline további frissítések megelőzése érdekében. Ezt követően egy párhuzamos adatbázis állítja vissza a visszaállítási pont létrehozása.  A visszaállított adatbázis neve az időbélyegzőnek annak érdekében, hogy az adatbázis neve nem ütközik a meglévő bérlő adatbázis nevét. Ezután a régi bérlői adatbázisa törlődik, és a visszaállított adatbázis átnevezése az eredeti adatbázis neve. Végezetül Contoso energiaoptimalizálást egyszerre Hall online állapotba a visszaállított adatbázis alkalmazás hozzá.

Sikeresen visszaállította a az adatbázis pontra előtti az esemény törölve lett. Az események lap megnyílik, ezért győződjön meg róla, az utolsó esemény vissza lett állítva.

Vegye figyelembe, hogy után állítsa vissza az adatbázist tart egy további 10 – 15 perc múlva az első teljes biztonsági visszaállíthatja őket újra. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Egy párhuzamos adatbázisba (párhuzamos-) adatbázis visszaállítása
> * Egy helyen adatbázis visszaállítása

[Bérlői adatbázisséma kezelése](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md)
* [További tudnivalók az SQL-adatbázis biztonsági mentése](sql-database-automated-backups.md)
