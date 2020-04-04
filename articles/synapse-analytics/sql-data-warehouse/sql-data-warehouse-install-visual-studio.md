---
title: A Visual Studio 2019 telepítése
description: A Visual Studio és az SQL Server Development Tools (SSDT) telepítése a Synapse SQL-hez
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655917"
---
# <a name="getting-started-with-visual-studio-2019"></a>A Visual Studio 2019 – első lépések

A Visual Studio **2019** SQL Server Data Tools (SSDT) egyetlen olyan eszköz, amely lehetővé teszi a következőket:

- Alkalmazások csatlakoztatása, lekérdezése és fejlesztése
- Használja ki az objektumkezelőt, hogy vizuálisan feltárja az adatmodell összes objektumát, beleértve a táblákat, nézeteket, tárolt eljárásokat stb.
- T-SQL adatdefiníciós nyelv (DDL) parancsfájlok létrehozása az objektumokhoz
- Az adattárház fejlesztése állapotalapú megközelítéssel az SSDT adatbázis-projektekkel
- Integrálja az adatbázis-projektet a forrásvezérlő rendszerekkel, például a Git-tel az Azure Repos-okkal
- Folyamatos integrációs és üzembe helyezési folyamatok beállítása automatizálási kiszolgálókkal, például az Azure DevOps-szal

## <a name="install-visual-studio-2019"></a>A Visual Studio 2019 telepítése

Lásd: [Visual Studio 2019 letöltése][] és telepítése a Visual Studio **16.3-as vagy újabb fájlhoz.** A telepítés során válassza ki az adattárolási és feldolgozási munkaterhelést. A Visual Studio 2019-ben már nincs szükség önálló SSDT-telepítésre.

## <a name="unsupported-features-in-ssdt"></a>Az SSDT nem támogatott szolgáltatásai

Vannak esetek, amikor a synapse SQL szolgáltatáskiadásai nem tartalmazzák az SSDT támogatását. A következő szolgáltatások jelenleg nem támogatottak:

- [Materializált nézetek](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Rendezett fürtözött oszlopcentrikus indexek](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [COPY utasítás](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Munkaterhelés-kezelés](sql-data-warehouse-workload-management.md) – munkaterhelés-csoportok és osztályozók
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Küldjön be egy támogatási jegyet, vagy szavazzon [itt,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) hogy a funkció támogatott.
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - Küldjön be egy támogatási jegyet, vagy szavazzon [itt,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) hogy a funkció támogatott.
- [A megkötésekkel rendelkező táblák](sql-data-warehouse-table-constraints.md#table-constraints) nem támogatottak. Ezeknél a táblázatobjektumokhoz állítsa a buildműveletet "Nincs" beállításra.

## <a name="next-steps"></a>További lépések

Most, hogy az SSDT legújabb verzióját használja, készen áll az SQL-készlethez [való csatlakozásra.](sql-data-warehouse-query-visual-studio.md)

<!--Other-->

[A Visual Studio 2019 letöltése]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
