---
title: Nem sikerült létrehozni vagy törölni az adatbázist vagy a táblát az Azure Data Explorerben
description: Ez a cikk az Azure Data Explorer ben létrehozott és törlési lépések hibaelhárítási lépéseit ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562394"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Hibaelhárítás: Adatbázis vagy tábla létrehozása vagy törlése az Azure Data Explorerben

Az Azure Data Explorerben rendszeresen dolgozik adatbázisokkal és táblákkal. Ez a cikk a felmerülő problémák hibaelhárítási lépéseit ismerteti.

## <a name="creating-a-database"></a>Adatbázis létrehozása

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Adatbázis létrehozásához az Azure-előfizetés *közreműködői* vagy *tulajdonosi* szerepkörének tagjának kell lennie. Szükség esetén működjön együtt az előfizetés rendszergazdájával, hogy hozzávehessenek a megfelelő szerepkörhöz.

1. Győződjön meg arról, hogy nincsenek névérvényesítési hibák az adatbázis nevéhez. A névnek alfanumerikusnak kell lennie, legfeljebb 260 karakter hosszú.

1. Győződjön meg arról, hogy az adatbázis-megőrzési és gyorsítótárazási értékek a megengedett tartományokon belül vannak. A megőrzésnek 1 és 36500 nap között kell lennie (100 év). A gyorsítótárazásnak 1 és a megtartáshoz beállított maximális érték között kell lennie.

## <a name="deleting-or-renaming-a-database"></a>Adatbázis törlése vagy átnevezése

Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Adatbázis törléséhez vagy átnevezéséhez az Azure-előfizetés *közreműködői* vagy *tulajdonosi* szerepkörének tagjának kell lennie. Szükség esetén működjön együtt az előfizetés rendszergazdájával, hogy hozzávehessenek a megfelelő szerepkörhöz.

## <a name="creating-a-table"></a>Táblázat létrehozása

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Tábla létrehozásához az *adatbázis-rendszergazdai* vagy *adatbázis-felhasználói* szerepkör tagjának kell lennie az adatbázisban, vagy az Azure-előfizetés *közreműködői* vagy *tulajdonosi* szerepkörének. Ha szükséges, működjön együtt az előfizetés vagy a fürt rendszergazdájával, hogy hozzávehessenek a megfelelő szerepkörhöz.

    További információk a jogosultságokról: [Adatbázis-engedélyek kezelése](manage-database-permissions.md).

1. Győződjön meg arról, hogy egy ilyen nevű tábla még nem létezik. Ha létezik, akkor a következő: Hozzon létre egy másik nevű táblát; nevezze át a meglévő táblát *(táblafelügyeleti* szerepkörszükséges); vagy dobja el a meglévő táblát *(adatbázis-felügyeleti* szerepkörszükséges). Használja a következő parancsokat.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Tábla törlése vagy átnevezése

Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Tábla törléséhez vagy átnevezéséhez az *adatbázis-rendszergazdai* vagy *táblafelügyeleti* szerepkör tagjának kell lennie az adatbázisban. Ha szükséges, működjön együtt az előfizetés vagy a fürt rendszergazdájával, hogy hozzávehessenek a megfelelő szerepkörhöz.

További információk a jogosultságokról: [Adatbázis-engedélyek kezelése](manage-database-permissions.md).

## <a name="general-guidance"></a>Általános útmutatás

1. Ellenőrizze az [Azure Service Health irányítópultját](https://azure.microsoft.com/status/). Keresse meg az Azure Data Explorer állapotát abban a régióban, ahol egy adatbázissal vagy táblával próbál dolgozni.

    Ha az állapot nem **jó** (zöld pipa), próbálkozzon újra, miután az állapot javult.

1. Ha továbbra is segítségre van szüksége a probléma megoldásához, nyisson meg egy támogatási kérelmet az [Azure Portalon.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="next-steps"></a>További lépések

[Fürt állapotának ellenőrzése](check-cluster-health.md)