---
title: Replika-lekérdezések olvasása
description: A Azure SQL Database a csak olvasható replikák kapacitásával képes a csak olvasási terhelések terheléselosztására – az olvasási Felskálázási szolgáltatással.
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
ms.openlocfilehash: f111b19eb07c218a9f3250ef3ffdb8a97cf07542
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420727"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Csak olvasható replikák használata a csak olvasási lekérdezési feladatok terheléselosztásához

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A [magas rendelkezésre állású architektúra](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)részeként a prémium és üzletileg kritikus szolgáltatási szint minden adatbázisa automatikusan egy elsődleges replikával és több másodlagos replikával van kiépítve. A másodlagos replikákat ugyanazzal a számítási mérettel kell kiépíteni, mint az elsődleges replikát. Az **olvasási Felskálázási** funkció lehetővé teszi, hogy terheléselosztást SQL Database csak olvasható munkaterheléseket az írásvédett replikák egyikének kapacitásával az írható-olvasható replika megosztása helyett. Ezzel a módszerrel a csak olvasható számítási feladat elkülönül a fő olvasási és írási számítási feladattól, és nem befolyásolja annak teljesítményét. A szolgáltatás olyan alkalmazások számára készült, amelyek logikailag elkülönített, csak olvasható számítási feladatokat tartalmaznak, például az elemzést. A prémium és üzletileg kritikus szolgáltatási szinten az alkalmazások a további kapacitás nélkül vehetik igénybe a teljesítmény előnyeit.

Az **olvasási kibővítő** funkció a nagy kapacitású szolgáltatási rétegében is elérhető, ha legalább egy másodlagos replika létrejön. Több másodlagos replika is használható, ha az írásvédett munkaterhelések több erőforrást igényelnek, mint amennyi egy másodlagos replikán elérhető. Az alapszintű, standard és általános célú szolgáltatási rétegek magas rendelkezésre állású architektúrája nem tartalmaz replikákat. Az **olvasási Felskálázási** funkció nem érhető el ezekben a szolgáltatási rétegekben.

A következő ábra egy üzletileg kritikus-adatbázis használatát szemlélteti.

![Írásvédett replikák](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Az olvasási Felskálázási funkció alapértelmezés szerint engedélyezve van az új Premium-, üzletileg kritikus-és nagy kapacitású-adatbázisokon. A nagy kapacitású esetében az új adatbázisokhoz alapértelmezés szerint egy másodlagos replika jön létre. Ha az SQL-kapcsolódási karakterlánca `ApplicationIntent=ReadOnly`-vel van konfigurálva, az alkalmazást az átjáró átirányítja az adatbázis írásvédett replikájának. További információ a `ApplicationIntent` tulajdonság használatáról: az [alkalmazás szándékának megadása](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Ha biztosítani szeretné, hogy az alkalmazás az SQL-kapcsolati sztring `ApplicationIntent` beállításától függetlenül az elsődleges replikához kapcsolódjon, explicit módon le kell tiltania az olvasási felskálázást az adatbázis létrehozásakor vagy a konfigurációjának módosításakor. Ha például az adatbázist standard vagy általános célú szintről prémium, üzletileg kritikus vagy nagy kapacitású szintre frissíti, és biztosítani szeretné, hogy az összes kapcsolat továbbra is az elsődleges replikára lépjen, tiltsa le az olvasási felskálázást. A letiltásával kapcsolatos további információkért lásd: az [olvasási felskálázás engedélyezése és letiltása](#enable-and-disable-read-scale-out).

> [!NOTE]
> A lekérdezési adattár, a bővített események, az SQL Profiler és a naplózási funkciók nem támogatottak a csak olvasható replikák esetében.

## <a name="data-consistency"></a>Adatkonzisztencia

A replikák egyik előnye, hogy a replikák mindig tranzakciós szempontból konzisztens állapotban vannak, de a különböző időpontokban előfordulhat, hogy a különböző replikák között kis késés van. Az olvasási felskálázás támogatja a munkamenet-szintű konzisztenciát. Ez azt jelenti, hogy ha a csak olvasási munkamenet újracsatlakozik a replika nem rendelkezésre állása okozta kapcsolódási hiba után, a rendszer átirányítja egy olyan másodpéldányra, amely nem 100%-kal naprakész az írható-olvasható replikával. Hasonlóképpen, ha egy alkalmazás egy írható-olvasható munkamenet használatával ír adatokat, és azonnal beolvassa azt egy írásvédett munkamenettel, akkor lehetséges, hogy a legújabb frissítések nem láthatók azonnal a replikán. A késést egy aszinkron tranzakciónapló-ismétlési művelet okozta.

> [!NOTE]
> A régión belüli replikációs késések alacsonyak, és ez a helyzet ritka.

## <a name="connect-to-a-read-only-replica"></a>Kapcsolódás írásvédett replikához

Ha engedélyezi az olvasási felskálázást egy adatbázishoz, az ügyfél által megadott kapcsolódási karakterlánc `ApplicationIntent` beállítása azt diktálja, hogy a rendszer átirányítja-e a kapcsolódást az írási replikára vagy egy írásvédett replikára. Ha a `ApplicationIntent` értéke `ReadWrite` (az alapértelmezett érték), a rendszer átirányítja a kapcsolódást az adatbázis írható-olvasható replikájának. Ez megegyezik a meglévő viselkedéssel. Ha a `ApplicationIntent` értéke `ReadOnly`, a rendszer egy írásvédett replikához irányítja a kapcsolódást.

A következő kapcsolati karakterlánc például összekapcsolja az ügyfelet egy írásvédett replikával (a szögletes zárójelben lévő elemeket az adott környezet megfelelő értékeivel és a szögletes zárójelek eldobásával):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

A következő kapcsolati karakterláncok valamelyike az-ügyfelet egy írható-olvasható replikához csatlakoztatja (a szögletes zárójelben lévő elemeket a környezet megfelelő értékeivel és a szögletes zárójelek eldobásával helyettesíti):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Annak ellenőrzése, hogy a kapcsolódás írásvédett replikához van-e

A következő lekérdezés futtatásával ellenőrizheti, hogy csatlakozik-e egy írásvédett replikához. READ_ONLYt ad vissza, ha egy írásvédett replikához csatlakozik.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Az írásvédett munkamenetek csak az egyik AlwaysON replikát érhetik el.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Írásvédett replika figyelése és hibaelhárítása

Ha egy írásvédett replikához csatlakozik, a teljesítmény mérőszámait a `sys.dm_db_resource_stats` DMV használatával érheti el. A lekérdezési terv statisztikáinak eléréséhez használja a `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` és `sys.dm_exec_sql_text` DMV.

> [!NOTE]
> A logikai Master adatbázisban található DMV-`sys.resource_stats` az elsődleges replika CPU-használati és tárolási adatait adja vissza.

## <a name="enable-and-disable-read-scale-out"></a>Az olvasási felskálázás engedélyezése és letiltása

Az olvasási felskálázás alapértelmezés szerint engedélyezve van a Premium, a üzletileg kritikus és a nagy kapacitású szolgáltatás szintjein. Az olvasási felskálázás nem engedélyezhető alapszintű, standard vagy általános célú szolgáltatási szinteken. Az olvasási felskálázás automatikusan le van tiltva a 0 replikával konfigurált nagy kapacitású-adatbázisokban.

A következő módszerekkel letilthatja és újból engedélyezheti az olvasási felskálázást az önálló adatbázisok és a rugalmas készlet adatbázisaiban prémium vagy üzletileg kritikus szolgáltatási szinten.

> [!NOTE]
> A visszamenőleges kompatibilitás érdekében letilthatja az olvasási felskálázást.

### <a name="azure-portal"></a>Azure Portal

Az olvasási Felskálázási beállítást az adatbázis **konfigurálása** panelen kezelheti.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

Azure PowerShell az olvasási felskálázás kezelése a december 2016 Azure PowerShell vagy újabb verzióra van szükség. A legújabb PowerShell-kiadást itt tekintheti meg: [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

A `-ReadScale` paraméterhez a [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) `Disabled` `Enabled` parancsmag meghívásával letilthatja vagy újból engedélyezheti az olvasási felskálázást Azure PowerShell.

Ha le szeretné tiltani az olvasási felskálázást egy meglévő adatbázison (a szögletes zárójelben lévő elemek cseréje a környezet megfelelő értékeire, és a szögletes zárójelek eldobása):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Ha le szeretné tiltani az olvasási felskálázást egy új adatbázison (a szögletes zárójelben lévő elemek cseréje a környezet megfelelő értékeire, és a szögletes zárójelek eldobása):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Ha újra engedélyezni szeretné az olvasási felskálázást egy meglévő adatbázison (a szögletes zárójelben lévő elemek cseréje a környezet megfelelő értékeire, és a szögletes zárójelek eldobása):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Ha olyan adatbázist szeretne létrehozni, amelynél le van tiltva az olvasási felskálázás, vagy egy meglévő adatbázis beállítását szeretné módosítani, használja a következő metódust az `readScale` tulajdonsággal `Enabled` vagy `Disabled`, ahogy az alábbi példában szereplő kérelemben.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

További információ: [adatbázisok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>A TempDB használata írásvédett replikán

A TempDB-adatbázis nem replikálódik a csak olvasható replikára. Minden replika rendelkezik a TempDB adatbázisának saját verziójával, amely a replika létrehozásakor jön létre. Biztosítja, hogy a TempDB frissíthető legyen, és a lekérdezés végrehajtása során módosítható legyen. Ha az írásvédett számítási feladatok a TempDB-objektumok használatával függenek, ezeket az objektumokat a lekérdezési parancsfájl részeként kell létrehoznia.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Az olvasási méretezés földrajzilag replikált adatbázisokkal

Ha olvasási felskálázást használ a csak olvasási feladatok terheléselosztásához egy földrajzilag replikált adatbázison (például egy feladatátvételi csoport tagjaként), akkor ügyeljen arra, hogy az olvasási felskálázás engedélyezve legyen mind az elsődleges, mind a földrajzilag replikált másodlagos adatbázison. Ez a konfiguráció biztosítja, hogy ugyanaz a terheléselosztási élmény akkor is folytatódjon, ha az alkalmazás az új elsődleges feladatátvétel után csatlakozik. Ha a földrajzilag replikált másodlagos adatbázishoz csatlakozik, és az olvasási méretezés engedélyezve van, akkor a `ApplicationIntent=ReadOnly`-munkamenetek az egyik replikához lesznek irányítva, ugyanúgy, ahogy a kapcsolatokat az elsődleges adatbázison irányítjuk.  A `ApplicationIntent=ReadOnly` nélküli munkamenetek átirányítva lesznek a földrajzilag replikált másodlagos másodlagos replika elsődleges replikájának, amely szintén írásvédett. Mivel a földrajzilag replikált másodlagos adatbázis eltérő végponttal rendelkezik, mint az elsődleges adatbázis, a másodlagoshoz való hozzáférés nem volt szükséges a `ApplicationIntent=ReadOnly`beállításához. A visszamenőleges kompatibilitás érdekében `sys.geo_replication_links` DMV megjeleníti a `secondary_allow_connections=2`t (minden ügyfél-kapcsolódás engedélyezve van).

> [!NOTE]
> A másodlagos adatbázis helyi replikái közötti ciklikus multiplexelés vagy más terheléselosztási útválasztás nem támogatott.

## <a name="next-steps"></a>További lépések

- További információ a SQL Database nagy kapacitású-ajánlatról: [nagy kapacitású szolgáltatási szintje](./sql-database-service-tier-hyperscale.md).
