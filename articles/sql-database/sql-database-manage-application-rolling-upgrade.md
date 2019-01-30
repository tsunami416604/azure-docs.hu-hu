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
ms.date: 01/29/2019
ms.openlocfilehash: 1aa3960e3a974703cfecec2bd28fc41f74f7df96
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238407"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>SQL-adatbázis aktív georeplikációt használ, a felhőalapú alkalmazások működés közbeni frissítések kezelése

Ismerje meg, hogyan használható [aktív georeplikáció](sql-database-auto-failover-group.md) ahhoz, hogy a felhőalapú alkalmazások működés közbeni frissítése SQL Database-ben. Mivel a frissítés zavart okozó műveletet, akkor az üzletmenet folytonosságának megtervezése és kialakítása részének kell lennie. Ebben a cikkben azt tekintse meg a frissítési folyamat replikálásával segít a vállalatnak két eltérő módszert, és előnyeit és hátrányait feláldozását tárgyalják. Ez a cikk az alkalmazásában ezzel egy alkalmazás, amely egy webhely, az adatok rétegként egyetlen adatbázishoz kapcsolódó áll. Az a célunk, hogy a 2-es verzió anélkül, hogy a végfelhasználói élmény jelentős hatással az alkalmazás 1 verziójának frissítése.

A frissítési beállításokban kiértékelésekor fontolja meg a következő tényezőket:

* A művelet hatása rendelkezésre állásának frissítések során. Mennyi ideig a kérelem függvény korlátozható vagy csökkentett teljesítményű.
* Visszaállítás, ha a sikertelen frissítés esetén lehetősége.
* Biztonsági rés az alkalmazás egy független Katasztrofális hiba esetén a frissítés során.
* Teljes dollár költsége.  Ez magában foglalja a további redundancia jelentkezhet és a frissítési folyamat által használt ideiglenes összetevőket a növekményes költségekkel.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>A vész-helyreállítási adatbázis biztonsági mentését használó alkalmazások frissítése

Ha az alkalmazás az adatbázis automatikus biztonsági mentések támaszkodik, és a vész-helyreállítási geo-visszaállítás használ, egyetlen Azure-régióban üzemel. A végfelhasználói leskálázáskor átmeneti környezetet hoz létre az adott régióban, az összes alkalmazás-összetevőket a frissítés részt. A következő ábra szemlélteti a működési környezetben a frissítés előtt. A végpont `contoso.azurewebsites.net` egy éles webalkalmazásra a webalkalmazás jelöli. Ahhoz, hogy a visszaállítás a frissítés lehetővé teszi, az előkészítési pontra kell létrehozása egy teljesen szinkronizált másolatot az adatbázisról. Az alábbi lépéseket a frissítés átmeneti környezetet hoz létre:

1. Hozzon létre egy másodlagos adatbázis ugyanazon Azure-régióban. Figyelje meg, ha a kiindulási folyamat befejeződött (1)-e a másodlagos.
2. Hozzon létre egy új üzembe helyezési pont "Átmeneti" nevű webalkalmazása számára. Az URL-CÍMMEL rendelkező DNS-ben regisztrálják `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Megjegyzés: az előkészítő lépések nem lesz hatással az éles webalkalmazásra, és teljes hozzáférési módban működhetnek.
>  

![SQL-adatbázis-Go-replikáció konfigurálása. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Miután elvégezte a adatelőkészítési lépéseket, a tényleges frissítés készen áll az alkalmazás. A következő ábra szemlélteti a frissítési folyamat lépéseit.

1. Állítsa be az elsődleges adatbázis csak olvasható módra (3). Ebben a módban garantálja, hogy az éles webalkalmazásra a webalkalmazás (V1) marad csak olvasható megelőzve a adateltérésekkel a V1 és v2-es adatbázis-példányok között a frissítés során.  
2. Válassza le a másodlagos adatbázis, a tervezett lezárást mód (4). Az elsődleges adatbázis teljesen szinkronizált független másolatot hoz létre. Ez az adatbázis lesz frissítve.
3. Kapcsolja be az elsődleges adatbázis írható-olvasható módba, és futtassa a frissítési parancsfájl (5).

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Ha a frissítés sikeresen befejeződött, most már készen áll a végfelhasználók váltson át a frissített példányt az alkalmazás. Most már válik egy éles webalkalmazásra.  A Váltás magában foglalja az az alábbi ábrán szemléltetett módon néhány további lépést.

1. Aktiválja a csereművelet éles és átmeneti tárhelyek a webalkalmazás (6) között. Az URL-címeket, a két tárolóhelyek színűre azt. Most már `contoso.azurewebsites.net` V2 verzióját a webhely és az adatbázis (éles környezet) mutasson.  
2. Ha már nincs szüksége a V1 verziót, amely egy tesztelési másolatot vált a felcserélés után, leszerelheti az átmeneti envoronment (7).

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Ha a frissítési folyamat sikertelen, például a frissítési parancsfájl hibája miatt a fázis tárolóhely kell alkalmazni az sérült a biztonsága. A visszaállítás az alkalmazást a frissítés előtti állapotot az alkalmazás teljes körű hozzáféréssel az üzemelési vissza. A folyamat lépéseit a következő diagramon láthatók.

1. Az adatbázis-másolat állítsa írható-olvasható módban (8). Azt állítja vissza a teljes V1 funkcionálisan, az éles üzemi példány.
2. Hajtsa végre a kiváltó okok elemzése, és az átmeneti környezetben (9) leszerelése.

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket lehessen ismételni.

> [!NOTE]
> A visszaállítás nincs szükség DNS-módosítások, nem végezte még el a felcserélési műveletet.

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

A kulcs **advantage** ezt a beállítást az, hogy egy alkalmazás egy adott régióban használatával egy egyszerű lépés végrehajtásával frissítheti. A frissítés dollár költségei viszonylag kis. A fő **kompromisszum** , hogy a frissítés során végzetes hiba történik a helyreállítás a frissítés előtti állapotba magában foglalja egy másik régióban, és az adatbázis visszaállítása a biztonsági mentés az alkalmazás újbóli üzembe helyezés a GEO-visszaállítás. Ez a folyamat jelentős állásidőt eredményez.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>A database georeplikációja a vész-helyreállítási használó alkalmazások frissítése

Az alkalmazás aktív georeplikációs és feladatátvételi csoportok az üzletmenet folytonosságának használja, ha az elsődleges régióban egy aktív elsődleges adatbázis és a egy csak olvasható másodlagos adatbázis biztonsági mentése a régióban, legalább két különböző régióban üzemel. Mellett a tényezőket, korábban említettük a frissítési folyamat garantálnia kell, hogy:

* Az alkalmazás a katasztrofális hibák védett marad a frissítési folyamat során mindig
* A georedundáns összetevő az alkalmazás frissítésének párhuzamos aktív összetevői

Az ezen célok eléréséhez, a webes alkalmazás üzembe helyezési pontok használata mellett használja az Azure Traffic Manager (ATM) egy aktív és a egy biztonsági mentési végpontok feladatátvételi profilt használ.  A következő ábra szemlélteti a működési környezetben a frissítés előtt. A webhelyek `contoso-1.azurewebsites.net` és `contoso-dr.azurewebsites.net` jelölik az alkalmazás éles környezetben a teljes földrajzi redundancia céljából. Az éles környezetben a következő összetevőket tartalmazza:

1. A webalkalmazás üzemelési `contoso-1.azurewebsites.net` az elsődleges régióban (1)
2. Az elsődleges régió (2) az elsődleges adatbázis 
3. A készenléti példány a webes alkalmazás a biztonsági mentési régióban (3)
4. Georeplikált másodlagos adatbázis a biztonsági mentési régióban (4)
5. Az Azure traffic manager teljesítményprofilját online végponttal rendelkező `contoso-1.azurewebsites.net` és offline végpont `contoso-dr.azurewebsites.net`

Ahhoz, hogy a visszaállítás a frissítés lehetővé teszi, egy átmeneti környezetben kell létrehozni az alkalmazás teljesen szinkronizált másolatával. Győződjön meg arról, hogy az alkalmazás képes gyorsan helyreállíthatja az adatokat a frissítési folyamat során végzetes hiba bekövetkezése van szüksége, mert az átmeneti környezetben kell lennie, valamint a georedundáns tárolást. Hozzon létre egy átmeneti környezetben a frissítés a következő lépések szükségesek:

1. Az előkészítési pont az elsődleges régióban (6) a webalkalmazás üzembe helyezése
2. Hozzon létre egy másodlagos adatbázis elsődleges Azure-régióban (7). A webes alkalmazás a csatlakozáshoz az előkészítési pont konfigurálása. 
3. Hozzon létre egy másik georedundáns adatbázisőrzés (ezt nevezik "kapcsolt georeplikációs") az elsődleges régióban, a másodlagos adatbázis replikálásával a biztonsági mentési régióban (8).
3. A biztonsági mentési régióban (9), a webes alkalmazás példány az előkészítési pont telepítése és konfigurálása, [9]. lépésében létrehozott földrajzilag másodlagos csatlakozni.


> [!NOTE]
> Megjegyzés: az előkészítő lépések nem lesz hatással az alkalmazás az üzemelési és írható-olvasható módban teljesen működőképes marad.

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Miután végrehajtotta az előkészítő lépéseket, az átmeneti környezetben a frissítés készen áll. A következő ábra szemlélteti a frissítési lépéseket.

1. Az elsődleges adatbázis csak olvasható módra (10) üzemelési állítja be. Ebben a módban garantálja, hogy az éles adatbázisban (V1) nem változik a megelőzve a adateltérésekkel a V1 és v2-es adatbázis-példány közötti frissítés során.  
2. Válassza le a másodlagos adatbázis ugyanabban a régióban a tervezett lezárást mód (11) használatával. Az éles adatbázis független, de teljes körűen szinkronizált másolatot hoz létre. Ez az adatbázis lesz frissítve.
3. Frissítési szkriptek futtatása `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` és az elsődleges adatbázis átmeneti (12). Az adatbázis-módosítások automatikusan replikálja a másodlagos átmeneti környezetbe 

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Ha a frissítés sikeresen befejeződött, most már készen áll a végfelhasználók váltson az alkalmazás V2 verziója. A következő ábra szemlélteti a folyamat lépéseit.

1. Aktiválja a csereművelet éles és átmeneti pontok az elsődleges régióban (13), és a biztonsági mentési régióban (14) a webes alkalmazás között. Az alkalmazás most már v2 egy üzemelési redundáns másolatát, a biztonsági mentési régióban lesz.
2. Leszerelheti a az átmeneti környezet, ha már nincs szüksége a V1-alkalmazás (15 és 16).  

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Ha a frissítési folyamat sikertelen, például a frissítési parancsfájl hibája miatt az átmeneti környezetben figyelembe kell venni inkonzisztens állapotba. Visszaállítás az alkalmazást a frissítés előtti állapotra, eredeti V1 az alkalmazás az éles környezetben. A következő diagramon jelennek meg a szükséges lépéseket.

1. Állítsa be az elsődleges adatbázis-másolat az üzemelési írható-olvasható módba (17). Azt állítja vissza a teljes V1 funkcionálisan az üzemelési.
2. Hajtsa végre a kiváltó okok elemzése és a javítás, vagy távolítsa el az átmeneti környezetben (18 és 19).

Ezen a ponton az alkalmazás teljesen működőképes, és a frissítési lépéseket lehessen ismételni.

> [!NOTE]
> A visszaállítás nem igényel a DNS-módosítások, mert nem végezte el a felcserélési műveletet.

![SQL-adatbázis georeplikációs konfiguráció. Felhőalapú vészhelyreállítással.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

A kulcs **advantage** ezt a beállítást az, hogy frissítheti az alkalmazás és a párhuzamos georedundáns példányát a frissítés során az üzletmenet-folytonossági veszélyeztetése nélkül. A fő **kompromisszum** , hogy minden egyes alkalmazás-összetevő kettős redundancia igényel, és ezért tekintetében költségesebb dollár. Egy összetettebb munkafolyamat is magában foglalja.

## <a name="summary"></a>Összegzés

A két frissítési módszer a cikkben ismertetett különböznek az összetettséget és a költségek dollár, de ezek is koncentrálhat minimálisra csökkentik az idő, amikor a végfelhasználó korlátozódik, csak olvasható műveletekhez. A frissítési parancsfájl időtartama közvetlenül határozza meg, hogy időt. Nem függ a az adatbázis méretétől, a kiválasztott szolgáltatási rétegben, a webhely konfigurációja és egyéb tényezőket, amelyek egyszerűen nem szabályozhatja. Az előkészítő lépések vannak választva a frissítési lépéseket a, és nem érinti a feldolgozott alkalmazás éles üzemét. A frissítési parancsfájl hatékonyságának egy kulcsfontosságú tényező, amely meghatározza, hogy a végfelhasználói élmény frissítések során. Így a legjobb módszer, növelheti a átállásig a lehető leghatékonyabb gondoskodik a frissítési parancsfájl.  

## <a name="next-steps"></a>További lépések

* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* Az Azure SQL adatbázis aktív georeplikációt kapcsolatos további információkért lásd: [olvasható másodlagos adatbázis aktív georeplikációt használ létrehozása](sql-database-active-geo-replication.md).
* Az Azure SQL Database feladatátvételi csoportok kapcsolatos további információkért lásd: [automatikus feladatátvételi csoportok használatával engedélyezhető az átlátható és koordinált több adatbázis feladatátvételét](sql-database-auto-failover-group.md).
* Üzembe helyezési pontok és átmeneti környezet az Azure App Service kapcsolatos további információkért lásd: [állítsa be átmeneti környezeteket az Azure App Service](../app-service/deploy-staging-slots.md).  
* Az Azure traffic manager-profilok kapcsolatban lásd: [kezelése az Azure Traffic Manager-profilok](../traffic-manager/traffic-manager-manage-profiles.md).  


