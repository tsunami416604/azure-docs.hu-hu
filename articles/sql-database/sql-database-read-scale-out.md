---
title: Az Azure SQL adatbázis - replikák lekérdezései olvasási |} Microsoft Docs
description: Az Azure SQL Database lehetővé teszi a egyenleg írásvédett munkaterhelés kapacitását, csak olvasható replika - olvasási kibővített nevű betöltése.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: sashan
ms.openlocfilehash: 8de70c01f4c04d6df85c2f5acfe9efe18ff59c0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649686"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Csak olvasható replikákat használ a csak olvasható elosztásában (előzetes verzió) betöltése

**Olvassa el a kibővített** lehetővé teszi, hogy az Azure SQL-adatbázis csak olvasható elosztásában írásvédett replikák tartalékkapacitását használja. 

## <a name="overview-of-read-scale-out"></a>Olvassa el a kibővített áttekintése

A prémium szinten lévő minden egyes adatbázis ([alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md)) vagy a fontos üzleti szinten lévő ([vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md)) automatikusan ki van építve több Always ON replikák SLA-elérhetőséget támogatásához. Ezekre a replikákra ki vannak építve az írható-olvasható replika rendszeres adatbázis-kapcsolatok által használt teljesítmény szintjét. A **olvasási kibővített** funkció lehetővé teszi, hogy SQL-adatbázis csak olvasható elosztásában a csak olvasható replika kapacitása nem osztja meg a írható-olvasható replika használatával. Ezzel a módszerrel a csak olvasható munkaterhelés elkülönül a fő olvasási és írási terhelést, és nem lesz hatással a teljesítményét. A szolgáltatás célja, az alkalmazásokat, amelyek logikailag tartalmaznak csak olvasható munkaterhelések, például az elemzés, szóközzel, és ezért szerezhet jobb teljesítményt nyújt a további tartalékkapacitását használja, nem kapcsolódik további költség.

Az olvasási kibővített szolgáltatással az adott adatbázishoz, explicit módon engedélyeznie kell azt az adatbázis létrehozásakor vagy ezek után a PowerShell használatával való konfigurációját megváltoztatásával a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [ Új AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) parancsmagok vagy az Azure Resource Manager REST API használatával a [- adatbázisok létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) metódust. 

Olvasási kibővített adatbázis engedélyezése után az adatbázishoz való kapcsolódás alkalmazásokat a rendszer kéri, vagy az olvasási és írási replikára vagy egy csak olvasható replika az adatbázist a következők szerint a `ApplicationIntent` tulajdonság, az alkalmazás konfigurálva kapcsolati karakterlánc. Információk a `ApplicationIntent` tulajdonság, lásd: [megadó alkalmazás szándéka](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Olvasási kibővített le van tiltva, vagy a ReadScale tulajdonsága egy nem támogatott szolgáltatási rétegben, ha az írható-olvasható replika, függetlenül az összes kapcsolat van irányítva a `ApplicationIntent` tulajdonság.

> [!NOTE]
> Előzetes lekérdezés adattár és a bővített eseményektől nem támogatottak a csak olvasható replikákon.

## <a name="data-consistency"></a>Adatkonzisztencia

Az Always ON előnyei egyike, hogy a replikák mindig a tranzakciós úton konzisztens állapotban van, de különböző időpontokban időben lehet néhány kis késleltetésű között különböző replikákat. Olvassa el a kibővített munkamenet szintű konzisztencia támogatja. Azt jelenti, ha a csak olvasható munkamenet újracsatlakozik a replika elérhetetlensége miatt kapcsolódási hiba után, akkor átirányíthatók replika nem naprakész, és az írható-olvasható replika 100 %. Ehhez hasonlóan az alkalmazások írja az adatokat, olvasási és írási munkamenet használatával, és azonnal olvassa be a csak olvasható munkamenet használatával, esetén előfordulhat, hogy a legújabb frissítések olyan nem jelennek meg azonnal. Ez azért, mert a tranzakciós napló ismét: a replikához aszinkron.

> [!NOTE]
> Replikációs késések fordulnak elő a régión belül alacsony, és ez a helyzet ritkán fordul elő.


## <a name="connecting-to-a-read-only-replica"></a>Csatlakozás egy csak olvasható replika

-Adatbázis, olvassa el a kibővített engedélyezésekor a `ApplicationIntent` az ügyfél által megadott kapcsolati karakterlánc beállítása szabja meg, hogy az írási replikára vagy egy csak olvasható replika történik-e a kapcsolat. Pontosabban Ha a `ApplicationIntent` értéke `ReadWrite` (az alapértelmezett érték), a rendszer kéri a kapcsolatot az adatbázis írható-olvasható replika. Ez megegyezik a létező esetén. Ha a `ApplicationIntent` értéke `ReadOnly`, a kapcsolat olvasható replika van átirányítva.

A következő kapcsolati karakterlánc például az ügyfél csatlakozik egy csak olvasható replika (a csúcsos zárójelek elemek cseréje a környezetének megfelelő értékekkel, és a csúcsos zárójelek eldobása):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Az ügyfél csatlakozik a következő kapcsolati karakterláncok vagy írható-olvasható replika (a csúcsos zárójelek elemek cseréje a környezetének megfelelő értékekkel, és a csúcsos zárójelek eldobása):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Ellenőrizheti, hogy csatlakozik egy csak olvasható replika a következő lekérdezés futtatásával. Ha csatlakozik egy csak olvasható replika READ_ONLY ad vissza.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Engedélyezheti vagy letilthatja a olvasási kibővített Azure PowerShell használatával

Olvassa el a kibővített az Azure PowerShell kezelése igényel a December 2016 az Azure PowerShell kiadás vagy újabb. A legújabb PowerShell-verzió, lásd: [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Engedélyezheti vagy tilthatja le az Azure PowerShell olvasási kibővített figyelőn a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) parancsmag és a kívánt értéket – a tompított `Enabled` vagy `Disabled` – az a `-ReadScale` paraméter. Másik lehetőségként használhatja a [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) parancsmaggal hozzon létre egy új adatbázist, olvassa el a kibővített engedélyezve van.

Például ahhoz, hogy olvassa el a kibővített az egy létező adatbázis-(a csúcsos zárójelek elemek cseréje a környezetének megfelelő értékekkel, és a csúcsos zárójelek eldobása):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Olvasási kibővíthető egy meglévő adatbázist (a csúcsos zárójelek elemek cseréje a környezetének megfelelő értékekkel, és a csúcsos zárójelek eldobása) való letiltása:

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Hozzon létre egy új adatbázist olvasási kibővített engedélyezni (a csúcsos zárójelek elemek cseréje a környezetének megfelelő értékekkel, és a csúcsos zárójelek eldobása):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Engedélyezése és letiltása olvasási kibővített az Azure SQL Database REST API használatával

Olvasási kibővített engedélyezve van, az adatbázis létrehozásához vagy engedélyezi vagy letiltja a meglévő adatbázis olvasási kibővített, hozzon létre, vagy frissítse az adatbázis megfelelő entitásának a `readScale` tulajdonsága `Enabled` vagy `Disabled` és a az alábbi minta a kérést.

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

További információkért lásd: [- adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate).

## <a name="next-steps"></a>További lépések

- Olvasási kibővített beállítása a PowerShell használatával kapcsolatos információkért lásd: a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) parancsmagok.
- Olvassa el a kibővített beállítása a REST API használatával kapcsolatos információkért lásd: [- adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate).
