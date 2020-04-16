---
title: Azure Data Factory-összekötők hibaelhárítása
description: Ismerje meg, hogyan háríthatja el az összekötővel kapcsolatos problémákat az Azure Data Factoryban.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 62ad337646cf3fc0bbe4305dccad5adb56f8ee15
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410234"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory-összekötők hibaelhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az Azure Data Factory összekötőivel kapcsolatos gyakori hibaelhárítási módszereket ismerteti.
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Hibakód: Az AzureBlobOperation sikertelen

- **Üzenet**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Ok**: Blob tárolási művelet hit probléma.

- **Ajánlás**: Ellenőrizze a hibát részletesen. Tekintse meg a https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codesblob súgódokumentumát: . Ha segítségre van szüksége, forduljon a tárolócsapathoz.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Hibakód: AzureBlobServiceNotReturnExpectedDataLength

- **Üzenet**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Hibakód: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Üzenet**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Hibakód: AzureStorageOperationFailedConcurrentWrite

- **Üzenet**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: A kérelem mérete túl nagy

- **Jelenségek**: Adatokat másol az Azure Cosmos DB-be az alapértelmezett írási kötegmérettel, és a ***"Request size is too large" hibaüzenetjelenik*** meg.

- **Ok:** A Cosmos DB egyetlen kérelem méretét 2 MB-ra korlátozza. A képlet: Kérelem mérete = Egydokumentum mérete * Írási kötegméret. Ha a dokumentum mérete nagy, az alapértelmezett viselkedés túl nagy kérésméretet eredményez. Az írási köteg méretét beállíthatja.

- **Megoldás**: A másolási tevékenység fogadójában csökkentse az "Írási kötegméret" értéket (az alapértelmezett érték 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Hibaüzenet: Egyedi indexmegkötés-megsértés

- **Jelenségek**: Adatok Cosmos DB-be másolásakor a következő hibaüzenetjelenik meg:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Ok**: Két oka lehet:

    - Ha a **Beszúrás** írási viselkedésként használja, ez a hiba azt jelenti, hogy a forrásadatok sorai/objektumai azonos azonosítóval rendelkeznek.

    - Ha **az Upsert-et** írási viselkedésként használja, és egy másik egyedi kulcsot állít be a tárolóhoz, ez a hiba azt jelenti, hogy a forrásadatok különböző azonosítókkal rendelkező sorokkal/objektumokkal rendelkeznek, de a megadott egyedi kulcs értéke megegyezik.

- **Állásfoglalás**: 

    - Az 1 ok esetén állítsa be **az Upsert-et** írási viselkedésként.
    - A 2-es ok esetén győződjön meg arról, hogy minden dokumentum különböző értékkel rendelkezik a definiált egyedi kulcshoz.

### <a name="error-message-request-rate-is-large"></a>Hibaüzenet: A kérelmek aránya nagy

- **Jelenségek**: Adatok Cosmos DB-be másolásakor a következő hibaüzenetjelenik meg:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Ok:** A használt kérelem egységek nagyobbak, mint a rendelkezésre álló RU konfigurálva Cosmos DB. Ismerje meg, hogyan Cosmos DB kiszámítja RU [innen](../cosmos-db/request-units.md#request-unit-considerations).

- **Megoldás**: Itt van két megoldás:

    1. **Növelje a tároló RU** nagyobb értéket a Cosmos DB, amely javítja a másolási tevékenység teljesítményét, bár több költséget a Cosmos DB. 

    2. Csökkentse **a writeBatchSize** értéket kisebb értékre (például 1000- re), és állítsa a **parallelCopies** kisebb értékre (például 1, amely a másolási futtatási teljesítményt rosszabbá teszi, mint az aktuális, de nem jár több költséggel a Cosmos DB-ben.

### <a name="column-missing-in-column-mapping"></a>Hiányzik az oszlopleképezésből hiányzó oszlop

- **Jelenségek**: Amikor a Cosmos DB sémáját importálja az oszlopleképezéshez, néhány oszlop hiányzik. 

- **Ok**: Az ADF az első 10 Cosmos DB-dokumentum sérelméből következtet. Ha egyes oszlopok/tulajdonságok nem rendelkeznek értékkel ezekben a dokumentumokban, akkor az ADF nem fogja észlelni őket, így nem jelennek meg.

- **Megoldás**: A lekérdezést az alábbi módon hangolhatja be, hogy az oszlop üres értékkel jelenjen meg az eredményhalmazban: (tegyük fel, hogy a "lehetetlen" oszlop hiányzik az első 10 dokumentumból). Azt is megteheti, hogy manuálisan adja hozzá az oszlopot a leképezéshez.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hibaüzenet: Az olvasó GuidRepresentation ábrázolása CSharpLegacy

- **Jelenségek**: Amikor adatokat másol a Cosmos DB MongoAPI/MongoDB uuid mezővel, a következő hibát találja be:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Ok**: Kétféleképpen lehet képviselni uuid a BSON - UuidStardard és UuidLegacy. Alapértelmezés szerint az UuidLegacy az adatok olvasására szolgál. Akkor jön a hiba, ha az UUID adatai a MongoDB-ban UuidStandard.

- **Megoldás**: A MongoDB kapcsolati karakterláncban adja hozzá az "**uuidRepresentation=standard " opciót.** További információ: [MongoDB connection string](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="error-code--adlsgen2operationfailed"></a>Hibakód: AdlsGen2OperationFailed

- **Üzenet**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Ok**: Az ADLS Gen2 eldobja a hibát, amely azt jelzi, hogy a művelet sikertelen volt.

- **Javaslat**: Ellenőrizze az ADLS Gen2 által eldobott részletes hibaüzenetet. Ha átmeneti hiba okozza, próbálkozzon újra. Ha további segítségre van szüksége, forduljon az Azure Storage támogatási szolgálatához, és adja meg a kérelemazonosítót hibaüzenetben.

- **Ok:** Ha a hibaüzenet "Tiltott" üzenetet tartalmaz, előfordulhat, hogy a használt egyszerű szolgáltatás vagy felügyelt identitás nem rendelkezik elegendő engedéllyel az ADLS Gen2 eléréséhez.

- **Ajánlás**: Olvassa el https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticationa súgódokumentumot: .

- **Ok**: Ha a hibaüzenet "InternalServerError" üzenetet tartalmaz, a hibát az ADLS Gen2 adja vissza.

- **Javaslat**: Átmeneti hiba okozhatja, próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az Azure Storage támogatási szolgálatához, és adja meg a kérelem azonosítóját a hibaüzenetben.


### <a name="error-code--adlsgen2invalidurl"></a>Hibakód: AdlsGen2InvalidUrl

- **Üzenet**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Hibakód: AdlsGen2InvalidFolderPath

- **Üzenet**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Hibakód: AdlsGen2OperationFailedConcurrentWrite

- **Üzenet**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Hibakód: AdlsGen2TimeoutError

- **Üzenet**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hibaüzenet: A távoli kiszolgáló hibát adott vissza: (403) Tiltott

- **Jelenségek**: A másolási tevékenység a következő hibával sikertelen: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Ok:** Az egyik lehetséges ok az, hogy az egyszerű szolgáltatás vagy a felügyelt identitás használata nem rendelkezik engedéllyel az adott mappához/fájlhoz való hozzáféréshez.

- **Megoldás:** Adjon megfelelő engedélyeket a másolni kívánt mappákra és almappákra. Olvassa el ezt a [doc](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hibaüzenet: Nem sikerült beszerezni a hozzáférési jogkivonatot az egyszerű szolgáltatás használatával. ADAL hiba: service_unavailable

- **Jelenségek**: A másolási tevékenység a következő hibával sikertelen:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Ok:** Ha az Azure Active Directory tulajdonában lévő Service Token Server (STS) nem érhető el, azaz túl elfoglalt a kérelmek kezeléséhez, akkor egy HTTP-hibát 503-as értéket ad vissza. 

- **Megoldás**: A másolási tevékenység et néhány perc elteltével futtassa újra.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Adattárház/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Hibakód: SqlFailedToConnect

- **Üzenet**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Ok**: Ha a hibaüzenet "SqlException" üzenetet tartalmaz, az SQL Database eldobja a hibát, amely azt jelzi, hogy egy adott művelet sikertelen volt.

- **Javaslat**: Kérjük, keressen az SQL hibakód https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsebben a referencia doc további részletekért: . Ha további segítségre van szüksége, forduljon az Azure SQL-támogatáshoz.

- **Ok**: Ha a hibaüzenet "Ügyfél IP-címmel"...' nem érhető el a kiszolgáló", és az Azure SQL Database-hez próbál csatlakozni, általában az Azure SQL Database tűzfal problémája okozza.

- **Javaslat:** Az Azure SQL Server tűzfalkonfigurációjában engedélyezze az "Azure-szolgáltatások és -erőforrások hozzáférését a kiszolgálóhoz" beállításengedélyezéséhez. Referencia dokumentum: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Hibakód: SqlOperationFailed

- **Üzenet**:`A database operation failed. Please search error to get more details.`

- **Ok**: Ha a hibaüzenet "SqlException" üzenetet tartalmaz, az SQL Database eldobja a hibát, amely azt jelzi, hogy egy adott művelet sikertelen volt.

- **Javaslat**: Ha az SQL-hiba nem egyértelmű, próbálja meg módosítani az adatbázist a legújabb kompatibilitási szintre "150". Ez dobja legújabb verziós SQL hibákat. Kérjük, olvassa https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompatel a részleteket doc: .
        Az SQL-problémák elhárításához további részletekért keressen az SQL https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorshibakód alapján ebben a kézikönyvben: . Ha további segítségre van szüksége, forduljon az Azure SQL-támogatáshoz.

- **Ok**: Ha a hibaüzenet "PdwManagedToNativeInteropException" értéket tartalmaz, általában a forrás- és a fogadóoszlop mérete közötti eltérés okozza.

- **Javaslat**: Ellenőrizze a forrás- és a fogadóoszlopok méretét. Ha további segítségre van szüksége, forduljon az Azure SQL-támogatáshoz.

- **Ok**: Ha a hibaüzenet "InvalidOperationException" üzenetet tartalmaz, általában érvénytelen bemeneti adatok okozzák.

- **Javaslat**: Annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységen, amely átirányíthatja a problémás sorokat a tárolóba további vizsgálat céljából. Referencia dokumentum: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Hibakód: SqlUnauthorizedAccess

- **Üzenet**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Ok:** A hitelesítő adatok helytelenek, vagy a bejelentkezési fiók nem fér hozzá az SQL Database-hez.

- **Javaslat**: Ellenőrizze, hogy a bejelentkezési fiók elegendő engedéllyel rendelkezik az SQL-adatbázis eléréséhez.


### <a name="error-code--sqlopenconnectiontimeout"></a>Hibakód: SqlOpenConnectionTimeout

- **Üzenet**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Ok**: Lehet SQL Database átmeneti hiba.

- **Javaslat:** Próbálkozzon újra a nagyobb kapcsolatidő-túllépési időtúllépési értékkel rendelkező csatolt szolgáltatáskapcsolati karakterlánc frissítésével.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Hibakód: SqlAutoCreateTableTypeMapFailed

- **Üzenet**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Ok**: Az automatikus létrehozási tábla nem felel meg a forráskövetelménynek.

- **Javaslat**: Frissítse az oszloptípust a "leképezések" mezőben, vagy manuálisan hozza létre a fogadótáblát a célkiszolgálón.


### <a name="error-code--sqldatatypenotsupported"></a>Hibakód: SqlDataTypeNotSupported

- **Üzenet**:`A database operation failed. Check the SQL errors.`

- **Ok:** Ha a probléma SQL-forrásból történik, és a hiba az SqlDateTime túlcsordulásával kapcsolatos, az adatérték a logikai típustartományon (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM) túlvan.

- **Javaslat:** A típust karakterláncba adja a forrás SQL-lekérdezésben, vagy a másolási tevékenység oszlopban az oszloptípust "Karakterlánc" -ra módosítja.

- **Ok:** Ha a probléma az SQL-fogadóban fordul elő, és a hiba az SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a fogadótábla megengedett tartománya felett van.

- **Javaslat**: Frissítse a megfelelő oszloptípust "datetime2" típusra a fogadótáblában.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Hibakód: SqlInvalidDbStoredEljárás

- **Üzenet**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Ok**: A megadott tárolt eljárás érvénytelen. Ezt az okozhatja, hogy a tárolt eljárás nem ad vissza adatokat.

- **Javaslat**: Ellenőrizze a tárolt eljárást az SQL Tools. Győződjön meg arról, hogy a tárolt eljárás vissza tudja adni az adatokat.


### <a name="error-code--sqlinvaliddbquerystring"></a>Hibakód: SqlInvalidDbQueryString

- **Üzenet**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Ok**: A megadott SQL-lekérdezés érvénytelen. Ezt az okozhatja, hogy a lekérdezés nem ad vissza adatokat

- **Javaslat**: Érvényesítse az SQL-lekérdezést az SQL Tools segítségével. Győződjön meg arról, hogy a lekérdezés adatokat tud visszaadni.


### <a name="error-code--sqlinvalidcolumnname"></a>Hibakód: SqlInvalidColumnName

- **Üzenet**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Ok**: Nem található az oszlop. Lehetséges konfiguráció rossz.

- **Javaslat:** Ellenőrizze az oszlopot a lekérdezésben, a "struktúra" az adatkészletben és a "leképezések" tevékenységben.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Hibakód: SqlColumnNameMismatchByCaseSensitive

- **Üzenet**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Hibakód: SqlBatchWriteTimeout

- **Üzenet**:`Timeouts in SQL write operation.`

- **Ok**: Lehet SQL Database átmeneti hiba.

- **Javaslat**: Próbálkozzon újra. Ha probléma repro, forduljon az Azure SQL-támogatás.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Hibakód: SqlBatchWriteTransactionFailed

- **Üzenet**:`SQL transaction commits failed`

- **Ok:** Ha a kivétel részletei folyamatosan megmondják a tranzakció időhalátrát, az integrációs futásidő és az adatbázis közötti hálózati késés magasabb, mint az alapértelmezett küszöbérték 30 másodperc.

- **Javaslat:** Frissítse az Sql-hez kapcsolódó szolgáltatás kapcsolati karakterláncát a "kapcsolat időtúllépési" értéke 120 vagy magasabb, és futtassa újra a tevékenységet.

- **Ok**: Ha a kivétel részletei időnként azt mondják, hogy megszakadt az sqlconnection, lehet, hogy csak átmeneti hálózati hiba vagy SQL Database oldalprobléma

- **Javaslat:** Próbálkozzon újra a tevékenységgel, és tekintse át az SQL Database oldalmetrikáit.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Hibakód: SqlBulkCopyInvalidColumnLength

- **Üzenet**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Ok:** Az SQL tömeges másolása nem sikerült, mert érvénytelen oszlophosszt kapott az bcp-ügyféltől.

- **Javaslat**: Annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységen, amely átirányíthatja a problémás sorokat a tárolóba további vizsgálat céljából. Referencia dokumentum: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Hibakód: SqlConnectionIsClosed

- **Üzenet**:`The connection is closed by SQL Database.`

- **Ok:** Az SQL-kapcsolatot az SQL Database bezárja, ha a nagy egyidejű futtatás és a kiszolgáló megszakítja a kapcsolatot.

- **Javaslat**: A távoli kiszolgáló bezárta az SQL-kapcsolatot. Próbálkozzon újra. Ha probléma repro, forduljon az Azure SQL-támogatás.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Hibakód: SqlCreateTableFailedUnsupportedType

- **Üzenet**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hibaüzenet: Az átalakítás nem sikerült, amikor karakterkarakterláncból egyedi azonosítóvá konvertált

- **Jelenségek**: Amikor táblázatos adatforrásból (például SQL Serverből) másolja az adatokat az Azure SQL Data Warehouse-ba szakaszos másolással és polybase használatával, a következő hibaüzenetjelenik meg:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Ok:** Az Azure SQL Data Warehouse PolyBase nem tudja konvertálni az üres karakterláncot GUID-ra.

- **Megoldás**: A Másolási tevékenység fogadójában a Polybase-beállítások csoportban állítsa a **"Type type default**" beállítást hamisra.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hibaüzenet: Várt adattípus: DECIMAL(x,x), Jogsértő érték

- **Jelenségek**: Ha táblázatos adatforrásból (például SQL Serverből) másolja az adatokat az SQL DW-be szakaszos másolással és polybase használatával, a következő hibaüzenet jelenik meg:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Ok:** Az Azure SQL Data Warehouse Polybase nem tud üres karakterláncot (null értéket) beszúrni a decimális oszlopba.

- **Megoldás**: A Másolási tevékenység fogadójában a Polybase-beállítások csoportban állítsa a **"Type type default**" beállítást hamisra.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Hibaüzenet: Java-kivételüzenet:HdfsBridge::CreateRecordReader

- **Jelenségek:** Adatokat másol az Azure SQL Data Warehouse-ba a PolyBase használatával, és a következő hibát találja el:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Ok**: Ennek oka az, hogy a séma (teljes oszlopszélesség) túl nagy (1 MB-nál nagyobb). Ellenőrizze a cél SQL DW tábla sémáját az összes oszlop méretének hozzáadásával:

    - Int -> 4 bájt
    - Bigint -> 8 bájt
    - Varchar(n),char(n),bináris(n), varbinary(n) -> n bájt
    - Nvarchar(n), nchar(n) -> n*2 bájt
    - Dátum -> 6 bájt
    - Datetime/(2), smalldatetime -> 16 bájt
    - Datetimeoffset -> 20 bájt
    - Decimális -> 19 bájt
    - Úszó -> 8 bájt
    - Pénz -> 8 bájt
    - Smallmoney -> 4 byte
    - Real -> 4 bájt
    - Smallint -> 2 bájt
    - Idő -> 12 bájt
    - Tinyint -> 1 bájt

- **Felbontás**: Az oszlop szélességének csökkentése 1 MB-nál kisebbre

- Vagy használjon tömeges lapkamegközelítést a Polybase letiltásával

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hibaüzenet: A HTTP feltételes fejléc(ek) használatával megadott feltétel nem teljesül.

- **Jelenségek**: Sql-lekérdezéssel adatokat kell lekérdezni az Azure SQL Data Warehouse-ból, és a következő hibaüzenetet használhatja:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Ok:** Az Azure SQL Data Warehouse találati probléma lekérdezése a külső tábla az Azure Storage-ban.

- **Megoldás**: Futtassa ugyanazt a lekérdezést az SSMS-ben, és ellenőrizze, hogy ugyanazt az eredményt látja-e. Ha igen, nyisson egy támogatási jegyet az Azure SQL Data Warehouse-hoz kapcsolódóan, és adja meg az SQL DW-kiszolgáló és az adatbázis nevét a további hibaelhárításhoz.
            

## <a name="delimited-text-format"></a>Tagolt szöveg formátuma

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Hibakód: DelimitedTextColumnNameNotAllowNull

- **Üzenet**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Ok**: Amikor a tevékenységben a "firstRowAsHeader" beállítás van beállítva, az első sor lesz az oszlopnév. Ez a hiba azt jelenti, hogy az első sor üres értéket tartalmaz. Például: "ColumnA,,ColumnB".

- **Javaslat**: Ellenőrizze az első sort, és rögzítse az értéket, ha üres érték van.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Hibakód: DelimitedTextMoreColumnsThanDefined

- **Üzenet**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Ok**: A problémás sor oszlopszáma nagy, mint az első sor oszlopszáma. Ezt okozhatja az adatprobléma vagy a helytelen oszlophatároló/idézőjel beállításai.

- **Ajánlás**: Kérjük, olvassa el a sorok számát hibaüzenetben, ellenőrizze a sor oszlopát, és javítsa ki az adatokat.

- **Ok:** Ha a várt oszlopszám "1" a hibaüzenetben, lehetséges, hogy helytelen tömörítési vagy formázási beállításokat adott meg, ami miatt az ADF helytelenül elemezte a fájlt(oka)t.

- **Javaslat**: Ellenőrizze a formátumbeállításokat, hogy biztosan megegyezik-e a forrásfájl(ok)kal.

- **Ok:** Ha a forrás mappa, lehetséges, hogy a megadott mappa alatti fájlok sémája eltérő.

- **Javaslat**: Győződjön meg arról, hogy az adott mappában lévő fájlok azonos sémával rendelkeznek.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Hibakód: DelimitedTextIncorrectRowDelimiter

- **Üzenet**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Hibakód: DelimitedTextTooLargeColumnCount

- **Üzenet**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Hibakód: DelimitedTextInvalidSettings

- **Üzenet**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Hibakód: DynamicsCreateServiceClientError

- **Üzenet**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Ok**: Ez egy átmeneti probléma a dynamics kiszolgáló oldalán.

- **Javaslat**: Futtassa újra a folyamatot. Ha folyamatosan nem sikerül, próbálja csökkenteni a párhuzamosság. Ha még mindig sikertelen, forduljon a Dynamics támogatási szolgálatához.



## <a name="json-format"></a>JSON formátum

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Hibakód: JsonInvalidArrayPathDefinition

- **Üzenet**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Hibakód: JsonEmptyJObjectData

- **Üzenet**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Hibakód: JsonNullValueInPathDefinition

- **Üzenet**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Hibakód: JsonUnsupportedHierarchicalComplexValue

- **Üzenet**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Hibakód: JsonConflictPartitionDiscoverySchema

- **Üzenet**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Hibakód: JsonInvalidDataFormat

- **Üzenet**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Hibakód: JsonInvalidDataMixedArrayAndObject

- **Üzenet**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parketta formátuma

### <a name="error-code--parquetjavainvocationexception"></a>Hibakód: ParquetJavaInvocationException

- **Üzenet**:`An error occurred when invoking java, message: %javaException;.`

- **Ok**: Ha a hibaüzenet a "java.lang.OutOfMemory", a "Java halomterület" és a "doubleCapacity" értéket tartalmazza, általában memóriakezelési probléma az integrációs futásidejű verzióban.

- **Javaslat:** Ha önkiszolgáló integrációs futásidőt használ, és a verzió korábbi, mint a 3.20.7159.1, javasoljuk, hogy frissítsen a legújabb verzióra.

- **Ok**: Ha a hibaüzenet a "java.lang.OutOfMemory" fájlt tartalmazza, az integrációs futásidejű nem rendelkezik elegendő erőforrással a fájl(ok) feldolgozásához.

- **Javaslat**: Korlátozza az egyidejű futtatások az integrációs futásidejű. Az önkiszolgáló integrációs futásidejű, felskálázás egy nagy teljesítményű gép memóriája egyenlő vagy nagyobb, mint 8 GB.

- **Ok**: Ha a hibaüzenet "NullPointerReference" üzenetet tartalmaz, akkor lehetséges, hogy átmeneti hiba.

- **Javaslat**: Próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.


### <a name="error-code--parquetinvalidfile"></a>Hibakód: ParquetInvalidFile

- **Üzenet**:`File is not a valid parquet file.`

- **Ok**: Parkettafájl-probléma.

- **Javaslat**: Ellenőrizze, hogy a bemenet érvényes parkettafájl-e.


### <a name="error-code--parquetnotsupportedtype"></a>Hibakód: ParquetNotSupportedType

- **Üzenet**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Ok:** A parketta formátum nem támogatott az Azure Data Factory.

- **Javaslat**: Ellenőrizze a forrásadatokat. Nézze meg a https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecsdoc: .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Hibakód: ParquetMissedDecimalPrecisionScale

- **Üzenet**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Ok:** Próbálja meg elemezni a szám pontosságát és léptékét, de ilyen információ nem áll rendelkezésre.

- **Javaslat**: A "forrás" nem ad vissza megfelelő pontosságot és skálát. Ellenőrizze a problémaoszlop pontosságát és léptékét.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Hibakód: ParquetInvalidDecimalPrecisionScale

- **Üzenet**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Ok**: A séma érvénytelen.

- **Javaslat**: Ellenőrizze a problémaoszlop pontosságát és léptékét.


### <a name="error-code--parquetcolumnnotfound"></a>Hibakód: ParquetColumnNotFound

- **Üzenet**:`Column %column; does not exist in Parquet file.`

- **Ok**: A forrásséma nem egyezik a fogadósével.

- **Ajánlás**: Ellenőrizze a "leképezések" a "tevékenység". Győződjön meg arról, hogy a forrásoszlop leképezhető a jobb fogadó oszlopra.


### <a name="error-code--parquetinvaliddataformat"></a>Hibakód: ParquetInvalidDataFormat

- **Üzenet**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **Ok**: Az adatok nem konvertálhatók a mappings.source-ban megadott típussá.

- **Javaslat**: Ellenőrizze duplán a forrásadatokat, vagy adja meg az oszlop megfelelő adattípusát a másolási tevékenységoszlop-leképezésben. Nézze meg a https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecsdoc: .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Hibakód: ParquetDataCountNotMatchColumnCount

- **Üzenet**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Ok**: A forrásoszlopok száma és a fogadóoszlopszám nem egyezik

- **Javaslat:** A forrásoszlopok számának kettős ellenőrzése megegyezik a "leképezés" fogadóoszlopszámával.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Hibakód: ParquetDataTypeNotMatchColumnType

- **Üzenet**: A(z) %srcType; nem egyezik a megadott oszloptípussal: %dstType; a következő oszlopban: "%columnIndex;".

- **Ok**: A forrásból származó adatok nem konvertálhatók a fogadóban definiált gépelt adatokká

- **Javaslat**: Adjon meg megfelelő típust a mapping.sink.Recommendation : Please specify a correct type in mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Hibakód: ParquetBridgeInvalidData

- **Üzenet**:`%message;`

- **Ok**: Az adatérték túllépési

- **Javaslat**: Próbálkozzon újra. Ha a probléma továbbra is fennáll, kérjük, lépjen kapcsolatba velünk.


### <a name="error-code--parquetunsupportedinterpretation"></a>Hibakód: ParquetUnsupportedInterpretation

- **Üzenet**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Ok**: Nem támogatott forgatókönyv

- **Javaslat**: A "ParquetInterpretFor" nem lehet "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Hibakód: ParquetUnsupportFileLevelCompressionOption

- **Üzenet**:`File level compression is not supported for Parquet.`

- **Ok**: Nem támogatott forgatókönyv

- **Javaslat**: Távolítsa el a "CompressionType" a hasznos teher.



## <a name="general-copy-activity-error"></a>Általános másolási tevékenység hiba

### <a name="error-code--jrenotfound"></a>Hibakód: JreNotFound

- **Üzenet**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Ok:** A saját üzemeltetésű integrációs futásidejű nem találja a Java Runtime. A Java Runtime adott forrás olvasásához szükséges.

- **Javaslat**: Ellenőrizze az integrációs futásidejű környezetet, a referencia-dokumentum:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Hibakód: A helyettesítő karakterhez való elérési karakternem támogatott

- **Üzenet**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Ok:** A fogadó adatkészlet nem támogatja a helyettesítő karaktert.

- **Javaslat**: Ellenőrizze a fogadó adatkészletet, és javítsa ki a helyettesítő karakter érték nélküli elérési utat.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Hibakód: MappingInvalidPropertyWithEmptyValue

- **Üzenet**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Hibakód: MappingInvalidPropertyWithNamePathAndOrdinalal

- **Üzenet**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Hibakód: MappingDuplicatedOrdinal

- **Üzenet**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Hibakód: MappingInvalidOrdinalForSinkColumn

- **Üzenet**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálkozzon az alábbi forrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatáskérések](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack túlcsordulás fórum Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter információk a Data Factory-ról](https://twitter.com/hashtag/DataFactory)
            
