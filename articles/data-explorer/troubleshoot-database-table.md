---
title: Hozzon létre vagy adatbázis vagy az Adatkezelőben az Azure tábla törlése
description: Ez a cikk létrehozásához és törléséhez, adatbázisok és táblák az Azure Data Explorer hibaelhárítási lépéseket ismerteti.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0bd9f5ed00193aa9f872287b6afaf9e2b28c24dc
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188339"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Hibaelhárítás: Hozzon létre vagy adatbázis vagy az Adatkezelőben az Azure tábla törlése

Az Adatkezelőben az Azure rendszeresen használható adatbázisok és táblák. A cikk ismerteti a hibaelhárítási lépéseket a problémákra, amelyek is kapja meg.

## <a name="creating-a-database"></a>Adatbázis létrehozása

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Hozzon létre egy adatbázist, tagjának kell lennie a *közreműködői* vagy *tulajdonosa* szerepkör esetében az Azure-előfizetést. Ha szükséges, ahol az előfizetés-rendszergazdától, azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy nincsenek-e az adatbázis nevét neve érvényesítési hibák. A név alfanumerikus karakterek, és a egy legfeljebb 260 karakter hosszúságú lehet.

1. Ellenőrizze, hogy az adatbázis megőrzési és gyorsítótárazás értékek megengedett tartományba. Adatmegőrzési 1 és 36500 nap (100 éves) között kell lennie. Gyorsítótárazás 1 és a megőrzési beállítva maximális érték között kell lennie.

## <a name="deleting-or-renaming-a-database"></a>Törlése és a egy adatbázis átnevezése

Győződjön meg arról, hogy a megfelelő engedélyekkel. Törlése és a egy adatbázis átnevezése, tagjának kell lennie a *közreműködői* vagy *tulajdonosa* szerepkör esetében az Azure-előfizetést. Ha szükséges, ahol az előfizetés-rendszergazdától, azokat is hozzáadhatja a megfelelő szerepkört.

## <a name="creating-a-table"></a>Tábla létrehozása

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Hozzon létre egy táblát, tagjának kell lennie a *adatbázis rendszergazdai* vagy *adatbázis-felhasználót* szerepkör az adatbázisban vagy a *közreműködői* vagy *tulajdonosa* az Azure-előfizetés szerepkör. Szükség esetén az előfizetés használata, vagy a fürt rendszergazdai így azokat is hozzáadhatja a megfelelő szerepkört.

    Engedélyekkel kapcsolatos további információkért lásd: [adatbázis-engedélyek kezeléséhez](manage-database-permissions.md).

1. Győződjön meg arról, hogy egy azonos nevű tábla nem létezik. Ha létezik, majd a következőket teheti: Hozzon létre egy táblát egy másik nevet; a meglévő táblázat átnevezése (igényel *tábla rendszergazdai* szerepkör); vagy dobja el a meglévő táblázat (igényel *adatbázis rendszergazdai* szerepkör). Használja a következő parancsokat.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Törlése, illetve egy táblázat átnevezése

Győződjön meg arról, hogy a megfelelő engedélyekkel. Törlése és a egy tábla átnevezése, tagjának kell lennie a *adatbázis rendszergazdai* vagy *tábla rendszergazdai* szerepkör az adatbázisban. Szükség esetén az előfizetés használata, vagy a fürt rendszergazdai így azokat is hozzáadhatja a megfelelő szerepkört.

Engedélyekkel kapcsolatos további információkért lásd: [adatbázis-engedélyek kezeléséhez](manage-database-permissions.md).

## <a name="general-guidance"></a>Általános útmutatás

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/). Keresse meg az Azure Data Explorer a régióban, ahol szeretne dolgozni, adatbázis vagy táblázat állapotát.

    Ha az állapot nem **jó** (zöld pipa), az állapot javítja után próbálja újra.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>További lépések

[Fürt állapotának ellenőrzése](check-cluster-health.md)