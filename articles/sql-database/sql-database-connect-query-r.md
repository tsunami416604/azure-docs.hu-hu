---
title: Rövid útmutató a Machine Learning Services (with R) az Azure SQL Database-ben (előzetes verzió) | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használhatja a Machine Learning Servicest az Azure SQL Database-ben, illetve hogyan futtathat R-szkripteket nagy léptékű speciális elemzések biztosításához, továbbá ahhoz, hogy számítási és feldolgozási képességeket használhasson az adatok tárolási helyén, hogy ne legyen szükség az adatok hálózaton belüli mozgatására.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 11/07/2018
ms.openlocfilehash: 382ac23ea4c8e0ec54314bb754c00a8e6e43e9f6
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300965"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Rövid útmutató: A Machine Learning Services (with R) az Azure SQL Database-ben (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a Machine Learning Services (with R) nyilvános előzetes verzióját az Azure SQL Database-ben. Megismerkedhet az adatok az SQL-adatbázis és az R közötti áthelyezésének alapjaival. Ezenkívül azt is bemutatja, hogyan csomagolhat megfelelő formátumú R-kódot az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásba, amellyel gépi tanulási modelleket állíthat össze, taníthat be és használhat az SQL-adatbázisban.

Az SQL Database-ben a gépi tanulás az R-kód és -függvények futtatásához használatos, a kód pedig teljes mértékben rendelkezésre áll a relációs adatokhoz tárolt eljárások, R-utasításokat tartalmazó T-SQL-szkriptek vagy T-SQL-t tartalmazó R-kódok formájában. Használja a vállalati R-csomagokat, amelyekkel speciális elemzéseket futtathat nagy léptékben, valamint számítási és feldolgozási műveleteket használhat az adatok tárolási helyén, hogy ne legyen szükség az adatok hálózaton belüli mozgatására.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-up-for-the-preview"></a>Feliratkozás az előzetes verzióra

A Machine Learning Services (with R) nyilvános előzetes verziójának használata az SQL Database-ben alapértelmezés szerint nem engedélyezett. E-mail küldése a Microsoftnak, [ sqldbml@microsoft.com ](mailto:sqldbml@microsoft.com) regisztrálhat a nyilvános előzetes verziója.

Miután regisztrált a programban, a Microsoft belépteti Önt a nyilvános előzetes verzióba, és vagy migrálja a meglévő adatbázist, vagy egy új adatbázist hoz létre egy R-kompatibilis szolgáltatásban.

A Machine Learning Services (with R) az SQL Database-ben jelenleg csak a vCore-alapú vásárlási modellben érhetők el a készletezett adatbázisok **Általános célú** és **Üzleti szempontból kritikus** szolgáltatásszintjein. A kezdeti nyilvános előzetes verzióban sem a **Rugalmas skálázás** szolgáltatásszint, sem a **Felügyelt példányok** nem támogatottak. A Machine Learning Services (with R) nem használható éles számítási feladatokhoz a nyilvános előzetes verzióban.

Ha a Machine Learning Services (with R) használata engedélyezve van az Ön SQL-adatbázisához, térjen vissza erre az oldalra, ahol megtudhatja, hogyan futtathat R-szkripteket tárolt eljárások esetén.

Jelenleg az R az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

A gyakorlatokban szereplő példakód futtatásához először szüksége lesz egy Machine Learning Services (with R)-kompatibilis SQL-adatbázisra. A nyilvános előzetes verzióban a Microsoft belépteti Önt, és a fent leírt módon engedélyezi a gépi tanulást a meglévő vagy az új adatbázisban.

Csatlakozhat az SQL Database-adatbázishoz, továbbá bármilyen adatbázis-kezelő vagy lekérdezési eszközzel futtathatja az R-szkripteket, amíg az képes egy SQL Database-adatbázishoz való csatlakozásra, illetve T-SQL-lekérdezések vagy tárolt eljárások futtatására. Ez a rövid útmutató az [SQL Server Management Studio](sql-database-connect-query-ssms.md) eszközt használja.

A [csomag hozzáadása](#add-package) gyakorlatban az [R](https://www.r-project.org/)-t és az [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) alkalmazást is telepítenie kell a helyi számítógépen.

Ez a rövid útmutató egy kiszolgálószintű tűzfalszabály konfigurálását is megköveteli. Ennek módját a [Kiszolgálószintű tűzfalszabály létrehozása](sql-database-get-started-portal-firewall.md) szakasz ismerteti.

## <a name="different-from-sql-server"></a>Más, mint az SQL Server

Az Azure SQL Database-ben a Machine Learning Services (with R) funkciói hasonlóak az [SQL Server Machine Learning Services](https://review.docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) funkcióihoz. Van azonban közöttük néhány különbség:

- Csak az R-rel kompatibilis. A Python jelenleg nem támogatott.
- Nincs szükség az `external scripts enabled` az `sp_configure` használatával való konfigurálására.
- Nem kell szkriptvégrehajtási engedélyt adni a felhasználóknak.
- A csomagokat az **sqlmlutils** használatával kell telepíteni.
- Nincs külön külsőerőforrás-szabályozás. Az R-erőforrások az SQL-erőforrások adott százalékát teszik ki, a szolgáltatásszinttől függően.

## <a name="verify-r-exists"></a>Az R meglétének ellenőrzése

Ellenőrizheti, hogy a Machine Learning Services (with R) engedélyezve van-e az SQL-adatbázisban. Kövesse az alábbi lépéseket.

1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon az SQL-adatbázishoz.

1. Futtassa az alábbi kódot. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Ha minden rendben van, a következőhöz hasonló eredményt kell látnia.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. A nyilvános verzióra való regisztrálás menete fentebb olvasható.

## <a name="basic-r-interaction"></a>Az R alapszintű használata

Kétféleképpen futtathat R-kódokat az SQL Database-ben:

+ Hozzáadhat egy R-szkriptet a rendszer által tárolt [sp_execute_external_script](https://docs.microsoft.com/sql//relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) eljárás argumentumaként.
+ Csatlakozzon az SQL-adatbázishoz egy [távoli R-ügyfélről](https://review.docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client), majd futtassa a kódot úgy, hogy a számítási környezet az SQL Database legyen.

A következő gyakorlat az első használati modellre fókuszál: R-kód továbbítása egy tárolt eljárásnak.

1. Futtasson egy egyszerű szkriptet, hogy láthassa, hogyan fut le egy R-szkript az SQL-adatbázisban.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. Ha mindent megfelelően állított be, a rendszer a helyes eredményt számítja ki, az R `print` függvénye pedig a **Messages** (Üzenetek) ablakban adja vissza az eredményeket.

    **Results**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Bár hasznos, ha a kód tesztelése során **stdout** üzeneteket kap, ám gyakrabban van szükség arra, hogy a rendszer táblázatos formában adja vissza az eredményeket, hogy felhasználhassa azokat egy alkalmazásban, vagy egy táblába írhassa. További információért tekintse meg a bemenetekkel és kimenetekkel foglalkozó szakaszt.

Ne feledje, hogy az `@script` argumentumban csak érvényes R-kódok szerepelhetnek.

## <a name="inputs-and-outputs"></a>Bemenetek és kimenetek

Alapértelmezés szerint az [sp_execute_external_script](https://review.docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) egyetlen bemeneti adathalmazt fogad, amelyet általában Önnek kell megadnia érvényes SQL-lekérdezés formájában. Az egyéb bemeneti típusok SQL-változóként továbbíthatók.

A tárolt eljárás egy R-adatkeretet ad vissza kimenetként, de a skaláris értékeket és a modelleket változóként is visszaadhatja. Például bináris változóként adhat vissza kimenetként egy betanított modellt, és továbbíthatja egy T-SQL INSERT utasításhoz, ha a modellt egy táblába szeretné írni. Ezenkívül ábrázolásokat (bináris formátumban) vagy skaláris értékeket (egyéni értékek, például a dátum és az idő, a modell betanításával telt idő, stb.) is létrehozhat.

Egyelőre foglalkozzunk csak az sp_execute_external_script alapértelmezett bemeneti és kimeneti változóival: az `InputDataSet` és az `OutputDataSet` változókkal.

1. Hozzon létre egy tesztadatokból álló kis méretű táblát az alábbi T-SQL-utasítás végrehajtásával:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    Ha a tábla létrejött, a következő utasítással futtathat rajta lekérdezést:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Results**

    ![Az RTestData tábla tartalma](./media/sql-database-connect-query-r/select-rtestdata.png)

2. A táblában szereplő adatokat lekérheti az R-szkript bemeneteként. Hajtsa végre az alábbi utasítást. Az utasítás lekéri az adatokat a táblából, adatváltást végez az R-futtatókörnyezetben, majd egy *NewColName* nevű oszlopban adja vissza az értékeket.

    A lekérdezés által visszaadott adatokat a rendszer az R-futtatókörnyezethez továbbítja, amely adatkeret formájában adja vissza az adatokat az SQL Database-nek. A WITH RESULT SETS záradék meghatározza az SQL Database-nek visszaadott adattábla sémáját.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Egy R-szkript kimenete, amely adatokat ad vissza egy táblából](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Módosítsa a bemeneti vagy kimeneti változók nevét. A fenti szkript az alapértelmezett bemeneti és kimeneti változóneveket (_InputDataSet_ és _OutputDataSet_) használta. Az _InputDataSet_ változóhoz tartozó bemeneti adatok meghatározásához használja az *@input_data_1* változót.

    Ebben a szkriptben a tárolt eljárás bemeneti és kimeneti változói *SQL_out* és *SQL_in* értékre lettek módosítva:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Vegye figyelembe, hogy az R megkülönbözteti a kis-és nagybetűket, ezért az `@input_data_1_name` és az `@output_data_1_name` kimeneti és bemeneti változóinak egyeznie kell az `@script` R-kódjában szereplő változókkal. 

    Emellett a paraméterek sorrendje is fontos. Először meg kell adnia a szükséges *@input_data_1* és *@output_data_1* paramétereket, hogy használhassa az *@input_data_1_name* és *@output_data_1_name* választható változókat.

    Csak egy bemeneti adathalmaz továbbítható paraméterként, és csak egy adathalmaz adható vissza. Azonban az R-kódból más adathalmazokat is meghívhat, és az adathalmaz mellett más típusú kimeneteket is visszaadhat. Az OUTPUT kulcsszó hozzáadásával az eredmények között bármely paramétert visszaadhatja. 

    A `WITH RESULT SETS` utasítás határozza meg az SQL Database-ben használt adatok sémáját. Az R-ből visszaadott minden egyes oszlophoz SQL-kompatibilis adattípusokat kell megadnia. A sémadefiníciók használatával új oszlopneveket is megadhat, mivel nem kell az R-adatkeretben szereplő oszlopneveket használnia.

4. Értékeket az R-szkript használatával is létrehozhat, és üresen hagyhatja az _@input_data_1_ bementi lekérdezési sztringjét.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Eredmények lekérdezése az @script bemenetként való használatával](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Az R verziószámának ellenőrzése

Ha kíváncsi, hogy az R melyik verziója van telepítve a SQL-adatbázisban, tegye a következőket:

1. Futtassa az alábbi szkriptet az SQL-adatbázison.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. Az R `print` funkciója a **Messages** (Üzenetek) ablakban adja vissza a verziót. Az alábbi példakimenetben látható, hogy az SQL Database-ben az R 3.4.4-es verziója van telepítve.

    **Results**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>R-csomagok listázása

A Microsoft számos előre telepített R-csomagot biztosít a Machine Learning Services-hez az SQL-adatbázisban. Ha szeretné megtekinteni, hogy melyik R-csomagok vannak telepítve, valamint kíváncsi azok verziójára, függőségeire, licenceire és kódtárútvonal-adataira, kövesse az alábbi lépéseket. További csomagok hozzáadásához tekintse meg a [csomagok hozzáadásáról](#add-package) szóló szakaszt.

1. Futtassa az alábbi szkriptet az SQL-adatbázison.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. A kimenet az R `installed.packages()` függvényéből származik, és a rendszer eredményhalmazként adja vissza.

    **Results**

    ![Telepített csomagok az R-ben](./media/sql-database-connect-query-r/r-installed-packages.png)


## <a name="create-a-predictive-model"></a>Prediktív modell létrehozása

Betaníthat egy modellt az R használatával, és mentheti egy SQL-adatbázisban szereplő táblába. Ebben a gyakorlatban egy egyszerű regressziós modellt fog betanítani, amely egy autó sebessége alapján előrejelzi annak féktávját. Az R-ben található `cars` adathalmazt fogja használni, mivel az kis méretű és könnyen érthető.

1. Első lépésként hozzon létre egy táblát a betanítási adatok mentéséhez.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Az R-futtatókörnyezetben számos kis- és nagyméretű adathalmaz szerepel. Az R-rel együtt telepített adatkészletek listájának megtekintéséhez írja be a `library(help="datasets")` utasítást az R parancssorába.

2. Hozzon létre egy regressziós modellt. Az autó sebességadatai két numerikus oszlopot tartalmaznak: `dist` és `speed`. Bizonyos sebességértékek többször is előfordulnak. Az adatok alapján egy lineáris regressziós modellt hozhat létre, amely az autó sebessége és az autó megállításához szükséges távolság közötti összefüggéseket ír le.

    A lineáris modell követelményei a következők:

    - Határozzon meg egy képletet, amely leírja a `speed` függő változó és a `distance` független változó közötti összefüggést.

    - Adja meg a modell betanításához használni kívánt bemeneti adatokat.

    > [!TIP]
    > Ha szeretné felfrissíteni a tudását a lineáris modellekkel kapcsolatban, javasoljuk a [Lineáris modellek igazítása](https://docs.microsoft.com/r-server/r/how-to-revoscaler-linear-model) című oktatóanyagot, amely a modellek az rxLinMod függvény használatával való igazításának folyamatát mutatja be.

    A modell létrehozásához adja meg a képletet az R-kódban, majd továbbítsa az adatokat bemeneti paraméterként.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
        EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
            trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @output_data_1_name = N'trained_model'
        WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

    Az rxLinMod függvény első argumentuma a *képlet* paraméter, amely a távolságot a sebesség függvényében határozza meg. A bemeneti adatok a `CarsData` változóban tárolódnak, amelyet az SQL-lekérdezés tölt ki. Ha nem rendel konkrét nevet a bemeneti adatokhoz, akkor a változó alapértelmezett neve _InputDataSet_ lesz.

2. Ezután hozzon létre egy táblát a modell tárolásához újbóli betanítás vagy előrejelzés céljából. Egy modellt létrehozó R-csomag kimenete általában egy **bináris objektum**. Ezért a táblában szerepelnie kell egy **VARBINARY(max)** típusú oszlopnak.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. A modell mentéséhez futtassa a következő Transact-SQL-utasítást a tárolt eljárás meghívásához, hozza létre a modellt, és mentse egy táblába.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Ne feledje, hogy ha másodjára is futtatja ezt a kódot, akkor a következő hibát fogja kapni:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    A hiba elkerülésének egyik módja az, ha minden új modell esetén frissíti a nevet. Például módosíthatja a nevet valami közérthetőbb névre, és hozzáadhatja a modell típusát, a létrehozás napját stb.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Az R kimenete az [sp_execute_external_script](https://review.docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) tárolt eljárásból általában egyetlen adatkeretre korlátozódik.

    Azonban más típusú kimeneteket, például skaláris értékeket is visszaadhat az adatkeret mellett.

    Például tegyük fel, hogy egy modellt szeretne betanítani, de azonnal szeretné látni a modell együtthatóinak táblázatát is. Fő eredménykészletként létrehozhatja az együtthatók táblázatát, a betanított modell pedig létrejöhet kimenetként egy SQL-változóban. A változó meghívásával azonnal újra felhasználhatja a modellt, vagy egy táblába is mentheti az itt látható módon.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Results**

    ![Betanított modell további kimenettel](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Előrejelzés

Az előző szakaszban létrehozott modell használatával pontozhatja az új adatokra vonatkozó előrejelzéseket. Új adatokkal végzett _pontozás_ végrehajtásához kérje le az egyik betanított modellt a táblából, majd hívjon meg egy új adathalmazt, amely az előrejelzések alapjául szolgálhat. Az adatelemzés esetében a pontozás kifejezés időnként előrejelzések, valószínűségek vagy egyéb értékek meghatározását jelenti új adatok betanított modellekbe való betöltése révén.

1. Első lépésként hozzon létre egy táblát az új sebességadatokkal. Észrevette, hogy az eredeti betanítási adatok nem emelkednek 25 mérföld/óra sebesség fölé? Ez azért van, mert az eredeti adatok egy 1920-as kísérletből származnak! Felmerülhet a kérdés, hogy milyen távolságon tudna megállni egy 1920-as évekből származó autó, ha képes lenne 60, vagy akár 100 mérföld/órás sebesség elérésére? A kérdés megválaszolásához meg kell adnia néhány új sebességértéket.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    Mivel ebben a példában a modell a **RevoScaleR** csomag részeként biztosított **rxLinMod** algoritmuson alapul, az R általános `predict` függvénye helyett hívja meg az [rxPredict](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxpredict) függvényt.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    A fenti szkript a következő lépéseket hajtja végre:

    + Egy SELECT utasítás használatával kérjen le egy modellt a táblából, és továbbítsa bemeneti paraméterként.

    + Miután lekérte a modellt a táblából, hívja a meg az `unserialize` függvényt a modellhez.

        > [!TIP] 
        > Emellett tekintse meg a RevoScaleR által biztosított új [szerializálási függvényeket](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxserializemodel) is, amelyek támogatják a valós idejű pontozást.
    + Alkalmazza a modellre az `rxPredict` függvényt a megfelelő argumentumokkal, és adja meg az új bemeneti adatokat.

    + A példában az `str` függvényt adjuk hozzá a tesztelési fázisban, hogy ellenőrizzük az R-ből visszaadott adatok sémáját. Az utasítást később eltávolíthatja.

    + Az R-szkriptben használt oszlopneveket a rendszer nem feltétlenül továbbítja a tárolt eljárás kimenetébe. Ebben az esetben a WITH RESULTS záradékot használtuk új oszlopnevek meghatározásához.

    **Results**

    ![Eredményhalmaz a fékezési távolság előrejelzéséhez](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

    A [Transact-SQL PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) elemét is használhatja az előrejelzett érték vagy pontszám tárolt eljárás alapján való létrehozásához.

<a name="add-package"></a>

## <a name="add-a-package"></a>Csomag hozzáadása

Ha olyan csomagot szeretne használni, amely még nincs telepítve az SQL-adatbázisban, az [sqlmlutils](https://github.com/Microsoft/sqlmlutils) használatával telepítheti azt. A csomag telepítéséhez kövesse az alábbi lépéseket.

1. Töltse le a legújabb **sqlmlutils** zip-fájlt a [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) webhelyről a helyi számítógépére. A fájlt nem kell kibontania.

1. Ha még nincs telepítve az R, töltse le a [www.r-project.org](https://www.r-project.org/) webhelyről, és telepítse a helyi számítógépen. Az R Windows, macOS és a Linux rendszerekhez érhető el. Ebben a példában a Windowst használjuk.

1. Először is telepítse az **RODBCext** csomagot, amely az **sqlmlutils** előfeltétele. Az **RODBCext** az **RODBC** csomag függőségeit is telepíti. Nyisson meg egy **parancssort**, és futtassa a következő parancsot:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Ha például **„Az „R” nem ismerhető fel belső vagy külső parancsként, működő programként vagy batch-fájlként.”** üzenetet kapja, az valószínűleg azt jelenti, hogy az R.exe nem szerepel a **PATH** környezeti változóban a Windows rendszeren. Ebben az esetben hozzáadhatja a könyvtárat a környezeti változóhoz, vagy megkeresheti a könyvtárat a parancssorban (például `cd C:\Program Files\R\R-3.5.1\bin`).

1. Az **sqlmlutils** telepítéséhez használja az **R CMD INSTALL** parancsot. Adja meg annak a könyvtárnak az elérési útját, amelybe letöltötte a zip-fájlt, valamint adja meg a zip-fájl nevét. Példa:

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    A kimenet az alábbihoz hasonló lesz:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. Ebben a példában az RStudio Desktop lesz az IDE. Ha szeretne, más IDE-t is használhat. Ha az RStudio még nincs telepítve, töltse le és telepítse az RStudio Desktop alkalmazást a [www.rstudio.com/products/rstudio/download](https://www.rstudio.com/products/rstudio/download/) webhelyről.

1. Indítsa el az **RStudio** alkalmazást, majd hozzon létre egy új **R-szkript** fájlt. Az alábbi R-kóddal telepítsen egy csomagot az sqlmlutils használatával. Az alábbi példában a [glue](https://cran.r-project.org/web/packages/glue/) csomagot fogja telepíteni, amely képes a sztringek formázására és interpolálására.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > A **hatókör** lehet **PUBLIC** (nyilvános) vagy **PRIVATE** (privát). A nyilvános hatókörrel az adatbázis rendszergazdája minden felhasználó által használható csomagokat tölthet fel. A privát hatókörrel csak az a felhasználó használhatja a csomagot, amelyik telepítette. Ha nem adja meg a hatókört, a rendszer alapértelmezés szerint a **PRIVATE** értéket fogja használni.

1. Ellenőrizze, hogy a **glue** csomag telepítve lett-e.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Results**

    ![Az RTestData tábla tartalma](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. A csomag telepítését követően az **sp_execute_external_script** segítségével használhatja azt az R-szkriptben. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz. Futtassa a következő parancsfájlt:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    A következő eredmény látható az Üzenetek lapon.

    **Results**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Ha szeretné eltávolítani a csomagot, futtassa a következő R-szkriptet az **RStudio** alkalmazásban a helyi számítógépen.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Az R-csomagok az SQL-adatbázisban való telepítésének másik módja az, ha a [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) használatával feltölti az R-csomagot egy bájtstreambe.

## <a name="next-steps"></a>További lépések

A Machine Learning Serviceszel kapcsolatos további információért olvassa el az SQL Server Machine Learning-szolgáltatásokkal foglalkozó alábbi cikkeket. Bár ezek a cikkek az SQL Serverhez készültek, az azokban szereplő információk a Machine Learning Services (with R) esetében is alkalmazhatók az Azure SQL Database-ben.

- [SQL Server Machine Learning-szolgáltatások](https://review.docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Oktatóanyag: Ismerkedés az adatbázison belüli elemzésekkel az R használatával az SQL Serverben](https://review.docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Teljes körű adatelemzési útmutató az R-hez és az SQL Serverhez](https://review.docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Oktatóanyag: A RevoScaleR R-függvényeinek használata az SQL Server adataival](https://review.docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
