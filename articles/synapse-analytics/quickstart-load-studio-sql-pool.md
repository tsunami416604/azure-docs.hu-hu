---
title: 'Rövid útmutató: az adat tömeges betöltése dedikált SQL-készlettel'
description: A szinapszis Studióval tömeges betöltést használhat a dedikált SQL-készletbe az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 312c57c103bf733bc72c5de1d22ab3239d5b5e96
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484663"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Gyors útmutató: tömeges betöltés a szinapszis SQL-sel

Az adatgyűjtés egyszerűen betöltődik a szinapszis Studióban található tömeges betöltés varázslóval. A tömeges betöltés varázsló végigvezeti egy T-SQL-parancsfájl létrehozásán a [másolási utasítással](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) a tömeges betöltési adatokhoz. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Belépési pontok a tömeges betöltés varázslóba

A speciális SQL-készletek használatával egyszerűen tömegesen betöltheti az betöltést a szinapszis Studióban található alábbi területekre kattintva:

- Egy fájl vagy mappa a munkaterülethez csatolt Azure Storage-fiókból, a ![ jobb gombbal egy fájlra vagy mappára kattint a Storage-fiókból](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Előfeltételek

- A varázsló létrehoz egy MÁSOLÁSi utasítást, amely az Azure AD átmenő hitelesítést használja a hitelesítéshez. Az [Azure ad-felhasználónak hozzáféréssel](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) kell rendelkeznie a munkaterülethez legalább a Storage blob-adatközreműködői Azure-szerepkörrel a ADLS Gen2 fiókhoz. 

- A [másolási utasítás használatához](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) és a tábla létrehozásához szükséges engedélyekkel kell rendelkeznie, ha új táblát hoz létre a betöltéshez.

- A ADLS Gen2 fiókhoz társított társított szolgáltatásnak **hozzáféréssel kell rendelkeznie a** / betöltendő fájl **mappához** . Ha például a társított szolgáltatás hitelesítési mechanizmusa felügyelt identitás, a munkaterület felügyelt identitásának legalább Storage blob-olvasó engedéllyel kell rendelkeznie a Storage-fiókban.

- Ha a VNet engedélyezve van a munkaterületen, győződjön meg arról, hogy a forrásadatok és a fájl helyéhez társított ADLS Gen2-fiókhoz társított integrált futtatókörnyezetben engedélyezve van az interaktív létrehozás. Interaktív szerzői műveletek szükségesek az autoschema-észleléshez, a forrásfájl tartalmának megtekintéséhez és a varázslón belüli ADLS Gen2 Storage-fiókok tallózásához.

### <a name="steps"></a>Lépések

1. Válassza ki a Storage-fiókot és a betöltéshez használt fájlt vagy mappát a forrás tárolási hely paneljén. A varázsló automatikusan megkísérli a parketta-fájlok észlelését. Ha a Parquet fájltípust nem lehet megerősíteni, a rendszer alapértelmezés szerint tagolt szöveget (CSV) fog használni.

   ![Forrás helyének kiválasztása](./sql/media/bulk-load/bulk-load-source-location.png)

2. Válassza ki a fájlformátum beállításait, beleértve azt a Storage-fiókot, ahol el szeretné írni az elutasított sorokat (hibakód). Jelenleg csak a CSV-és a parketta-fájlok támogatottak.

    ![A fájlformátum beállításainak kiválasztása](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Az "adatok előnézete" lehetőség kiválasztásával megtekintheti, hogy a MÁSOLÁSi utasítás hogyan elemezi a fájlt, hogy segítsen a fájlformátum beállításainak konfigurálásában. Válassza az "adatok előnézete" lehetőséget, amikor módosít egy fájlformátum-beállítást, hogy megtudja, hogyan elemezze a fájlt a frissített beállítással: ![ adatok megtekintése](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - Az adatmegjelenítés több karakterből álló lezárókkal nem támogatott a tömeges betöltés varázslóban. A tömeges betöltés varázsló egyetlen oszlopon belül megtekinti az adatmegjelenítést, ha meg van adva egy többkarakteres mező lezáró értéke. 
> - A több karakterből álló sorok lezáróinak megadása a COPY utasításban támogatott. Ez azonban nem támogatott a tömeges betöltés varázslóban, ahol hiba történik.

4. Válassza ki azt a dedikált SQL-készletet, amelyet a betöltéshez használ, beleértve azt is, hogy a terhelés egy meglévő táblára vagy új táblára vonatkozik-e: a ![ célhely kiválasztása](./sql/media/bulk-load/bulk-load-target-location.png)

5. Válassza az "oszlop-hozzárendelés konfigurálása" lehetőséget, és győződjön meg arról, hogy rendelkezik a megfelelő oszlop-hozzárendeléssel. Megjegyzés: a rendszer automatikusan észleli az oszlopok neveit, ha engedélyezve van az "oszlopfejlécek következtetése". Az új táblák esetében az oszlop-hozzárendelés konfigurálása kritikus fontosságú a cél oszlop adattípusok frissítéséhez: ![ oszlop-hozzárendelés konfigurálása](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Válassza a "parancsfájl megnyitása" lehetőséget, és egy T-SQL-szkriptet fog generálni a MÁSOLÁSi utasítással, amely betöltődik a adattóban: ![ az SQL-szkript megnyitása](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>További lépések

- A MÁSOLÁSi lehetőségekkel kapcsolatos további információkért olvassa el a [copy utasításról](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) szóló cikket.
- Tekintse át az [adatbetöltések áttekintése című](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) cikket
