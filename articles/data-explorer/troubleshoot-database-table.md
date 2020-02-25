---
title: Nem sikerült adatbázist vagy táblát létrehozni vagy törölni az Azure Adatkezelő
description: Ez a cikk az adatbázisok és táblák Azure Adatkezelőban való létrehozásának és törlésének hibaelhárítási lépéseit ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562394"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Hibaelhárítás: nem sikerült adatbázist vagy táblát létrehozni vagy törölni az Azure-ban Adatkezelő

Az Azure Adatkezelőban rendszeresen együttműködik adatbázisokkal és táblázatokkal. Ez a cikk hibaelhárítási lépéseket tesz elérhetővé a felmerülő problémák esetén.

## <a name="creating-a-database"></a>Adatbázis létrehozása

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Adatbázis létrehozásához az Azure-előfizetéshez tartozó *közreműködő* vagy *tulajdonos* szerepkör tagjának kell lennie. Ha szükséges, működjön együtt az előfizetés rendszergazdájával, hogy hozzá lehessen adni a megfelelő szerepkörhöz.

1. Győződjön meg arról, hogy az adatbázis neve nem rendelkezik a név-ellenőrzési hibákkal. A névnek alfanumerikusnak kell lennie, legfeljebb 260 karakter hosszú lehet.

1. Győződjön meg arról, hogy az adatbázis-megőrzési és a gyorsítótárazási értékek engedélyezett tartományokon belül vannak. Az adatmegőrzési értéknek 1 és 36500 nap között kell lennie (100 év). A gyorsítótárazásnak 1 és a megőrzéshez beállított maximális értéknek kell lennie.

## <a name="deleting-or-renaming-a-database"></a>Adatbázis törlése vagy átnevezése

Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Adatbázis törléséhez vagy átnevezéséhez az Azure-előfizetéshez tartozó *közreműködő* vagy *tulajdonos* szerepkör tagjának kell lennie. Ha szükséges, működjön együtt az előfizetés rendszergazdájával, hogy hozzá lehessen adni a megfelelő szerepkörhöz.

## <a name="creating-a-table"></a>Tábla létrehozása

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Tábla létrehozásához az adatbázis- *rendszergazdai* vagy *adatbázis-felhasználói* szerepkör tagjának kell lennie az adatbázisban vagy az Azure-előfizetés *közreműködői* vagy *tulajdonosi* szerepkörében. Ha szükséges, működjön együtt az előfizetéssel vagy a fürt rendszergazdájával, hogy hozzá lehessen adni a megfelelő szerepkörhöz.

    További információk a jogosultságokról: [Adatbázis-engedélyek kezelése](manage-database-permissions.md).

1. Győződjön meg arról, hogy még nem létezik ugyanilyen nevű tábla. Ha létezik, a következőket teheti: hozzon létre egy másik nevű táblát; a meglévő tábla átnevezése ( *tábla-rendszergazdai* szerepkör szükséges); vagy dobja el a meglévő táblát ( *adatbázis-rendszergazdai* szerepkör szükséges). Használja az alábbi parancsokat.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Tábla törlése vagy átnevezése

Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Egy tábla törléséhez vagy átnevezéséhez az adatbázis- *rendszergazda* vagy a *tábla rendszergazdai* szerepkör tagjának kell lennie. Ha szükséges, működjön együtt az előfizetéssel vagy a fürt rendszergazdájával, hogy hozzá lehessen adni a megfelelő szerepkörhöz.

További információk a jogosultságokról: [Adatbázis-engedélyek kezelése](manage-database-permissions.md).

## <a name="general-guidance"></a>Általános útmutatás

1. Ellenőrizze az [Azure Service Health irányítópultját](https://azure.microsoft.com/status/). Keresse meg az Azure-Adatkezelő állapotát abban a régióban, ahol az adatbázist vagy a táblázatot kívánja dolgozni.

    Ha az állapot nem **jó** (zöld pipa), próbálkozzon újra az állapot javulása után.

1. Ha továbbra is segítségre van szüksége a probléma megoldásához, nyisson meg egy támogatási kérést a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>További lépések

[A fürt állapotának megtekintése](check-cluster-health.md)