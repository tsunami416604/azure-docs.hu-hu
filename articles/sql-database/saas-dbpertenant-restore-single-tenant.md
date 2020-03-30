---
title: Adatbázis visszaállítása több-bérlős SaaS-alkalmazásban
description: Megtudhatja, hogyan állítható kontor egyetlen bérlő SQL-adatbázisa az adatok véletlen törlése után
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818526"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Egyetlen bérlő visszaállítása bérlőnkénti adatbázissal SaaS-alkalmazással

Az adatbázis-bérlőnkénti modell megkönnyíti, hogy visszaállítsa az egyetlen bérlő egy korábbi időpontban anélkül, hogy más bérlők.

Ebben az oktatóanyagban két adat-helyreállítási mintát ismerhet meg:

> [!div class="checklist"]
> * Adatbázis visszaállítása párhuzamos adatbázisba (egymás mellett).
> * Adatbázis visszaállítása a meglévő adatbázis cseréje érdekében.

|||
|:--|:--|
| Visszaállítás párhuzamos adatbázisba | Ez a minta olyan feladatokhoz használható, mint például a felülvizsgálat, a naplózás és a megfelelőség, hogy a bérlő egy korábbi pontból ellenőrizhesse az adataikat. A bérlő jelenlegi adatbázisa online állapotban marad, és változatlan marad. |
| Visszaállítás a helyén | Ez a minta általában egy korábbi pontra történő helyreállításra szolgál, miután a bérlő véletlenül törli vagy megrongálja az adatokat. Az eredeti adatbázis lekerül a vonalról, és a visszaállított adatbázisra cserélődik. |
|||

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip SaaS-alkalmazás telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. További információt az [Azure PowerShell – első lépések.](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Bevezetés a SaaS-bérlői visszaállítási mintákba

Két egyszerű minták egy adott bérlő adatainak visszaállításához két egyszerű mintákat. Mivel a bérlői adatbázisok el vannak különítve egymástól, egy bérlő visszaállítása nincs hatással a többi bérlő adataira. Az Azure SQL Database point-in-time-restore (PITR) szolgáltatás mindkét mintában használatos. A PITR mindig új adatbázist hoz létre.

* **Visszaállítás párhuzamosan**: Az első mintában egy új párhuzamos adatbázis jön létre a bérlő aktuális adatbázisa mellett. A bérlő ezután csak olvasható hozzáférést kap a visszaállított adatbázishoz. A visszaállított adatok áttekinthetők, és potenciálisan felhasználhatók az aktuális adatértékek felülírására. Az alkalmazástervező feladata annak meghatározása, hogy a bérlő hogyan fér hozzá a visszaállított adatbázishoz, és milyen helyreállítási lehetőségek állnak rendelkezésre. Egyszerűen lehetővé teszi a bérlő, hogy tekintse át az adatokat egy korábbi ponton lehet, hogy minden, ami szükséges bizonyos forgatókönyvekben.

* **Visszaállítás a helyén**: A második minta akkor hasznos, ha az adatok elvesztek vagy sérültek, és a bérlő vissza szeretne térni egy korábbi pontra. A bérlő az adatbázis visszaállítása közben lesz kikapcsolva. Az eredeti adatbázis törlődik, és a visszaállított adatbázis neve megtörténik. Az eredeti adatbázis biztonsági mentési lánca a törlés után is elérhető marad, így szükség esetén visszaállíthatja az adatbázist egy korábbi időpontra.

Ha az adatbázis [aktív georeplikációt](sql-database-active-geo-replication.md) és párhuzamos visszaállítást használ, javasoljuk, hogy a visszaállított példányból a szükséges adatokat másolja az eredeti adatbázisba. Ha az eredeti adatbázist a visszaállított adatbázisra cseréli, újra kell konfigurálnia és újra kell szinkronizálnia a georeplikációt.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis-bérlőnkénti alkalmazásparancsfájlok beszereznie

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub tárház. A Wingtip Tickets SaaS-parancsfájlok letöltésének és blokkolásának feloldásához az általános útmutatásban talál [útmutatást.](saas-tenancy-wingtip-app-guidance-tips.md)

## <a name="before-you-start"></a>Előkészületek

Adatbázis létrehozásakor 10–15 percet is igénybe vehet, amíg az első teljes biztonsági mentés visszaállítható. Ha csak telepítette az alkalmazást, előfordulhat, hogy várnia kell néhány percet, mielőtt megpróbálna ezzel a forgatókönyvvel.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Bérlő véletlen törlése az adatokról

Ezek a helyreállítási forgatókönyvek bemutatásához először "véletlenül" töröljön egy eseményt az egyik bérlői adatbázisból. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Az Események alkalmazás megnyitása az aktuális események áttekintéséhez

1. Nyissa meg azhttp://events.wtp.&ltEseményközpontot ( ;user&gt;.trafficmanager.net), és válassza **a Contoso Concert Hall**lehetőséget.

   ![Eseményközpont](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Görgessen az események listájában, és jegyezze fel a lista utolsó eseményét.

   ![Utoljára esemény jelenik meg](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Véletlenül" törli az utolsó eseményt

1. A PowerShell ISE,open ... \\Tanulási modulok\\üzletmenet-folytonossági\\és\\vész-helyreállítási visszaállítási*demó-restoretenant.ps1*, és állítsa be a következő értéket:

   * **$DemoScenario** = **1**, Utolsó esemény *törlése (jegyértékesítés nélkül).*
2. Nyomja le az F5 billentyűt a parancsfájl futtatásához és az utolsó esemény törléséhez. A következő megerősítő üzenet jelenik meg:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Megnyílik a Contoso események lap. Görgessen le, és ellenőrizze, hogy az esemény eltűnt-e. Ha az esemény még mindig szerepel a listában, válassza a **Frissítés** lehetőséget, és ellenőrizze, hogy eltűnt-e.
   ![Utolsó esemény eltávolítva](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Bérlői adatbázis visszaállítása az éles adatbázissal párhuzamosan

Ez a gyakorlat visszaállítja a Contoso Concert Hall adatbázisát az esemény törlése előtti időpontra. Ebben az esetben feltételezi, hogy szeretné áttekinteni a törölt adatokat egy párhuzamos adatbázisban.

 A *Restore-TenantInParallel.ps1* parancsfájl létrehoz egy párhuzamos bérlői adatbázis nevű *ContosoConcertHall\_régi*, egy párhuzamos katalógus bejegyzést. Ez a visszaállítási minta a legalkalmasabb egy kisebb adatvesztésből való helyreállításhoz. Ezt a mintát akkor is használhatja, ha megfelelőségi vagy naplózási célokra adatokat kell áttekintenie. Ez az ajánlott megközelítés [az aktív georeplikáció](sql-database-active-geo-replication.md)használatakor.

1. Fejezze be a [Bérlő véletlen törlése az adatokat](#simulate-a-tenant-accidentally-deleting-data) szakasz.
2. A PowerShell ISE,open ... \\Tanulási modulok\\üzletmenet-folytonosságés\\vész-helyreállítási visszaállítási bérlő\\_demo-restoretenant.ps1_.
3. Állítsa **be $DemoScenario** = **2**, A *bérlő visszaállítása párhuzamosan*.
4. A parancsfájl futtatásához nyomja le az F5 billentyűt.

A parancsfájl visszaállítja a bérlői adatbázist egy adott időpontra, mielőtt törölte az eseményt. Az adatbázis visszaáll egy új adatbázis nevű _\_ContosoConcertHall régi_. A visszaállított adatbázisban található katalógus metaadatai törlődnek, majd az adatbázis a *\_ContosoConcertHall régi* nevéből származó kulcs használatával kerül a katalógusba.

A bemutató parancsfájl megnyitja az új bérlői adatbázis eseményoldalát a böngészőben. Az URL-címből ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` vegye figyelembe, hogy ez a lap a visszaállított adatbázis azon adatait jeleníti *meg,* ahol _old a névhez adódik.

Görgessen a böngészőben felsorolt események között annak ellenőrzéséhez, hogy az előző szakaszban törölt esemény helyreállt-e.

A visszaállított bérlő további bérlőként való felfedése saját Események alkalmazással nem valószínű, hogy hogyan biztosít bérlői hozzáférést a visszaállított adatokhoz. A visszaállítási minta szemléltetésére szolgál. Általában csak olvasható hozzáférést biztosít a régi adatokhoz, és a visszaállított adatbázist egy meghatározott ideig megőrzi. A mintában törölheti a visszaállított bérlői bejegyzést, miután befejezte a _visszaállított bérlői_ forgatókönyv eltávolítása futtatásával.

1. Állítsa **be $DemoScenario** = **4**, Távolítsa el a *visszaállított bérlőt.*
2. A parancsfájl futtatásához nyomja le az F5 billentyűt.
3. A *\_ContosoConcertHall régi* bejegyzése törlődik a katalógusból. Zárja be a bérlő eseménylapját a böngészőben.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Bérlő visszaállítása a helyén, a meglévő bérlői adatbázis cseréje

Ez a gyakorlat visszaállítja a Contoso Concert Hall bérlő egy pontra, mielőtt az esemény törölve lett. A *Restore-TenantInPlace* parancsfájl visszaállítja a bérlői adatbázist egy új adatbázisba, és törli az eredetit. Ez a visszaállítási minta a legalkalmasabb a súlyos adatsérülés ből való helyreállításhoz, és a bérlőnek jelentős adatvesztést kell kezelnie.

1. A PowerShell ISE-ben nyissa meg a **Demo-RestoreTenant.ps1** fájlt.
2. Állítsa be **$DemoScenario** = **5**, *Bérlő visszaállítása a helyén*.
3. A parancsfájl futtatásához nyomja le az F5 billentyűt.

A parancsfájl visszaállítja a bérlői adatbázist egy pontra az esemény törlése előtt. Először a Contoso Concert Hall bérlőt kapcsolja ki a rendszerből a további frissítések megakadályozása érdekében. Ezután egy párhuzamos adatbázis jön létre a visszaállítási pontról való visszaállítással. A visszaállított adatbázis neve időbélyegzővel történik, hogy az adatbázis neve ne ütközjön a meglévő bérlői adatbázis nevével. Ezután a régi bérlői adatbázis törlődik, és a visszaállított adatbázis az eredeti adatbázis nevére lesz átnevezve. Végül a Contoso Koncertterem online állapotba kerül, hogy az alkalmazás hozzáférhessen a visszaállított adatbázishoz.

Sikeresen visszaállította az adatbázist az esemény törlése előtti időpontra. Az **Események** lap megnyitásakor ellenőrizze, hogy az utolsó esemény helyre állt-e.

Az adatbázis visszaállítása után további 10–15 percet vesz igénybe, amíg az első teljes biztonsági mentés ismét visszaállítható.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis visszaállítása párhuzamos adatbázisba (egymás mellett).
> * Adatbázis visszaállítása a helyén.

Próbálja ki a [Bérlői adatbázis séma kezelése](saas-tenancy-schema-management.md) oktatóanyag.

## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek a Wingtip SaaS alkalmazásra épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md)
* [Tudnivalók az SQL Database biztonsági másolatairól](sql-database-automated-backups.md)
