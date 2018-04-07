---
title: A rugalmas adatbázis ügyféloldali kódtára a hitelesítő adatok kezelése |} Microsoft Docs
description: A megfelelő szintű hitelesítő adatokat, csak olvasható, a rugalmas adatbázis-alkalmazások rendszergazdai beállítása
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c18e5da04fd92fed2cb9e93d13b5f3ecdeaac1a1
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatokat
A [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) elérésére használt hitelesítő adatok három különböző típusú a [shard térkép manager](sql-database-elastic-scale-shard-map-management.md). Attól függően, hogy szükség van használja a legalacsonyabb szintű hozzáférés lehetséges a hitelesítő adatokat.

* **Felügyeleti hitelesítő adatok**: létrehozásához vagy a shard térkép manager kezelésére. (Lásd a [szószedet](sql-database-elastic-scale-glossary.md).) 
* **Hozzáférési hitelesítő adatokat**: egy meglévő shard térkép manager szilánkok információ eléréséhez.
* **Kapcsolat hitelesítő adatait**: szilánkok való kapcsolódáshoz. 

Lásd még: [adatbázisok és az Azure SQL Database bejelentkezések kezelése](sql-database-manage-logins.md). 

## <a name="about-management-credentials"></a>Tudnivalók a felügyeleti hitelesítő adatok
Felügyeleti hitelesítő adatok segítségével hozzon létre egy **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) alkalmazásokat, amelyek kezelhetők a shard maps-objektumot. (Lásd például: [hozzáadása egy rugalmas eszközökkel shard](sql-database-elastic-scale-add-a-shard.md) és [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md)). A felhasználó a rugalmas bővítést ügyféloldali kódtár létrehozza az SQL-felhasználók és az SQL bejelentkezési adatok, és biztosítja, hogy minden egyes kap a globális shard adatbázist és az összes shard adatbázis, valamint az olvasási/írási engedéllyel. Ezek a hitelesítő adatok segítségével a globális shard térkép és a helyi shard maps karbantartása, a shard térkép módosításai végrehajtásakor. Használja például a felügyeleti hitelesítő adatokat az objektum shard térkép létrehozásához (használatával **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)): 

```
// Obtain a shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy); 
```

A változó **smmAdminConnectionString** egy kapcsolati karakterlánc, amely tartalmazza a felügyeleti hitelesítő adatokat. A felhasználói Azonosítót és jelszót a shard adatbázist és a egyes szilánkok olvasási/írási hozzáférést biztosítanak. A felügyeleti kapcsolati karakterláncot is a kiszolgáló nevét és az adatbázis nevét a globális shard adatbázist. Íme egy jellegzetes kapcsolati karakterlánc erre a célra:

```
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 
```

Ne használjon értékek formájában "username@server" – Ehelyett használja a "felhasználónév" értéket.  Ennek az az oka a hitelesítő adatokat kell működnie, szemben a shard manager adatbázist és az egyes szilánkok, lehet, hogy a különböző kiszolgálókon.

## <a name="access-credentials"></a>Elérési hitelesítő adatok
Létrehozásakor a shard térkép manager alkalmazásban nem felügyelheti a shard maps, használja a hitelesítő adatokat, amelyek csak olvasási engedéllyel a globális shard térképen. Ezeket a hitelesítő adatokat a globális shard leképezés beolvasott adatok használt [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) és az ügyfélen a shard térkép gyorsítótár adatokkal való feltöltése. A megadott hitelesítő adatok vannak a azonos hívás mintát keresztül **GetSqlShardMapManager**: 

```
// Obtain shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Vegye figyelembe a használatát a **smmReadOnlyConnectionString** különböző hitelesítő adatok használatát a nevében hozzáférés megfelelően **nem rendszergazdai** felhasználók: ezek a hitelesítő adatok nem biztosítania kell írási engedéllyel a globális shard térképen. 

## <a name="connection-credentials"></a>Kapcsolat hitelesítő adatait
További hitelesítő adatok szükségesek a használatakor a **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) metódus egy egy horizontális skálázási kulcshoz tartozó shard eléréséhez. Ezeket a hitelesítő adatokat kell megadnia a csak olvasási hozzáféréssel a helyi shard térkép táblákban található a szilánkcímtárban engedélyeit. Ez az adatok függő az Útválasztás a shard kapcsolat-ellenőrzés végrehajtása szükséges. A kódrészletet lehetővé teszi, hogy adat-hozzáférési adatokat függő útválasztási környezetében: 

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 
```

Ebben a példában **smmUserConnectionString** tárolja a felhasználói hitelesítő adatokat a kapcsolati karakterláncot. Az Azure SQL Database Szolgáltatásnak Ez a felhasználói hitelesítő adatokat egy tipikus kapcsolati karakterlánc: 

```
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Mivel a rendszergazdai hitelesítő adatokkal nem értékek formájában "username@server". Ehelyett használja "felhasználónév".  Ne feledje, hogy a kapcsolati karakterlánc nem tartalmaz a kiszolgáló és adatbázis nevét. Mivel ez a **OpenConnectionForKey** hívás automatikusan arra utasítja a kapcsolatot a megfelelő shard kulcs alapján. Ezért az adatbázis nevét és a kiszolgáló neve nincs megadva. 

## <a name="see-also"></a>Lásd még
[Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben](sql-database-manage-logins.md)

[Az SQL Database-adatbázis védelme](sql-database-security-overview.md)

[Ismerkedés a rugalmas adatbázis-feladatok](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

