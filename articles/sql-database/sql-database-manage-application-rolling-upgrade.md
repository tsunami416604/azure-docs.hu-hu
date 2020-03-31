---
title: Működés közbeni alkalmazásfrissítések
description: Ismerje meg, hogyan használhatja az Azure SQL Database georeplikációját a felhőalapú alkalmazás online frissítéseinek támogatásához.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822861"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Felhőalapú alkalmazások működés közbeni frissítéseinek kezelése az SQL Database aktív georeplikációjával

Ismerje meg, hogyan [használhatja az aktív georeplikációt](sql-database-auto-failover-group.md) az Azure SQL Database-ben a felhőalapú alkalmazás működés közbeni frissítéseinek engedélyezéséhez. Mivel a frissítések zavaró műveletek, az üzletmenet-folytonossági tervezés és tervezés részét kell, hogy képezik. Ebben a cikkben a frissítési folyamat vezénylésének két különböző módszerét vizsgáljuk, és megtárgyaljuk az egyes lehetőségek előnyeit és kompromisszumait. A cikk alkalmazásában egy olyan alkalmazásra hivatkozunk, amely egy olyan webhelyből áll, amely adatrétegként egyetlen adatbázishoz csatlakozik. Célunk, hogy az alkalmazás 1-es verzióját (V1) frissítsük a 2-es verzióra (V2), anélkül, hogy jelentős hatással lenne a felhasználói élményre.

A frissítési lehetőségek értékelésekor vegye figyelembe az alábbi tényezőket:

* Hatással van az alkalmazások rendelkezésre állására a frissítések során, például azt, hogy az alkalmazásfunkciók mennyi ideig korlátozottak vagy korlátozottak lehetnek.
* A frissítés sikertelensége esetén a visszaállítás lehetősége.
* Az alkalmazás biztonsági rése, ha a frissítés során nem kapcsolódó, katasztrofális hiba lép fel.
* Teljes dollárköltség. Ez a tényező magában foglalja a további adatbázis-redundanciát és a frissítési folyamat által használt ideiglenes összetevők többletköltségeit.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>A vész-helyreállítási adatbázis biztonsági másolatait használó alkalmazások frissítése

Ha az alkalmazás automatikus adatbázis-biztonsági mentések és geo-visszaállítás t használ a vész-helyreállításhoz, egyetlen Azure-régióban üzembe helyezett. A felhasználói zavarok minimalizálása érdekében hozzon létre egy átmeneti környezetet az adott régióban a frissítésben részt vevő összes alkalmazás-összetevővel. Az első ábra a frissítési folyamat előtti működési környezetet mutatja be. A végpont `contoso.azurewebsites.net` a webalkalmazás éles környezetét jelöli. A frissítés visszaállításához létre kell hoznia egy átmeneti környezetet az adatbázis teljesen szinkronizált másolatával. A frissítés hez átmeneti környezet létrehozásához kövesse az alábbi lépéseket:

1. Hozzon létre egy másodlagos adatbázist ugyanabban az Azure-régióban. Figyelje a másodlagos, hogy ha a vetési folyamat befejeződött (1).
2. Hozzon létre egy új környezetet a webalkalmazáshoz, és hívja "Átmeneti" néven. Az Azure DNS-ben regisztrálva `contoso-staging.azurewebsites.net` lesz az URL-címmel (2).

> [!NOTE]
> Ezek az előkészítési lépések nem befolyásolják az éles környezetet, amely teljes hozzáférési módban is működhet.

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Amikor az előkészítési lépések befejeződtek, az alkalmazás készen áll a tényleges frissítésre. A következő ábra a frissítési folyamat lépéseit mutatja be:

1. Állítsa az elsődleges adatbázist írásvédett módba (3). Ez a mód garantálja, hogy a webalkalmazás (V1) éles környezete írásvédett maradjon a frissítés során, így megakadályozva a V1 és V2 adatbázispéldányok közötti adateltérést.
2. Válassza le a másodlagos adatbázist a tervezett befejezési mód (4) használatával. Ez a művelet létrehozza az elsődleges adatbázis teljesen szinkronizált, független másolatát. Ez az adatbázis frissítésre kerül.
3. Kapcsolja be a másodlagos adatbázist írási és írási módba, és futtassa a frissítési parancsfájlt (5).

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Ha a frissítés sikeresen befejeződik, most már készen áll arra, hogy a felhasználókat a frissített példányra váltsa az alkalmazásra, amely éles környezetté válik. A váltás néhány további lépést tartalmaz, amint azt a következő ábra szemlélteti:

1. A webalkalmazás éles és átmeneti környezetei közötti csereművelet aktiválása (6). Ez a művelet átkapcsolja a két környezet URL-címeit. Most `contoso.azurewebsites.net` rámutat, hogy a V2 változata a webhely és az adatbázis (éles környezet). 
2. Ha már nincs szüksége a V1-es verzióra, amely a csere után átmeneti példánylett, leszerelheti az átmeneti környezetet (7).

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Ha a frissítési folyamat sikertelen (például a frissítési parancsfájl hibája miatt), vegye figyelembe az átmeneti környezetet. Az alkalmazás visszaállítása a frissítés előtti állapotba, állítsa vissza az alkalmazást az éles környezetben teljes hozzáférésre. A következő ábra a visszafordítási lépéseket mutatja be:

1. Állítsa az adatbázis másolását írási-olvasási módba (8). Ez a művelet visszaállítja a gyártási példány teljes V1-funkcióját.
2. Végezze el a kiváltó ok elemzését, és szerelje le az átmeneti környezetet (9).

Ezen a ponton az alkalmazás teljesen működőképes, és megismételheti a frissítési lépéseket.

> [!NOTE]
> A visszaállítás nem igényel DNS-módosításokat, mert még nem végzett csereműveletet.

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Ennek a beállításnak a legfontosabb előnye, hogy egy alkalmazást egyetlen régióban frissíthet egy egyszerű lépéskészlet végrehajtásával. A dollár költsége a frissítés viszonylag alacsony. 

A fő kompromisszumot az, hogy ha egy katasztrofális hiba történik a frissítés során, a frissítés előtti állapotba való helyreállítás magában foglalja az alkalmazás újratelepítése egy másik régióban, és az adatbázis visszaállítása biztonsági mentésből geo-visszaállítás használatával. Ez a folyamat jelentős állásidőt eredményez.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Az adatbázis georeplikációjára támaszkodó alkalmazások frissítése a vészhelyreállításhoz

Ha az alkalmazás aktív georeplikációt vagy automatikus feladatátvételi csoportokat használ az üzletmenet folytonosságához, legalább két különböző régióra van telepítve. Van egy aktív, elsődleges adatbázis egy elsődleges régióban, és egy csak olvasható, másodlagos adatbázis egy biztonsági mentési régióban. A cikk elején említett tényezőkmellett a frissítési folyamatnak azt is garantálnia kell, hogy:

* Az alkalmazás a frissítési folyamat során mindig védve marad a katasztrofális hibáktól.
* Az alkalmazás georedundáns összetevői az aktív összetevőkkel párhuzamosan frissülnek.

Ezek a célok elérése érdekében a Web Apps-környezetek használata mellett az Azure Traffic Manager előnyeit egy aktív végpontot és egy biztonsági mentési végpontot használó feladatátvételi profil használatával is kihasználhatja. A következő ábra a frissítési folyamat előtti működési környezetet mutatja be. A `contoso-1.azurewebsites.net` weboldalak, `contoso-dr.azurewebsites.net` és képviseli a termelési környezetaz alkalmazás teljes földrajzi redundancia. Az éles környezet a következő összetevőket tartalmazza:

* A webalkalmazás `contoso-1.azurewebsites.net` éles környezete az elsődleges régióban (1)
* Az elsődleges adatbázis az elsődleges régióban (2)
* A webalkalmazás készenléti példánya a biztonsági mentési régióban (3)
* A georeplikált másodlagos adatbázis a biztonsági mentési régióban (4)
* Traffic Manager teljesítményprofil egy online `contoso-1.azurewebsites.net` végpont neve és egy offline végpont neve`contoso-dr.azurewebsites.net`

A frissítés visszaállításának lehetővé teszi, hogy hozzon létre egy átmeneti környezetet az alkalmazás teljesen szinkronizált másolatával. Mivel biztosítania kell, hogy az alkalmazás gyorsan helyreáll, ha a frissítési folyamat során katasztrofális hiba lép fel, az átmeneti környezetnek georedundánsnak is kell lennie. A frissítés hez átmeneti környezet létrehozásához a következő lépések szükségesek:

1. Telepítse a webalkalmazás átmeneti környezetét az elsődleges régióban (6).
2. Hozzon létre egy másodlagos adatbázist az elsődleges Azure-régióban (7). Konfigurálja a webalkalmazás átmeneti környezetét, hogy csatlakozzon hozzá. 
3. Hozzon létre egy másik georedundáns, másodlagos adatbázist a biztonsági mentési régióban a másodlagos adatbázis replikálásával az elsődleges régióban. (Ezt a módszert *láncolt georeplikációnak nevezzük.)* (8).
4. Telepítse a webalkalmazás-példány átmeneti környezetét a biztonsági mentési régióban (9), és konfigurálja úgy, hogy csatlakoztassa a (8) helyen létrehozott georedundáns másodlagos adatbázist.

> [!NOTE]
> Ezek az előkészítési lépések nem befolyásolják az alkalmazást az éles környezetben. Írás-olvasási módban is teljesen működőképes marad.

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Amikor az előkészítési lépések befejeződtek, az átmeneti környezet készen áll a frissítésre. A következő ábra a következő frissítési lépéseket mutatja be:

1. Állítsa az elsődleges adatbázist az éles környezetben írásvédett módba (10). Ez a mód garantálja, hogy az éles adatbázis (V1) nem változik a frissítés során, így megakadályozza az adatok közötti eltérést a V1 és V2 adatbázispéldányok között.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. A georeplikáció leállítása a másodlagos (11) bontásával. Ez a művelet létrehoz egy független, de teljesen szinkronizált másolatot a termelési adatbázisról. Ez az adatbázis frissítésre kerül. A következő példa a Transact-SQL-t használja, de a [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) is elérhető. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Futtassa a `contoso-1-staging.azurewebsites.net` `contoso-dr-staging.azurewebsites.net`frissítési parancsfájlt a on , és az elsődleges átmeneti adatbázis (12) között. Az adatbázis módosításai automatikusan replikálódnak az átmeneti másodlagosra.

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Ha a frissítés sikeresen befejeződik, most már készen áll a felhasználók közötti váltásra az alkalmazás V2-es verziójára. A következő ábra a következő lépéseket mutatja be:

1. A webalkalmazás éles és átmeneti környezetei közötti csereművelet aktiválása az elsődleges régióban (13) és a biztonsági mentési régióban (14). V2 az alkalmazás most válik egy éles környezetben, egy redundáns másolatot a biztonsági mentési régióban.
2. Ha már nincs szüksége a V1-es alkalmazásra (15 és 16), leszerelheti az átmeneti környezetet.

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Ha a frissítési folyamat sikertelen (például a frissítési parancsfájl hibája miatt), tekintse úgy, hogy az átmeneti környezet inkonzisztens állapotban van. Az alkalmazás visszaállítása a frissítés előtti állapotba, állítsa vissza az alkalmazás V1-es használatával az éles környezetben. A szükséges lépések a következő ábrán láthatók:

1. Állítsa az elsődleges adatbázis-másolatot az éles környezetben írási és írási módra (17). Ez a művelet visszaállítja a V1 teljes funkcionalitását az éles környezetben.
2. Végezze el a kiváltó ok elemzését, és javítsa ki vagy távolítsa el az átmeneti környezetet (18 és 19).

Ezen a ponton az alkalmazás teljesen működőképes, és megismételheti a frissítési lépéseket.

> [!NOTE]
> A visszaállítás nem igényel DNS-módosításokat, mert nem végzett csereműveletet.

![SQL Database georeplikációs konfiguráció a felhőbeli vészhelyreállításhoz.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

A fő előnye ennek a lehetőségnek, hogy az alkalmazás és a georedundáns másolatot párhuzamosan frissítheti anélkül, hogy veszélyeztetné az üzletmenet folytonosságát a frissítés során.

A fő kompromisszum az, hogy az egyes alkalmazásösszetevők dupla redundanciát igényel, és ezért magasabb dollárköltséget von maga után. Ez egy bonyolultabb munkafolyamatot is magában foglal.

## <a name="summary"></a>Összefoglalás

A cikkben ismertetett két frissítési módszer összetettsége és a dollár költsége eltérő, de mindkettő arra összpontosít, hogy minimalizálja, hogy a felhasználó mennyi ideig korlátozódik az írásvédett műveletekre. Ezt az időt közvetlenül a frissítési parancsfájl időtartama határozza meg. Ez nem függ az adatbázis méretétől, a választott szolgáltatási szinttől, a webhely konfigurációjától vagy más olyan tényezőktől, amelyeket nem lehet könnyen szabályozni. Minden előkészítési lépés levan választva a frissítési lépésektől, és nincs hatással az éles alkalmazásra. A frissítési parancsfájl hatékonysága kulcsfontosságú tényező, amely meghatározza a frissítés során a felhasználói élményt. Tehát a legjobb módja annak, hogy javítsa ezt a tapasztalatot, hogy összpontosítson az erőfeszítéseket, hogy a frissítési szkript a lehető leghatékonyabb.

## <a name="next-steps"></a>További lépések

* Az üzletmenet folytonosságának áttekintését és forgatókönyveit az [Üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md)című témakörben találja.
* Az Azure SQL Database aktív georeplikációjáról az [Olvasható másodlagos adatbázisok létrehozása aktív georeplikációval című](sql-database-active-geo-replication.md)témakörben olvashat.
* Az Azure SQL Database automatikus feladatátvételi csoportjairól az [Automatikus feladatátvételi csoportok használata több adatbázis transzparens és összehangolt feladatátvételének engedélyezéséhez](sql-database-auto-failover-group.md)című témakörben olvashat.
* Az Azure App Service átmeneti környezeteiről az [Átmeneti környezetek beállítása az Azure App Service-ben című témakörben](../app-service/deploy-staging-slots.md)olvashat.
* Az Azure Traffic Manager-profilokról az [Azure Traffic Manager-profil kezelése.Forgnot](../traffic-manager/traffic-manager-manage-profiles.md)as
