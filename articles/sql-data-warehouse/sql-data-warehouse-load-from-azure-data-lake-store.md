---
title: 'Oktatóanyag: Azure SQL Data warehouse betöltése az Azure Data Lake Store |} Microsoft Docs'
description: A PolyBase külső táblák segítségével az adatok betöltése az Azure Data Lake Store az Azure SQL Data warehouse-bA.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c6030d1951c22dddfe6df01225c63cf503a370ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188378"
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Adatok betöltése az Azure Data Lake Store az SQL Data Warehouse
A PolyBase külső táblák segítségével az adatok betöltése az Azure Data Lake Store az Azure SQL Data warehouse-bA. Bár az adatok tárolva ADLS ad hoc lekérdezéseket is futtathat, azt javasoljuk az adatok importálása az SQL Data Warehouse a legjobb teljesítmény érdekében.

> [!div class="checklist"]
> * Az Azure Data Lake Store betöltéséhez szükséges adatbázis-objektumok létrehozása.
> * Csatlakozás az Azure Data Lake Store-címtárhoz.
> * Adatok betöltése az Azure SQL Data warehouse-bA.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek
Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.

Ez az oktatóanyag futtatásához szükséges:

* Az Azure Active Directory-alkalmazást a szolgáltatások közötti hitelesítéshez használandó. Létrehozásához hajtsa végre az [Active directory-hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Az ügyfél-azonosító, a kulcs és a OAuth2.0 Token végpont értékét az Active Directory-alkalmazás csatlakozni az SQL Data Warehouse az Azure Data Lake van szükség. Ezek az értékek beolvasásával részleteit a fenti hivatkozás van. Az Azure Active Directory-alkalmazás regisztráció Alkalmazásazonosító használják az ügyfél-azonosító.
> 

* Egy Azure SQL Data Warehouse. Lásd: [létrehozása és a lekérdezés és az Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Egy Azure Data Lake Store. Lásd: [Ismerkedés az Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Hitelesítő adatok létrehozása
Az Azure Data Lake Store eléréséhez szüksége lesz a következő lépésben használt hitelesítő adatok titkos titkosításához Adatbázisfőkulcs létrehozása. Ezután hozzon létre egy Database Scoped Credential, amely tárolja a szolgáltatás egyszerű hitelesítő adatok beállítása az aad-ben. Azok az Ön számára, akik használt PolyBase csatlakozni a Windows Azure Storage Blobs, vegye figyelembe, hogy a credential szintaxis különböző.

Azure Data Lake Store csatlakozni kell **első** hozzon létre egy Azure Active Directory-alkalmazást, hozzáférési kulcs létrehozása, és adja meg az alkalmazás-hozzáférés az Azure Data Lake-erőforráshoz. Útmutatásért lásd: [hitelesítés az Azure Data Lake Store használatával Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

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

## <a name="create-the-external-data-source"></a>A külső adatforrás létrehozása
Ezzel [külső ADATFORRÁS létrehozása](/sql/t-sql/statements/create-external-data-source-transact-sql) parancs használatával tárolja az adatok helyét. 

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
Az adatok importálása ADLS, meg kell adnia a külső fájlformátumot. Ezt az objektumot határozza meg, hogyan az ADLS írja a fájlt.
A teljes listát, tekintse meg a T-SQL dokumentációról [külső FÁJLFORMÁTUM létrehozása](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Most, hogy a megadott forrás- és a fájl adatformátum, készen áll a külső táblák létrehozására. Külső táblák, hogyan működnek együtt a külső adatforráshoz. A hely paraméter adhat meg egy fájl vagy könyvtár. Meghatározza egy könyvtárat, ha a könyvtárban található összes fájl lesz betöltve.

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

Külső táblák erős típusmegadású vannak. Ez azt jelenti, hogy minden egyes sorára okozhatnak alatt álló adatokat meg kell felelniük a tábla sémadefiníciója.
Egy sor nem egyezik meg a sémadefiníciót, ha a terhelést a sor fogja elutasítani.

A REJECT_TYPE és REJECT_VALUE beállítások megadása a sorok számát, vagy az adatok hány százaléka szerepelnie kell a végső tábla teszik lehetővé. Betöltéskor utasítsa el az érték elérésekor, a betöltés sikertelen lesz. Elutasított sorok leggyakoribb oka schema definíció nem egyeznek. Például ha az oszlop helytelenül int sémája nincs megadva, ha a fájl egy karakterlánc, minden sor fognak tudni betöltődni.

 Azure Data Lake store szerepköralapú hozzáférés vezérlés (RBAC) segítségével az adatok hozzáférésének vezérléséhez. Ez azt jelenti, hogy a szolgáltatás egyszerű Olvasás engedéllyel kell rendelkeznie a könyvtárak a hely paraméterben meghatározott, és végső könyvtárat és fájlokat gyermekei. Ez lehetővé teszi, hogy a hitelesítéshez, és adott adatok betöltése a PolyBase. 

## <a name="load-the-data"></a>Az adatok betöltése
Az adatok betöltése az Azure Data Lake Store használatát a [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) utasítást. 

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
Alapértelmezés szerint az SQL Data Warehouse tárolja a tábla fürtözött oszlopcentrikus index. A betöltés befejezése után az adatsorok némelyike esetleg nem tömörítse össze az oszlopcentrikus.  Miért Ez akkor fordulhat elő, ennek több van. További tudnivalókért lásd: [oszlopcentrikus Indexek kezelése](sql-data-warehouse-tables-index.md).

Optimalizálható a teljesítmény-küszöbérték és a betöltés után oszlopcentrikus tömörítés, építse újra az oszloptárindexet az összes sor tömörítendő kényszerítése a tábla.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statisztika optimalizálása
Érdemes a betöltés után azonnal létrehozza a egyoszlopos statisztikát. Nincsenek néhány statisztikai lehetőségeit. Például ha egyoszlopos statisztikát hoz létre minden egyes oszlophoz a statisztika, hogy hosszú ideig eltarthat. Ha ismeri az egyes oszlopok nem tervezi, hogy a lekérdezés predikátumok kell, kihagyhatja a statisztikák létrehozása az ilyen oszlopokat.

Ha egy oszlop statisztikákat létrehozni minden tábla minden egyes oszlophoz, használhatja a tárolt eljárás kódminta `prc_sqldw_create_stats` a a [statisztika](sql-data-warehouse-tables-statistics.md) cikk.

A következő példa: statisztikák létrehozása az jó kiindulási pont. Minden egyes oszlopának a dimenziótáblában és a ténytáblák csatlakozó oszlopainak egyoszlopos statisztikát hoz. Mindig hozzáadhat egy vagy több oszlop statisztikai más tény táblaoszlopok később.

## <a name="achievement-unlocked"></a>Zárolása feloldva elérésének!
Az Azure SQL Data Warehouse sikeresen betöltött adatok. Remek munka!

## <a name="next-steps"></a>További lépések 
Ebben az oktatóanyagban létre Azure Data Lake Store-ban tárolt adatok struktúra külső táblák, és a PolyBase CREATE TABLE AS SELECT utasítás használatával adatok betöltése az az adatraktár. 

A következőket hajtotta végre:
> [!div class="checklist"]
> * Szükséges betölteni az Azure Data Lake Store a létrehozott adatbázis-objektumokat.
> * Az Azure Data Lake Store-címtárhoz csatlakozik.
> * Az Azure SQL Data Warehouse betöltött adatokról.
> 

Adatok betöltése az SQL Data Warehouse data warehouse megoldás fejlesztése első lépése. Tekintse meg a fejlesztői erőforrások.

> [!div class="nextstepaction"]
>[Ismerje meg, hogyan fejleszthet az SQL Data Warehouse táblák](sql-data-warehouse-tables-overview.md)




