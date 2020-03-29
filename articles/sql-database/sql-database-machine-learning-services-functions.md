---
title: Speciális R-függvények írása
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ismerje meg, hogyan írhat R-függvényt a fejlett statisztikai számításhoz az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702452"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)

Ez a cikk azt ismerteti, hogy miként ágyazható be az R matematikai és segédprogram-függvények sql-tárolt eljárásba. A T-SQL-ben megvalósítandó speciális statisztikai függvények r-ben csak egyetlen kódsorgal hajthatók végre.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

- A példakód futtatásához ezekben a gyakorlatokban először rendelkeznie kell egy Azure SQL-adatbázis machine learning-szolgáltatások (R) engedélyezve van. A nyilvános előzetes verzió során a Microsoft befogja önt, és engedélyezi a gépi tanulást a meglévő vagy az új adatbázishoz. Kövesse a [Regisztráció az előnézetre](sql-database-machine-learning-services-overview.md#signup)című részben található lépéseket.

- Ellenőrizze, hogy telepítette-e a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) rendszert. Az R-parancsfájlokat más adatbázis-kezelő vagy lekérdezési eszközökkel is futtathatja, de ebben a rövid útmutatóban az SSMS-t fogja használni.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Tárolt eljárás létrehozása véletlenszerű számok létrehozásához

Az egyszerűség kedvéért használjuk `stats` az R-csomagot, amely alapértelmezés szerint telepítve van és betöltve az Azure SQL Database-lel a Machine Learning Services használatával (előzetes verzió). A csomag több száz funkciót tartalmaz a `rnorm` gyakori statisztikai feladatokhoz, köztük a funkciót. Ez a funkció meghatározott számú véletlen számot hoz létre a normál eloszlás használatával, szórással és értékkel.

Például a következő R kód 100 számot ad vissza átlagosan 50-en, 3-as szórással.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Ha ezt az R-vonalat t-SQL-ből szeretné hívni, futtassa `sp_execute_external_script` és adja hozzá az R függvényt az R parancsfájl paraméterhez, a következőkhöz:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Mi a teendő, ha szeretné, hogy könnyebb létrehozni egy másik sor véletlenszerű számok?

Ez egyszerű, ha együtt SQL. Meg kell adnia egy tárolt eljárást, amely leveszi az argumentumokat a felhasználótól, majd ezeket az argumentumokat változóként továbbítja az R parancsfájlba.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- Az első sor határozza meg az sql bemeneti paramétereket, amelyek szükségesek a tárolt eljárás végrehajtásakor.

- Az R-kód által használt összes változót és a megfelelő SQL-adattípusokat `@params` definiáló sor határozza meg.

- Az azonnal követett sorok lerendelik az SQL paraméterneveket a megfelelő R-változónevekhez.

Most, hogy az R függvényt egy tárolt eljárásba csomagolta, egyszerűen meghívhatja a függvényt, és különböző értékekben adhatja át a funkciót, például:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Hibaelhárításhoz használja az R segédprogram-funkciókat

Az `utils` alapértelmezés szerint telepített csomag számos közüzemi funkciót biztosít az aktuális R-környezet vizsgálatához. Ezek a függvények akkor lehetnek hasznosak, ha eltéréseket talál az R-kód SQL-ben és külső környezetekben történő teljesítményében. Előfordulhat például, hogy `memory.limit()` az R függvénnyel memóriát kap az aktuális R környezethez.

Mivel `utils` a csomag telepítve van, de alapértelmezés `library()` szerint nincs betöltve, először a függvényt kell használnia a betöltéséhez.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Sok felhasználó szeretné használni a rendszer időzítési `system.time` `proc.time`funkcióit az R-ben, például és a , az R folyamatok által használt idő rögzítéséhez és a teljesítményproblémák elemzéséhez.
