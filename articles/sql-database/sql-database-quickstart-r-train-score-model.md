---
title: Prediktív modell létrehozása és betanítása R-ben
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Hozzon létre egy egyszerű prediktív modellt az R-ben az Azure SQL Database Machine Learning Services (előzetes verzió) használatával, majd az új adatok használatával előre jelezheti az eredményt.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 3c88bdf141e7784837a89c8104574d97c93296dc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460154"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Rövid útmutató: Prediktív modell létrehozása és betanítása az R-ben az Azure SQL Database Machine Learning Services szolgáltatással (előzetes verzió)

Ebben a rövid útmutatóban hozzon létre és tanítsa be a prediktív modellt az R használatával, mentse a modellt egy táblába az adatbázisban, majd a modell segítségével előre jelezze az új adatok értékeit az Azure SQL Database Machine Learning Services (R) használatával.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL-adatbázis](sql-database-single-database-get-started.md) [kiszolgálószintű tűzfalszabállyal](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) engedélyezve van az R szolgáltatással.
- [SQL Server Felügyeleti stúdió](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Ez a példa egy egyszerű regressziós modellsegítségével előre jelzi az autó féktávolságát az R-ben található **autók** adatkészletének használatával.

> [!TIP]
> Az R-futásidejű számos adatkészletet tartalmaz a telepített adatkészletek `library(help="datasets")` listájának lekérése érdekében írja be az R parancssorból.

## <a name="create-and-train-a-predictive-model"></a>Prediktív modell létrehozása és betanítása

Az **autók** adatkészletében lévő autósebesség-adatok két oszlopot tartalmaznak, mind numerikus: **dist** és **speed**. Az adatok több megállási megfigyelést is tartalmaznak különböző sebességgel. Ezekből az adatokból létrehozhat egy lineáris regressziós modellt, amely leírja az autó sebessége és az autó megállításához szükséges távolság közötti kapcsolatot.

A lineáris modell követelményei a következők:
- Adjon meg egy képletet, amely leírja a függő változó *sebesség* és a független *változótávolság*közötti kapcsolatot.
- Adja meg a modell betanításához használni kívánt bemeneti adatokat.

> [!TIP]
> Ha szüksége van egy frissítő a lineáris modellek, próbálja meg ezt a bemutató, amely leírja a folyamat felszerelése modell rxLinMod: [Felszerelése lineáris modellek](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

A következő lépésekben beállítja a betanítási adatokat, hozzon létre egy regressziós modellt, tanítsa be a betanítási adatok használatával, majd mentse a modellt egy SQL-táblába.

1. Nyissa meg **az SQL Server Management Studio alkalmazást,** és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, [olvassa el a rövid útmutató: Az SQL Server Management Studio használata Azure SQL-adatbázis csatlakoztatásához és lekérdezéséhez című témakört.](sql-database-connect-query-ssms.md)

1. Hozza létre a **CarSpeed** táblát a betanítási adatok mentéséhez.

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

1. Regressziós `rxLinMod`modell létrehozása a használatával. 

   A modell létrehozásához adja meg a képletet az R-kódon belül, majd adja át a betanítási adatok **CarSpeed** bemeneti paraméterként.

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

1. Hozzon létre egy táblázatot, ahol tárolja a modellt, így később használhatja előrejelzéshez. 

   A modellt létrehozó R-csomag kimenete általában **bináris objektum,** ezért a táblának **VARBINARY(max)** típusú oszloppal kell rendelkeznie.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Most hívja meg a tárolt eljárást, hozza létre a modellt, és mentse egy táblába.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Ne feledje, hogy ha másodjára is futtatja ezt a kódot, akkor a következő hibát fogja kapni:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   A hiba elkerülésének egyik lehetősége az egyes új modellek nevének frissítése. Például módosíthatja a nevet valami közérthetőbb névre, és hozzáadhatja a modell típusát, a létrehozás napját stb.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Az együtthatók táblázatának megtekintése

Az R kimenete az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásból általában egyetlen adatkeretre korlátozódik. Azonban más típusú kimeneteket, például skaláris értékeket is visszaadhat az adatkeret mellett.

Tegyük fel például, hogy be szeretne képezni egy modellt, de azonnal meg szeretné tekinteni a modell együtthatók táblázatát. Ehhez hozza létre az együtthatók táblázatát fő eredményhalmazként, és adja ki a betanított modellt egy SQL változóban. A modellt azonnal újra használhatja a változó hívásával, vagy mentheti a modellt egy táblába az itt látható módon.

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

**Results (Eredmények)**

![Betanított modell további kimenettel](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Új adatok pontozása a betanított modell használatával

*A pontozás* az adatelemzésben használt kifejezés előrejelzések, valószínűségek vagy más értékek generálását jelenti egy betanított modellbe adagolni. Az előző szakaszban létrehozott modellt fogja használni az új adatokkal szembeni előrejelzések pontozásához.

Észrevette, hogy az eredeti betanítási adatok nem emelkednek 25 mérföld/óra sebesség fölé? Ez azért van, mert az eredeti adatok egy 1920-as kísérletből származnak! Lehet, hogy csoda, mennyi ideig tartana egy autó a 1920-as években, hogy hagyja abba, ha lehetne menni, amilyen gyorsan 60 mph, vagy akár 100 mph? A kérdés megválaszolásához adjon meg néhány új sebességértéket a modellnek.

1. Hozzon létre egy táblázatot új sebességadatokkal.

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

2. Az új sebességértékektől a féktávolság előrejelzése.

   Mivel a modell a **RevoScaleR** csomag részeként megadott **rxLinMod** algoritmuson alapul, az általános R `predict` függvény helyett az [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) függvényt hívja meg.

   Ez a példa parancsfájl:
   - Select utasítás thasználja egyetlen modell bekéséhez a táblából
   - Bemeneti paraméterként adja át
   - Meghívja `unserialize` a függvényt a modellen
   - A `rxPredict` megfelelő argumentumokkal rendelkező függvény alkalmazása a modellre
   - Biztosítja az új bemeneti adatokat

   > [!TIP]
   > A valós idejű pontozás, [lásd: Szerializálási funkciók](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) által biztosított RevoScaleR.

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

   **Results (Eredmények)**

   ![Eredményhalmaz a fékezési távolság előrejelzéséhez](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> Ebben a példaparancsfájlban a függvény a `str` tesztelési fázis ban hozzáadódik az R-ből visszaadott adatok sémájának ellenőrzéséhez. Az utasítást később is eltávolíthatja.
>
> Az R-szkriptben használt oszlopneveket a rendszer nem feltétlenül továbbítja a tárolt eljárás kimenetébe. Itt a WITH RESULTS záradék néhány új oszlopnevet határoz meg.

## <a name="next-steps"></a>További lépések

Az Azure SQL Database Machine Learning Services r-rel (előzetes verzió) című témakörről az alábbi cikkekben talál további információt.

- [Azure SQL Database Machine Learning Services R-rel (előzetes verzió)](sql-database-machine-learning-services-overview.md)
- [Egyszerű R-parancsfájlok létrehozása és futtatása az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Speciális R-függvények írása az Azure SQL Database-ben a Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
- [R- és SQL-adatok kal való kapcsolat az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)
