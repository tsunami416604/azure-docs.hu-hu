---
title: 'Oktatóanyag: Tölti be az Azure Data Lake Storage Gen1 az Azure SQL Data Warehouse |} A Microsoft Docs'
description: Adatok betöltése az Azure Data Lake Storage Gen1 az Azure SQL Data Warehouse a PolyBase külső táblák használatával.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c3902061264b75ba177ba150176d784ad5384a9f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297196"
---
# <a name="load-data-from-azure-data-lake-storage-gen1-to-sql-data-warehouse"></a>Adatok betöltése az Azure Data Lake Storage Gen1 az SQL Data warehouse-bA
Adatok betöltése az Azure Data Lake Storage Gen1 az Azure SQL Data Warehouse a PolyBase külső táblák használatával. Bár a Data Lake Storage Gen1-ban tárolt adatokkal kapcsolatos ad hoc ad hoc lekérdezéseket is futtathat, javasoljuk, hogy az adatok importálása a legjobb teljesítmény érdekében az SQL Data Warehouse-bA.

> [!div class="checklist"]
> * Hozzon létre a Data Lake Storage Gen1 betöltéshez szükséges adatbázis-objektumokat.
> * Csatlakozzon egy Data Lake Storage Gen1 könyvtárba.
> * Adatok betöltése az Azure SQL Data Warehouse-bA.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek
Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.

Ez az oktatóanyag futtatásához szüksége:

* Az Azure Active Directory-alkalmazás szolgáltatások közötti hitelesítés használatához. Létrehozásához hajtsa végre az [Active directory-hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Az ügyfél-azonosító, a kulcs és a végpont Jogkivonatérték OAuth2.0-fiókhoz való csatlakozás a Data Lake Storage Gen1 SQL Data Warehouse-ból az Active Directory-alkalmazás van szüksége. Ezeket az értékeket beszerzése részletei találhatók a fenti hivatkozásra. Az Azure Active Directory Alkalmazásregisztráció használja az alkalmazás azonosítója, az ügyfél-azonosító.
> 

* Az Azure SQL Data Warehouse. Lásd: [létrehozása és a lekérdezés és az Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Egy Data Lake Storage Gen1 fiók. Lásd: [Ismerkedés az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Hitelesítő adatok létrehozása
A Data Lake Storage Gen1 fiókjába szüksége lesz egy fő adatbáziskulcsot titkosíthatja a hitelesítésiadat-titok a következő lépésben hozzon létre. Ezután hozzon létre egy Database Scoped Credential, amely tárolja az egyszerű szolgáltatás hitelesítő adatai állítsa be az aad-ben. A azoknak, akik már használtak a PolyBase szeretne csatlakozni a Windows Azure Storage-Blobokkal vegye figyelembe, hogy a credential szintaxis különböző.

Data Lake Storage Gen1 csatlakozni kell **első** hozzon létre egy Azure Active Directory-alkalmazás, hozzon létre egy hozzáférési kulcsot és az alkalmazás hozzáférést biztosít a Data Lake Storage Gen1 erőforráshoz. Útmutatásért lásd: [hitelesítés az Azure Data Lake Storage Gen1 használó Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>A külső adatforrás létrehozása
Ezzel [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) tárolja az adatok helyének parancsot. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage Gen1.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorageGen1
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSG1Credential
);
```

## <a name="configure-data-format"></a>Adatformátum konfigurálása
Az adatok importálása a Data Lake Storage Gen1 adja meg a külső fájlformátumot kell. Ezt az objektumot határozza meg, hogyan a fájlok a Data Lake Storage Gen1 készültek.
A teljes listát, tekintse meg a T-SQL-dokumentációnk [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>A külső táblák létrehozása
Most, hogy megadta a forrás- és a fájl adatformátum, készen áll a külső táblák létrehozásához. Külső táblák olyan külső adatok kezelését ismerteti. A hely paraméter megadhatja egy fájl vagy könyvtár. Ha egy könyvtárat, a címtárban található összes fájl lesz betöltve.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage Gen1 root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorageGen1
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Külső tábla kapcsolatos szempontok
Egy külső tábla létrehozása egyszerű, de van néhány kell vitatni részleteiről.

Külső táblák vannak listaobjektum. Ez azt jelenti, hogy az adatok betöltése folyamatban minden egyes sorára meg kell felelniük a tábla sémadefiníciója.
Egy sor nem egyezik a sémadefiníciót, ha a terhelést a sor fogja elutasítani.

A REJECT_TYPE és REJECT_VALUE beállítás lehetővé teszi, hogy meghatározza, hogy hány sort vagy az adatok milyen százalékos szerepelnie kell a végső táblázatba. Betöltéskor Ha az elutasítás érték elérésekor a terhelés sikertelen lesz. Visszautasított sorok leggyakoribb oka séma definíció nem egyeznek. Például ha egy oszlop helytelenül van megadva az int-séma, amikor az adatok a fájl egy karakterlánc, minden egyes sora lesz nem sikerült betölteni.

Data Lake Storage Gen1 szerepkör alapú hozzáférés-vezérlés (RBAC) használ az adatokhoz való hozzáférés vezérlésére. Ez azt jelenti, hogy az egyszerű szolgáltatás Olvasás engedéllyel rendelkezik a könyvtárak a helyre paraméterben meghatározott, és végső könyvtárat és fájlokat gyermekei. Ez lehetővé teszi a hitelesítéshez és az adatok betöltése a PolyBase. 

## <a name="load-the-data"></a>Az adatok betöltése
A Data Lake Storage Gen1 használatra betölteni az adatokat a [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) utasítást. 

A CTAS egy új táblát hoz létre, és feltölti azt egy kiválasztási utasítás eredményeivel. A CTAS határozza meg az új tábla, ugyanazokat az oszlopokat és adattípusokkal rendelkezik, mint a kiválasztási utasítás eredményei. Ha minden oszlop egy külső táblából választ, az új táblázat a külső tábla adattípusok és az oszlopok egy replikát.

Ebben a példában létrehozunk egy kivonat alapján elosztott tábla a külső tábla DimProduct_external DimProduct meghívva.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása
Alapértelmezés szerint az SQL Data Warehouse tárolja a tábla egy fürtözött oszlopcentrikus indexet. A betöltés befejezése után az adatsorokat némelyike előfordulhat, hogy nem tömöríti az oszloptárba.  Nincs több okból miért Ez akkor történhet. További tudnivalókért lásd: [oszlopcentrikus Indexek kezelése](sql-data-warehouse-tables-index.md).

Lekérdezési teljesítmény és a egy betöltés után oszlopcentrikus tömörítés optimalizálása érdekében végezze el a tábla az oszlopcentrikus indexet az összes sor tömörítendő kényszerítése.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statisztika optimalizálása
Célszerű a betöltés után azonnal létrehozza a egyoszlopos statisztikát. Van néhány statisztikai lehetőségeit. Például ha egyoszlopos statisztikát hoz létre minden oszlopról építse újra az összes statisztika hosszú ideig eltarthat. Ha ismeri az egyes oszlopok nem gazdakiszolgálói lesznek az olyan predikátumokban lekérdezés, kihagyhatja a statisztikák létrehozása az oszlopokat.

Ha úgy dönt, hogy egy oszlop statisztikákat létrehozni minden tábla minden oszlopához, használhatja a tárolt eljárás kódminta `prc_sqldw_create_stats` a a [statisztika](sql-data-warehouse-tables-statistics.md) cikk.

Az alábbi példa a statisztikák létrehozása az jó kiindulási pont. A dimenzió tábla minden oszlopához, és az egyes csatlakozó oszlopa a ténytáblák hozza egyoszlopos statisztikát. Mindig hozzáadhat egy vagy több oszlop statisztikai más (tény) tábla oszlopait később.

## <a name="achievement-unlocked"></a>Feloldotta eléréséhez!
Az Azure SQL Data Warehouse-bA betöltött adatok sikeresen. Remek munka!

## <a name="next-steps"></a>További lépések 
Ebben az oktatóanyagban a Data Lake Storage Gen1 tárolt adatok struktúráját külső táblákat hozott létre, és a PolyBase CREATE TABLE AS SELECT utasítás segítségével adatok betöltése az adattárházba. 

A következőket hajtotta végre:
> [!div class="checklist"]
> * A Data Lake Storage Gen1 betöltéséhez szükséges létrehozott adatbázis-objektumokat.
> * A Data Lake Storage Gen1 directory csatlakozik.
> * Az Azure SQL Data Warehouse-bA betöltött adatokról.
> 

Adatok betöltése az adattárház-megoldását az SQL Data Warehouse fejlesztés első lépése. Tekintse meg a fejlesztői erőforrások.

> [!div class="nextstepaction"]
>[Ismerje meg, hogyan hozhat létre a táblát az SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




