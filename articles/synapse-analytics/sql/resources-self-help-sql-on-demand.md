---
title: SQL igény szerinti előzetes verzió) önkiszolgáló Súgó
description: Ez a szakasz olyan információkat tartalmaz, amelyek segítséget nyújtanak az SQL on-demand (előzetes verzió) problémáinak elhárításában.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424831"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Önkiszolgáló Súgó az SQL igény szerinti használatra (előzetes verzió)

Ez a cikk azt ismerteti, hogyan lehet az Azure szinapszis Analytics szolgáltatásban az SQL on-demand (előzetes verzió) leggyakoribb problémáinak elhárítására.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>Az SQL on-demand szürkén jelenik meg a szinapszis Studióban

Ha a szinapszis Studio nem tud kapcsolatot létesíteni az SQL-on igény szerinti kapcsolattal, megfigyelheti, hogy az SQL on-demand szürkén jelenik meg, vagy a "kapcsolat nélküli" állapotot jeleníti meg. Ez a probléma általában akkor fordul elő, ha a következő esetek valamelyike történik:

1) A hálózat megakadályozza az Azure szinapszis-háttérrel való kommunikációt. A leggyakoribb eset az, hogy a 1443-es port le van tiltva. Az igény szerinti SQL-szolgáltatás letiltásának feloldásához a portot. Más problémák miatt előfordulhat, hogy az SQL on-demand is működik. [További információért látogasson el a teljes hibaelhárítási útmutatóba](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nincs engedélye az SQL igény szerinti bejelentkezni. A hozzáféréshez az Azure szinapszis-munkaterület rendszergazdáinak egyike adja hozzá a munkaterület-rendszergazdához vagy az SQL-rendszergazdai szerepkörhöz. [További információkért tekintse meg a hozzáférés-vezérlés teljes útmutatóját](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>A lekérdezés sikertelen, mert a fájl nem nyitható meg

Ha a lekérdezés sikertelen a "fájl nem nyitható meg, mert nem létezik, vagy egy másik folyamat használja", és biztos benne, hogy mindkét fájl létezik, és egy másik folyamat nem használja azt, azt jelenti, hogy az SQL igény szerint nem fér hozzá a fájlhoz. Ez a probléma általában azért fordul elő, mert a Azure Active Directory identitása nem rendelkezik jogosultsággal a fájl eléréséhez. Alapértelmezés szerint az SQL on-demand a Azure Active Directory identitás használatával próbál hozzáférni a fájlhoz. A probléma megoldásához megfelelő jogosultságokkal kell rendelkeznie a fájl eléréséhez. A legegyszerűbb módszer, ha a lekérdezni kívánt Storage-fiókban megadják a Storage blob adatközreműködői szerepkört. [További információkért tekintse meg a Azure Active Directory hozzáférés-vezérlésének teljes útmutatóját](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A lekérdezés sikertelen, mert a jelenlegi erőforrás-megkötések miatt nem hajtható végre. 

Ha a lekérdezés meghiúsul a következő hibaüzenettel: "Ez a lekérdezés nem hajtható végre az aktuális erőforrás-korlátozások miatt", az azt jelenti, hogy az SQL OD nem tudja végrehajtani az adott pillanatban az erőforrás-korlátozások miatt: 

- Győződjön meg arról, hogy megfelelő méretű adattípusok vannak használatban. Emellett a karakterlánc-oszlopokhoz tartozó Parquet-fájlok sémáját is megadhatja, mivel alapértelmezés szerint VARCHAR (8000) lesz. 

- Ha a lekérdezés CSV-fájlokat céloz meg, érdemes lehet [statisztikai adatokat létrehoznia](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- A lekérdezés optimalizálásához tekintse meg [az SQL igény szerinti teljesítményére vonatkozó ajánlott eljárásokat](best-practices-sql-on-demand.md) .  

## <a name="next-steps"></a>További lépések

Az SQL igény szerinti használatáról az alábbi cikkekben olvashat bővebben:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)

- [Külső táblák létrehozása és használata](create-use-external-tables.md)

- [Lekérdezés eredményeinek tárolása a tárterületen](create-external-table-as-select.md)
