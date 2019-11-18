---
title: A Visual Studio 2019 telepítése
description: A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 11/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 758c9b323e78e7e86312555f5fabdcc9c4c28330
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123728"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Első lépések a Visual Studio 2019 for SQL Data Warehouse
A Visual Studio **2019** SQL Server Data Tools (SSDT) egyetlen eszköz, amely lehetővé teszi a következőket:

- Alkalmazások összekötése, lekérdezése és fejlesztése SQL Data Warehouse 
- Az Object Explorer használatával vizuálisan feltárhatja az adatmodellben lévő összes objektumot, beleértve a táblákat, a nézeteket, a tárolt eljárásokat és az egyéb műveleteket.
- T-SQL adatdefiníciós nyelvi (DDL) parancsfájlok létrehozása az objektumokhoz
- Az adattárház fejlesztése SSDT adatbázis-projektekkel rendelkező, állapot-alapú megközelítés használatával
- Az adatbázis-projekt integrálása verziókövetés-rendszerekkel, mint például a git és az Azure Repos
- Folyamatos integrációs és üzembe helyezési folyamatok beállítása automatizálási kiszolgálókkal, például Azure DevOps

## <a name="install-visual-studio-2019"></a>A Visual Studio 2019 telepítése
A Visual Studio **16,3 vagy újabb**verziójának letöltéséhez és telepítéséhez tekintse meg a [A Visual Studio 2019 letöltése][] ismertető témakört. A telepítés során válassza ki az adattárolási és-feldolgozási munkaterhelést. A Visual Studio 2019-es verziójában már nem szükséges a különálló SSDT telepítése.

## <a name="unsupported-features-in-ssdt"></a>A SSDT nem támogatott funkciói

Előfordulhat, hogy a SQL Data Warehouse funkcióinak kiadásai nem tartalmazzák a SSDT támogatását. A következő szolgáltatások jelenleg nem támogatottak:

- [Anyagelszámolású nézetek](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (folyamatban)
- [Rendezett fürtözött Oszlopcentrikus indexek](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (folyamatban)
- [Másolási utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (folyamatban)
- [Munkaterhelés-kezelés](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) – munkaterhelési csoportok és osztályozók (folyamatban)
- [Sor szintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Küldjön be egy támogatási jegyet, vagy szavazzon [ide](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) a funkció támogatásának megszerzéséhez.
- [Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Küldjön be egy támogatási jegyet, vagy szavazzon [ide](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) a funkció támogatásának megszerzéséhez. 
- [Előrejelzési](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) függvény 

## <a name="next-steps"></a>További lépések

Most, hogy a SSDT legújabb verzióját használja, készen áll a SQL Data Warehouse való [kapcsolódásra][connect] .

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[A Visual Studio 2019 letöltése]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
