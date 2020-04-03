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
ms.openlocfilehash: aa2f903611f52f8f7a8ede0040b592a7dddd0e89
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584466"
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

- [Materializált nézetek](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (folyamatban)
- [Rendezett fürtözött oszlopcentrikus indexek](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (folyamatban)
- [COPY utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (folyamatban)
- [Munkaterhelés-kezelés](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) – munkaterhelés-csoportok és osztályozók (folyamatban)
- [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Küldjön be egy támogatási jegyet, vagy szavazzon [itt,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) hogy a funkció támogatott.
- [Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Küldjön be egy támogatási jegyet, vagy szavazzon [itt,](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) hogy a funkció támogatott. 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) függvény 
- [A megkötésekkel rendelkező táblák](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) nem támogatottak. Ezeknél a táblázatobjektumokhoz állítsa a buildműveletet "Nincs" beállításra.

## <a name="next-steps"></a>További lépések

Most, hogy az SSDT legújabb verzióját használja, készen áll az SQL-készlethez [való csatlakozásra.](sql-data-warehouse-query-visual-studio.md)




<!--Other-->

[A Visual Studio 2019 letöltése]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
