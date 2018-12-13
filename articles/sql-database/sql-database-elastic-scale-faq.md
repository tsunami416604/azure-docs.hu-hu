---
title: Az Azure SQL rugalmas méretezés – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések az Azure SQL Database szolgáltatás rugalmasan méretezhető.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: a8a26a0a273c8e3cf1880ce277c2d4b4241e35ee
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874736"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Elastic database-eszközök – gyakori kérdések (GYIK)

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Ha van egy egybérlős szegmensek és horizontális particionálás kulcs nélküli, hogyan do I feltöltéséhez a séma információkat a horizontális skálázási kulcs?

Az adatok sémaobjektum felosztása, egyesítése forgatókönyvek csak használatos. Természetüknél fogva egybérlős alkalmazások esetén nem szükséges a felosztási-egyesítési eszközzel, és így nincs szükség az adatok sémaobjektum feltöltéséhez.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>I már kiépített egy adatbázis és a egy Szilánkleképezés-kezelővel már rendelkezik, hogyan regisztrálhatom az új adatbázis szegmensek szerint?

Lásd:  **[szegmensek hozzáadása egy alkalmazáshoz, az elastic database ügyfélkódtár használatával](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>IP-címek fenntartási költségeket a rugalmas Adatbáziseszközök?

Az elastic database ügyfélkódtár használatával nem jár semmilyen költségeket. Csak a szegmensek és a Szegmenstérkép-kezelőt használó Azure SQL-adatbázisok, valamint a webes/feldolgozói szerepkörök üzembe helyezi a felosztási-egyesítési eszközének lépheti túl a költségek.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Miért érdemes a hitelesítő adataimat nem működnek a szegmensek hozzáadásakor egy másik kiszolgálóról?

Ne használja a hitelesítő adatok formájában "felhasználói azonosító =username@servername", ehelyett egyszerűen használja "felhasználói azonosító = username".  Is arról, hogy a "felhasználónév" bejelentkezés engedélyekkel rendelkezik a szegmensben.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Van a Szilánkleképezés-kezelő létre és tölthet fel a szegmensek, minden indításakor alkalmazásaimat?

Nem – létrehozása a Szilánkleképezés-kezelővel (például  **[ShardMapManagerFactory.CreateSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) egy egyszeri művelet.  Az alkalmazás használ a hívás **[ShardMapManagerFactory.TryGetSqlShardMapManager()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** indítási időpontban.  Nem kell application tartományonként csak egy ilyen hívást.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Elastic database-eszközök használatával kapcsolatos kérdése van, hogyan érhetők el válaszok?

Vegye fel velünk a kapcsolatot a a [Azure SQL Database fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Ha egy adatbázis-kapcsolat segítségével olyan szegmenskulcsot lekéréséhez I továbbra is más horizontális skálázási kulcs ugyanabban a szegmensben lévő adatokat lekérdezni.  Ez az elvárt?

A rugalmasan méretezhető horizontális skálázási kulcs egy kapcsolatot a megfelelő adatbázist biztosít, de nem biztosít horizontális skálázási kulcs szűrés.  Adjon hozzá **ahol** záradékok való korlátozásához a hatókört a megadott horizontális skálázási kulcs szükséges a lekérdezéshez.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Használható különböző Azure-adatbázist a kiadás minden egyes szegmens a saját horizontális csoportban?

Igen, egy szegmens egy önálló adatbázis, és így egyik adatszilánkba író lehet egy prémium szintű kiadás a Standard edition egy másik közben. További szegmensek kiadása méretezhető felfelé és lefelé a szegmens élettartama során többször.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A felosztási-egyesítési eszközzel kiépítése nem (és törlése) egy adatbázis felosztása és egyesítése művelet során?

Nem. A **felosztása** műveletek, a céladatbázis léteznie kell a megfelelő sémával és regisztrálni kell a Szilánkleképezés-kezelővel.  A **egyesítési** operations, törölnie kell a szegmens a a szilánkleképezés-kezelővel, majd törölje az adatbázis.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]