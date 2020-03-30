---
title: Kópiák lekérdezéseinek olvasása
description: Az Azure SQL Database lehetővé teszi, hogy csak olvasható számítási feladatok terheléselosztása az írásvédett replikák kapacitása használatával – az úgynevezett olvasási horizontális felskálázás.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206945"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Csak olvasható tevékenységprofil terheléselosztása csak olvasható replikák használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A magas [rendelkezésre állású architektúra részeként](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)a prémium szintű és az üzleti legkritikusabb szolgáltatási szint minden adatbázisa automatikusan kivan építve egy elsődleges replika és több másodlagos replika. A másodlagos replikák az elsődleges replikával megegyező számítási mérettel vannak kiépítve. Az **Olvasási horizontális felskálázás** szolgáltatás lehetővé teszi az SQL Database csak olvasható számítási feladatok terhelését az írásvédett kópiák kapacitása használatával az írás-olvasási replika megosztása helyett. Ezzel a módszerrel a csak olvasható számítási feladat elkülönül a fő olvasási és írási számítási feladattól, és nem befolyásolja annak teljesítményét. A szolgáltatás olyan alkalmazásokhoz készült, amelyek logikailag elkülönített írásvédett számítási feladatokat, például elemzéseket tartalmaznak. A prémium szintű és üzleti legkritikusabb szolgáltatási szinteken az alkalmazások további költségek nélkül teljesítményelőnyöket érhetnek el ezzel a további kapacitással.

A **Leolvasási horizontális felskálázás** szolgáltatás is elérhető a nagy kapacitású szolgáltatási szint, ha legalább egy másodlagos replika jön létre. Több másodlagos replikák is használható, ha csak olvasható számítási feladatok igényelnek több erőforrást, mint a rendelkezésre álló egy másodlagos replika. Az alapszintű, a standard és az általános célú szolgáltatási szintek magas rendelkezésre állású architektúrája nem tartalmaz replikákat. A **Leolvasási horizontális felskálázás** funkció nem érhető el ezekben a szolgáltatási szintekben.

Az alábbi ábra egy üzleti legkritikusabb adatbázis használatával szemlélteti.

![Csak olvasható kópiák](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

A Leolvasási horizontális felskálázás funkció alapértelmezés szerint engedélyezve van az új prémium szintű, üzleti legkritikusabb és nagy méretű adatbázisokban. A Nagykapacitás esetében alapértelmezés szerint egy másodlagos replika jön létre az új adatbázisokhoz. Ha az SQL-kapcsolati `ApplicationIntent=ReadOnly`karakterlánc konfigurálva van, az alkalmazást az átjáró átirányítja az adatbázis csak olvasható replikájára. A tulajdonság használatáról `ApplicationIntent` az [Alkalmazásleképezés megadása című](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)témakörben talál további információt.

Ha biztosítani szeretné, hogy az alkalmazás az SQL-kapcsolati karakterlánc `ApplicationIntent` ban lévő beállítástól függetlenül csatlakozzon az elsődleges kópiához, az adatbázis létrehozásakor vagy a konfiguráció módosításakor explicit módon le kell tiltania az olvasási horizontális felskálázást. Ha például az adatbázist normál vagy általános célú szintről prémium, üzleti legkritikusabb vagy nagy kapacitású szintre frissíti, és biztosítani szeretné, hogy az összes kapcsolat továbbra is az elsődleges replika legyen, tiltsa le az Olvasási horizontális felskálázást. A letiltásáról az [Olvasási kicsinyítés engedélyezése és letiltása](#enable-and-disable-read-scale-out)témakörben olvashat.

> [!NOTE]
> A Query Data Store, a Extended Events és az SQL Profiler szolgáltatások nem támogatottak az írásvédett replikákon.

## <a name="data-consistency"></a>Adatkonzisztencia

A replikák egyik előnye, hogy a replikák mindig tranzakcióskonzisztens állapotban vannak, de különböző időpontokban előfordulhat, hogy a különböző replikák között kis késés van. A Read Scale-Out támogatja a munkamenet-szintű konzisztenciát. Ez azt jelenti, hogy ha az írásvédett munkamenet a replika elérhetetlensége által okozott csatlakozási hiba után újracsatlakozik, előfordulhat, hogy átirányítja egy olyan replikára, amely nem 100%-osan naprakész az írás-olvasás i replikával. Hasonlóképpen, ha egy alkalmazás írási és írási és olvasási munkamenet használatával írja az adatokat, és azonnal beolvassa azt egy csak olvasható munkamenet használatával, lehetséges, hogy a legújabb frissítések nem azonnal látható a replika. A késést egy aszinkron tranzakciónapló-ismétlési művelet okozza.

> [!NOTE]
> A régión belüli replikációs késések alacsonyak, és ez a helyzet ritka.

## <a name="connect-to-a-read-only-replica"></a>Csatlakozás írásvédett kópiához

Ha engedélyezi az olvasási horizontális felskálázást egy adatbázishoz, az `ApplicationIntent` ügyfél által biztosított kapcsolati karakterláncban megadott beállítás határozza meg, hogy a kapcsolat az írási kópiához vagy egy írásvédett kópiához kerül-e. Ha az `ApplicationIntent` érték (az alapértelmezett érték) a `ReadWrite` kapcsolat az adatbázis írás-olvasási replikájára lesz irányítva. Ez megegyezik a meglévő viselkedéssel. Ha `ApplicationIntent` az `ReadOnly`érték a , a kapcsolat egy írásvédett kópiához lesz irányítva.

Például a következő kapcsolati karakterlánc az ügyfelet egy írásvédett kópiához kapcsolja (a szögletes zárójelben lévő elemeket a környezetnek megfelelő értékekre cseréli, és eldobja a szögletes zárójeleket):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Az alábbi kapcsolati karakterláncok bármelyike egy írás-olvasási kópiához kapcsolja az ügyfelet (a szögletes zárójelben lévő elemeket a környezetnek megfelelő értékekre cseréli, és eldobja a szögletes zárójeleket):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Annak ellenőrzése, hogy a kapcsolat írásvédett kópiához kapcsolódik-e

A következő lekérdezés futtatásával ellenőrizheti, hogy kapcsolódik-e írásvédett kópiához. Írásvédett kópiához való csatlakozáskor READ_ONLY ad vissza.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Adott időpontban csak az AlwaysON replikák egyike érhető el a Csak olvasható munkamenetek által.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Írásvédett replika figyelése és hibaelhárítása

Ha csak olvasható kópiához csatlakozik, a teljesítménymutatók at a `sys.dm_db_resource_stats` DMV használatával érheti el. A lekérdezési terv statisztikáinak `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` eléréséhez használja a és a `sys.dm_exec_query_stats`DMV-ket.

> [!NOTE]
> A logikai `sys.resource_stats` főadatbázisban lévő DMV az elsődleges replika processzorhasználati és tárolási adatait adja vissza.

## <a name="enable-and-disable-read-scale-out"></a>Lehetővé teszi és letiltja az olvasási horizontális felskálázást

Az olvasási horizontális felskálázás alapértelmezés szerint engedélyezve van a prémium, üzleti kritikus és nagy kapacitású szolgáltatási szinteken. Az olvasási horizontális felskálázás nem engedélyezhető alapszintű, szabványos vagy általános célú szolgáltatási szinteken. A 0 kópiával konfigurált nagy méretű adatbázisokon az olvasási horizontális felskálázás automatikusan le van tiltva.

Letilthatja és újra engedélyezheti a lekicsidlés letiltását egyetlen adatbázisokban és rugalmas készletadatbázisokon a prémium szintű vagy az üzleti legkritikusabb szolgáltatási szinten az alábbi módszerekkel.

> [!NOTE]
> A leolvasási kicsinyítés letiltása a visszamenőleges kompatibilitás érdekében biztosított.

### <a name="azure-portal"></a>Azure portál

Az Adatbázis **konfigurálása** panelen kezelheti a Leolvasási horizontális felskálázás beállítást.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

Az Azure PowerShellben az olvasási horizontális felskálázás kezeléséhez a 2016 decemberi Azure PowerShell-kiadásra vagy újabb ra van szükség. A legújabb PowerShell-kiadásról az [Azure PowerShell című témakörben van.](https://docs.microsoft.com/powershell/azure/install-az-ps)

Letilthatja vagy újra engedélyezheti a lekicsizés olvasását az Azure PowerShellben a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) `Disabled` parancsmag meghívásával és a `-ReadScale` kívánt érték – `Enabled` vagy -- paraméter átadásával.

Az olvasási horizontális felskálázás letiltása meglévő adatbázisban (a szögletes zárójelekben lévő elemek cseréje a környezetnek megfelelő értékekre, valamint a szögletes zárójelek eldobása):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Az olvasási horizontális felskálázás letiltása egy új adatbázisban (a szögletes zárójelekben lévő elemek cseréje a környezetnek megfelelő értékekre, valamint a szögletes zárójelek eldobása):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

A leolvasási horizontális felskálázás újbóli engedélyezése egy meglévő adatbázisban (a szögletes zárójelben lévő elemek cseréje a környezetnek megfelelő értékekre, valamint a szögletes zárójelek eldobása):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Ha le tiltott olvasási kibővített adatbázist szeretne létrehozni, vagy módosítani szeretné egy `readScale` meglévő adatbázis `Enabled` `Disabled` beállítását, használja a következő módszert úgy, hogy a tulajdonság az alábbi mintakérelemben vagy az alábbi módon van beállítva.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

További információt az [Adatbázisok – Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)című témakörben talál.

## <a name="using-tempdb-on-read-only-replica"></a>A TempDB használata írásvédett kópián

A TempDB-adatbázis nem replikálódik az írásvédett replikákra. Minden kópia saját TempDB-adatbázis-verzióval rendelkezik, amely a kópia létrehozásakor jön létre. Ez biztosítja, hogy a TempDB frissíthető, és a lekérdezés végrehajtása során módosítható. Ha az írásvédett munkaterhelés a TempDB-objektumok használatától függ, ezeket az objektumokat a lekérdezési parancsfájl részeként kell létrehozni.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Leolvasási horizontális felskálázás georeplikált adatbázisokkal

Ha olvasási horizontális felskálázást használ a csak földrajzilag replikált adatbázis olvasási számítási feladatainak terheléselosztásához (például egy feladatátvételi csoport tagjaként), győződjön meg arról, hogy az olvasási horizontális felskálázás engedélyezve van az elsődleges és a georeplikált másodlagos adatbázisokban is. Ez a konfiguráció biztosítja, hogy ugyanaz a terheléselosztási élmény folytatódik, amikor az alkalmazás a feladatátvétel után csatlakozik az új elsődlegeshez. Ha a georeplikált másodlagos adatbázishoz csatlakozik olvasási skálával `ApplicationIntent=ReadOnly` engedélyezve, a munkamenetek az egyik replikához leszirányítva, ugyanúgy, ahogy az elsődleges adatbázisban lévő kapcsolatokat továbbítjuk.  A munkamenetek nélkül `ApplicationIntent=ReadOnly` lesz irányítva az elsődleges replikája a georeplikált másodlagos, amely szintén csak olvasható. Mivel a georeplikált másodlagos adatbázis végpontja eltér az elsődleges adatbázistól, a másodlagos adatbázis `ApplicationIntent=ReadOnly`eléréséhez korábban nem volt szükség a beállításhoz. A visszamenőleges kompatibilitás `sys.geo_replication_links` biztosítása érdekében `secondary_allow_connections=2` a DMV megjelenik (bármilyen ügyfélkapcsolat engedélyezett).

> [!NOTE]
> Ciklikus multiplexelés vagy bármely más terheléselosztásos útválasztás a másodlagos adatbázis helyi replikái között nem támogatott.

## <a name="next-steps"></a>További lépések

- Az SQL Database nagy kapacitású ajánlatáról a [Nagykapacitású szolgáltatási szint](./sql-database-service-tier-hyperscale.md)című témakörben talál további információt.
