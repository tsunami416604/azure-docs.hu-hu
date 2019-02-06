---
title: Az SQL Database vészhelyreállítása |} A Microsoft Docs
description: Ismerje meg, hogyan regionális adatközpont-meghibásodás után vagy hiba az Azure SQL-adatbázis aktív georeplikációt, és a geo-visszaállítás képességek az adatbázis helyreállítása.
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
ms.date: 01/25/2019
ms.openlocfilehash: 5fcd7f05aeb294441b16bab0aeb18ef5d0bf5c28
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749035"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Visszaállítása egy Azure SQL Database vagy feladatátvétel a másodlagos kiszolgálóra

Az Azure SQL Database-beli szolgáltatáskimaradás utáni helyreállításhoz a következő lehetőségeket biztosítja:

- [Aktív georeplikáció](sql-database-active-geo-replication.md)
- [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)
- [Geo-restore](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zónaredundáns adatbázisok](sql-database-high-availability.md)

Folyamatos üzletmenet – forgatókönyvek és a szolgáltatások ezeket a forgatókönyveket támogató kapcsolatos további információkért lásd: [üzletmenet-folytonossági](sql-database-business-continuity.md).

> [!NOTE]
> Zónaredundáns prémium és az üzletileg kritikus adatbázisokat vagy készleteket használ, ha a helyreállítási folyamat automatikus, és a többi anyag nem vonatkozik.

## <a name="prepare-for-the-event-of-an-outage"></a>Az esemény szolgáltatáskimaradások előkészítése

A sikeres a helyreállítás egy másik adatrégióba feladatátvételi csoportok vagy georedundáns biztonsági mentés használatával, elő kell készíteni egy kiszolgáló egy másik adatközpontban szolgáltatáskimaradás, az új elsődleges kiszolgáló lesz szükség kell merülnek fel, valamint rendelkezik, jól definiált lépéseit és tesztelt a zökkenőmentes helyreállítás biztosítása érdekében. Előkészítő lépések a következők:

- Azonosítsa az SQL Database-kiszolgálóhoz egy másik régióban az új elsődleges kiszolgáló lesz. Geo-visszaállítás esetén ez általában a kiszolgáló a [párosított régióra](../best-practices-availability-paired-regions.md) a régió, amelyben az adatbázis nem található. Ezzel elkerülhető a további adatforgalom költség a geo-visszaállítás műveletek során.
- Azonosítsa, és igény szerint határozza meg, a kiszolgálói szintű IP-tűzfalszabályainak a felhasználók számára az új elsődleges adatbázis eléréséhez szükséges.
- Határozza meg, hogyan kívánja irányítsa át a felhasználókat az új elsődleges kiszolgáló, például kapcsolati karakterláncok módosításával, vagy meg szeretné változtatni a DNS-bejegyzéseket.
- Azonosítsa, és újat is létrehozhat, a bejelentkezések, amelyek kell jelen lennie a master adatbázisban az új elsődleges kiszolgáló, és győződjön meg arról, ezek a bejelentkezéseket a master adatbázisban, rendelkezik megfelelő engedélyekkel, ha van ilyen. További információkért lásd: [SQL Database biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md)
- Azonosítsa a riasztási szabályok, amelyek az új elsődleges adatbázis leképezése frissíteni kell.
- A dokumentum a az aktuális elsődleges adatbázis naplózási konfiguráció
- Hajtsa végre egy [vészhelyreállítási próba végrehajtása](sql-database-disaster-recovery-drills.md). A geo-visszaállítás leállások szimulálását, törölheti, vagy nevezze át a forrásadatbázis az alkalmazás hibája miatt. Feladatátvételi csoportok használata a leállások szimulálását, letilthatja, hogy a webalkalmazás vagy virtuális gép csatlakozik az adatbázishoz vagy az adatbázis feladatátvételi alkalmazás csatlakozási hibákhoz vezethet.

## <a name="when-to-initiate-recovery"></a>Mikor érdemes elindítani a helyreállítást

A helyreállítási műveletet az alkalmazás hatással van. Ez az SQL-kapcsolati karakterlánc vagy a DNS-sel átirányítás módosítani kell, és végleges adatvesztést eredményezhet. Ezért el kell végezni csak akkor, ha a szolgáltatáskimaradás elhárítása után valószínű, hogy az alkalmazás helyreállítási idő célkitűzése hosszabb ideig. Ha az alkalmazás éles üzembe helyezés az alkalmazás állapotáról rendszeres figyelése, és használja a következő adatpontokhoz fogadhatja, hogy indokolt-e a helyreállítás:

1. Állandó csatlakozási hiba az alkalmazás szintről az adatbázishoz.
2. Az Azure Portalon a régióban, széles körű hatással van egy incidens kapcsolatos riasztás jelenik meg.

> [!NOTE]
> Ha feladatátvételi csoportokat használ, és automatikus feladatátvételi választotta, a helyreállítási folyamat az automatizált és átlátható az alkalmazás.

Az alkalmazás tolerancia állásidő és a lehetséges üzleti felelősséget függően érdemes lehet a következő helyreállítási beállítások.

Használja a [helyreállítható adatbázishibák első](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*), a legújabb georeplikált visszaállítási pont beolvasása.

## <a name="wait-for-service-recovery"></a>Várjon, amíg a kiszolgáló helyreállítás

Azure csapatok munka térségekre való kiterjesztésén állítsa vissza a szolgáltatás rendelkezésre állása, gyorsan lehető, de attól függően, a legfelső szintű okozhatnak, órákig vagy napokig eltarthat.  Ha az alkalmazás működését jelentős állásidőt egyszerűen várja meg a helyreállítás végrehajtásához. Ebben az esetben az Ön részéről semmit nem kell. A szolgáltatás aktuális állapotát megtekintheti a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/). A régió helyreállítása után az alkalmazás rendelkezésre állási szintet.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Feladatátvétel georeplikált másodlagos kiszolgáló feladatátvételi csoport

Ha az alkalmazás állásidőt eredményezhet üzleti felelősség, kellene használni feladatátvételi csoportok. Lehetővé teszi az alkalmazások rendelkezésre állását szolgáltatáskimaradás esetén más régióban gyors visszaállításához. Foglalkozó oktatóanyagért lásd: [földrajzilag elosztott adatbázis implementálása](sql-database-implement-geo-distributed-database.md).

Kezdeményezze a feladatátvételt a másodlagos kiszolgálóra, a támogatott módszerek egyikének használatával kell az adatbázis(ok) rendelkezésre állásának visszaállítása.

A következő útmutatók használatával feladatátvételt egy georeplikált másodlagos adatbázis:

- [Feladatátvétel georeplikált másodlagos kiszolgáló használatával az Azure portal használatával](sql-database-geo-replication-portal.md)
- [Átadja a feladatokat a másodlagos kiszolgáló PowerShell-lel](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)

## <a name="recover-using-geo-restore"></a>Helyreállítása geo-visszaállítás használatával

Ha az alkalmazás állásidőt eredményez üzleti felelősség [geo-visszaállítás](sql-database-recovery-using-backups.md) módszerként az alkalmazás-adatbázis helyreállításához. A legújabb georedundáns biztonsági másolatból létrehoz egy másolatot az adatbázisról.

## <a name="configure-your-database-after-recovery"></a>A helyreállítás után az adatbázis konfigurálása

A kimaradás utáni helyreállítása geo-visszaállítás használ, ha győződjön meg arról, hogy a kapcsolatot az új adatbázisok megfelelően van konfigurálva, hogy a normál alkalmazási függvény folytathatók. Ez a ellenőrzőlistája, feladatok, a helyreállított adatbázis éles készen álljon.

### <a name="update-connection-strings"></a>Kapcsolati sztringek frissítése

A helyreállított adatbázis egy másik kiszolgálón található, mert azt frissítenie kell az alkalmazás kapcsolati karakterláncában, erre a kiszolgálóra mutasson.

Kapcsolati karakterláncok módosításával kapcsolatos további információkért tekintse meg a megfelelő fejlesztési nyelvét a [kapcsolat könyvtár](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Tűzfalszabályok konfigurálása

Győződjön meg arról, hogy a tűzfalszabályok konfigurálása a kiszolgálón és az adatbázis megegyeznek az elsődleges kiszolgálón és az elsődleges adatbázis konfigurált kell. További információkért lásd: [hogyan: Konfigurálhatja a tűzfal beállításait (az Azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Bejelentkezések és felhasználók adatbázis konfigurálása

Győződjön meg arról, hogy az alkalmazása által használt összes bejelentkezések a kiszolgálón, amely a helyreállított adatbázis létezik-e szüksége. További információkért lásd: [biztonsági konfiguráció georeplikációhoz](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Állítsa be, és a kiszolgáló tűzfalszabályait és a bejelentkezések (és a rájuk vonatkozó engedélyek) tesztelje a vészhelyreállítási próba végrehajtása során. Ezen kiszolgálói szintű objektumok és azok konfigurációja nem lehet elérhető a szolgáltatáskimaradás közben.

### <a name="setup-telemetry-alerts"></a>Telemetria beállítása

Győződjön meg arról, hogy a meglévő riasztásiszabály-beállításokat a helyreállított adatbázis és a másik kiszolgáló leképezése frissülnek kell.

Adatbázis-riasztási szabályok kapcsolatos további információkért lásd: [riasztás értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) és [nyomon követése a Service Health](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>A naplózás engedélyezéséhez

Naplózás az adatbázis eléréséhez szükséges, ha szüksége az adatbázis helyreállítása után a naplózás engedélyezése. További információkért lásd: [Database auditing](sql-database-auditing.md).

## <a name="next-steps"></a>További lépések

- További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági másolatai](sql-database-automated-backups.md)
- Folyamatos üzletmenet – tervezési és helyreállítási forgatókönyvek kapcsolatos további információkért lásd: [folyamatos üzletmenet – forgatókönyvek](sql-database-business-continuity.md)
- Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása a szolgáltatás által létrehozott biztonsági másolatokból](sql-database-recovery-using-backups.md)
