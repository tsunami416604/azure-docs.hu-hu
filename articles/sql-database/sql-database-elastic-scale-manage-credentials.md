---
title: Hitelesítő adatok kezelése a rugalmas adatbázis-ügyféltárban
description: A megfelelő szintű hitelesítő adatok beállítása, rendszergazda írásvédettre, rugalmas adatbázis-alkalmazásokhoz
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823581"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>A rugalmas adatbázis-ügyféltár eléréséhez használt hitelesítő adatok

A [rugalmas adatbázis-ügyfélkódtár](sql-database-elastic-database-client-library.md) három különböző típusú hitelesítő adatokat használ a [szegmenstérkép-kezelő](sql-database-elastic-scale-shard-map-management.md)eléréséhez. Attól függően, hogy szükség van,használja a hitelesítő adatokat a lehető legalacsonyabb szintű hozzáférést.

* **Felügyeleti hitelesítő adatok:** shard térképkezelő létrehozásához vagy kezeléséhez. (Lásd a [szószedetet.)](sql-database-elastic-scale-glossary.md)
* **Hozzáférési hitelesítő adatok:** egy meglévő shard térképkezelő eléréséhez a szegmensek információszerzéséhez.
* **Kapcsolat hitelesítő adatok:** a szilánkok hoz csatlakozni.

Lásd [még: Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben.](sql-database-manage-logins.md)

## <a name="about-management-credentials"></a>Felügyeleti hitelesítő adatok –

A felügyeleti hitelesítő adatok **segítségével shardmapmanager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) objektumot hozhat létre a szegmensleképezéseket kezelő alkalmazások számára. (Például lásd: [Szegmens hozzáadása rugalmas adatbázis-eszközök és](sql-database-elastic-scale-add-a-shard.md) [adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)használatával). A rugalmas méretezési ügyfélkódtár felhasználója létrehozza az SQL-felhasználókat és az SQL bejelentkezéseket, és gondoskodik arról, hogy mindegyik megkapja az olvasási/írási engedélyeket a globális shard térkép-adatbázishoz és az összes szegmens-adatbázishoz is. Ezek a hitelesítő adatok a globális shard térkép és a helyi shard térképek karbantartásához használatosak, amikor a shard térkép módosításai végrehajtása. Például használja a felügyeleti hitelesítő adatokat a shard map manager objektum létrehozásához **(A GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)használatával:

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Az **smmAdminConnectionString** változó egy kapcsolati karakterlánc, amely a felügyeleti hitelesítő adatokat tartalmazza. A felhasználói azonosító és a jelszó olvasási/írási hozzáférést biztosít a szegmenstérkép-adatbázishoz és az egyes szegmensekhez. A felügyeleti kapcsolati karakterlánc tartalmazza a kiszolgáló nevét és az adatbázis nevét is a globális shard térképadatbázis azonosításához. Itt van egy tipikus kapcsolat string erre a célra:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Ne használjon értékeket "username@server"– helyett csak használja a "felhasználónév" értéket.  Ennek az az oka, hogy a hitelesítő adatoknak a szegmenstérkép-kezelő adatbázisával és az egyes szegmensekkel szemben is működniük kell, amelyek különböző kiszolgálókon lehetnek.

## <a name="access-credentials"></a>Hitelesítő adatok elérése

Amikor egy shard map manager egy olyan alkalmazásban, amely nem felügyeli a szegmens leképezések, használja a hitelesítő adatokat, amelyek csak olvasható engedélyekkel rendelkeznek a globális shard térkép. A globális shard térképről ezek a hitelesítő adatok alatt beolvasott adatok [adatfüggő útválasztáshoz](sql-database-elastic-scale-data-dependent-routing.md) és az ügyfél shard térképgyorsítótárának feltöltéséhez használatos. A hitelesítő adatok a **GetSqlShardMapManager**számára ugyanazon a hívásmintán keresztül érhetők el:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Vegye figyelembe, hogy az **smmReadOnlyConnectionString** használata a hozzáféréshez a **nem rendszergazda** felhasználók nevében különböző hitelesítő adatok használatát tükrözi: ezek a hitelesítő adatok nem adhatnak írási engedélyeket a globális shard térképhez.

## <a name="connection-credentials"></a>Kapcsolat hitelesítő adatai

További hitelesítő adatokra van szükség, ha az **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) metódus használatával egy szilánkos kulcshoz társított shard eléréséhez. Ezek a hitelesítő adatok a shardon található helyi shard térképtáblák írásvédett eléréséhez szükséges engedélyeket kell biztosítaniuk. Ez szükséges a shard adatfüggő útválasztásának kapcsolat-ellenőrzéséhez. Ez a kódrészlet adatfüggő útválasztás környezetében teszi lehetővé az adatok elérését:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Ebben a példában **az smmUserConnectionString** megtartja a felhasználói hitelesítő adatok kapcsolati karakterláncát. Az Azure SQL DB esetében az alábbiakban a felhasználói hitelesítő adatok tipikus kapcsolati karakterlánca látható:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

A rendszergazdai hitelesítő adatokhoz ugyanúgy ne használjon értékeket "username@server" formában. Ehelyett csak használja a "felhasználónevet".  Azt is vegye figyelembe, hogy a kapcsolati karakterlánc nem tartalmaz kiszolgálónevet és adatbázisnevet. Ennek az az oka, hogy az **OpenConnectionForKey** hívás automatikusan irányítja a kapcsolatot a megfelelő shard a kulcs alapján. Ezért az adatbázis neve és a kiszolgáló neve nincs megadva.

## <a name="see-also"></a>Lásd még

[Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben](sql-database-manage-logins.md)

[Az SQL Database-adatbázis védelme](sql-database-security-overview.md)

[Rugalmas adatbázis-feladatok](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
