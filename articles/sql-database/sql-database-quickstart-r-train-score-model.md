---
title: Hozzon létre és betanítunk egy prediktív modellt az R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Hozzon létre egy egyszerű prediktív modellen az R használata Azure SQL Database, Machine Learning Services (előzetes verzió), majd egy új adatok eredmények.
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
ms.openlocfilehash: c1719064de53b79a127146d0ab034f461657cc64
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714889"
---
# <a name="create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Hozzon létre és betanítunk egy prediktív modellt az R az Azure SQL Database, Machine Learning Services (előzetes verzió)

Ebben a rövid fog létrehoz és betanítunk egy prediktív modellt, az r nyelv használatát, mentse a modell egy táblát az SQL database-ben, majd a modellt használja a nyilvános előzetes verziójának használatával új adatok értékeinek [Machine Learning-szolgáltatások (az r nyelv) Azure SQL Database-ben ](sql-database-machine-learning-services-overview.md). 

Ebben a rövid útmutatóban használni kívánt modellje egy egyszerű regressziós modellt, amely előre megbecsüli egy autó sebessége alapján leállításával távolság. Szeretné használni a **autók** kis- és könnyen érthető, mert részét képező R-adatkészlet.

> [!TIP]
> Az R-futtatókörnyezetben számos kis- és nagyméretű adathalmaz szerepel. Az R telepítve van az adatkészletek listájának lekéréséhez írja be a `library(help="datasets")` R parancsot egy parancssorból.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

- Példakód futtatható a témaköröket, először szüksége van egy Azure SQL database, a Machine Learning-szolgáltatások (az r nyelv) engedélyezve van. A nyilvános előzetes verzió ideje alatt a Microsoft is megjelenik majd, és engedélyezze a gépi tanulás a meglévő vagy új adatbázis számára. Kövesse a [regisztrálni az előzetes verzióra](sql-database-machine-learning-services-overview.md#signup).

- Győződjön meg arról, hogy telepítette a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Az R-szkriptek más adatbázis-kezelő vagy a lekérdezési eszközökkel futtathatja, de ez a rövid útmutató az ssms-t fogja használni.

- Ebben a rövid útmutatóban egy kiszolgálószintű tűzfalszabály konfigurálását igényli. Ennek módjáról további információkért lásd: [kiszolgálószintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md).

## <a name="create-and-train-a-predictive-model"></a>Hozzon létre és betanítunk egy prediktív modellt

Az autó sebességű adatok a **autók** adatkészletet tartalmaz két oszlop, mindkét numerikus: **dist** és **sebesség**. Több leállítási megfigyelések különböző sebességek szerepel. Az adatok alapján létrehozhat egy lineáris regressziós modellt, amely leírja az autó sebességről és a egy autó leállításához szükséges közötti kapcsolat.

A lineáris modell követelményei a következők:
- Adja meg egy képletet, amely a függő változó közötti kapcsolat *sebesség* és a független változó *távolság*.
- Adja meg a modell betanításához használni kívánt bemeneti adatokat.

> [!TIP]
> Ha Lineáris modellt felelevenítéséhez van szüksége, próbálja meg ebben az oktatóanyagban, amely rxLinMod használó modell igyekeznek folyamatát ismerteti: [Méretezés lineáris modellt](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

Az alábbi lépéseket kell beállítani a betanítási adatok egy regressziós modell létrehozásához, betanítása a betanítási adatok használatával, majd mentse a módosítást az SQL-táblájába.

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha a kapcsolódás segítségre van szüksége, tekintse meg [a rövid útmutató: SQL Server Management Studio használatával csatlakozhat, és az Azure SQL Database-adatbázis lekérdezéséhez](sql-database-connect-query-ssms.md).

1. Hozzon létre a **CarSpeed** tábla a betanítási adatok mentéséhez.

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

1. Hozzon létre egy regressziós modell `rxLinMod`. 

   A modell létrehozásához a képlet belül az R-kód megadása, és akkor továbbítja a betanítási adatok **CarSpeed** bemeneti paraméterként.

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

1. Hozzon létre egy táblát, így később is előrejelzéshez használhatja hol tárolja a modell. 

   Egy R-csomag, amely létrehoz egy modellt kimenete általában egy **bináris objektum**, így a táblának rendelkeznie kell egy számoszlop **VARBINARY(max)** típusa.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Most már a tárolt eljárást hívja, a modell generálásához, és mentse egy táblához.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Ne feledje, hogy ha másodjára is futtatja ezt a kódot, akkor a következő hibát fogja kapni:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Ez a hiba elkerülése érdekében egy lehetőség, hogy frissítse az egyes új modell nevét. Például módosíthatja a nevet valami közérthetőbb névre, és hozzáadhatja a modell típusát, a létrehozás napját stb.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>A táblázat együttható megtekintése

Az R kimenete az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásból általában egyetlen adatkeretre korlátozódik. Azonban más típusú kimeneteket, például skaláris értékeket is visszaadhat az adatkeret mellett.

Például tegyük fel, hogy a modell betanítását, de azonnal tekintse meg a táblázatot a modellből együttható. Ehhez hozzon létre a tábla együttható, a fő eredményt, és kimeneti beállítása a betanított modell egy SQL-változóban. Azonnal újból felhasználhatja a modell a változó meghívásával, vagy mentheti a modell egy táblába az itt látható módon.

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

## <a name="score-new-data-using-the-trained-model"></a>A betanított modell használatával új adatok pontozásához

*A pontozás* jelenti azt, hogy előrejelzéseket, valószínűségek vagy más értékek alapján a betanított modell az adatkéréseket az új adatok létrehozása a data science használt kifejezés. A modell új adatokkal szemben előrejelzéseket pontozásához az előző szakaszban létrehozott fogja használni.

Észrevette, hogy az eredeti betanítási adatok nem emelkednek 25 mérföld/óra sebesség fölé? Ez azért van, mert az eredeti adatok egy 1920-as kísérletből származnak! Vezetőnév, hogy mennyi ideig szeretné időt vesz igénybe egy autó a 1920s leáll, ha azt 60 mph vagy akár 100 mph gyors sikerült kezdheti a? Ez a kérdés megválaszolásához is értékeket ad meg néhány új sebessége a modellhez.

1. Tábla létrehozása az új sebességű adatokkal.

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

2. Ezek az új sebesség értékek leállításával távolsága előrejelzésére.

   Mivel a modell alapján a **rxLinMod** részeként algoritmus a **RevoScaleR** csomag, hívja a [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) működni, ahelyett, hogy az általános R `predict` függvény.

   Ez a példa parancsfájl:
   - Egy adott modellt beolvasása a táblából egy kiválasztási utasítás használatával
   - Bemeneti paraméterként átadja azokat
   - Hívások a `unserialize` funkciót, a modell
   - Alkalmazza a `rxPredict` függvény mintának megfelelő argumentumokkal
   - Adja meg az új bemeneti adatait

   > [!TIP]
   > Valós idejű pontozás, lásd: [szerializálási funkciók](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) RevoScaleR által biztosított.

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
> Az alábbi példában a `str` függvény bekerül a tesztelési fázisban, nézze meg a sémában az R. a visszaadott adatok Később az utasítással távolíthatja el.
>
> Az R-szkriptben használt oszlopneveket a rendszer nem feltétlenül továbbítja a tárolt eljárás kimenetébe. Az eredmények a záradék Itt néhány új oszlopok nevét határozza meg.

## <a name="next-steps"></a>További lépések

További információ az Azure SQL Database Machine Learning Services (előzetes verzió) R-rel tekintse meg a következő cikkekben talál.

- [Az Azure SQL Database Machine Learning-szolgáltatások (előzetes verzió) R-rel](sql-database-machine-learning-services-overview.md)
- [Hozzon létre, és egyszerű R-szkriptek futtatása az Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Az Azure SQL Database, Machine Learning-Szolgáltatásokoz (előzetes verzió) használatával speciális R-függvények írása](sql-database-machine-learning-services-functions.md)
- [R- és SQL adatok használata Azure SQL Database, Machine Learning Services (előzetes verzió)](sql-database-machine-learning-services-data-issues.md)