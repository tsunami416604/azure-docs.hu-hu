---
title: Vészhelyreállítás
description: Megtudhatja, hogyan állíthatja helyre az adatbázist egy regionális adatközpont-kimaradásból vagy -meghibásodásból az Azure SQL Database aktív georeplikációjával és geo-visszaállítási képességeivel.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: d28edd28dcbe31bfe63c2d0a9c3e975967efef04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256379"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Azure SQL-adatbázis vagy feladatátvétel visszaállítása másodlagos

Az Azure SQL Database a következő képességeket kínálja a kimaradásból való helyreállításhoz:

- [Aktív georeplikáció](sql-database-active-geo-replication.md)
- [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)
- [Geo-visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zónaredundáns adatbázisok](sql-database-high-availability.md)

Az üzletmenet-folytonossági forgatókönyvekről és az ezeket a forgatókönyveket támogató funkciókról az [Üzletmenet folytonossága](sql-database-business-continuity.md)című témakörben olvashat.

> [!NOTE]
> Zónaredundáns prémium szintű vagy üzleti legkritikusabb adatbázisok vagy készletek használata esetén a helyreállítási folyamat automatikus, és az anyag többi része nem vonatkozik.

> [!NOTE]
> Mind az elsődleges, mind a másodlagos adatbázisoknak azonos szolgáltatási szinttel kell rendelkezniük. Azt is erősen ajánlott, hogy a másodlagos adatbázis jön létre az elsődleges azonos számítási mérettel (DT-k vagy virtuális magok). További információt a [Frissítés vagy visszaminősítés elsődleges adatbázisként](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database)című témakörben talál.

> [!NOTE]
> Használjon egy vagy több feladatátvételi csoportot több adatbázis feladatátvételének kezeléséhez.
> Ha egy meglévő georeplikációs kapcsolatot ad hozzá a feladatátvételi csoporthoz, győződjön meg arról, hogy a geo-másodlagos konfigurálva van az elsődleges szolgáltatásszinttel és számítási mérettel. További információ: [Az automatikus feladatátvételi csoportok használata több adatbázis transzparens és összehangolt feladatátvételének engedélyezéséhez című témakörben található.](sql-database-auto-failover-group.md)

## <a name="prepare-for-the-event-of-an-outage"></a>Felkészülés a kimaradás eseményére

A feladatátvételi csoportok vagy georedundáns biztonsági mentések használatával egy másik adatrégióba történő helyreállítás sikeres elvégzéséhez egy másik adatközpont-kimaradásban lévő kiszolgálót kell előkészítenie, hogy szükség esetén az új elsődleges kiszolgálóvá váljon, valamint jól meghatározott lépéseket kell készítenie, és a zökkenőmentes helyreállítás biztosítása érdekében. Ezek az előkészítési lépések a következők:

- Azonosítsa az SQL Database kiszolgálót egy másik régióban, hogy az új elsődleges kiszolgáló legyen. A geo-visszaállítás, ez általában egy kiszolgáló a [párosított régióban,](../best-practices-availability-paired-regions.md) amelyben az adatbázis található. Ez kiküszöböli a további forgalmi költségeket a geo-visszaállítási műveletek során.
- Azonosítsa és tetszés szerint definiálja azokat a kiszolgálószintű IP-tűzfalszabályokat, amelyek szükségesek ahhoz, hogy a felhasználók hozzáférjenek az új elsődleges adatbázishoz.
- Határozza meg, hogyan fogja átirányítani a felhasználókat az új elsődleges kiszolgálóra, például a kapcsolati karakterláncok módosításával vagy a DNS-bejegyzések módosításával.
- Azonosítsa és tetszés szerint hozza létre azokat a bejelentkezéseket, amelyeknek meg kell jelenniük az új elsődleges kiszolgáló fő adatbázisában, és győződjön meg arról, hogy ezek a bejelentkezések megfelelő engedélyekkel rendelkeznek a főadatbázisban, ha vannak ilyenek. További információ: [SQL Database security after disaster recovery](sql-database-geo-replication-security-config.md)
- Azonosítsa azokat a riasztási szabályokat, amelyeket frissíteni kell az új elsődleges adatbázishoz való hozzárendeléshez.
- Az aktuális elsődleges adatbázis naplózási konfigurációjának dokumentálása
- [Vész-helyreállítási gyakorlat](sql-database-disaster-recovery-drills.md)végrehajtása . A geo-visszaállítás imázsának szimulálásához törölheti vagy átnevezheti a forrásadatbázist, hogy az alkalmazás kapcsolatának hibája miatt. Ha feladatátvételi csoportokkal szeretne kimaradást szimulálni, letilthatja az adatbázishoz csatlakoztatott webalkalmazást vagy virtuális gépet, vagy feladatátvételt okozhat az alkalmazás kapcsolatának hibáihoz.

## <a name="when-to-initiate-recovery"></a>Mikor kell kezdeményezni a helyreállítást?

A helyreállítási művelet hatással van az alkalmazásra. Az SQL-kapcsolati karakterlánc vagy átirányítás dns használatával történő módosítását igényli, és tartós adatvesztést okozhat. Ezért csak akkor kell elvégezni, ha a kimaradás valószínűleg tovább tart, mint az alkalmazás helyreállítási idő célkitűzés. Amikor az alkalmazás éles környezetben van telepítve, rendszeresen figyelje az alkalmazás állapotát, és a következő adatpontokhasználatával bizonyítsa, hogy a helyreállítás indokolt:

1. Állandó kapcsolódási hiba az alkalmazásrétegtől az adatbázisig.
2. Az Azure Portalon egy riasztást jelenít meg egy incidens a régióban, amelynek nagy hatással.

> [!NOTE]
> Ha feladatátvételi csoportokat használ, és az automatikus feladatátvételt választja, a helyreállítási folyamat automatikus és az alkalmazás számára átlátható.

Attól függően, hogy az alkalmazás tolerancia állásidő és az esetleges üzleti felelősség figyelembe veheti a következő helyreállítási lehetőségeket.

A [Helyreállítható adatbázis beszerzése](https://msdn.microsoft.com/library/dn800985.aspx) *(LastAvailableBackupDate)* segítségével szerezze be a legújabb georeplikált visszaállítási pontot.

## <a name="wait-for-service-recovery"></a>Várakozás a szolgáltatás helyreállítására

Az Azure-csapatok szorgalmasan dolgoznak a szolgáltatás rendelkezésre állásának mielőbbi visszaállításán, de a ttól függően, hogy milyen alapvető oka lehet órákig vagy napokig.  Ha az alkalmazás képes elviselni jelentős állásidő egyszerűen megvárhatja a helyreállítás befejezéséhez. Ebben az esetben nincs szükség beavatkozásra az Ön részéről. Az aktuális szolgáltatásállapotát az [Azure Service Health Dashboard oldalon láthatja.](https://azure.microsoft.com/status/) A régió helyreállítása után az alkalmazás rendelkezésre állása helyreáll.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Feladatátvétel a georeplikált másodlagos kiszolgálónak a feladatátvételi csoportban

Ha az alkalmazás állásideje üzleti felelősséget eredményezhet, feladatátvételi csoportokat kell használnia. Lehetővé teszi, hogy az alkalmazás gyorsan visszaállítsa a rendelkezésre állást egy másik régióban kimaradás esetén. Az oktatóanyagról a [Geoelosztott adatbázis megvalósítása (Implement a) (Geodistributed database) (Megvalósítása) témakörben](sql-database-implement-geo-distributed-database.md)található.

Az adatbázis(ok) rendelkezésre állásának visszaállításához kezdeményeznie kell a feladatátvételt a másodlagos kiszolgálónak a támogatott módszerek egyikével.

Az alábbi útmutatók egyikével átadódik egy georeplikált másodlagos adatbázisnak:

- [Feladatátvétel georeplikált másodlagos kiszolgálóra az Azure Portal használatával](sql-database-geo-replication-portal.md)
- [Feladatátvétel a másodlagos kiszolgálóra a PowerShell használatával](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Feladatátvétel egy másodlagos kiszolgálóra transact-SQL (T-SQL) használatával](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Helyreállítás geo-visszaállítással

Ha az alkalmazás állásideje nem eredményez üzleti felelősséget, [használhatja a geo-visszaállítás](sql-database-recovery-using-backups.md) t az alkalmazásadatbázis(ok) helyreállításának módszerként. Létrehoz egy másolatot az adatbázis a legújabb georedundáns biztonsági mentés.

## <a name="configure-your-database-after-recovery"></a>Az adatbázis konfigurálása helyreállítás után

Ha geo-visszaállítást használ a kimaradás utáni helyreállításhoz, győződjön meg arról, hogy az új adatbázisokhoz való csatlakozás megfelelően van konfigurálva, hogy a normál alkalmazásfunkció folytatható legyen. Ez a feladatok ellenőrzőlistája, amely előkészíti a helyreállított adatbázis-éleskörnyezetet.

### <a name="update-connection-strings"></a>Kapcsolati karakterláncok frissítése

Mivel a helyreállított adatbázis egy másik kiszolgálón található, frissítenie kell az alkalmazás kapcsolati karakterláncát, hogy az adott kiszolgálóra mutasson.

A kapcsolati karakterláncok módosításáról a [kapcsolattárnak](sql-database-libraries.md)megfelelő fejlesztői nyelven talál további információt.

### <a name="configure-firewall-rules"></a>Tűzfalszabályok konfigurálása

Meg kell győződnie arról, hogy a kiszolgálón és az adatbázisban konfigurált tűzfalszabályok megegyeznek az elsődleges kiszolgálón és az elsődleges adatbázisban konfigurált szabályokkal. További információ: [A tűzfalbeállítások konfigurálása (Azure SQL Database)](sql-database-configure-firewall-settings.md)című témakörben talál.

### <a name="configure-logins-and-database-users"></a>Bejelentkezések és adatbázis-felhasználók konfigurálása

Meg kell győződnie arról, hogy az alkalmazás által használt összes bejelentkezés létezik a kiszolgálón, amely a helyreállított adatbázist üzemelteti. További információ: [Security Configuration for geo-replication](sql-database-geo-replication-security-config.md).

> [!NOTE]
> A vész-helyreállítási gyakorlat során konfigurálnia és tesztelnie kell a kiszolgáló tűzfalszabályait és bejelentkezéseit (és engedélyeit). Előfordulhat, hogy ezek a kiszolgálószintű objektumok és konfigurációjuk nem érhetők el a szolgáltatáskimaradás során.

### <a name="setup-telemetry-alerts"></a>Telemetriai riasztások beállítása

Győződjön meg arról, hogy a meglévő riasztási szabály beállításai frissülnek, hogy leképezzék a helyreállított adatbázisra és a különböző kiszolgálóra.

Az adatbázis-riasztási szabályokról további információt a [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) és [a Szolgáltatás állapotának nyomon követése című](../monitoring-and-diagnostics/insights-service-health.md)témakörben talál.

### <a name="enable-auditing"></a>Naplózás engedélyezése

Ha naplózásra van szükség az adatbázis eléréséhez, engedélyeznie kell a naplózást az adatbázis helyreállítása után. További információt az [Adatbázis naplózása című témakörben](sql-database-auditing.md)talál.

## <a name="next-steps"></a>További lépések

- Az Azure SQL Database automatikus biztonsági mentéseiről az SQL Database automatikus biztonsági mentései című [témakörben olvashat.](sql-database-automated-backups.md)
- Az üzletmenet-folytonossági tervezési és helyreállítási forgatókönyvekről a [Folytonossági forgatókönyvek](sql-database-business-continuity.md)
- Az automatikus biztonsági mentések helyreállítási használatáról az [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági mentések ből című témakörben olvashat.](sql-database-recovery-using-backups.md)
