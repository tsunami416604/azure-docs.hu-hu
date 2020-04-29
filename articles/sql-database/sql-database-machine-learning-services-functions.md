---
title: Speciális R-függvények írása
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Megtudhatja, hogyan írhat R-függvényt speciális statisztikai számításokhoz a Azure SQL Database Machine Learning Services (előzetes verzió) használatával.
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
ROBOTS: NOINDEX
ms.openlocfilehash: ba78267b1c6dc8f0e1bd25bb8ecdb1d8d344d03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453114"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)

Ez a cikk bemutatja, hogyan ágyazhat be R matematikai és segédprogram-függvényeket egy SQL tárolt eljárásba. A T-SQL használatával bonyolult, összetett statisztikai függvények az R-ben csak egyetlen sor kóddal hajthatók végre.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/) .

- A példában szereplő programkód futtatásához először Azure SQL Databasenek kell lennie, [Machine learning Services (R)](sql-database-machine-learning-services-overview.md) engedélyezve van.

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Az R-szkripteket más adatbázis-kezelő vagy lekérdezési eszközök használatával is futtathatja, de ebben a rövid útmutatóban a SSMS-t fogja használni.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Tárolt eljárás létrehozása véletlenszerű számok létrehozásához

Az egyszerűség kedvéért használja a Machine Learning Services (előzetes `stats` verzió) használatával a Azure SQL Database alapértelmezés szerint telepített és betöltött R-csomagot. A csomag több száz funkciót tartalmaz a közös statisztikai feladatokhoz, köztük `rnorm` a függvényhez. Ez a függvény meghatározott számú véletlenszerű számot hoz létre a normál eloszlással, a szórás és az azt jelenti.

Például a következő R-kód a 100-es számot adja vissza a 50-es átlagban, a 3. szórástól függően.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Ha a T-SQL-ből szeretné hívni az R- `sp_execute_external_script` vonalat, futtassa a parancsot, és adja hozzá az r-függvényt az r script paraméterben, például:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Mi a teendő, ha egyszerűbbé szeretné tenni a véletlenszerű számok különböző készletének létrehozását?

Ez egyszerűen kombinálható SQL-sel. Meghatároz egy tárolt eljárást, amely beolvassa a felhasználó argumentumait, majd átadja ezeket az argumentumokat az R-parancsfájlnak változóként.

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

- Az első sor határozza meg a tárolt eljárás végrehajtásakor szükséges SQL-bemeneti paramétereket.

- A kezdetű sor `@params` meghatározza az R-kód által használt összes változót, valamint a megfelelő SQL-adattípusokat.

- Azok a sorok, amelyek azonnal követik az SQL-paraméterek nevét a megfelelő R-változók neveire.

Most, hogy becsomagolta az R-függvényt egy tárolt eljárásban, könnyedén hívhatja a függvényt, és különböző értékeket adhat át, például a következőt:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Az R Utility functions használata a hibaelhárításhoz

Az `utils` alapértelmezés szerint telepített csomag számos segédprogram-függvényt biztosít az aktuális R-környezet kivizsgálása érdekében. Ezek a függvények akkor lehetnek hasznosak, ha eltéréseket talál az R-kód SQL-ben és külső környezetekben történő végrehajtása során. Használhatja például az R `memory.limit()` függvényt az aktuális r-környezet memóriájának lekéréséhez.

Mivel a `utils` csomag telepítve van, de alapértelmezés szerint nincs betöltve, a `library()` függvényt kell használnia az első betöltéséhez.

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
> Számos felhasználó szeretné használni az r-folyamatok rendszer-időzítési funkcióit `system.time` , `proc.time`például a és a-t, hogy rögzítse az r-folyamatok által használt időt, és elemezze a teljesítménnyel kapcsolatos problémákat.
