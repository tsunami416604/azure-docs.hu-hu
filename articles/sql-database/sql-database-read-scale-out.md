---
title: Az Azure SQL Database - lekérdezések a replikák olvasása |} A Microsoft Docs
description: Az Azure SQL Database lehetővé teszi betöltése terheléselosztása csak olvasható-alapú számítási feladatokat a csak olvasható replika - olvasási kibővített nevű kapacitását.
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
ms.date: 03/12/2019
ms.openlocfilehash: 8f34b3ed91e4b470fdfa7c2ffad401e7890abe1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886456"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Csak olvasható replikákat használ a betöltése terheléselosztása csak olvasható lekérdezési számítási feladatok (előzetes verzió)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

**Horizontális Felskálázás olvasása** lehetővé teszi, hogy terheléselosztása az Azure SQL Database csak olvasható-alapú számítási feladatokat egy csak olvasható replika kapacitásának betölteni.

Prémium szinten lévő minden egyes adatbázishoz ([DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md)) vagy az üzletileg kritikus ([Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)) automatikusan hozzáférést kapnak az AlwaysON replikák több a rendelkezésre állási SLA-t támogatja.

![Csak olvasható replikák](media/sql-database-managed-instance/business-critical-service-tier.png)

Ezek a replikák számítási mérete megegyezik a normál adatbázis-kapcsolatok által használt írható-olvasható replika vannak kiosztva. A **olvasási kibővített** funkció lehetővé teszi az egyenleg SQL-adatbázis csak olvasható-alapú számítási feladatokat a kapacitását, csak olvasási replikára osztozik az írható-olvasható replika betölteni. Ezzel a módszerrel a csak olvasható munkaterhelés elkülönül a fő olvasási és írási számítási feladatok, és nem lesz hatással a teljesítményét. A funkció célja az alkalmazások, amelyek tartalmazzák a logikailag elválasztott csak olvasható feladatokhoz, például elemzési, és ezért juthat által nyújtott további kapacitás használatának nélkül többletköltség.

Az olvasási horizontális Felskálázás funkció használatához, hogy adott adatbázissal, explicit módon engedélyeznie kell azt az adatbázis létrehozásakor vagy később a PowerShell használatával meghívásával konfiguráció módosítása a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) vagy a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmagok vagy az Azure Resource Manager REST API használatával a [- adatbázisok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) metódust.

Olvasási horizontális Felskálázás egy adatbázis engedélyezését követően, hogy az adatbázis csatlakozó alkalmazások lesznek irányítva, vagy az írható-olvasható replika, vagy egy csak olvasható replika adatbázis szerint a `ApplicationIntent` tulajdonság, az alkalmazás konfigurálása kapcsolati karakterlánc. Információk a `ApplicationIntent` tulajdonságot használja, lásd: [adja meg az alkalmazások szándékáról](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Ha olvasási kibővített le van tiltva, vagy a ReadScale tulajdonsága egy nem támogatott szolgáltatási rétegben, minden kapcsolat a rendszer átirányítja az írható-olvasható replika, a független a `ApplicationIntent` tulajdonság.

> [!NOTE]
> Lekérdezés Data Store és a bővített események nem támogatottak a csak olvasható replika.

## <a name="data-consistency"></a>Adatkonzisztencia

A replikák előnyeit egyike, hogy a replika mindig a tranzakciós szempontból konzisztens állapotban van, de különböző időpontokban időben lehet néhány kisebb késés között különböző replikába. Horizontális Felskálázás olvasása támogatja a munkamenet-szintű konzisztencia. Ez azt jelenti, hogy a csak olvasható munkamenet újracsatlakozik a replika elérhetetlensége által okozott kapcsolódási hiba után, ha átirányítható egy replikára, amely nem naprakész, és az írható-olvasható replika 100 %-os. Hasonlóképpen ha egy alkalmazás írja az adatokat, olvasási és írási munkamenet használatával, és azonnal olvassa azokat a csak olvasható munkamenet használatával, lehetséges, hogy a legújabb frissítéseket nem láthatók azonnal. Ez azért, mert a tranzakciós napló Ismétlés a replikákra aszinkron.

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

## <a name="enable-and-disable-read-scale-out"></a>Engedélyezheti vagy letilthatja a horizontális Felskálázás olvasása

Alapértelmezés szerint engedélyezve van a olvasási kibővített [felügyelt példány](sql-database-managed-instance.md) üzletileg kritikus. Ez explicit módon engedélyezni kell a [adatbázis SQL Database-kiszolgálót helyezett](sql-database-servers.md) prémium és üzletileg kritikus szintet. A módszerek engedélyezése és letiltása olvasási horizontális Felskálázás az alábbiakban ismertetjük.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Engedélyezheti vagy letilthatja a horizontális Felskálázás olvasása

A 2016. December kezelése olvasási horizontális Felskálázás az Azure PowerShell szükséges az Azure PowerShell kiadás vagy újabb. A legújabb PowerShell-verzió, lásd: [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-az-ps).

Engedélyezi vagy letiltja az olvasási horizontális felskálázás az Azure PowerShell meghívásával a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsmag és a kívánt értéket – a passzok `Enabled` vagy `Disabled` – az a `-ReadScale` paraméter. Másik lehetőségként használhatja a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmaggal hozzon létre egy új adatbázis olvassa el a horizontális felskálázás engedélyezve van.

Például ahhoz, hogy olvassa el az horizontális felskálázás a meglévő adatbázis-(cserélje le a csúcsos zárójeleket található elemek a környezetének megfelelő értékekkel, és a csúcsos zárójeleket elvetését):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Egy meglévő adatbázist (a környezet megfelelő értékeit cserélje le a csúcsos zárójeleket elemeinek és a csúcsos zárójeleket elvetését) olvasási kibővített letiltásához:

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Hozhat létre egy új adatbázist a olvasási kibővített engedélyezve (cserélje le a csúcsos zárójeleket található elemek a környezetének megfelelő értékekkel és a csúcsos zárójeleket elvetése):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API: Engedélyezheti vagy letilthatja a horizontális Felskálázás olvasása

Új adatbázis létrehozása az olvasási kibővített engedélyezve van, vagy a engedélyezi vagy letiltja a meglévő adatbázis olvasási kibővített, létrehozása vagy frissítése az adatbázis megfelelő entitásának a `readScale` tulajdonság `Enabled` vagy `Disabled` hasonlóan a az alábbi minta a kérést.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

További információkért lásd: [- adatbázisok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Horizontális Felskálázás olvasása használata a georeplikált adatbázis

Ha használ betölteni az egyenleg csak olvasható számítási feladatok, a georeplikált (pl. tagjaként egy feladatátvételi csoportot) egy adatbázis, győződjön meg arról, hogy olvasási kibővített engedélyezve van az elsődleges, mind a georeplikált másodlagos adatbázisok a felskálázás olvasása. Amikor az alkalmazás csatlakozik az új elsődleges a feladatátvételt követően ugyanezt a terheléselosztás hatást tud biztosítani. Ha az olvasási szintű engedélyezve van, a georeplikált másodlagos adatbázishoz kapcsolódik a munkamenetek `ApplicationIntent=ReadOnly` továbbítja a replikára ugyanúgy azt átirányíthatja a kapcsolatokat az elsődleges adatbázison.  A munkamenetek nélkül `ApplicationIntent=ReadOnly` a rendszer átirányítja az elsődleges replika, a georeplikált másodlagos, ami egyben csak olvasható. Georeplikált másodlagos adatbázis egy másik végponti, mint az elsődleges adatbázissal rendelkezik, mert hagyományosan eléréséhez a másodlagos ez nem szükséges beállítása `ApplicationIntent=ReadOnly`. Előző verziókkal való kompatibilitás érdekében `sys.geo_replication_links` DMV látható `secondary_allow_connections=2` (bármely ügyfél kapcsolat engedélyezett).

> [!NOTE]
> Ciklikus időszeletelés vagy bármely más elosztott terhelésű helyi replikáit a másodlagos adatbázis között nem támogatott.

## <a name="next-steps"></a>További lépések

- Olvasási kibővített beállítása a PowerShell használatával kapcsolatos információkért lásd: a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) vagy a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmagok.
- Olvasási kibővített beállítása a REST API használatával kapcsolatos információkért lásd: [- adatbázisok létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
