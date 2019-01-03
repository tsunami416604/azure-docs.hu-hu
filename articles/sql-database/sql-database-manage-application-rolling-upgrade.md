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
ms.date: 08/23/2018
ms.openlocfilehash: f560f053b7aa7f4e90ebcc611119e1c552f6df7c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608051"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>SQL-adatbázis aktív georeplikációt használ, a felhőalapú alkalmazások működés közbeni frissítések kezelése

Ismerje meg, hogyan használható [aktív georeplikáció](sql-database-auto-failover-group.md) ahhoz, hogy a felhőalapú alkalmazások működés közbeni frissítése SQL Database-ben. Mivel a frissítés zavart okozó műveletet, akkor az üzletmenet folytonosságának megtervezése és kialakítása részének kell lennie. Ebben a cikkben azt tekintse meg a frissítési folyamat replikálásával segít a vállalatnak két eltérő módszert, és előnyeit és hátrányait feláldozását tárgyalják. Ez a cikk az alkalmazásában használjuk egy egyszerű alkalmazás, amely egy webhely, az adatok rétegként egyetlen adatbázishoz kapcsolódó áll. Az a célunk, hogy a 2-es verzió anélkül, hogy a végfelhasználói élmény jelentős hatással az alkalmazás 1 verziójának frissítése.

A frissítési beállításokban kiértékelése során fontolja meg a következő tényezőket:

* A művelet hatása rendelkezésre állásának frissítések során. Mennyi ideig a kérelem függvény korlátozható vagy csökkentett teljesítményű.
* Lehetővé teszi egy frissítési hiba esetén visszaállítása.
* Biztonsági rés az alkalmazás egy független Katasztrofális hiba esetén a frissítés során.
* Teljes dollár költsége.  Ez magában foglalja a további redundancia jelentkezhet és a frissítési folyamat által használt ideiglenes összetevőket a növekményes költségekkel.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>A vész-helyreállítási adatbázis biztonsági mentését használó alkalmazások frissítése

Ha az alkalmazás az adatbázis automatikus biztonsági mentések támaszkodik, és a vész-helyreállítási geo-visszaállítás használ, azt általában telepítve van a egyetlen Azure-régióban. Ebben az esetben a frissítési folyamat magában foglalja a biztonsági mentés üzembe helyezése az összes alkalmazás-összetevők részt vesz a frissítés létrehozása. A végfelhasználói leskálázáskor, használja az Azure Traffic Manager (ATM) feladatátvételi profillal.  A következő ábra szemlélteti a működési környezetben a frissítés előtt. A végpont `contoso-1.azurewebsites.net` jelöli az alkalmazást, amelyet frissíteni kell az éles tárhely. Ahhoz, hogy vissza tudja a frissítés, kell fázis tárhely létrehozása az alkalmazás teljesen szinkronizált másolatával. Készítse elő az alkalmazást a frissítéshez a következő lépések szükségesek:

1. A verziófrissítés szakasz tárhely létrehozása. Ehhez hozzon létre egy másodlagos adatbázist (1), és üzembe helyezése egy azonos webhely ugyanazon Azure-régióban. Figyelheti a másodlagos megtekintheti, ha a kiindulási befejeződött.
2. Hozzon létre egy feladatátvételi profilt az ATM- `contoso-1.azurewebsites.net` online végpontként és `contoso-2.azurewebsites.net` offline állapotúként.

> [!NOTE]
> Megjegyzés: az előkészítő lépések nem lesz hatással az alkalmazás az üzemelési és teljes hozzáférési módban működhetnek.
>  

![SQL-adatbázis-Go-replikáció konfigurálása. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Miután elvégezte a adatelőkészítési lépéseket a tényleges frissítés készen áll az alkalmazás. A következő ábra szemlélteti a frissítési folyamat lépéseit.

1. Az elsődleges adatbázis csak olvasható módra (3) az éles tárolóhelyre állítja be. Ez garantálja, hogy az alkalmazás (V1) az éles üzemelő példányok marad csak olvasható megelőzve a adateltérésekkel a V1 és v2-es adatbázis-példányok között a frissítés során.  
2. Válassza le a másodlagos adatbázis, a tervezett lezárást mód (4). Az elsődleges adatbázis teljesen szinkronizált független másolatot hoz létre. Ez az adatbázis lesz frissítve.
3. Kapcsolja be az elsődleges adatbázis írható-olvasható módba, és futtassa a frissítési parancsfájlt a fázis tárolóhely (5).

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Ha a frissítés sikeresen befejeződött most már készen áll a végfelhasználók váltson át a szakaszos másolás az alkalmazást. Most már válik az alkalmazás az éles webalkalmazásra.  Ebbe beletartozik az néhány további lépést az az alábbi ábrán szemléltetett módon.

1. Az online végpont váltson a ATM profil `contoso-2.azurewebsites.net`, amely a V2 verzióját a webhely (6) mutat. Mostantól a V2-alkalmazás az éles webalkalmazásra, és a végfelhasználói átirányítja a forgalmat.  
2. Ha már nincs szüksége a V1 alkalmazás-összetevők így biztonságosan eltávolíthatja őket (7).

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Ha a frissítési folyamat sikertelen, például a frissítési parancsfájl hibája miatt a fázis tárolóhely kell alkalmazni az sérült a biztonsága. Állítsa vissza az alkalmazást a frissítés előtti állapotot, az alkalmazás teljes körű hozzáféréssel az üzemelési egyszerűen vissza. A folyamat lépéseit a következő diagramon láthatók.

1. Az adatbázis-másolat állítsa írható-olvasható módban (8). A művelettel visszaállítja a teljes V1 funkcionálisan az üzemelési.
2. Hajtsa végre a kiváltó okok elemzése, és távolítsa el a feltört összetevők a a fázis [9] bővítőhelyen.

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket lehessen ismételni.

> [!NOTE]
> A visszaállítás nem igényel módosításokat az ATM-profilban, már mutat `contoso-1.azurewebsites.net` aktív végpontjaként.

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

A kulcs **advantage** ezt a beállítást az, hogy egy alkalmazás egy adott régióban használatával egy egyszerű lépés végrehajtásával frissítheti. A frissítés dollár költségei viszonylag kis. A fő **kompromisszum** , hogy a frissítés során végzetes hiba történik a helyreállítás a frissítés előtti állapotba magában foglalja egy másik régióban, és az adatbázis visszaállítása a biztonsági mentés az alkalmazás újbóli üzembe helyezés a GEO-visszaállítás. Ez a folyamat jelentős állásidőt eredményez.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>A database georeplikációja a vész-helyreállítási használó alkalmazások frissítése

Ha az alkalmazás kihasználja az üzletmenet folytonosságának georeplikáció, az elsődleges régióban egy aktív központi telepítés és a egy biztonsági mentési régióban készenléti telepítési legalább két különböző régióban van telepítve. Mellett a tényezőket, korábban említettük a frissítési folyamat garantálnia kell, hogy:

* Az alkalmazás a katasztrofális hibák védett marad a frissítési folyamat során mindig
* A georedundáns összetevő az alkalmazás frissítésének párhuzamos aktív összetevői

Ezen célok eléréséhez használja a Feladatátvevőfürt-profil használata egy aktív és három biztonsági mentés végpontok Azure Traffic Manager (ATM).  A következő ábra szemlélteti a működési környezetben a frissítés előtt. A webhelyek `contoso-1.azurewebsites.net` és `contoso-dr.azurewebsites.net` jelölik az alkalmazás éles tárhely a teljes földrajzi redundancia céljából. Ahhoz, hogy vissza tudja a frissítés, kell fázis tárhely létrehozása az alkalmazás teljesen szinkronizált másolatával. Győződjön meg arról, hogy az alkalmazás képes gyorsan helyreállíthatja az adatokat a frissítési folyamat során végzetes hiba bekövetkezése van szüksége, mivel a fázis tárolóhely kell lennie, valamint a georedundáns. Készítse elő az alkalmazást a frissítéshez a következő lépések szükségesek:

1. A verziófrissítés szakasz tárhely létrehozása. A nincs, hozzon létre egy másodlagos adatbázist (1) és a egy azonos másolatot készít a webhely ugyanazon Azure-régióban. Figyelheti a másodlagos megtekintheti, ha a kiindulási befejeződött.
2. Hozzon létre egy georedundáns adatbázisőrzés fázis nyílásba georeplikálásával a másodlagos adatbázis (ezt nevezik "kapcsolt georeplikációs") a biztonsági mentési régióba. A biztonsági mentés másodlagos, ha a kiindulási folyamat befejeződött (3)-e figyelni.
3. A webhely készenléti másolatának létrehozása a biztonsági mentési régióban, és a georedundáns másodlagos (4) hivatkozás.  
4. Adja hozzá a további végpontokat `contoso-2.azurewebsites.net` és `contoso-3.azurewebsites.net` a feladatátvételi profilhoz ATM offline végpontként (5).

> [!NOTE]
> Megjegyzés: az előkészítő lépések nem lesz hatással az alkalmazás az üzemelési és teljes hozzáférési módban működhetnek.

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Után az előkészítő lépéseket, a frissítés készen áll a fázis tárolóhely. A következő ábra szemlélteti a frissítési lépéseket.

1. Az elsődleges adatbázis csak olvasható módra (6) az éles tárolóhelyre állítja be. Ez garantálja, hogy az alkalmazás (V1) az éles üzemelő példányok marad csak olvasható megelőzve a adateltérésekkel a V1 és v2-es adatbázis-példányok között a frissítés során.  
2. Válassza le a másodlagos adatbázis ugyanabban a régióban a tervezett lezárást mód (7). Az elsődleges adatbázis a megszüntetése után automatikusan válnak elsődleges teljesen szinkronizált független másolatát hoz létre. Ez az adatbázis lesz frissítve.
3. Kapcsolja be az elsődleges adatbázis írható-olvasható módba fázis nyílásba, majd futtassa a frissítési parancsfájl (8).

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Ha a frissítés sikeresen befejeződött, most már készen áll a végfelhasználók váltson át az alkalmazás V2 verziója. A következő ábra szemlélteti a folyamat lépéseit.

1. Az aktív végpontja. Váltson a ATM profil `contoso-2.azurewebsites.net`, így a V2 verzióját a webhely (9) mutat. Mostantól a V2-alkalmazás az éles tárhely és a végfelhasználói átirányítja a forgalmat.
2. Ha már nincs szüksége a V1 alkalmazás így biztonságosan eltávolíthatja azt (10-es és 11).  

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Ha a frissítési folyamat sikertelen, például a frissítési parancsfájl hibája miatt a fázis tárolóhely kell alkalmazni az sérült a biztonsága. Szeretné visszaállítani az alkalmazást a frissítés előtti állapotot, egyszerűen csak visszatér az üzemelési teljes körű hozzáféréssel az alkalmazás használatával. A folyamat lépéseit a következő diagramon láthatók.

1. Állítsa be az elsődleges adatbázis-másolat az üzemelési írható-olvasható módba (12). A művelettel visszaállítja a teljes V1 funkcionálisan az üzemelési.
2. Hajtsa végre a kiváltó okok elemzése, és távolítsa el a feltört összetevők a fázis tárolóhely (13 és 14).

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket lehessen ismételni.

> [!NOTE]
> A visszaállítás nem igényel módosításokat az ATM-profilban, már mutat `contoso-1.azurewebsites.net` aktív végpontjaként.

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

A kulcs **advantage** ezt a beállítást az, hogy frissítheti az alkalmazás és a párhuzamos georedundáns példányát a frissítés során az üzletmenet-folytonossági veszélyeztetése nélkül. A fő **kompromisszum** , hogy minden egyes alkalmazás-összetevő kettős redundancia igényel, és ezért tekintetében költségesebb dollár. Egy összetettebb munkafolyamat is magában foglalja.

## <a name="summary"></a>Összegzés

A két frissítési módszer a cikkben ismertetett különböznek az összetettséget és a költségek dollár, de ezek is koncentrálhat minimálisra csökkentik az idő, amikor a végfelhasználó korlátozódik, csak olvasható műveletekhez. A frissítési parancsfájl időtartama közvetlenül határozza meg, hogy időt. Nem függ a az adatbázis méretétől, a kiválasztott szolgáltatási rétegben, a webhely konfigurációja és egyéb tényezőket, amelyek egyszerűen nem szabályozhatja. Ez azért, mert az előkészítő lépések vannak választva a a frissítési lépéseket, és a termelési alkalmazások befolyásolása nélkül teheti meg. A frissítési parancsfájl hatékonyságát a kulcsfontosságú tényező, amely meghatározza, hogy a végfelhasználói élmény frissítés alatt. Így a legjobb módszer, növelheti a átállásig a lehető leghatékonyabb gondoskodik a frissítési parancsfájl.  

## <a name="next-steps"></a>További lépések

* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
* Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása az adatokat automatikus biztonsági mentésekből](sql-database-recovery-using-backups.md).
