---
title: 'Rövid útmutató: tömeges betöltési adatmennyiség (szinapszis SQ)'
description: A szinapszis Studio használata tömeges betöltésre a szinapszis SQL-ben
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7f840d4a75be72a4317860dcf4a6708642369a24
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274220"
---
# <a name="bulk-loading-with-synapse-sql"></a>Tömeges betöltés a szinapszis SQL-sel

Az adatbetöltések soha nem voltak egyszerűbbek a szinapszis Studióban a tömeges betöltés varázsló használatakor. A varázsló végigvezeti egy T-SQL-parancsfájl létrehozásán a [másolási utasítással](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) a tömeges betöltési adatokhoz. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Belépési pontok a tömeges betöltés varázslóba

Mostantól egyszerűen, a szinapszis Studióban található alábbi területekre kattintva könnyedén tömegesen betöltheti az adatmennyiséget az SQL-készletek használatával:

- Egy fájl vagy mappa a munkaterülethez csatolt Azure Storage-fiókból, a ![ jobb gombbal egy fájlra vagy mappára kattint a Storage-fiókból](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Előfeltételek

- A varázsló létrehoz egy MÁSOLÁSi utasítást, amely a HRE átmenő authentcation használja. A [HRE-felhasználónak hozzáféréssel](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples#d-azure-active-directory-authentication-aad) kell rendelkeznie a munkaterülethez legalább a Storage blob adatközreműködői RBAC szerepkörrel a ADLS Gen2 fiókhoz.

- A [másolási utasítás használatához](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#permissions) és a tábla létrehozásához szükséges engedélyekkel kell rendelkeznie, ha új táblát hoz létre a betöltéshez.

- A ADLS Gen2 fiókhoz társított társított szolgáltatásnak **hozzáféréssel kell rendelkeznie a** / betöltendő fájl**mappához** . Ha például a társított szolgáltatás hitelesítési mechanizmusa felügyelt identitás, a munkaterület felügyelt identitásának legalább Storage blob-olvasó engedéllyel kell rendelkeznie a Storage-fiókban.

- Ha a VNet engedélyezve van a munkaterületen, győződjön meg arról, hogy a forrásadatok és a fájl helyéhez társított ADLS Gen2-fiókhoz társított integrált futtatókörnyezetben engedélyezve van az interaktív létrehozás. Interaktív szerzői műveletek szükségesek az automatikus sémák észleléséhez, a forrásfájl tartalmának megtekintéséhez és a varázslón belüli ADLS Gen2 Storage-fiókok tallózásához.

### <a name="steps"></a>Lépések

1. Válassza ki a Storage-fiókot és a fájl vagy mappa betöltését a forrás tárolási hely paneljén: a ![ forrás helyének kiválasztása](./sql/media/bulk-load/bulk-load-source-location.png)

2. Válassza ki a fájlformátum beállításait, beleértve azt a Storage-fiókot, ahol el szeretné írni az elutasított sorokat (hibakód). Jelenleg csak a CSV-és a parketta-fájlok támogatottak.

    ![A fájlformátum beállításainak kiválasztása](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Az "előzetes adatok" gombra kattintva megtekintheti, hogy a MÁSOLÁSi utasítás hogyan elemezi a fájlt, hogy segítsen a fájlformátum beállításainak konfigurálásában. Kattintson az "adatok előnézete" gombra minden egyes fájlformátum beállításnál, hogy megtudja, hogyan elemezze a fájlt a frissített beállítással: ![ adatok megtekintése](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Válassza ki a betölteni kívánt SQL-készletet, beleértve azt is, hogy a terhelés egy meglévő táblára vagy új táblára vonatkozik-e: a ![ célhely kiválasztása](./sql/media/bulk-load/bulk-load-target-location.png)

5. Kattintson az "oszlop-hozzárendelés konfigurálása" elemre, és győződjön meg arról, hogy rendelkezik a megfelelő oszlop-hozzárendeléssel. Az új táblák esetében az oszlop-hozzárendelés konfigurálása kritikus fontosságú a cél oszlop adattípusok frissítéséhez: ![ oszlop-hozzárendelés konfigurálása](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Kattintson a "parancsfájl megnyitása" elemre, és a MÁSOLÁSi utasítással hozza létre a T-SQL-szkriptet a következővel: ![ az SQL-szkript megnyitása](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Következő lépések

- A MÁSOLÁSi lehetőségekkel kapcsolatos további információkért olvassa el a [copy utasításról](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) szóló cikket.
- Tekintse át az [adatbetöltések áttekintése című](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) cikket
