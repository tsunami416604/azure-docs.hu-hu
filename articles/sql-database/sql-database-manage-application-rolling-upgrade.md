---
title: Működés közbeni alkalmazásfrissítések – Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure SQL Database georeplikációja támogatási online frissítéseket a felhőalapú alkalmazások.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 63f301b4618df9764460d0a9a133834fb72e33bb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540584"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>SQL-adatbázis aktív georeplikáció használatával kezelheti a felhőalapú alkalmazások működés közbeni frissítése

Ismerje meg, hogyan használható [aktív georeplikáció](sql-database-auto-failover-group.md) ahhoz, hogy a felhőalapú alkalmazások működés közbeni frissítése az Azure SQL Database-ben. Mivel a frissítések zavart okozó operations, akkor a folyamatos üzletmenet – tervezési és kialakítási részének kell lennie. Ebben a cikkben azt tekintse meg a frissítési folyamat replikálásával segít a vállalatnak két eltérő módszert és beszélgetés előnyeiről és hátrányairól az egyes lehetőségek. Ez a cikk célja hogy tekintse meg egy alkalmazás, amely egy webhely, amely csatlakozik egy önálló adatbázis, mint az adatréteg áll. Az alkalmazás 2-es verziójú (V2) anélkül, hogy a felhasználói élmény jelentős hatással az célunk, hogy frissítése (V1) 1-es verzió.

Ha kipróbálja a frissítési beállítások, vegye figyelembe a következőket:

* A művelet hatása rendszerre, például hogy mennyi ideig alkalmazás funkciók előfordulhat, hogy korlátozott vagy csökkentett teljesítményű rendelkezésre állását.
* Lehetővé teszi állítja vissza, ha a frissítés sikertelen lesz.
* Biztonsági rés az alkalmazás, hogy egy független, Katasztrofális hiba esetén a frissítés során.
* Teljes dollár költsége. Ez magában foglalja a további adatbázis-redundancia és a frissítési folyamat által használt ideiglenes összetevőket a növekményes költségekkel.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>A vész-helyreállítási adatbázis biztonsági mentését használó alkalmazások frissítése

Ha az alkalmazás az adatbázis automatikus biztonsági mentések támaszkodik, és a vész-helyreállítási geo-visszaállítás használ, egyetlen Azure-régióban üzemel. Felhasználói leskálázáskor, hozzon létre egy átmeneti környezetben az összes alkalmazás-összetevőket a frissítés részt az adott régióban. Az első ábra szemlélteti a működési környezetben a frissítés előtt. A végpont `contoso.azurewebsites.net` éles környezetben a webalkalmazás jelöli. Hogy a frissítés visszaállítása, létre kell hoznia egy átmeneti környezetben a teljesen szinkronizált másolatot készít az adatbázisról. Kövesse az alábbi lépéseket követve létrehozhat egy átmeneti környezetben a frissítés:

1. Hozzon létre egy másodlagos adatbázis ugyanazon Azure-régióban. Figyelje meg, ha a kiindulási folyamat befejeződött (1)-e a másodlagos.
2. Hozzon létre egy új környezetet a webalkalmazás, és nevezze el "Átmeneti". Az Azure DNS-ben az URL-címmel lesz regisztrálva `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Előkészítő lépések nem érinti az éles környezetben, amely teljes hozzáférési módban működjön.

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Amikor az előkészítési végzett, a tényleges frissítés készen áll az alkalmazás. A következő diagram azt ábrázolja, hogy a frissítési folyamat lépéseit:

1. Állítsa be az elsődleges adatbázis csak olvasható módra (3). Ebben a módban garantálja, hogy az éles környezetben, a web app (V1) csak olvasható marad a frissítés során, így megakadályozza a adateltérésekkel a V1 és v2-es adatbázis-példányok között.
2. A másodlagos adatbázis leválasztása a tervezett lezárást mód (4) segítségével. Ez a művelet létrehoz egy teljesen szinkronizált, független az elsődleges adatbázis másolatát. Ez az adatbázis lesz frissítve.
3. A másodlagos adatbázis írható-olvasható módba kapcsolni, és futtassa a frissítési parancsfájl (5).

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Ha a frissítés sikeres befejezését követően készen áll felhasználók váltson át a frissített másolatot az alkalmazásról, így az válik az éles környezetben. Néhány további lépést váltás áll, a következő ábrán szemléltetett módon:

1. Aktiválja a csereművelet éles és átmeneti környezetek, a web app (6) között. Ez a művelet vált, amennyiben a két környezet URL-címei. Most már `contoso.azurewebsites.net` a V2 verzióját a webhely és az adatbázis (éles környezet) mutat. 
2. Ha már nincs szüksége a V1 verziót, amely egy tesztelési másolatot vált a felcserélés után, leszerelheti az átmeneti környezetben (7).

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Ha a frissítési folyamat (például a frissítési parancsfájl hibája) miatt sikertelen, fontolja meg az átmeneti környezet sérülhet. Szeretné visszaállítani az alkalmazás a frissítés előtti állapotra, visszatérhet az alkalmazás az éles környezetben való teljes hozzáférés. A következő ábrán a reverziós lépéseket:

1. Az adatbázis-másolat állítsa írható-olvasható módban (8). Ez a művelet visszaállítja az éles üzemi példány V1 funkcióját.
2. Az alapvető okok elemzését, és az átmeneti környezetben (9) leszerelése.

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket megismételve.

> [!NOTE]
> A visszaállítás nem igényel a DNS-módosítások, mert nem végezte még el a felcserélési műveletet.

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Ez a beállítás legfontosabb előnye, hogy egy alkalmazás egy adott régióban frissítheti az alábbi egyszerű lépéseket egy készletét. A frissítés dollár költségei viszonylag kis. 

A rendszer a fő kompromisszummal jár, hogy a frissítés során végzetes hiba akkor fordul elő, ha a helyreállítás a frissítés előtti állapotba magában foglalja a más régióban az alkalmazás újbóli telepítése és az adatbázis biztonsági másolatból történő visszaállítását a geo-visszaállítás használatával. Ez a folyamat jelentős állásidőt eredményez.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Frissítse a database georeplikációja a vész-helyreállítási használó alkalmazások

Ha az alkalmazás aktív georeplikáció által vagy automatikus feladatátvételi csoportok az üzletmenet folytonosságának, legalább két különböző régióban üzemel. Egy aktív, az elsődleges adatbázis egy elsődleges régióban, és a egy csak olvasható, másodlagos adatbázist egy biztonsági mentési régióban van. Ez a cikk elején említett tényezők, valamint a frissítési folyamat is garantálnia kell, hogy:

* Az alkalmazás mindig a frissítési folyamat során a katasztrofális hibák védett marad.
* A georedundáns összetevő az alkalmazás frissítésének párhuzamos az aktív összetevőkkel.

Az ezen célok eléréséhez, mellett a Web Apps-környezeteket használó feladatátvételi profilt használó egy aktív végpontot és a egy biztonsági mentési végpontot fog igénybe vehetik az Azure Traffic Manager. A következő ábra szemlélteti a működési környezetben a frissítés előtt. A webhelyek `contoso-1.azurewebsites.net` és `contoso-dr.azurewebsites.net` jelölik az alkalmazás éles környezetben a teljes földrajzi redundancia céljából. Az éles környezetben a következő összetevőket tartalmazza:

* Az éles környezetben a webalkalmazás `contoso-1.azurewebsites.net` az elsődleges régióban (1)
* Az elsődleges régióban (2) az elsődleges adatbázis
* A web app (3) a biztonsági mentési régióban készenléti példánya
* A georeplikált másodlagos adatbázis a biztonsági mentési régióban (4)
* Online végpont a Traffic Manager teljesítményének profil nevű `contoso-1.azurewebsites.net` és a egy offline végpont neve `contoso-dr.azurewebsites.net`

Lehetővé teszik a frissítés visszaállítása, létre kell hoznia egy átmeneti környezetben az alkalmazás teljesen szinkronizált másolatával. Győződjön meg arról, hogy az alkalmazás képes gyorsan helyreállíthatja az adatokat a frissítési folyamat során végzetes hiba bekövetkezése van szüksége, mert az átmeneti környezetben kell georedundáns tárolást is. Hozzon létre egy átmeneti környezetben a frissítés a következő lépések szükségesek:

1. Helyezze üzembe helyezése átmeneti környezetben a webalkalmazás az elsődleges régióban (6).
2. Hozzon létre egy másodlagos adatbázis elsődleges Azure-régióban (7). Az átmeneti környezetben szeretne csatlakozni, a webalkalmazás konfigurálásához. 
3. Hozzon létre egy másik georedundáns tárolást, a másodlagos adatbázis a biztonsági mentési régióban az elsődleges régióban, a másodlagos adatbázis replikálásával. (Ezt a metódust meghívják *georeplikációs láncolt*.) (8).
4. Üzembe helyezése átmeneti környezetben, a web app-példány a biztonsági mentési régióban (9), és konfigurálja úgy, hogy csatlakozzon a georedundáns másodlagos adatbázis (8).

> [!NOTE]
> Előkészítő lépések nem érinti az éles környezetben az alkalmazást. Írható-olvasható módban teljesen működőképes marad.

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Az előkészítési végzett, amikor a frissítés készen áll az átmeneti környezetben. A következő diagram azt ábrázolja, hogy ezeket a frissítési lépéseket:

1. Állítsa be az elsődleges adatbázis csak olvasható módra (10) az éles környezetben. Ebben a módban garantálja, hogy az éles adatbázisban (V1) időközben nem változnak a frissítés, így megakadályozza a adateltérésekkel a V1 és v2-es adatbázis-példányok között.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Állítsa le a georeplikáció leválasztásával a másodlagos (11). Ez a művelet létrehoz egy független, de teljes körűen szinkronizált másolatot az éles adatbázisban. Ez az adatbázis lesz frissítve. Az alábbi példában a Transact-SQL, de [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) is rendelkezésre áll. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABSE V1
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Frissítési szkriptek futtatása `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`, és az elsődleges adatbázis átmeneti (12). Az adatbázis-módosítások automatikusan replikálja az előkészítési másodlagos.

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Ha a frissítés sikeresen befejeződött, készen áll a felhasználók váltson az alkalmazás V2 verziója. A következő diagram azt ábrázolja, hogy a folyamat lépései:

1. Aktiválja a csereművelet éles és átmeneti környezetek, a web app (13) az elsődleges régióban, és a biztonsági mentési régióban (14) között. V2 alkalmazás most már éles környezetben, a biztonsági mentési régióban található redundáns másolatát válik.
2. Ha már nincs szüksége a V1-alkalmazás (15 és 16), leszerelheti az átmeneti környezetben.

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Ha a frissítési folyamat (például a frissítési parancsfájl hibája) miatt sikertelen, fontolja meg az átmeneti környezetben egy inkonzisztens állapotban kell lennie. V1 alkalmazás használatával az üzemi környezetben szeretné visszaállítani az alkalmazás a frissítés előtti állapotba, vissza. A szükséges lépéseket a következő diagramon láthatók:

1. Állítsa be az elsődleges adatbázis-másolat (17) írható-olvasható módba az éles környezetben. Ez a művelet visszaállítja az összes V1 funkciójának az éles környezetben.
2. Hajtsa végre az alapvető okok elemzése és a javítás, vagy távolítsa el az átmeneti környezetben (18 és 19).

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket megismételve.

> [!NOTE]
> A visszaállítás nincs szükség DNS változik, mert nem hajtja végre a csereművelet.

![SQL-adatbázis georeplikációs konfiguráció felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Ez a beállítás legfontosabb előnye, hogy frissítheti az alkalmazás és a párhuzamos georedundáns példányát a frissítés során az üzletmenet-folytonossági veszélyeztetése nélkül.

A fő kompromisszummal jár, hogy minden egyes alkalmazás-összetevő kettős redundancia igényel, és ezért tekintetében költségesebb dollár. Egy összetettebb munkafolyamat is magában foglalja.

## <a name="summary"></a>Összegzés

A két frissítési módszer a cikkben ismertetett, dollár költséghatékonyság különböznek, de mindkettő összpontosíthat minimalizálása, mennyi ideig a felhasználó korlátozódik, csak olvasható műveletekhez. A frissítési parancsfájl időtartama közvetlenül határozza meg, hogy időt. Nem függ az adatbázis mérete, a szolgáltatási rétegben választotta, a webhely-konfiguráció vagy egyéb tényezőkkel, amelyek nem könnyen szabályozhatja. Az összes előkészítési lépéseket vannak választva a frissítési lépéseket a, és nincs hatással az éles alkalmazások. A frissítési parancsfájl hatékonyságának egy kulcsfontosságú tényező, amely meghatározza, hogy a felhasználói élmény frissítések során. Így a legjobb módszer annak, hogy a gyakoriságukat a lehető leghatékonyabb tétele a frissítési parancsfájl.

## <a name="next-steps"></a>További lépések

* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* Az Azure SQL-adatbázis aktív georeplikációt kapcsolatos további információkért lásd: [olvasható másodlagos adatbázis aktív georeplikációt használ létrehozása](sql-database-active-geo-replication.md).
* Azure SQL Database automatikus feladatátvételi csoportok kapcsolatos további információkért lásd: [automatikus feladatátvételi csoportok használatával engedélyezhető az átlátható és koordinált több adatbázis feladatátvételét](sql-database-auto-failover-group.md).
* Átmeneti környezetek, az Azure App Service kapcsolatos további információkért lásd: [állítsa be átmeneti környezeteket az Azure App Service](../app-service/deploy-staging-slots.md).
* Az Azure Traffic Manager-profilok kapcsolatos további információkért lásd: [kezelése az Azure Traffic Manager-profilok](../traffic-manager/traffic-manager-manage-profiles.md).
