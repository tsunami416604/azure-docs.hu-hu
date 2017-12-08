---
title: "Betöltési – az Azure Data Lake Store az SQL Data Warehouse |} Microsoft Docs"
description: "Útmutató: az adatok betöltése az Azure Data Lake Store az Azure SQL Data Warehouse PolyBase külső táblák segítségével."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 09/15/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: bb478484fba5a76fa12d5d1976919224965b6e0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Adatok betöltése az Azure Data Lake Store az SQL Data Warehouse
Ez a dokumentum lehetővé teszi az összes lépést kell az Azure Data Lake Store-(ADLS-) a saját adatok betöltése az SQL Data Warehouse PolyBase használatával.
Miközben az ad hoc lekérdezéseket futtatnia a külső táblák segítségével ADLS tárolt adatokat képes, ajánlott eljárásként javasoljuk, hogy az adatok importálása az SQL Data Warehouse.

Ebből az oktatóanyagból megtudhatja, hogyan:

1. Külső adatbázis-objektumok betöltése az Azure Data Lake Store létrehozása.
2. Csatlakozás az Azure Data Lake Store-címtárhoz.
3. Adatok betöltése az Azure SQL Data warehouse-bA.

## <a name="before-you-begin"></a>Előkészületek
Ez az oktatóanyag futtatásához szükséges:

* Az Azure Active Directory-alkalmazást a szolgáltatások közötti hitelesítéshez használandó. Létrehozásához hajtsa végre az [Active directory-hitelesítés](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)

>[!NOTE] 
> Az ügyfél-azonosító, a kulcs és a OAuth2.0 Token végpont értékét az Active Directory-alkalmazás csatlakozni az SQL Data Warehouse az Azure Data Lake van szükség. Ezek az értékek beolvasásával részleteit a fenti hivatkozás van.
>Megjegyzés: az Azure Active Directory-alkalmazás regisztráció alkalmazás-azonosító használata az ügyfél-azonosító.

* SQL Server Management Studio vagy SQL Server Data Tools SSMS töltse le és csatlakoztassa [lekérdezés SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* Egy Azure SQL Data Warehouse létrehozása egy kövesse: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* Egy Azure Data Lake Store, vagy a engedélyezhető a titkosítás nélkül. Egy utólagos létrehozni: https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal




## <a name="configure-the-data-source"></a>Konfigurálja az adatforrást
A polybase külső T-SQL-objektumok a hely és a külső adatokra vonatkozó attribútumok megadhatók. A külső objektumok SQL Data Warehouse tárolja, és a th külsőleg tárolt adatokra hivatkoztak.


###  <a name="create-a-credential"></a>Hitelesítő adatok létrehozása
Az Azure Data Lake Store eléréséhez szüksége lesz a következő lépésben használt hitelesítő adatok titkos titkosításához Adatbázisfőkulcs létrehozása.
Ezután hozzon létre egy adatbázishoz kötődő hitelesítő adat, amely tárolja a szolgáltatás egyszerű hitelesítő adatok beállítása az aad-ben. Azok az Ön számára, akik használt PolyBase csatlakozni a Windows Azure Storage Blobs, vegye figyelembe, hogy a credential szintaxis különböző.
Azure Data Lake Store csatlakozni kell **első** hozzon létre egy Azure Active Directory-alkalmazást, hozzáférési kulcs létrehozása, és adja meg az alkalmazás-hozzáférés az Azure Data Lake-erőforráshoz. Utasításokat a következő lépésekkel találhatók [Itt](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```


### <a name="create-the-external-data-source"></a>A külső adatforrás létrehozása
Ezzel [külső ADATFORRÁS létrehozása] [ CREATE EXTERNAL DATA SOURCE] parancs a helyét, valamint az adatok, milyen típusú adatok tárolásához. Az ADL URI az Azure portálon található, az Azure Data Lake Store keresse meg, és tekintse meg a Essentials panelen.

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```



## <a name="configure-data-format"></a>Az adatformátum konfigurálása
Az adatok importálása ADLS, meg kell adnia a külső fájlformátumot. Ez a parancs formátummal kapcsolatos beállítások és az adatok leírására rendelkezik.
Alább példája egy gyakran használt fájlformátum cső tagolt szövegfájl.
Tekintse át a T-SQL dokumentációját teljes listáját [külső FÁJLFORMÁTUM létrehozása][CREATE EXTERNAL FILE FORMAT]

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

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
Most, hogy a megadott forrás- és a fájl adatformátum, készen áll a külső táblák létrehozására. Külső táblák, hogyan működnek együtt a külső adatforráshoz. A polybase rekurzív directory átjárás segítségével annak a könyvtárnak a hely paraméterben megadott alkönyvtáraiban található összes fájl olvasása. Emellett a következő példa bemutatja, hogyan lehet létrehozni az objektumot. Az utasítást, hogy az adatok kezeléséhez vannak ADLS testreszabásához szükséges.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
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
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>A külső tábla kapcsolatos szempontok
Egy külső tábla létrehozása egyszerű, de van néhány apró igénylő tárgyalja.

A polybase-zel adatok betöltése erős típusmegadású. Ez azt jelenti, hogy minden egyes sorára okozhatnak alatt álló adatokat meg kell felelniük a tábla sémadefiníciója.
Ha egy adott sor nem egyezik meg a sémadefiníciót, a sort a terhelés fogja elutasítani.

A REJECT_TYPE és REJECT_VALUE beállítások megadása a sorok számát, vagy az adatok hány százaléka szerepelnie kell a végső tábla teszik lehetővé.
Betöltéskor utasítsa el az érték elérésekor, a betöltés sikertelen lesz. Elutasított sorok leggyakoribb oka schema definíció nem egyeznek.
Például ha az oszlop helytelenül int sémája nincs megadva, ha a fájl egy karakterlánc, minden sor fognak tudni betöltődni.

A hely határozza meg, a legfelső directory adatokat olvasni kívánt.
Ebben az esetben volt /DimProduct/ PolyBase alkönyvtárába alkönyvtárak belül minden adat importálásától. Azure Data Lake store szerepköralapú hozzáférés vezérlés (RBAC) segítségével az adatok hozzáférésének vezérléséhez. Ez azt jelenti, hogy a szolgáltatás egyszerű Olvasás engedéllyel kell rendelkeznie a könyvtárak a hely paraméterben meghatározott, és végső könyvtárat és fájlokat gyermekei. Ez lehetővé teszi, hogy a hitelesítéshez, és olvassa el az adatok betöltése a PolyBase. 

## <a name="load-the-data"></a>Az adatok betöltése
Az adatok betöltése az Azure Data Lake Store használatát a [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] utasítást. A CTAS betöltése a létrehozott szigorú típusmegadású külső tábla használja.

CTAS új táblát hoz létre, és feltölti a select utasítás a eredményekkel. CTAS határozza meg az új tábla ugyanazon oszlopok és adattípusok rendelkezik, mint a select utasítás eredményét. Ha minden oszlop egy külső tábla, az új táblázat a külső tábla az adattípusokat és az oszlopok egy replikát.

Ebben a példában létrehozzuk elosztott kivonattáblát a külső tábla DimProduct_external DimProduct hívása.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása
Alapértelmezés szerint az SQL Data Warehouse tárolja a tábla fürtözött oszlopcentrikus index. A betöltés befejezése után az adatsorok némelyike esetleg nem tömörítse össze az oszlopcentrikus.  Miért Ez akkor fordulhat elő, ennek több van. További tudnivalókért lásd: [oszlopcentrikus Indexek kezelése][manage columnstore indexes].

Optimalizálható a teljesítmény-küszöbérték és a betöltés után oszlopcentrikus tömörítés, építse újra az oszloptárindexet az összes sor tömörítendő kényszerítése a tábla.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Az oszlopcentrikus indexek fenntartása További információkért lásd: a [oszlopcentrikus Indexek kezelése] [ manage columnstore indexes] cikk.

## <a name="optimize-statistics"></a>Statisztika optimalizálása
Érdemes a betöltés után azonnal létrehozza a egyoszlopos statisztikát. Nincsenek néhány statisztikai lehetőségeit. Például ha egyoszlopos statisztikát hoz létre minden egyes oszlophoz a statisztika, hogy hosszú ideig eltarthat. Ha ismeri az egyes oszlopok nem tervezi, hogy a lekérdezés predikátumok kell, kihagyhatja a statisztikák létrehozása az ilyen oszlopokat.

Ha egy oszlop statisztikákat létrehozni minden tábla minden egyes oszlophoz, használhatja a tárolt eljárás kódminta `prc_sqldw_create_stats` a a [statisztika] [ statistics] cikk.

A következő példa: statisztikák létrehozása az jó kiindulási pont. Minden egyes oszlopának a dimenziótáblában és a ténytáblák csatlakozó oszlopainak egyoszlopos statisztikát hoz. Mindig hozzáadhat egy vagy több oszlop statisztikai más tény táblaoszlopok később.


## <a name="achievement-unlocked"></a>Zárolása feloldva elérésének!
Az Azure SQL Data Warehouse sikeresen betöltött adatok. Remek munka!

## <a name="next-steps"></a>Következő lépések
Adatok betöltése az SQL Data Warehouse data warehouse megoldás fejlesztése első lépése. Tekintse meg a fejlesztői erőforrások a [táblák](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) és [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md).


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
