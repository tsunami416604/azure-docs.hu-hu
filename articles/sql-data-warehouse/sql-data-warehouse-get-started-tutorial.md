---
title: "Azure SQL Data Warehouse – Első lépéseket ismertető oktatóanyag | Microsoft Docs"
description: "Az Azure SQL Data Warehouse első lépéseit ismertető oktatóanyaga."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: 5bb75bf36892c737568b8129b30bb30b02d01bf2
ms.openlocfilehash: 1227903652a944d9d144917922fe36a4f149f820


---
# <a name="get-started-with-sql-data-warehouse"></a>Bevezetés az SQL Data Warehouse használatába

Az Azure SQL Data Warehouse első lépéseit ismertető oktatóanyaga. Ez az oktatóanyag egy SQL Data Warehouse üzembe helyezésének és az adatok betöltésének alapjait mutatja be, valamint ismertet néhány alapvető fogalmat a méretezéssel, a felfüggesztetéssel és a finomhangolással kapcsolatban. 

**Az oktatóanyag áttekintésének várható időtartama:** 75 perc

## <a name="prerequisites"></a>Előfeltételek


### <a name="sign-up-for-microsoft-azure"></a>Regisztráció a Microsoft Azure-ban
Ha még nem rendelkezik Microsoft Azure-fiókkal, először regisztrálnia kell egyet a szolgáltatás használatához. Ha már rendelkezik fiókkal, ezt a lépést kihagyhatja. 

1. Nyissa meg a fiókoldalakat: [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Hozzon létre egy ingyenes Azure-fiókot, vagy vásároljon egy fiókot.
3. Kövesse az utasításokat

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Telepítse a megfelelő SQL-ügyfélillesztőt és -ügyféleszközöket

A legtöbb SQL-ügyféleszköz képes csatlakozni az Azure SQL Data Warehouse-hoz a JDBC, az ODBC vagy az ADO.net használatával. Az SQL Data Warehouse termék összetettsége és a támogatott T-SQL-szolgáltatások széles köre miatt lehetséges, hogy nem minden ügyfélalkalmazás kompatibilis teljes mértékben az SQL Data Warehouse szolgáltatással.

Ha Windows operációs rendszert használ, javasoljuk a [Visual Studio] vagy az [SQL Server Management Studio] használatát.


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse létrehozása

> [!NOTE]
> Egy SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **New** > **Databases** > **SQL Data Warehouse** (Új > Adatbázisok > SQL Data Warehouse) elemre.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Adja meg az üzembe helyezés részleteit

    **Database name** (Adatbázis neve): Tetszés szerint bármit megadhat. Ha több SQL DW-példánnyal is rendelkezik, akkor javasoljuk, hogy a nevek tartalmazzák a régiót, környezetet, vagy hasonló részleteket (pl. *mydw-westus-1-test*)

    **Subscription** (Előfizetés): Az Ön Azure-előfizetése

    **Resource Group** (Erőforráscsoport): Új létrehozása (vagy egy meglévő használata, ha az Azure SQL Data Warehouse-t egyéb szolgáltatásokkal együtt tervezi használni)
    > [!NOTE]
    > Az azonos erőforráscsoportban lévő erőforrásoknak azonos életciklussal kell rendelkeznie. Az erőforráscsoportok hasznosak az erőforrások adminisztrációja, például a hozzáférés-vezérlés körének meghatározása és a sablonalapú üzembe helyezés során. Tudjon meg többet az Azure erőforráscsoportjairól és az ajánlott eljárásokról [itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).
    >

    **Forrás**: Üres adatbázis

    **Kiszolgáló**: Válassza ki az [Előfeltételek] szakaszban létrehozott kiszolgálót.

    **Rendezés**: Hagyja meg az alapértelmezett SQL_Latin1_General_CP1_CI_AS beállítást.

    **Teljesítmény kiválasztása**: Azt javasoljuk, hogy maradjon a standard 400DWU beállítás használatánál.

4. Válassza a **Rögzítés az irányítópulton**
    ![Rögzítés az irányítópulton](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png) lehetőséget.

5. Dőljön hátra, és várjon, amíg az Azure SQL Data Warehouse üzembe helyezése megtörténik. Ez a folyamat szokványos esetben több percig is eltarthat. A portál értesíti, amint a példány üzembe helyezése véget ért. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Csatlakozás az Azure SQL Data Warehouse-hoz SQL Serveren (logikai kiszolgáló) keresztül

Az oktatóanyagban az SQL Server Management Studio segítségével csatlakozunk az SQL Data Warehouse-hoz. Egyéb eszközökkel is csatlakozhat az SQL Data Warehouse-hoz a támogatott összekötőkön, például az ADO.NET, a JDBC, az ODBC és a PHP összekötőkön keresztül. Ne feledje, hogy a Microsoft által nem támogatott eszközök esetében a funkcionalitás korlátozott lehet.


### <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Az SQL Data Warehouse-hoz való kapcsolódáshoz az [Előfeltételek] szakaszban létrehozott SQL Serveren (logikai kiszolgáló) keresztül kell csatlakoznia.

1. Válassza ki az SQL Data Warehouse-t az irányítópulton vagy keresse meg az erőforrások között.

    ![SQL Data Warehouse irányítópult](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Keresse meg a logikai kiszolgáló teljes nevét.

    ![Kiszolgálónév kiválasztása](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Nyissa meg az SSMS-t, és az Object Explorer használatával csatlakozzon ehhez a kiszolgálóhoz az [Előfeltételek] szakaszban létrehozott hitelesítő adatok használatával

    ![Csatlakozás SSMS segítségével](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Ha minden jól megy, mostanra kapcsolódnia kell az SQL Server-példányhoz (logikai kiszolgálóhoz). Ezekkel a kiszolgálói hitelesítő adatokkal a kiszolgáló minden adatbázisában hitelesítheti magát az adatbázis tulajdonosaként. Azonban ajánlott eljárásként érdemes külön bejelentkezési adatokat és felhasználókat létrehoznia minden egyes adatbázishoz. A felhasználók létrehozását a [Felhasználó létrehozása az SQL Data Warehouse-hoz](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse) című szakaszban ismertetjük. 

## <a name="create-a-user-for-sql-data-warehouse"></a>Felhasználó létrehozása az SQL Data Warehouse-hoz

### <a name="why-create-a-separate-user"></a>Miért érdemes külön felhasználót létrehozni?

Az SQL Server (logikai kiszolgáló) és az előző lépésben létrehozott kiszolgálói hitelesítő adatok használatával egy új felhasználót hozunk létre az SQL Data Warehouse-hoz. Két fő oka van, amiért érdemes külön felhasználót/bejelentkezést létrehozni az SQL DW-hez.

1.  A szervezet felhasználóinak külön fiókokat kell használnia a hitelesítéshez. Ezzel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát.

2. Alapértelmezés szerint a kiszolgáló-rendszergazdai felhasználó, amellyel épp be van jelentkezve, kisebb erőforrásosztályt használ. Az erőforrásosztályok segítségével szabályozhatja a lekérdezésekhez rendelt memóriakiosztást és CPU-ciklusokat. A **smallrc** felhasználói kisebb mennyiségű memóriát kapnak, és kihasználhatják a nagyobb párhuzamosság előnyeit. Ezzel szemben az **xlargerc** alá rendelt felhasználók nagy mennyiségű memóriát kapnak, és ezért kevesebb lekérdezésük futhat egy időben. Az adatok olyan betöltéséhez, amely a legjobban optimalizálja a tömörítést, érdemes arra törekedni, hogy az adatokat betöltő felhasználó egy nagyobb erőforrásosztály része legyen. További információkat az erőforrásosztályokról [itt](./sql-data-warehouse-develop-concurrency.md#resource-classes) talál.

### <a name="creating-a-user-of-a-larger-resource-class"></a>Nagyobb erőforrásosztályba tartozó felhasználó létrehozása

1. Hozzon létre egy új lekérdezést a kiszolgáló **master** adatbázisán.

    ![Új lekérdezés a Master adatbázison](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Új lekérdezés a Master1 adatbázison](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Hozzon létre egy kiszolgálói bejelentkezést és felhasználót.

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Hozzon létre egy, a kiszolgálói bejelentkezésen alapuló új adatbázis-felhasználót.
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Adjon adatbázis-felügyeleti jogokat a felhasználónak.
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Ha az adatbázis nevében található kötőjel, mindenképp foglalja a nevet szögletes zárójelek közé. 
    >

5. Adja hozzá az adatbázis-felhasználót az **xlargerc** erőforrásosztály-szerepkörhöz.
    ```sql
    EXEC sp_addrolememeber 'xlargerc', 'LoadingUser';
    ```

6. Jelentkezzen be az adatbázisba az új hitelesítő adatokkal.

    ![Bejelentkezés az új bejelentkezési adatokkal](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Adatok betöltése

### <a name="defining-external-data"></a>Külső adatok meghatározása
1. Határozzon meg egy külső adatforrást.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Határozza meg a külső fájlformátumokat.

    A ```CREATE EXTERNAL FILE FORMAT``` parancs használatával adhatja meg a betöltés forrásaként szolgáló külső adatok formátumát. A New York-i taxiadatok esetében két formátumot alkalmaztunk az adatok tárolásához az Azure Blob Storage tárolóban.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Hozzon létre egy sémát a külső fájlformátum számára.

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Hozza létre a külső táblákat. Ezek a táblák a HDFS-ben vagy az Azure Blob Storage-ben tárolt adatokra hivatkoznak. 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Tábla létrehozása a Select utasítással (CTAS)

5. Töltse be az adatokat a külső táblákból az SQL Data Warehouse-példányba. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Több GB-nyi adatot tölt be és tömörít nagy teljesítményű fürtözött oszlopcentrikus indexekbe. Futtassa a következő DMV-lekérdezést, majd igyon egy kávét vagy szerezzen valami rágcsálnivalót, amíg az Azure SQL Data Warehouse keményen dolgozik.
    >

6. Hozzon létre egy új lekérdezést, és láthatja, ahogy adatai a dinamikusan felügyelt nézetben (DMV) megjelennek.

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. Tekintse meg az összes rendszerlekérdezést.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Láthatja, ahogy adatai szépen betöltődnek az Azure SQL Data Warehouse-ba.

    ![Betöltött adatok megjelenítése](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Adatok lekérdezése 

### <a name="scan-query-with-scaling"></a>Lekérdezés vizsgálata méretezéssel

Tekintsük meg, milyen hatással van a méretezés a lekérdezések sebességére.

Mielőtt nekilátnánk, csökkentse a DWU-k számát 100-ra, hogy láthassuk, hogyan teljesít egyetlen számítási csomópont önállóan.

1. Lépjen a portálra, és válassza ki az SQL Data Warehouse-példányt.

2. Válassza ki a méretet az SQL Data Warehouse panelen. 

    ![DW méretezése a portálról](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Csökkentse a teljesítményt a sávon 100 DWU-ra, és nyomja le a Mentés gombot.

    ![Méretezés és mentés](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Várjon, amíg a méretezési művelet befejeződik.

    > [!NOTE]
    > Vegye figyelembe, hogy a méretezési műveletek **leállítják** az aktuálisan folyamatban lévő lekérdezéseket, és nem engedik újak indítását.
    >
    
5. Végezzen egy vizsgálati műveletet az utazási adatokon, és válassza az első egymillió bejegyzést minden oszlopban. Ha szeretne gyorsabban továbblépni, választhat kevesebb sort is.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Jegyezze fel, hogy mennyi időbe telt ennek a műveletnek az elvégzése.

6. Skálázza fel a példányt 400 DWU-ra. Ne feledje, hogy minden 100 DWU egy újabb számítási csomópontot ad az Azure SQL Data Warehouse-hoz.

7. Futtassa újra a lekérdezést. Jelentős eltérést kell tapasztalnia. 

> [!NOTE]
> Az Azure SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) platform. Azok a lekérdezések és műveletek esetében tapasztalható meg az Azure SQL Data Warehouse igazi ereje, amelyeknél párhuzamosítható a munka a különféle csomópontok közt.
>

### <a name="join-query-with-statistics"></a>Összekapcsolási lekérdezés statisztikákkal

1. Futtasson egy lekérdezést, amely összekapcsolja a Date (Dátum) táblát a Trip (Utazás) táblával.

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Ahogy sejthető, a lekérdezés sokkal több időt vesz igénybe, ha az adatokat ide-oda mozgatja a csomópontok közt, különösen egy ehhez hasonló összekapcsolási forgatókönyvben.

2. Lássuk, mi a különbség, ha statisztikákat hozunk létre a csatlakoztatott oszlopon a következő futtatásával:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > Az SQL DW nem kezeli automatikusan a statisztikákat Ön helyett. A statisztikák fontosak a lekérdezések teljesítménye szempontjából, ezért határozottan javasoljuk, hogy hozzon létre statisztikákat, és frissítse azokat.
    > 
    > **A legnagyobb előnnyel az jár, ha az összekapcsolások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit készíti el.**
    >

3. Futtassa újra az Előfeltételek szakaszban szereplő lekérdezést, és figyelje meg a teljesítménybeli különbséget. Bár a lekérdezésteljesítmény változása nem olyan drámai, mint a felskálázás esetében, észrevehető gyorsulás figyelhető meg. 

## <a name="next-steps"></a>Következő lépések

Készen áll a lekérdezésre és vizsgálódásra. Tekintse meg gyakorlati tanácsainkat és tippjeinket.

Ha a mai napra befejezte a vizsgálódást, szüneteltesse a példány működését. Üzemi környezetben hatalmas megtakarításokat érhet el, ha üzleti igényei szerint szünetelteti és méretezi a működést.

![Szünet](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Hasznos olvasmányok

[Egyidejűség és a számítási feladatok kezelése]

[Ajánlott eljárások az Azure SQL Data Warehouse-hoz]

[Lekérdezések figyelése]

[A 10 leghasznosabb ajánlott eljárás nagyméretű relációs adattárházak létrehozásához]

[Adatok áttelepítése az Azure SQL Data Warehouse-ba]


[Egyidejűség és a számítási feladatok kezelése]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Ajánlott eljárások az Azure SQL Data Warehouse-hoz]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Lekérdezések figyelése]: sql-data-warehouse-manage-monitor.md
[A 10 leghasznosabb ajánlott eljárás nagyméretű relációs adattárházak létrehozásához]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Adatok áttelepítése az Azure SQL Data Warehouse-ba]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Előfeltételek]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO2-->


