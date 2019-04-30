---
title: Speciális R-függvények írása
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ismerje meg, hogyan írhat egy speciális statisztikai törölje a számításhoz R függvényt az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702452"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával speciális R-függvények írása

Ez a cikk bemutatja, hogyan ágyazhat be matematikai R és a segédprogram-funkciók egy SQL-ben tárolt eljárást. A T-SQL megvalósításához a bonyolult speciális statisztikai függvények csak egyetlen sor kód az R végezhető.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

- Példakód futtatható a témaköröket, először szüksége van egy Azure SQL database, a Machine Learning-szolgáltatások (az r nyelv) engedélyezve van. A nyilvános előzetes verzió ideje alatt a Microsoft is megjelenik majd, és engedélyezze a gépi tanulás a meglévő vagy új adatbázis számára. Kövesse a [regisztrálni az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Más adatbázis-kezelő vagy a lekérdezési eszközökkel R-szkriptek futtatása, de ez a rövid útmutató az ssms-t fogja használni.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Véletlenszerű számok létrehozására a tárolt eljárás létrehozása

Az egyszerűség kedvéért használja az R `stats` csomagot, amely telepítve van, és alapértelmezés szerint az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával betöltve. A csomag tartalmazza a Funkciók, többek között statisztikai általános feladatokhoz több száz a `rnorm` függvény. Ez a függvény a megadott számú véletlenszerű számok megadott egy szórás és az azt jelenti, hogy a normális eloszlás használatával hoz létre.

Például a következő R-kód az 50., 3 szórása megadott középérték 100 számot adja vissza.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Futtassa ezt a sort az R hívása a T-SQL, `sp_execute_external_script` , és adja hozzá a R függvényt az R parancsfájl paraméterben, ehhez hasonló:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Mi történik, ha szeretné létrehozni véletlenszerű számok külön készletét könnyebb?

Ez nem probléma, amikor az SQL. Megadhat egy tárolt eljárást, amely az argumentumok olvas be a felhasználó, majd az R-szkript változókként argumentumot átadandó.

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

- Az első sora határozza meg az SQL bemeneti paraméterek, amelyek szükségesek a tárolt eljárás végrehajtásakor.

- A sor elején `@params` R-kód és a megfelelő SQL-adattípusok által használt összes változó határozza meg.

- A sorokat, azonnal kövesse az SQL-paraméterek nevei rendelje hozzá a megfelelő R változó nevét.

Most, hogy az R függvény a tárolt beburkolását már egyszerűen meghívja a függvényt és adja át a különböző értékeket, ehhez hasonló:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>R-segédprogram funkciók használata a hibaelhárításhoz

A `utils` alapértelmezés szerint telepítve, a csomag biztosít számos segédprogram történő kivizsgálását az aktuális R-környezetben. Ezek a függvények akkor lehet hasznos, ha van megkeresni a eltérések az R-kód elvégzi, SQL és a külső környezetekben a. Például előfordulhat, hogy használja az R `memory.limit()` függvény az aktuális R-környezetben a memória.

Mivel a `utils` csomag telepítve van, de alapértelmezés szerint nincs betöltve, kell használnia a `library()` függvény betölti azt.

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
> Hány felhasználó, például az R, a rendszer időzítési függvények használhatók például `system.time` és `proc.time`, hogy az R-folyamatok által használt az idő rögzítése és teljesítménnyel kapcsolatos problémák elemzéséhez.
