---
title: Az Azure SQL rugalmas bővítést – gyakori kérdések |} Microsoft Docs
description: Gyakori kérdések az Azure SQL Database rugalmas méretezést.
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 94ae9549bb5e09c80703a7db316675bff1272372
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647476"
---
# <a name="elastic-database-tools-faq"></a>Rugalmas adatbáziseszközöket – gyakori kérdések
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Ha egyetlen-bérlő minden shard, és nem horizontális kulcs, hogyan tegye I feltöltése a séma adatait a horizontális kulcsát?
A séma objektum van csak egyesítési forgatókönyvek felosztásához használt. Ha egy alkalmazás eleve egyetlen bérlői, nem szükséges a felosztás egyesítőeszköz, és így nincs szükség a séma objektum feltöltéséhez.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Szeretnék már kiépített egy adatbázis és a Shard térkép kezelő már rendelkezik, hogyan regisztrálhatom ezt az új adatbázist, a shard?
Ellenőrizze a  **[egy shard ad hozzá a rugalmas adatbázis ügyféloldali kódtár használó alkalmazások](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Milyen mértékű költségeket a rugalmas adatbáziseszközöket?
A rugalmas adatbázis ügyféloldali kódtár használata nem jár költségeket. Költségek keletkeznek, csak a szilánkok és a Shard térkép Manager használó Azure SQL-adatbázisok, valamint az osztott egyesítése eszköz kiépítése webes/munkavégző szerepkörök.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Miért a hitelesítő adataimat nem működnek a shard egy másik kiszolgálóról való hozzáadásakor?
Ne használja a hitelesítő adatok formájában "felhasználói azonosító =username@servername", ehelyett egyszerűen használja "Felhasználóazonosító felhasználónév =".  Arra is ügyeljen, hogy a "felhasználónév" bejelentkezés engedélyekkel rendelkezzen a szilánkcímtárban.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Van egy Shard térkép Manager létrehozása és feltöltése szilánkok, az alkalmazás minden indításakor?
Nem – a Shard térkép Manager létrehozása (például  **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) műveletet egyszer kell elvégezni.  Az alkalmazást kell használnia a hívás **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** alkalmazás indítási időpontban.  Nem kell ilyen alkalmazás tartományonként csak egyszer hívható.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>A rugalmas adatbázis-eszközökkel kapcsolatos kérdéseket van, hogyan állíthatók válaszolni?
Lépjen kapcsolatba velünk a a [Azure SQL Database fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Amikor egy adatbázis-kapcsolatot egy horizontális skálázási kulccsal, Igen továbbra is ugyanazt a shard a más horizontális kulcs adatait.  Ez szándékosan van?
A rugalmas, méretezhető API-k adjon meg a megfelelő adatbázishoz csatlakozni a horizontális kulcs, de nem ad meg horizontális kulcs szűrést.  Adja hozzá **ahol** záradékok lehet hatókörének korlátozása a megadott horizontális skálázási kulcsot, ha szükséges a lekérdezéshez.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Használható egy másik Azure-adatbázis-kiadás az egyes shard a shard készlet?
Igen, a shard egyedi adatbázis, és így egy shard lehet egy Premium edition míg egy másik Standard kiadását. További a shard kiadása méretezheti felfelé vagy lefelé a shard élettartama során több alkalommal.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A felosztott egyesítési eszköz kiépítése nem (vagy törlése) egy adatbázist egy megosztott vagy egyesítési művelet során?
Nem. A **vágási** műveletek, a céladatbázis léteznie kell a megfelelő sémáját, és a Shard térkép Manager regisztrálni kell.  A **egyesítési** műveletek, törölnie kell a shard a shard térkép kezelőjéből, majd ezután törölje az adatbázist.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

