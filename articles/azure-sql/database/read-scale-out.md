---
title: Replika-lekérdezések olvasása
description: A Azure SQL Database a csak olvasható replikák kapacitásával képes a csak olvasási terhelések terheléselosztására, az úgynevezett olvasási felskálázással.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
titleSuffix: Azure SQL Database & SQL Managed Instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: a043eb5eed8f5554e42a113a3d7963be94da8a49
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985567"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Csak olvasható tevékenységprofil terheléselosztása csak olvasható replikák használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A [magas rendelkezésre állású architektúra](high-availability-sla.md#premium-and-business-critical-service-tier-availability)részeként a prémium és üzletileg kritikus szolgáltatási rétegek mindegyik adatbázisa automatikusan elsődleges replikával és több másodlagos replikával van kiépítve. A másodlagos replikákat ugyanazzal a számítási mérettel kell kiépíteni, mint az elsődleges replikát. Az *olvasási Felskálázási* funkció lehetővé teszi, hogy terheléselosztást SQL Database csak olvasható munkaterheléseket az írásvédett replikák egyikének kapacitásával az írható-olvasható replika megosztása helyett. Így a írásvédett munkaterhelés el lesz különítve a fő írási-olvasási számítási feladattól, és nem befolyásolja a teljesítményét. A szolgáltatás olyan alkalmazások számára készült, amelyek logikailag elkülönített, csak olvasható számítási feladatokat tartalmaznak, például az elemzést. A prémium és üzletileg kritikus szolgáltatási szinten az alkalmazások a további kapacitás nélkül vehetik igénybe a teljesítmény előnyeit.

Az olvasási kibővítő funkció a nagy kapacitású szolgáltatási rétegében is elérhető, ha legalább egy másodlagos replika létrejön. Több másodlagos replika is használható, ha az írásvédett munkaterhelések több erőforrást igényelnek, mint amennyi az egyik másodlagos replikán elérhető. Az alapszintű, standard és általános célú szolgáltatási rétegek magas rendelkezésre állású architektúrája nem tartalmaz replikákat. Az olvasási Felskálázási funkció nem érhető el ezekben a szolgáltatási rétegekben.

A következő ábra egy üzletileg kritikus-adatbázis használatát szemlélteti.

![Írásvédett replikák](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Az olvasási Felskálázási funkció alapértelmezés szerint engedélyezve van az új Premium-, üzletileg kritikus-és nagy kapacitású-adatbázisokon. A nagy kapacitású esetében az új adatbázisokhoz alapértelmezés szerint egy másodlagos replika jön létre. Ha az SQL-alapú kapcsolódási karakterlánca konfigurálva van `ApplicationIntent=ReadOnly` , az alkalmazást az átjáró átirányítja az adatbázis írásvédett replikájának. További információ a tulajdonság használatáról `ApplicationIntent` : az [alkalmazás szándékának megadása](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Ha biztosítani szeretné, hogy az alkalmazás az SQL-kapcsolati sztring beállításától függetlenül az elsődleges replikához kapcsolódjon `ApplicationIntent` , explicit módon le kell tiltania az olvasási méretezést az adatbázis létrehozásakor vagy a konfigurációjának módosításakor. Ha például az adatbázist standard vagy általános célú szintről prémium, üzletileg kritikus vagy nagy kapacitású szintre frissíti, és biztosítani szeretné, hogy az összes kapcsolat továbbra is az elsődleges replikára lépjen, tiltsa le az olvasási felskálázást. A letiltásával kapcsolatos további információkért lásd: az [olvasási felskálázás engedélyezése és letiltása](#enable-and-disable-read-scale-out).

> [!NOTE]
> A lekérdezési adattár, a bővített események és az SQL Profiler-funkciók nem támogatottak a csak olvasható replikák esetében.

## <a name="data-consistency"></a>Adatkonzisztencia

A replikák egyik előnye, hogy a replikák mindig tranzakciós szempontból konzisztens állapotban vannak, de a különböző időpontokban előfordulhat, hogy a különböző replikák között kis késés van. Az olvasási felskálázás támogatja a munkamenet-szintű konzisztenciát. Ez azt jelenti, hogy ha a csak olvasási munkamenet újracsatlakozik a replika nem rendelkezésre állása okozta kapcsolódási hiba után, a rendszer átirányítja egy olyan másodpéldányra, amely nem 100%-kal naprakész az írható-olvasható replikával. Hasonlóképpen, ha egy alkalmazás egy írható-olvasható munkamenet használatával ír adatokat, és azonnal beolvassa azt egy írásvédett munkamenettel, akkor lehetséges, hogy a legújabb frissítések nem láthatók azonnal a replikán. A késést egy aszinkron tranzakciónapló-ismétlési művelet okozta.

> [!NOTE]
> A régión belüli replikációs késések alacsonyak, és ez a helyzet ritka.

## <a name="connect-to-a-read-only-replica"></a>Kapcsolódás írásvédett replikához

Ha engedélyezi az olvasási felskálázást egy adatbázishoz, az `ApplicationIntent` ügyfél által megadott kapcsolódási karakterláncban szereplő beállítás azt határozza meg, hogy a rendszer átirányítja-e a kapcsolódást az írási replikára vagy egy írásvédett replikára. Pontosabban, ha az `ApplicationIntent` érték `ReadWrite` (az alapértelmezett érték), a rendszer átirányítja a kapcsolódást az adatbázis írható-olvasható replikájának. Ez megegyezik a meglévő viselkedéssel. Ha az `ApplicationIntent` érték `ReadOnly` , a rendszer a kapcsolódást egy írásvédett replikához irányítja.

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

A következő lekérdezés futtatásával ellenőrizheti, hogy csatlakozik-e egy írásvédett replikához. A READ_ONLY a csak olvasható replikához való csatlakozáskor fog visszaadni.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> A ReadOnly munkamenetek csak az egyik AlwaysON replikát érhetik el.

## <a name="monitor-and-troubleshoot-a-read-only-replica"></a>Írásvédett replika figyelése és hibakeresése

Ha egy írásvédett replikához csatlakozik, a teljesítmény mérőszámait a DMV használatával érheti el `sys.dm_db_resource_stats` . A lekérdezési terv statisztikáinak eléréséhez használja a `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` és a `sys.dm_exec_sql_text` DMV.

> [!NOTE]
> A `sys.resource_stats` logikai Master adatbázisban található DMV az elsődleges REPLIKA CPU-használati és tárolási adatait adja vissza.

## <a name="enable-and-disable-read-scale-out"></a>Az olvasási felskálázás engedélyezése és letiltása

Az olvasási Felskálázási funkció alapértelmezés szerint engedélyezve van a Premium, a üzletileg kritikus és a nagy kapacitású szolgáltatás szintjein. Az olvasási felskálázás nem engedélyezhető alapszintű, standard vagy általános célú szolgáltatási szinteken. Az olvasási felskálázás automatikusan le van tiltva a nulla replikákkal konfigurált nagy kapacitású-adatbázisokban.

A következő módszerekkel letilthatja és újból engedélyezheti az olvasási felskálázást az önálló adatbázisok és a rugalmas készletű adatbázisok esetében a prémium vagy üzletileg kritikus szolgáltatási szinten.

> [!NOTE]
> A visszamenőleges kompatibilitás érdekében letilthatja az olvasási felskálázást.

### <a name="azure-portal"></a>Azure Portal

Az olvasási Felskálázási beállítást az adatbázis **konfigurálása** panelen kezelheti.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A Azure Resource Manager modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a Azure Resource Manager modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

Azure PowerShell az olvasási felskálázás kezelése a december 2016 Azure PowerShell vagy újabb verzióra van szükség. A legújabb PowerShell-kiadást itt tekintheti meg: [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

A [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) parancsmag meghívásával letilthatja vagy újból engedélyezheti az olvasási felskálázást Azure PowerShell a `Enabled` `Disabled` paraméterhez a kívánt érték (vagy) megadásával `-ReadScale` .

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

Ha olyan adatbázist szeretne létrehozni, amelynél le van tiltva az olvasási felskálázás, vagy egy meglévő adatbázis beállítását szeretné módosítani, használja a következő metódust a vagy értékre `readScale` beállított tulajdonsággal, `Enabled` `Disabled` ahogy az az alábbi példában is látható.

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

## <a name="using-tempdb-on-a-read-only-replica"></a>A TempDB használata írásvédett replikán

A TempDB-adatbázis nem replikálódik a csak olvasható replikára. Minden replika rendelkezik a TempDB adatbázisának saját verziójával, amely a replika létrehozásakor jön létre. Biztosítja, hogy a TempDB frissíthető és módosítható legyen a lekérdezés végrehajtása során. Ha az írásvédett számítási feladatok a TempDB-objektumok használatával függenek, ezeket az objektumokat a lekérdezési parancsfájl részeként kell létrehoznia.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Az olvasási méretezés földrajzilag replikált adatbázisokkal

Ha olvasási felskálázást használ a csak olvasási feladatok terheléselosztásához egy földrajzilag replikált adatbázisban (például egy feladatátvételi csoport tagjaként), ügyeljen arra, hogy az olvasási felskálázás engedélyezve legyen mind az elsődleges, mind a földrajzilag replikált másodlagos adatbázison. Ez a konfiguráció biztosítja, hogy ugyanaz a terheléselosztási élmény akkor is folytatódjon, ha az alkalmazás az új elsődleges feladatátvétel után csatlakozik. 

Ha a földrajzilag replikált másodlagos adatbázishoz csatlakozik, és az olvasási méretezés engedélyezve van, a-munkamenetek az `ApplicationIntent=ReadOnly` egyik replikához lesznek irányítva, ugyanúgy, ahogy az elsődleges adatbázison keresztül irányítjuk a kapcsolatokat.  A munkamenetek nem `ApplicationIntent=ReadOnly` lesznek átirányítva a földrajzilag replikált másodlagos másodlagos replika elsődleges replikájának, amely szintén írásvédett. Mivel a Geo-replikált másodlagos adatbázis eltérő végponttal rendelkezik, mint az elsődleges adatbázis, a másodlagoshoz való hozzáféréshez nem szükséges a beállítás `ApplicationIntent=ReadOnly` . A visszamenőleges kompatibilitás érdekében a `sys.geo_replication_links` DMV a következőt jeleníti meg: `secondary_allow_connections=2` (minden ügyfél-kapcsolódás engedélyezett).

> [!NOTE]
> A másodlagos adatbázis helyi replikái közötti ciklikus multiplexelés vagy más terheléselosztási útválasztás nem támogatott.

## <a name="next-steps"></a>További lépések

További információ a SQL Database nagy kapacitású-ajánlatról: [nagy kapacitású szolgáltatási szintje](service-tier-hyperscale.md).
