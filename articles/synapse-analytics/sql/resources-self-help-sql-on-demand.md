---
title: SQL on-demand Előzetes) önsegítő
description: Ez a szakasz olyan információkat tartalmaz, amelyek segíthetnek az SQL on-demand (előzetes verzió) hibáinak elhárításában.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424831"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Önkiszolgáló segítség az IGÉNY szerinti SQL-hez (előzetes verzió)

Ez a cikk az Azure Synapse Analytics sql-verziójával (előzetes verzióval) kapcsolatos leggyakoribb problémák elhárításáról tartalmaz információkat.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>Sql on-demand szürkén jelenik meg a Synapse Studio

Ha a Synapse Studio nem tud kapcsolatot létesíteni az SQL-nel igény szerint, észre fogja venni, hogy az SQL igény szerinti szürkén jelenik meg, vagy az "Offline" állapotot jeleníti meg. Ez a probléma általában akkor fordul elő, ha az alábbi esetek valamelyike fordul elő:

1) A hálózat megakadályozza az Azure Synapse háttérrendszerével való kommunikációt. A leggyakoribb eset az, hogy az 1443-as port le van tiltva. Ahhoz, hogy az SQL igény szerint működjön a port blokkolásának feloldása. Más problémák megakadályozhatják, hogy az SQL igény szerint is működjön, [további információkért látogasson el a teljes hibaelhárítási útmutatóba.](../troubleshoot/troubleshoot-synapse-studio.md)
2) Nincs engedélye az SQL-be való igény szerinti bejelentkezéshez. Eléréséhez az Azure Synapse munkaterület-rendszergazdák egyikének hozzá kell adnia a munkaterület-rendszergazdai vagy SQL-rendszergazdai szerepkörhöz. [További információkért látogasson el a hozzáférés-vezérlésteljes útmutatójába.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>A lekérdezés sikertelen, mert a fájl nem nyitható meg

Ha a lekérdezés sikertelen, a "Fájl nem nyitható meg, mert nem létezik, vagy egy másik folyamat használja", és biztos benne, hogy mindkét fájl létezik, és nem használja egy másik folyamat, az azt jelenti, hogy az SQL nem tud hozzáférni a fájlhoz. Ez a probléma általában azért fordul elő, mert az Azure Active Directory-identitás nem rendelkezik a fájl eléréséhez való jogosultsággal. Alapértelmezés szerint az SQL igény szerinti próbál hozzáférni a fájlhoz az Azure Active Directory-identitás használatával. A probléma megoldásához megfelelő jogosultsággal kell rendelkeznie a fájl eléréséhez. Legegyszerűbb módja az, hogy megadja magát a "Storage Blob Data Contributor" szerepkört a lekérdezni kívánt tárfiókban. [További információkért látogasson el az Azure Active Directory hozzáférés-vezérlésteljes útmutatójába a tároláshoz.](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A lekérdezés sikertelen, mert az aktuális erőforrás-megkötések miatt nem hajtható végre 

Ha a lekérdezés sikertelen az "Ez a lekérdezés nem hajtható végre az aktuális erőforrás-megkötések miatt" hibaüzenettel, az azt jelenti, hogy az SQL OD jelenleg nem tudja végrehajtani az erőforrás-megkötések miatt: 

- Kérjük, győződjön meg arról, hogy ésszerű méretű adattípusokat használ. Adja meg a Parquet fájlok sémáját is a karakterláncoszlopokhoz, mivel alapértelmezés szerint VARCHAR(8000) lesznek. 

- Ha a lekérdezés CSV-fájlokat céloz meg, fontolja meg [a statisztikák létrehozását.](develop-tables-statistics.md#statistics-in-sql-on-demand-preview) 

- A lekérdezés optimalizálásához keresse fel [az SQL igény szerinti teljesítménygyakorlati tanácsait.](best-practices-sql-on-demand.md)  

## <a name="next-steps"></a>További lépések

Az SQL igény szerinti használatáról az alábbi cikkekben olvashat bővebben:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [Parketta beágyazott típusai lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)

- [Külső táblák létrehozása és használata](create-use-external-tables.md)

- [Lekérdezési eredmények tárolása a tárolóba](create-external-table-as-select.md)
