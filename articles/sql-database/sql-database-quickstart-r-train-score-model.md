---
title: Prediktív modell létrehozása és betanítása az R-ben
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Hozzon létre egy egyszerű prediktív modellt az R-ben Azure SQL Database Machine Learning Services (előzetes verzió) használatával, majd adja meg az eredményt az új adataival.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768465"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Gyors útmutató: prediktív modell létrehozása és betanítása az R-ben Azure SQL Database Machine Learning Services (előzetes verzió)

Ebben a rövid útmutatóban egy prediktív modellt fog létrehozni és betanítani az R használatával, mentse a modellt az adatbázis egyik táblájába, majd a modell használatával adja meg az új adatok értékeit az Azure SQL Database Machine Learning Services (R) használatával.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy [kiszolgálói szintű tűzfalszabály használatával](sql-database-server-level-firewall-rule.md) rendelkező [Azure SQL Database](sql-database-single-database-get-started.md)
- Az R-t engedélyező [Machine learning Services](sql-database-machine-learning-services-overview.md) . [Regisztráljon az előzetes](sql-database-machine-learning-services-overview.md#signup)verzióra.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> A nyilvános előzetes verzióban a Microsoft bevezeti Önt, és lehetővé teszi a gépi tanulást a meglévő vagy az új adatbázishoz.

Ebben a példában egy egyszerű regressziós modellt használunk, amellyel előre megjósolható, hogy a sebesség az R-vel együtt használt **autók** adatkészlete alapján történjen.

> [!TIP]
> Az R Runtime számos adatkészletet tartalmaz, a telepített adatkészletek listájának lekéréséhez írja be a `library(help="datasets")` értéket az R-parancssorból.

## <a name="create-and-train-a-predictive-model"></a>Prediktív modell létrehozása és betanítása

Az **autók** adatkészletében lévő autós adatátviteli adathalmaz két oszlopot tartalmaz, a numerikus: **dist** és a **Speed**értékeket is beleértve. Az adatértékek több, különböző sebességű leállítási észrevételt tartalmaznak. Ebből az adatokból egy lineáris regressziós modellt hozunk létre, amely leírja az autó sebességének és az autó leállításához szükséges távolságnak a kapcsolatát.

A lineáris modell követelményei a következők:
- Definiáljon egy olyan képletet, amely leírja a függő változó *sebességének* és a független változó *távolságának*a kapcsolatát.
- Adja meg a modell betanításához használni kívánt bemeneti adatokat.

> [!TIP]
> Ha a lineáris modelleken frissítőre van szüksége, próbálja ki ezt az oktatóanyagot, amely leírja, hogyan illeszthető be modell a rxLinMod használatával: [lineáris modellek illesztése](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

A következő lépésekben be kell állítania a betanítási adattípusokat, létre kell hoznia egy regressziós modellt, be kell tanítania a betanítási adataival, majd mentenie kell a modellt egy SQL-táblába.

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, tekintse meg [Az Azure SQL Database-adatbázisok csatlakoztatásához és lekérdezéséhez SQL Server Management Studio használata](sql-database-connect-query-ssms.md)című témakört.

1. Hozza létre a **CarSpeed** táblát a betanítási adatgyűjtés mentéséhez.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Regressziós modell létrehozása `rxLinMod`használatával. 

   A modell létrehozásához adja meg a képletet az R-kódban, majd adja át a betanítási adatok **CarSpeed** bemeneti paraméterként.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Az rxLinMod függvény első argumentuma a *képlet* paraméter, amely a távolságot a sebesség függvényében határozza meg. A bemeneti adatok a `CarsData` változóban tárolódnak, amelyet az SQL-lekérdezés tölt ki.

1. Hozzon létre egy táblázatot, amelyben tárolja a modellt, így később is felhasználhatja az előrejelzéshez. 

   A modellt létrehozó R-csomag kimenete általában **bináris objektum**, ezért a táblának **VARBINARY (max)** típusú oszlopra van szükség.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Most hívja meg a tárolt eljárást, létrehozza a modellt, és mentse egy táblába.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Ne feledje, hogy ha másodjára is futtatja ezt a kódot, akkor a következő hibát fogja kapni:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Az egyik lehetőség, hogy elkerülje ezt a hibát, hogy frissítse az egyes új modellek nevét. Például módosíthatja a nevet valami közérthetőbb névre, és hozzáadhatja a modell típusát, a létrehozás napját stb.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Az együtthatók táblázatának megtekintése

Az R kimenete az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásból általában egyetlen adatkeretre korlátozódik. Azonban más típusú kimeneteket, például skaláris értékeket is visszaadhat az adatkeret mellett.

Tegyük fel például, hogy egy modellt szeretne betanítani, de azonnal megtekinti a modellből származó együtthatók táblázatát. Ehhez létre kell hoznia az együtthatók táblázatát a fő eredményhalmazként, és a betanított modellt egy SQL-változóban kell kiállítania. A modellt azonnal újra használhatja a változó meghívásával, vagy mentheti a modellt egy táblázatba az itt látható módon.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Results**

![Betanított modell további kimenettel](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Új adatértékek kiértékelése a betanított modell használatával

A *pontozás* olyan kifejezés, amely az adattudományban az előrejelzések, valószínűségek vagy más értékek generálását jelenti a betanított modellbe betáplált új adatok alapján. Az előző szakaszban létrehozott modellt fogja használni az új adatértékekkel kapcsolatos előrejelzések kiértékeléséhez.

Észrevette, hogy az eredeti betanítási adatok nem emelkednek 25 mérföld/óra sebesség fölé? Ez azért van, mert az eredeti adatok egy 1920-as kísérletből származnak! Lehet, hogy mennyi ideig tart egy autó a 1920-as verzióról, hogy leálljanak, ha akár 60 mph vagy akár 100 mph? A kérdés megválaszolásához megadhat néhány új sebességet a modell számára.

1. Hozzon létre egy táblát új sebességgel.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Az új sebességi értékek megállítási távolságának előrejelzése.

   Mivel a modell a **RevoScaleR** -csomag részeként megadott **rxLinMod** -algoritmuson alapul, az általános R `predict` függvény helyett a [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) függvényt hívja meg.

   Példa a parancsfájlra:
   - A SELECT utasítás használatával egyetlen modellt kap a táblából.
   - Bemeneti paraméterként adja át.
   - A modell `unserialize` függvényének meghívása
   - A `rxPredict` függvény alkalmazása a modell megfelelő argumentumai alapján
   - Az új bemeneti adatokat adja meg

   > [!TIP]
   > A valós idejű pontozással kapcsolatban tekintse meg a RevoScaleR által biztosított [szerializálási függvények](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) című témakört.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Results**

   ![Eredményhalmaz a fékezési távolság előrejelzéséhez](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Ebben a példában a szkript a tesztelési fázisban a `str` függvényt adja hozzá az R-ből visszaadott adatok sémájának ellenőrzéséhez. Az utasítást később is eltávolíthatja.
>
> Az R-szkriptben használt oszlopneveket a rendszer nem feltétlenül továbbítja a tárolt eljárás kimenetébe. Itt a WITH RESULTs záradék definiál néhány új oszlopnevet.

## <a name="next-steps"></a>Következő lépések

Az R (előzetes verzió) Azure SQL Database Machine Learning Servicesról a következő cikkekben talál további információt.

- [Azure SQL Database Machine Learning Services R-vel (előzetes verzió)](sql-database-machine-learning-services-overview.md)
- [Egyszerű R-parancsfájlok létrehozása és futtatása Azure SQL Database Machine Learning Servicesban (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
- [R-és SQL-adatmennyiség használata Azure SQL Database Machine Learning Servicesban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
