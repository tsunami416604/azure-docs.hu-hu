---
title: Azure SQL rugalmas méretezés – gyakori kérdések
description: Gyakori kérdések Azure SQL Database rugalmas Skálázásról.
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
ms.openlocfilehash: 8ef99d6d752f1e9474fccea6c00b51b61ae1304b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690256"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Rugalmas adatbázis-eszközök – gyakori kérdések (GYIK)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Ha egy vagy több-bérlős szegmens van, és nincs horizontális Felskálázási kulcs, hogyan tölthetők fel a séma adataihoz tartozó horizontális Felskálázási kulcs.

A séma-információs objektum csak egyesítési forgatókönyvek felosztására szolgál. Ha egy alkalmazás eredendően egybérlős, nincs szükség a felosztás egyesítése eszközre, így nincs szükség a séma-információs objektum feltöltésére.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Létrehoztam egy adatbázist, és már van egy szegmenses Map Manager, hogyan regisztrálhatom ezt az új adatbázist szegmensként

[A rugalmas adatbázis ügyféloldali függvénytárának használatával kapcsolatban lásd: szegmens hozzáadása egy alkalmazáshoz](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Mennyibe kerül a rugalmas adatbázis eszközeinek díja

A rugalmas adatbázis-ügyfél függvénytárának használata nem jár semmilyen költséggel. A költségek csak az olyan Azure SQL Database-adatbázisok esetében merülnek fel, amelyeket a szegmensekhez és a szegmenses Térkép kezelőjéhez használ, valamint a felosztott egyesítési eszköz számára kiépített webes és feldolgozói szerepköröket.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Miért nem működnek a hitelesítő adataim, amikor egy másik kiszolgálóról veszek fel egy szegmenst

Ne használja a hitelesítő adatokat a "felhasználói azonosító =username@servername" formában, hanem egyszerűen használja a "felhasználói azonosító = Felhasználónév" értéket.  Győződjön meg arról is, hogy a "username" bejelentkezési engedélyekkel rendelkezik a szegmensen.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Létre kell hozni egy szegmenses Térkép-kezelőt, és fel kell tölteni a szegmenseket minden alkalommal, amikor elkezdem az alkalmazásaim

Nem – a [ShardMapManagerFactory. CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)egy egyszeri művelet, amely létrehozta a szegmenses Térkép-kezelőt.  Az alkalmazásnak a [ShardMapManagerFactory. TryGetSqlShardMapManager ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) hívást kell használnia az alkalmazás indítási ideje alatt.  Alkalmazás-tartományhoz csak egy ilyen hívásnak kell szerepelnie.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Kérdésem van a rugalmas adatbázis-eszközök használatával kapcsolatban, hogyan kaphatok választ

Kérjük, lépjen kapcsolatba velünk a [SQL Database fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Ha horizontális Felskálázási kulccsal szerezem be az adatbázis-csatlakozást, továbbra is lekérheti az ugyanazon a szegmensen lévő többi horizontális Felskálázási kulcsra vonatkozó adatlekérdezést.  Ez a kialakítás

A rugalmasan méretezhető API-k lehetővé teszik a megfelelő adatbázishoz való kapcsolódást a horizontális Felskálázási kulcshoz, de nem biztosítanak horizontális szűrést.  Adja hozzá a lekérdezés **Where** záradékát, hogy szükség esetén korlátozza a hatókört a megadott horizontális kulcsra.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Használhatok egy másik SQL Database-kiadást a saját szegmensem egyes szegmensei esetében

Igen, a szegmens egy önálló adatbázis, így az egyik szegmens lehet prémium kiadás, míg egy másik standard kiadás. Továbbá a szegmensek kiadása a szegmens élettartama során többször is fel-vagy leskálázást.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A felosztott egyesítés eszköz kiépíti (vagy törli) az adatbázist egy megosztott vagy egyesítési művelet során

Nem. A **felosztási** műveletek esetében a célként megadott adatbázisnak léteznie kell a megfelelő sémával, és regisztrálni kell a szegmenses Térkép kezelőjével.  Az **egyesítési** műveletekhez törölnie kell a szegmenst a szegmens Térkép kezelőjéből, majd törölnie kell az adatbázist.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]