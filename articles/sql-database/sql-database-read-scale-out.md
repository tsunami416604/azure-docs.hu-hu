---
title: Az Azure SQL Database - lekérdezések a replikák olvasása |} A Microsoft Docs
description: Az Azure SQL Database teszi lehetővé a terheléselosztás csak olvasható-alapú számítási feladatokat csak olvasható replika - nevű olvasható horizontális Felskálázási kapacitása.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66146110"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Csak olvasható replikák terheléselosztás csak olvasható lekérdezési számítási feladatok használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

Részeként a [magas rendelkezésre állású architektúra](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), a prémium szintű, fontos üzleti vagy nagy kapacitású szolgáltatási rétegben található minden egyes adatbázis automatikusan ki van építve a egy elsődleges replika, és több másodlagos replikát. A másodlagos replikák számítási mérete megegyezik az elsődleges replika vannak kiosztva. A **olvasási kibővített** funkció lehetővé teszi terheléselosztás SQL-adatbázis csak olvasható-alapú számítási feladatokat a kapacitását, csak olvasási replikára az írható-olvasható replika osztozik. Ezzel a módszerrel a csak olvasható munkaterhelés elkülönül a fő olvasási és írási számítási feladatok, és nem lesz hatással a teljesítményét. A funkció célja az alkalmazások, amelyek logikailag elkülönített csak olvasható feladatokhoz, például analytics tartalmazzák. Teljesítménybeli előnyök további kapacitás használatának nélkül juthat, külön költségek.

A következő ábra szemlélteti a kritikus fontosságú üzleti adatbázis használatával.

![Csak olvasható replikák](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Az olvasási horizontális Felskálázás funkció alapértelmezés szerint az új prémium szintű, fontos üzleti és nagy kapacitású adatbázisok engedélyezve van. Ha az SQL-kapcsolati sztring beállítása a `ApplicationIntent=ReadOnly`, az alkalmazás által az átjárót, hogy az adatbázis csak olvasható replikája irányítja. Információ a használatát a `ApplicationIntent` tulajdonságot használja, lásd: [adja meg az alkalmazások szándékáról](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Ha szeretné, hogy az alkalmazás csatlakozik-e az elsődleges másodpéldány, függetlenül attól, hogy a `ApplicationIntent` az SQL-kapcsolati sztring beállítása, kell explicit módon letiltja olvasási horizontális felskálázás az adatbázis létrehozásakor, vagy ha a konfiguráció módosítását. Például ha frissítse az adatbázist a Standard vagy általános célú csomag prémium szintű, fontos üzleti vagy nagy kapacitású szintre, és győződjön meg arról, hogy az összes saját kapcsolat továbbra is nyissa meg az elsődleges replika, tiltsa le a horizontális felskálázás olvasása. Hogyan lehet letiltani, részletek: [engedélyezheti és tilthatja le a kibővített olvasási](#enable-and-disable-read-scale-out).

> [!NOTE]
> Lekérdezés Data Store, a bővített események, az SQL Profiler és a naplózási szolgáltatások nem támogatottak a csak olvasható replika. 

## <a name="data-consistency"></a>Adatkonzisztencia

A replikák előnyeit egyike, hogy a replika mindig a tranzakciós szempontból konzisztens állapotban van, de különböző időpontokban időben lehet néhány kisebb késés között különböző replikába. Horizontális Felskálázás olvasása támogatja a munkamenet-szintű konzisztencia. Ez azt jelenti, hogy a csak olvasható munkamenet újracsatlakozik a replika elérhetetlensége által okozott kapcsolódási hiba után, ha ez lehetséges, hogy átirányítja őket egy replikát, amely nem naprakész, és az írható-olvasható replika 100 %-os. Hasonlóképpen ha egy alkalmazás írja az adatokat, olvasási és írási munkamenet használatával, és azonnal olvassa azokat a csak olvasható munkamenet használatával, lehetséges, hogy a legújabb frissítéseket nem láthatók azonnal a replikán. A késés egy aszinkron tranzakciós napló visszaállítási művelet okozza.

> [!NOTE]
> Replikációs a régión belül a késleltetések alacsony, és ebben a helyzetben ritkán fordul elő.

## <a name="connect-to-a-read-only-replica"></a>Csatlakozás egy csak olvasható replika

Olvasási horizontális Felskálázás egy adatbázis engedélyezésekor a `ApplicationIntent` az ügyfél által megadott kapcsolati karakterlánc található a beállítás szabja meg,-e a kapcsolat az írható replikával, vagy egy csak olvasási replikára irányítja-e. Pontosabban Ha a `ApplicationIntent` érték `ReadWrite` (az alapértelmezett érték), a rendszer átirányítja a kapcsolatot az adatbázis írható-olvasható replika. Ez megegyezik a létező esetén. Ha a `ApplicationIntent` érték `ReadOnly`, a kapcsolat csak olvasható replika lesz irányítva.

A következő kapcsolati karakterlánc például az ügyfél csatlakozik egy csak olvasható replika (cserélje le a csúcsos zárójeleket található elemek a környezetének megfelelő értékekkel és a csúcsos zárójeleket elvetése):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Az ügyfél a következő kapcsolati karakterláncok bármelyikét csatlakozik egy írható-olvasható replika (cserélje le a csúcsos zárójeleket található elemek a környezetének megfelelő értékekkel és a csúcsos zárójeleket elvetése):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Ellenőrizze, hogy a kapcsolat egy csak olvasható replika

Ellenőrizheti, hogy csatlakozik egy csak olvasható replika a következő lekérdezés futtatásával. Ha csatlakozik egy csak olvasható replika READ_ONLY adja vissza.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Egy adott időpontban csak az egyik az AlwaysON-replikák érhető el a csak olvasható munkameneteket.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorozási és hibaelhárítási csak olvasható replika

Ha csatlakozik egy csak olvasható replika, érheti el a teljesítmény metrikák használatával a `sys.dm_db_resource_stats` DMV Lekérdezéséhez. Lekérdezési terv statisztikák elérésére, használja a `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` és `sys.dm_exec_sql_text` DMV-k használatával.

> [!NOTE]
> A DMV `sys.resource_stats` CPU használatát és a tárolási adatokat az elsődleges replika adja vissza a logikai master adatbázisban.


## <a name="enable-and-disable-read-scale-out"></a>Engedélyezheti vagy letilthatja a horizontális Felskálázás olvasása

Horizontális Felskálázás olvasása a prémium szintű, fontos üzleti és nagy kapacitású szolgáltatásszintekkel alapértelmezés szerint engedélyezve van. Horizontális Felskálázás olvasása alapszintű, Standard vagy általános célú szolgáltatási szinten nem lehet engedélyezni. Horizontális Felskálázás olvasása 0 replikákkal rendelkező konfigurált nagy kapacitású adatbázisok automatikusan le van tiltva. 

Tiltsa le, majd engedélyezze újra az olvasási horizontális Felskálázás az önálló adatbázisok és rugalmas készlet adatbázisok a következő módszerekkel prémium és az üzletileg kritikus szolgáltatási rétegben található.

> [!NOTE]
> Az olvasási kibővített letiltásához biztosítja a visszamenőleges kompatibilitás érdekében.

### <a name="azure-portal"></a>Azure Portal

Kezelheti a kibővített Sacle olvasási beállítás a **konfigurálása** adatbázis panel. 

### <a name="powershell"></a>PowerShell

A 2016. December kezelése olvasási horizontális Felskálázás az Azure PowerShell szükséges az Azure PowerShell kiadás vagy újabb. A legújabb PowerShell-verzió, lásd: [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-az-ps).

Letilthatja vagy engedélyezze újra az olvasási horizontális Felskálázás az Azure PowerShell meghívásával a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsmag és a kívánt értéket – a passzok `Enabled` vagy `Disabled` – az a `-ReadScale` paraméter. 

Egy meglévő adatbázist (a környezet megfelelő értékeit cserélje le a csúcsos zárójeleket elemeinek és elvetését a csúcsos zárójeleket) az olvasási kibővített letiltásához:

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Egy új adatbázist (a környezet megfelelő értékeit cserélje le a csúcsos zárójeleket elemeinek és elvetését a csúcsos zárójeleket) az olvasási kibővített letiltásához:

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Olvasási horizontális felskálázás a meglévő adatbázis (cserélje le a csúcsos zárójeleket található elemek a környezetének megfelelő értékekkel, és a csúcsos zárójeleket elvetését) újbóli engedélyezése:

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Hozhat létre egy adatbázist a olvasási horizontális le van tiltva, vagy módosítani a beállítást a meglévő adatbázist, használja a következő metódust a `readScale` tulajdonság `Enabled` vagy `Disabled` hasonlóan a a mintakérelem alább.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

További információkért lásd: [- adatbázisok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>A csak olvasható replika használatával a TempDB

A TempDB adatbázist a csak olvasható replikák nem replikálódnak. Minden replika jön létre, ha a replika jön létre a TempDB adatbázis a saját verziója van. Biztosítja, hogy a TempDB frissíthető és módosítható a lekérdezés végrehajtása során. Ha a TempDB-objektumok a csak olvasható számítási feladat függ, létre kell hoznia ezeket az objektumokat a lekérdezési szkriptet részeként. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Horizontális Felskálázás olvasása használata a georeplikált adatbázis

Ha olvasási kibővített terheléselosztás csak olvasható számítási, adatbázis, amely georeplikált (például egy feladatátvételi csoport tagjai) használ, ügyeljen arra, hogy olvasási kibővített engedélyezve van az elsődleges és a georeplikált másodlagos adatbázisok is. Ez a konfiguráció biztosítja, hogy az ugyanazon terheléselosztó élmény továbbra is fennáll, amikor az alkalmazás csatlakozik az új elsődleges feladatátvétel után. Ha az olvasási szintű engedélyezve van, a georeplikált másodlagos adatbázishoz kapcsolódik a munkamenetek `ApplicationIntent=ReadOnly` továbbítja a replikára ugyanúgy azt átirányíthatja a kapcsolatokat az elsődleges adatbázison.  A munkamenetek nélkül `ApplicationIntent=ReadOnly` a rendszer átirányítja az elsődleges replika, a georeplikált másodlagos, ami egyben csak olvasható. Mivel a georeplikált másodlagos adatbázis rendelkezik, mint az elsődleges adatbázis egy másik végpontot, hagyományosan eléréséhez a másodlagos azt nem be kell állítaniuk `ApplicationIntent=ReadOnly`. Előző verziókkal való kompatibilitás érdekében `sys.geo_replication_links` DMV látható `secondary_allow_connections=2` (bármely ügyfél kapcsolat engedélyezett).

> [!NOTE]
> Ciklikus időszeletelés vagy bármely más elosztott terhelésű közötti útválasztást helyi replikáit a másodlagos adatbázis nem támogatott.

## <a name="next-steps"></a>További lépések

- Az SQL Database rendkívüli ajánlat kapcsolatos információkért lásd: [nagy kapacitású szolgáltatásszint](./sql-database-service-tier-hyperscale.md).
