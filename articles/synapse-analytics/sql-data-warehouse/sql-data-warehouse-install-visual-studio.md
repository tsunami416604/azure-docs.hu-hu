---
title: A Visual Studio 2019 telepítése
description: A Visual Studio és a SQL Server fejlesztői eszközök (SSDT) telepítése a szinapszis SQL-hez
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 073efd41c754b0d28e0bdab712f692f100d3316e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289514"
---
# <a name="getting-started-with-visual-studio-2019"></a>Bevezetés a Visual Studio 2019 használatába

A Visual Studio **2019** SQL Server Data Tools (SSDT) egyetlen eszköz, amely lehetővé teszi a következőket:

- Alkalmazások összekötése, lekérdezése és fejlesztése
- Az Object Explorer használatával vizuálisan feltárhatja az adatmodellben lévő összes objektumot, beleértve a táblákat, a nézeteket, a tárolt eljárásokat és az egyéb műveleteket.
- T-SQL adatdefiníciós nyelvi (DDL) parancsfájlok létrehozása az objektumokhoz
- Az adattárház fejlesztése SSDT adatbázis-projektekkel rendelkező, állapot-alapú megközelítés használatával
- Az adatbázis-projekt integrálása verziókövetés-rendszerekkel, mint például a git és az Azure Repos
- Folyamatos integrációs és üzembe helyezési folyamatok beállítása automatizálási kiszolgálókkal, például Azure DevOps

## <a name="install-visual-studio-2019"></a>A Visual Studio 2019 telepítése

A Visual Studio **16,3 vagy újabb**verziójának letöltéséhez és telepítéséhez tekintse meg a [Visual Studio 2019 letöltését](https://visualstudio.microsoft.com/downloads/) ismertető témakört. A telepítés során válassza ki az adattárolási és-feldolgozási munkaterhelést. A Visual Studio 2019-es verziójában már nem szükséges a különálló SSDT telepítése.

## <a name="unsupported-features-in-ssdt"></a>A SSDT nem támogatott funkciói

Előfordulhat, hogy a szinapszis SQL-hez tartozó szolgáltatás kiadásai nem tartalmazzák a SSDT támogatását. A következő szolgáltatások jelenleg nem támogatottak:


- [Munkaterhelés-kezelés](sql-data-warehouse-workload-management.md) – munkaterhelési csoportok és osztályozók
- [Sor szintű biztonság](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (beleértve a táblázat értékű függvényeket)
  - [Támogatási jegy vagy szavazás](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) beküldése a támogatott funkció beszerzéséhez.
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - [Támogatási jegy vagy szavazás](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) beküldése a támogatott funkció beszerzéséhez.
- [Azonosító oszlopot](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15) tartalmazó táblák

## <a name="next-steps"></a>Következő lépések

Most, hogy már rendelkezik a SSDT legújabb verziójával, készen áll az SQL-készlethez való [kapcsolódásra](sql-data-warehouse-query-visual-studio.md) .
