---
title: Rugalmas méretezés – gyakori kérdések
description: Gyakori kérdések az Azure SQL Database Elastic Scale.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3eedfb1e9ec59fbe12ee94a65d3702a7ef8ca95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823638"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Rugalmas adatbázis-kezelőeszközök – gyakori kérdések

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Ha egy-bérlős szegmensenként, és nincs szilánkok kulcs, hogyan feltölti a szilánkos kulcsot a séma info

A sémainformációs objektum csak egyesítési forgatókönyvek felosztására szolgál. Ha egy alkalmazás eredendően egy-bérlős, akkor nem igényel a Split Merge eszközt, és így nincs szükség a sémainformációs objektum feltöltésére.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Kidolgoztam egy adatbázist, és már van egy Shard Map Managerem, hogyan regisztrálhatom ezt az új adatbázist shardként

Olvassa el a Szegmens [hozzáadása egy alkalmazáshoz a rugalmas adatbázis-ügyfélkódtár használatával című témakört.](sql-database-elastic-scale-add-a-shard.md)

## <a name="how-much-do-elastic-database-tools-cost"></a>Mennyibe kerülnek a rugalmas adatbázis-eszközök

A rugalmas adatbázis-ügyfélkódtár használata nem jár költségekkel. A költségek csak a szegmensek és a szegmensek leképezési kezelője, valamint a split merge eszközhöz kiépített webes/feldolgozói szerepkörök esetében merülnek fel.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Miért nem működnek a hitelesítő adataim, ha egy másik kiszolgálóról hozok hozzá egy szilánkot?

Ne használjon hitelesítő adatokat "User ID=username@servername" formában, hanem egyszerűen használja a "User ID = username" kifejezést.  Is győződjön meg arról, hogy a "felhasználónév" bejelentkezési jogosultságokkal rendelkezik a shard.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Létre kell hoznom egy Shard Térképkezelőt, és minden alkalommal, amikor elkezdem az alkalmazásaimat, fel kell őket nagyítanom

Nem – a Shard Map Manager (például [ShardMapManagerFactory.CreateSqlShardMapManager)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)létrehozása egyszeri művelet.  Az alkalmazásnak a [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) hívást kell használnia az alkalmazás indítási idején.  Alkalmazástartományonként csak egy ilyen hívás nak lehet meg.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Kérdéseim vannak a rugalmas adatbázis-eszközök használatával kapcsolatban, hogyan kaphatom meg a választ

Kérjük, forduljon hozzánk az [SQL Database fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Amikor egy szilánkos kulccsal kap egy adatbázis-kapcsolatot, továbbra is lekérdezhetem az adatokat más szilánkok ugyanazon a szegmensen.  Ez a tervezés

A rugalmas méretezési API-k kapcsolatot biztosít a megfelelő adatbázisa a horizontális kulcs, de nem biztosítanak horizontális kulcs szűrés.  Adja **hozzá** a WHERE-záradékokat a lekérdezéshez, hogy szükség esetén korlátozza a hatókört a megadott szilánkolási kulcsra.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Használhatok más SQL Database kiadást a shard készletminden egyes szegmenséhez

Igen, a shard egy egyéni adatbázis, és így egy shard lehet egy prémium kiadás, míg egy másik standard kiadás. További, a shard kiadása skálázható fel-le többször a shard élettartama alatt.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Az egyesítési eszköz adatbázist létesít (vagy töröl) felosztási vagy egyesítési művelet során

Nem. **Felosztásos** műveletek esetén a céladatbázisnak a megfelelő sémával kell léteznie, és regisztrálnia kell a Shard Map Manager-szel.  **Egyesítési** műveletek hez törölnie kell a szegmenst a szegmens térképkezelőből, majd törölnie kell az adatbázist.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]