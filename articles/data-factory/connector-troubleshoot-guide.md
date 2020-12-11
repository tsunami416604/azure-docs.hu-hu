---
title: Azure Data Factory-összekötők hibaelhárítása
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory összekötői problémáit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: a7a81a742922d45be965c7f73e3cb910d0ef989a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109290"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory-összekötők hibaelhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory összekötők gyakori hibaelhárítási módszereit vizsgálja.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Hibakód: AzureBlobOperationFailed

- **Üzenet**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **OK**: a blob Storage-művelettel kapcsolatos probléma lépett fel.

- **Javaslat**: vizsgálja meg a hibát a részletek között. Tekintse meg a blob Súgó dokumentumát: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Ha segítségre van szüksége, lépjen kapcsolatba a Storage csapatával.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Hibakód: AzureBlobServiceNotReturnExpectedDataLength

- **Üzenet**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Hibakód: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Üzenet**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Hibakód: AzureStorageOperationFailedConcurrentWrite

- **Üzenet**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="invalid-property-during-copy-activity"></a>Érvénytelen tulajdonság a másolási tevékenység során

- **Üzenet**:  `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **OK**: az adatkészletben definiált típus nem konzisztens a másolási tevékenységben definiált forrás/fogadó típussal.

- **Megoldás**: szerkessze az adatkészlet vagy a folyamat JSON-definícióját, hogy a típusok konzisztensek legyenek, majd futtassa újra a telepítést.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: a kérelem mérete túl nagy

- **Tünetek**: az adatmásolás Azure Cosmos DBba az alapértelmezett írási köteg méretével, és a *"**kérés mérete túl nagy**"*.

- **OK**: a Cosmos db egy kérés méretét 2 MB-ra korlátozza. A képlet:, kérelem mérete = egydokumentumos méret * írási köteg mérete. Ha a dokumentum mérete nagy, az alapértelmezett viselkedés túl nagy méretű kérések méretét eredményezi. Beállíthatja az írási köteg méretét.

- **Megoldás**: a másolási tevékenység fogadójában csökkentse a "batch méretének megírása" értéket (az alapértelmezett érték 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Hibaüzenet: egyedi index megkötésének megsértése

- **Tünetek**: az adatok Cosmos DBba való másolása során a következő hibaüzenetet kapta:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **OK**: két lehetséges oka van:

    - Ha a **Beszúrás** írási viselkedést használja, ez a hiba azt jelenti, hogy az adatforráshoz tartozó sorok/objektumok UGYANAZZAL az azonosítóval rendelkeznek.

    - Ha a **Upsert** -et írási viselkedésként használja, és egy másik egyedi kulcsot állít be a tárolóhoz, akkor ez a hiba azt jelenti, hogy az adatforráshoz tartozó sorok/objektumok eltérő azonosítókkal rendelkeznek, de a definiált egyedi kulcshoz ugyanaz az érték tartozik.

- **Megoldás**: 

    - A cause1 esetében állítsa a **Upsert** írási viselkedésként.
    - A 2. ok esetén győződjön meg arról, hogy minden dokumentum eltérő értékkel rendelkezik a definiált egyedi kulcshoz.

### <a name="error-message-request-rate-is-large"></a>Hibaüzenet: a kérelmek aránya nagy

- **Tünetek**: az adatok Cosmos DBba való másolása során a következő hibaüzenetet kapta:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **OK**: a használt kérelmek száma nagyobb, mint a Cosmos DBban konfigurált, rendelkezésre álló ru. Ismerje meg, hogyan számítja ki a Cosmos DB RU- [t innen.](../cosmos-db/request-units.md#request-unit-considerations)

- **Megoldás**: két megoldás:

    1. **Növelje a tároló ru** -t nagyobb értékre Cosmos DBban, ami javítja a másolási tevékenység teljesítményét, de a Cosmos db további költségekkel járna. 

    2. Csökkentse a **writeBatchSize** kisebb értékre (például 1000), és állítsa be a **parallelCopies** -t kisebb értékre (például 1), amely a jelenleginél rosszabban fogja a másolási teljesítményt, de a Cosmos db nem jár további költségekkel.

### <a name="column-missing-in-column-mapping"></a>Az oszlop-hozzárendelésből hiányzó oszlop

- **Tünetek**: Ha az oszlop-hozzárendeléshez Cosmos db sémát importál, egyes oszlopok hiányoznak. 

- **OK**: Az ADF kikövetkezteti a sémát az első 10 Cosmos db dokumentumból. Ha egyes oszlopok/tulajdonságok nem rendelkeznek értékkel ezekben a dokumentumokban, az ADF nem észleli azt, így nem fog megjelenni.

- **Megoldás**: az alábbi módon beállíthatja a lekérdezést úgy, hogy az oszlop kikényszerítve jelenjen meg az eredményhalmaz üres értékével: (feltételezhető, hogy "Impossible" oszlop hiányzik az első 10 dokumentumból). Azt is megteheti, hogy manuálisan hozzáadja az oszlopot a leképezéshez.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hibaüzenet: az olvasó GuidRepresentation CSharpLegacy

- **Tünetek**: Cosmos db MongoAPI/MongoDB és az UUID mezőből származó adatok másolása során a következő hibaüzenetet kapta:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **OK**: az UUID-t kétféleképpen lehet a BSON-UuidStardard és a UuidLegacy-ben ábrázolni. Alapértelmezés szerint a UuidLegacy az adatolvasásra szolgál. Ha a MongoDB-beli UUID-adatai UuidStandard, a rendszer hibát jelez.

- **Megoldás**: a MongoDB-kapcsolatok karakterláncában adja hozzá a "**uuidRepresentation = standard**" beállítást. További információ: MongoDB- [kapcsolatok karakterlánca](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="error-code--adlsgen2operationfailed"></a>Hibakód: AdlsGen2OperationFailed

- **Üzenet**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **OK**: a ADLS Gen2 a művelet hibáját jelző hibaüzenetet nem sikerült.

- **Javaslat**: ADLS Gen2 által kiváltott részletes hibaüzenet. Ha átmeneti hiba okozta, próbálkozzon újra. Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure Storage támogatási szolgálatával, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben.

- **OK**: Ha a hibaüzenet "tiltott" üzenetet tartalmaz, akkor előfordulhat, hogy az Ön által használt egyszerű szolgáltatásnév vagy felügyelt identitás nem rendelkezik elegendő engedéllyel a ADLS Gen2 eléréséhez.

- **Javaslat**: Tekintse meg a Súgó dokumentumot: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **OK**: Ha a hibaüzenetben a "InternalServerError" szöveg szerepel, a ADLS Gen2 a hibát adja vissza.

- **Javaslat**: Előfordulhat, hogy az átmeneti hiba okozta. próbálkozzon újra. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot az Azure Storage támogatási szolgálatával, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben.


### <a name="error-code--adlsgen2invalidurl"></a>Hibakód: AdlsGen2InvalidUrl

- **Üzenet**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Hibakód: AdlsGen2InvalidFolderPath

- **Üzenet**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Hibakód: AdlsGen2OperationFailedConcurrentWrite

- **Üzenet**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Hibakód: AdlsGen2TimeoutError

- **Üzenet**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Kérelem ADLS Gen2 fiókra vonatkozó PGE időtúllépési hibája

- **Üzenet**: hibakód = `UserErrorFailedBlobFSOperation` , hibaüzenet = `BlobFS operation failed for: A task was canceled` .

- **OK**: a problémát a ADLS Gen2 fogadó időtúllépési hibája okozza, ami többnyire a saját üzemeltetésű IR-gépen történik.

- **Javaslat**: 

    1. Ha lehetséges, helyezze a saját üzemeltetésű IR-gépet és a cél ADLS Gen2 fiókot ugyanabban a régióban. Ez elkerülheti a véletlenszerű időtúllépési hibát, és jobb teljesítményt biztosíthat.

    1. Ellenőrizze, hogy van-e olyan speciális hálózati beállítás, mint a ExpressRoute, és győződjön meg arról, hogy a hálózatban elegendő sávszélesség van. Azt javasoljuk, hogy csökkentse a saját üzemeltetésű IR egyidejű feladatok beállítását, ha a teljes sávszélesség alacsony, és ezzel elkerülhető, hogy a hálózati erőforrások versenye több egyidejű feladaton keresztül történjen.

    1. A nem bináris másoláshoz használjon kisebb blokk-méretet, hogy csökkentse az időtúllépési hibát, ha a fájl mérete közepes vagy kicsi. Tekintse meg [blob Storage Put blokkot](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Az egyéni blokk méretének megadásához szerkessze a tulajdonságot a. JSON-szerkesztőben:
    ```
    "sink": {
        "type": "DelimitedTextSink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings",
            "blockSizeInMB": 8
        }
    }
    ```


## <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Hibaüzenet: az alapul szolgáló kapcsolat bezárult: nem sikerült létrehozni a megbízhatósági kapcsolatot az SSL/TLS biztonságos csatorna számára.

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **OK**: a tanúsítvány ellenőrzése nem sikerült a TLS-kézfogás során.

- **Megoldás**: Áthidaló megoldás: a ADLS Gen1 TLS-ellenőrzésének kihagyása szakaszos másolással. Újból létre kell hoznia ezt a problémát, és össze kell gyűjtenie a netmon-nyomkövetést, majd a hálózati csapatot kell használnia a helyi hálózati konfiguráció vizsgálatához.

    ![Hibakeresés ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hibaüzenet: a távoli kiszolgáló a következő hibát adta vissza: (403) tiltott

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **OK**: az egyik lehetséges ok az, hogy az Ön által használt szolgáltatásnév vagy felügyelt identitás nem rendelkezik engedéllyel az adott mappához vagy fájlhoz való hozzáféréshez.

- **Megoldás**: adjon meg megfelelő engedélyeket a másolandó mappákhoz és almappákhoz. Tekintse át [ezt a dokumentációt](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hibaüzenet: nem sikerült lekérni a hozzáférési tokent az egyszerű szolgáltatásnév használatával. ADAL-hiba: service_unavailable

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **OK**: ha a Azure Active Directory által birtokolt szolgáltatás-jogkivonat-kiszolgáló (STS) nem érhető el, azaz a kérelmek kezeléséhez túl elfoglalt, a 503 http-hibát ad vissza. 

- **Megoldás**: több perc elteltével futtassa újra a másolási tevékenységet.
                  

## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure szinapszis Analitika/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Hibakód: SqlFailedToConnect

- **Üzenet**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **OK**: Ha a hibaüzenetben a "SqlException" szerepel, akkor a SQL Database a hibát jelző hiba miatt nem sikerült.

- **Javaslat**: a jelen dokumentációban található SQL-hibakód alapján keressen további részleteket: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Ha további segítségre van szüksége, forduljon az Azure SQL támogatási szolgálatához.

- **OK**: Ha a hibaüzenetben szerepel az "ügyfél IP-címmel"... a nem fér hozzá a kiszolgálóhoz, és a Azure SQL Databasehoz próbál csatlakozni, általában Azure SQL Database tűzfal okozza a problémát.

- **Javaslat**: a logikai SQL Server tűzfal konfigurációjában engedélyezze az "Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgálóhoz" lehetőséget. Dokumentáció: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Hibakód: SqlOperationFailed

- **Üzenet**: `A database operation failed. Please search error to get more details.`

- **OK**: Ha a hibaüzenetben a "SqlException" szerepel, akkor a SQL Database a hibát jelző hiba miatt nem sikerült.

- **Javaslat**: Ha az SQL-hiba nem egyértelmű, próbálja meg módosítani az adatbázist a legújabb kompatibilitási szintre (150). A legújabb verziójú SQL-hibákat is el tudja dobni. Tekintse át a [részletes dokumentációt](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Az SQL-problémák elhárításához a jelen dokumentációban keresse meg az SQL-hibakódot a további részletekért: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Ha további segítségre van szüksége, forduljon az Azure SQL támogatási szolgálatához.

- **OK**: Ha a hibaüzenet "PdwManagedToNativeInteropException"-t tartalmaz, általában a forrás-és a fogadó oszlop méretének eltérése okozta.

- **Javaslat**: a forrás-és a fogadó oszlopok méretének megkeresése. Ha további segítségre van szüksége, forduljon az Azure SQL támogatási szolgálatához.

- **OK**: Ha a hibaüzenet "InvalidOperationException"-t tartalmaz, általában érvénytelen bemeneti adatok okozzák.

- **Javaslat**: annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sort (ka) t a tárolóba. Dokumentáció: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .



### <a name="error-code--sqlunauthorizedaccess"></a>Hibakód: SqlUnauthorizedAccess

- **Üzenet**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **OK**: a hitelesítő adatok helytelenek, vagy a bejelentkezési fiók nem fér hozzá SQL Databasehoz.

- **Javaslat**: Győződjön meg arról, hogy a bejelentkezési fiók rendelkezik a SQL Database eléréséhez szükséges engedéllyel.


### <a name="error-code--sqlopenconnectiontimeout"></a>Hibakód: SqlOpenConnectionTimeout

- **Üzenet**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **OK**: lehetséges, hogy SQL Database átmeneti hiba történt.

- **Javaslat**: próbálja meg újra frissíteni a társított szolgáltatás kapcsolati karakterláncát nagyobb kapcsolati időtúllépési értékkel.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Hibakód: SqlAutoCreateTableTypeMapFailed

- **Üzenet**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **OK**: az automatikus létrehozási tábla nem tudja kielégíteni a forrás követelményét.

- **Javaslat**: frissítse az oszlop típusát a hozzárendelések területen, vagy manuálisan hozza létre a fogadó táblát a célkiszolgálón.


### <a name="error-code--sqldatatypenotsupported"></a>Hibakód: SqlDataTypeNotSupported

- **Üzenet**: `A database operation failed. Check the SQL errors.`

- **OK**: Ha a probléma az SQL-forráson történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a logikai típus tartományán (1/1/1753 12:00:00 – 12/31/9999 11:59:59 PM) van.

- **Javaslat**: a típust írja a forrás SQL-lekérdezésbe, vagy a másolási tevékenység oszlopban módosítsa az oszlop típusát "string" értékre.

- **OK**: Ha a probléma az SQL-tárolón történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a fogadó tábla megengedett tartományán kívül esik.

- **Javaslat**: a megfelelő oszlop típusának frissítése a fogadó táblában lévő "datetime2" típusra.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Hibakód: SqlInvalidDbStoredProcedure

- **Üzenet**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **OK**: a megadott tárolt eljárás érvénytelen. Ennek oka az lehet, hogy a tárolt eljárás nem ad vissza semmilyen adatforrást.

- **Javaslat**: Ellenőrizze a tárolt eljárást az SQL-eszközök alapján. Győződjön meg arról, hogy a tárolt eljárás képes visszaadni az adatvesztést.


### <a name="error-code--sqlinvaliddbquerystring"></a>Hibakód: SqlInvalidDbQueryString

- **Üzenet**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **OK**: a megadott SQL-lekérdezés érvénytelen. Ennek oka az lehet, hogy a lekérdezés nem ad vissza semmilyen adatforrást.

- **Javaslat**: az SQL-lekérdezés ÉRVÉNYESÍTÉSe SQL-eszközök alapján. Győződjön meg arról, hogy a lekérdezés képes visszaadni az adatkérést.


### <a name="error-code--sqlinvalidcolumnname"></a>Hibakód: SqlInvalidColumnName

- **Üzenet**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **OK**: az oszlop nem található. A lehetséges konfiguráció helytelen.

- **Javaslat**: Ellenőrizze a lekérdezésben szereplő oszlopot, a "Structure" objektumot és a "leképezéseket" a tevékenységben.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Hibakód: SqlColumnNameMismatchByCaseSensitive

- **Üzenet**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Hibakód: SqlBatchWriteTimeout

- **Üzenet**: `Timeouts in SQL write operation.`

- **OK**: lehetséges, hogy SQL Database átmeneti hiba történt.

- **Javaslat**: próbálkozzon újra. Ha probléma Reprodukálási, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Hibakód: SqlBatchWriteTransactionFailed

- **Üzenet**: `SQL transaction commits failed`

- **OK**: Ha a kivétel részletei folyamatosan jelzik a tranzakció időtúllépését, az integrációs modul és az adatbázis közötti hálózati késés az alapértelmezett küszöbértéknél nagyobb, mint 30 másodperc.

- **Javaslat**: az SQL társított szolgáltatás kapcsolati karakterláncának frissítése a "kapcsolat időtúllépése" értékkel egyenlő, mint 120 vagy magasabb, majd futtassa újra a tevékenységet.

- **OK**: Ha a kivétel részletei időnként megszakadnak a SqlConnection, akkor csak átmeneti hálózati hiba vagy SQL Databasei oldal probléma

- **Javaslat**: próbálkozzon újra a tevékenységgel, és tekintse át SQL Database oldal metrikáit.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Hibakód: SqlBulkCopyInvalidColumnLength

- **Üzenet**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **OK**: az SQL tömeges másolása nem sikerült, mert a BCP-ügyfél érvénytelen hosszúságú oszlopot kapott.

- **Javaslat**: annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sort (ka) t a tárolóba. Dokumentáció: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Hibakód: SqlConnectionIsClosed

- **Üzenet**: `The connection is closed by SQL Database.`

- **OK**: az SQL-alapú kapcsolatok SQL Database lezárult, ha a nagy egyidejű Futtatás és a kiszolgáló leáll.

- **Javaslat**: a távoli kiszolgáló lezárta az SQL-kapcsolatokat. Próbálja megismételni. Ha probléma Reprodukálási, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Hibakód: SqlCreateTableFailedUnsupportedType

- **Üzenet**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hibaüzenet: nem sikerült az átalakítás a karakterláncról uniqueidentifier való átalakításkor

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az Azure szinapszis Analytics-be a szakaszos másolással és a kiindulással, a következő hibaüzenetet kapta:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **OK**: az Azure szinapszis Analytics-alapú albase nem tud üres karakterláncot konvertálni GUID-ra.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a "**alapértelmezett típus használata**" beállítást hamis értékre.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hibaüzenet: várt adattípus: decimális (x, x), jogsértő érték

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az Azure szinapszis Analytics-be a szakaszos másolással és a kiindulással, a következő hibaüzenetet kapta:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **OK**: az Azure szinapszis Analytics-alapú albase nem tud üres sztringet (null értéket) beszúrni decimális oszlopba.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a "**alapértelmezett típus használata**" beállítást hamis értékre.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Hibaüzenet: Java-kivételt jelző üzenet: HdfsBridge:: CreateRecordReader

- **Tünetek**: az adatok az Azure szinapszis analyticsbe való másolását a Base használatával végezheti el, és megtalálhatja a következő hibát:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **OK**: a lehetséges ok az, hogy a séma (az oszlopszélesség teljes szélessége) túl nagy (1 MB-nál nagyobb). Az összes oszlop méretének hozzáadásával keresse meg a cél Azure szinapszis Analytics-táblázat sémáját:

    - Int-> 4 bájt
    - Bigint – > 8 bájt
    - Varchar (n), char (n), Binary (n), varbinary (n) – > n bájt
    - Nvarchar (n), nchar (n) – > n * 2 bájt
    - Dátum – > 6 bájt
    - Datetime/(2), idő adattípusúra – > 16 bájt
    - DateTimeOffset – > 20 bájt
    - Decimális > 19 bájt
    - Float-> 8 bájt
    - Pénz – > 8 bájt
    - Túlcsordulási – > 4 bájt
    - Valós > 4 bájt
    - Smallint – > 2 bájt
    - Idő – > 12 bájt
    - Tinyint – > 1 bájt

- **Megoldás**: csökkentse az oszlopszélességet 1 MB-nál kisebb értékre

- Vagy a tömeges beszúrási módszer használata a "Base" letiltásával


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hibaüzenet: a HTTP feltételes fejléc (ek) használatával megadott feltétel nem teljesül.

- **Tünetek**: SQL-lekérdezést használ az adatok Azure szinapszis Analyticsből való lekéréséhez, és a következő hiba miatt:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **OK**: az Azure szinapszis Analytics a külső tábla Azure Storage szolgáltatásban való lekérdezését találta.

- **Megoldás**: futtassa ugyanazt a LEKÉRDEZÉST a SSMS, és ellenőrizze, hogy ugyanazt az eredményt látja-e. Ha igen, nyisson meg egy támogatási jegyet az Azure szinapszis Analytics szolgáltatáshoz, és adja meg az Azure szinapszis Analytics-kiszolgáló és az adatbázis nevét a további hibák megoldásához.
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>Alacsony teljesítmény az Azure SQL-ba való betöltéskor

- **Tünetek**: az adatok az Azure SQL-be való másolása lassúnak bizonyul.

- **OK**: a probléma kiváltó okát többnyire az Azure SQL-oldal szűk keresztmetszete váltja ki. A következő okok lehetséges okai:

    1. Az Azure-adatbázis szintje nem elég nagy.

    1. Az Azure DB-DTU használata a 100%-ban közelíthető meg. Nyomon követheti [a teljesítményt](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) , és megtekintheti az adatbázis-csomag frissítését.

    1. Az indexek nincsenek megfelelően beállítva. A betöltés befejezése után távolítsa el az összes indexet az adatterhelés előtt, majd hozza létre újra őket.

    1. A WriteBatchSize nem elég nagy ahhoz, hogy illeszkedjen a séma sorainak méretéhez. Próbálja meg bővíteni a probléma tulajdonságát.

    1. A tömeges beszúrta helyett a tárolt eljárás használatban van, ami várhatóan rosszabb teljesítményű. 

- **Megoldás**: a [másolási tevékenység teljesítményéhez](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting) tekintse meg a HKT-t.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>A teljesítményszint alacsony, és sikertelen másolást eredményez

- **Tünetek**: az adatok Azure SQL-be történő másolása során az alábbi hibaüzenet jelenik meg: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **OK**: az Azure SQL S1 használatban van, amely ebben az esetben az i/o-korlátokat érinti.

- **Megoldás**: a probléma megoldásához frissítse az Azure SQL Performance szintet. 


### <a name="sql-table-cannot-be-found"></a>Az SQL-tábla nem található 

- **Tünetek**: hiba történt az adatok hibridről helyszíni SQL Server táblába való másolása során:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **OK**: az aktuális SQL-fiók nem rendelkezik megfelelő engedélyekkel a .net SqlBulkCopy. WriteToServer által kiadott kérelmek végrehajtásához.

- **Megoldás**: váltson magasabb jogosultsági szintű SQL-fiókra.


### <a name="string-or-binary-data-would-be-truncated"></a>A karakterlánc-vagy bináris adatértékek csonkítva lesznek

- **Tünetek**: hiba történt az adatok helyszíni vagy Azure SQL Server-táblába való másolása során: 

- **OK**: az SQL-tábla sémájának definíciójában egy vagy több olyan oszlop van, amelynek kevesebb a várakozási ideje.

- **Megoldás**: próbálkozzon a következő lépésekkel a probléma megoldásához:

    1. Alkalmazzon [hibatűrést](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance), különösen a "redirectIncompatibleRowSettings" kifejezést a problémával kapcsolatos problémák megoldásához.

    1. Az átirányított és az SQL Table-séma oszlop hosszával ellenőrizze, hogy melyik oszlop (oka) t kell frissíteni.

    1. A tábla sémájának frissítése ennek megfelelően.


## <a name="delimited-text-format"></a>Tagolt szöveg formátuma

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Hibakód: DelimitedTextColumnNameNotAllowNull

- **Üzenet**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **OK**: a "firstRowAsHeader" tevékenységben való beállításakor az első sor lesz az oszlop neve. Ez a hiba azt jelenti, hogy az első sor üres értéket tartalmaz. Például: ' columna, ColumnB '.

- **Javaslat**: Ellenőrizze az első sort, és javítsa ki az értéket, ha üres érték van megadva.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Hibakód: DelimitedTextMoreColumnsThanDefined

- **Üzenet**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **OK**: a problémás sor oszlopainak száma nagyobb, mint az első sor oszlopainak száma. Ennek oka az lehet, hogy az adatprobléma vagy a helytelen oszlop-határolójel/quota karakteres beállítások szerepelnek.

- **Javaslat**: a sorok számának lekérése a hibaüzenetben, tekintse meg a sor oszlopát, és javítsa ki az adattípust.

- **OK**: Ha a várt oszlopok száma "1" a hibaüzenetben, lehet, hogy helytelen tömörítési vagy formázási beállításokat adott meg. Így az ADF helytelenül elemezte a fájl (oka) t.

- **Javaslat**: Ellenőrizze a formátum beállításait, és győződjön meg arról, hogy az megfelel a forrásfájl (ok) nak.

- **OK**: Ha a forrás egy mappa, lehetséges, hogy a megadott mappában található fájlok különböző sémával rendelkeznek.

- **Javaslat**: Győződjön meg arról, hogy a megadott mappában található fájlok azonos sémával rendelkeznek.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Hibakód: DelimitedTextIncorrectRowDelimiter

- **Üzenet**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Hibakód: DelimitedTextTooLargeColumnCount

- **Üzenet**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Hibakód: DelimitedTextInvalidSettings

- **Üzenet**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Hibakód: DynamicsCreateServiceClientError

- **Üzenet**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **OK**: ez egy átmeneti probléma a Dynamics Server oldalán.

- **Javaslat**: futtassa újra a folyamatot. Ha nem sikerül, próbálja meg csökkenteni a párhuzamosságot. Ha továbbra sem sikerül, forduljon a Dynamics ügyfélszolgálatához.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Az oszlopok hiányoznak a séma előnézete/importálása során

- **Tünetek**: egyes oszlopok nem hiányozhatnak a séma importálásakor vagy az adatnézetek megtekintésekor. Hibaüzenet: `The valid structure information (column name and type) are required for Dynamics source.`

- **OK**: Ez a probléma alapvetően a-design, mivel az ADF nem képes olyan oszlopokat megjeleníteni, amelyek nem rendelkeznek értékkel az első 10 rekordban. Ellenőrizze, hogy a hozzáadott oszlopok formátuma megfelelő-e. 

- **Javaslat**: manuálisan adja hozzá az oszlopokat a leképezés lapon.


## <a name="excel-format"></a>Excel-formátum

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Időtúllépés vagy lassú teljesítmény a nagyméretű Excel-fájlok elemzésekor

- **Tünetek**:

    1. Amikor létrehoz egy Excel-adatkészletet, és importálja a sémát a kapcsolatból/tárból, előnézeti adatokat, listát vagy frissítési munkalapokat, akkor időtúllépési hiba léphet fel, ha az Excel-fájl mérete nagyméretű.

    1. Ha a másolási tevékenységgel nagy méretű Excel-fájlból (>= 100MB) másol Adatmásolást más adattárba, a lassú teljesítmény vagy a bácsi probléma merülhet fel.

- **OK**: 

    1. Az olyan műveletek esetében, mint például a séma importálása, az adatok megtekintése és az Excel-adatkészletek listázása, az időtúllépés a 100s és a statikus. Nagyméretű Excel-fájl esetén előfordulhat, hogy ezek a műveletek nem fejeződik be az időtúllépési értéken belül.

    2. Az ADF másolási tevékenység beolvassa a teljes Excel-fájlt a memóriába, majd megkeresi a megadott munkalapot és cellákat az adatolvasáshoz. Ezt a viselkedést a mögöttes SDK ADF használja.

- **Megoldás**: 

    1. A séma importálásához létrehozhat egy kisebb mintát, amely az eredeti fájl egy részhalmaza, és a "séma importálása a fájlból" lehetőséget választja a "séma importálása a hálózatról/áruházból" lehetőség helyett.

    2. A felsorolási munkalap legördülő listájában kattintson a "szerkesztés" lehetőségre, és adja meg a lap nevét/indexét.

    3. Nagyméretű Excel-fájl (>100MB) más tárolóba való másolásához használhatja az adatfolyamok Excel-forrását, amelynél a sport streaming olvas és jobb teljesítményt nyújt.


## <a name="hdinsight"></a>HDInsight

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>SSL-hiba a társított szolgáltatás HDInsight ESP-fürttel való csatolásakor

- **Üzenet**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **OK**: a probléma valószínűleg a rendszer-megbízhatósági tárolóval kapcsolatos.

- **Megoldás**: Nyissa meg az elérési utat a **Microsoft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft kaptár ODBC Driver\lib** , és nyissa meg DriverConfiguration64.exe a beállítás módosításához.

    ![Törölje a rendszermegbízhatósági tároló használata jelölőnégyzet jelölését.](./media/connector-troubleshoot-guide/system-trust-store-setting.png)


## <a name="json-format"></a>JSON-formátum

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Hibakód: JsonInvalidArrayPathDefinition

- **Üzenet**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Hibakód: JsonEmptyJObjectData

- **Üzenet**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Hibakód: JsonNullValueInPathDefinition

- **Üzenet**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Hibakód: JsonUnsupportedHierarchicalComplexValue

- **Üzenet**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Hibakód: JsonConflictPartitionDiscoverySchema

- **Üzenet**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Hibakód: JsonInvalidDataFormat

- **Üzenet**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Hibakód: JsonInvalidDataMixedArrayAndObject

- **Üzenet**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Hibakód: ArgumentOutOfRangeException

- **Üzenet**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **OK**: Az ADF-ben a DateTime értékek a 0001-01-01 00:00:00 és 9999-12-31 23:59:59 közötti tartományban támogatottak. Az Oracle azonban támogatja a DateTime érték szélesebb tartományát (például a BC Century vagy a min/mp>59), ami az ADF hibája miatt meghiúsul.

- **Javaslat**: 

    A futtatásával `select dump(<column name>)` ellenőrizze, hogy az Oracle értéke az ADF tartományában van-e. 

    Ha tudni szeretné, hogy milyen bájtos sorozatot ad meg az eredmény, ellenőrizze a következőt: https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="parquet-format"></a>Parketta formátuma

### <a name="error-code--parquetjavainvocationexception"></a>Hibakód: ParquetJavaInvocationException

- **Üzenet**: `An error occurred when invoking java, message: %javaException;.`

- **OK**: Ha a hibaüzenetben a "Java. lang. OutOfMemory", a "Java heap Space" és a "doubleCapacity" szerepel, ez általában az integrációs modul régi verziójában található memória-kezelési probléma.

- **Javaslat**: Ha saját üzemeltetésű Integration Runtime használ, és a verzió korábbi, mint a 3.20.7159.1, javasoljuk, hogy frissítsen a legújabb verzióra.

- **OK**: Ha a hibaüzenetben a "Java. lang. OutOfMemory" szerepel, az Integration Runtime nem rendelkezik elegendő erőforrással a fájl (ok) feldolgozásához.

- **Javaslat**: az egyidejű futtatások korlátozása az Integration Runtime-ban. A saját üzemeltetésű Integration Runtime esetében akár 8 GB-nál nagyobb memóriával, akár egy olyan, nagy teljesítményű géppel is méretezhető.

- **OK**: Ha a hibaüzenetben a "NullPointerReference" szöveg szerepel, ez egy átmeneti hiba.

- **Javaslat**: próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.


### <a name="error-code--parquetinvalidfile"></a>Hibakód: ParquetInvalidFile

- **Üzenet**: `File is not a valid Parquet file.`

- **OK**: a parketta-fájl hibája.

- **Javaslat**: Győződjön meg arról, hogy a bemenet érvényes parketta-fájl.


### <a name="error-code--parquetnotsupportedtype"></a>Hibakód: ParquetNotSupportedType

- **Üzenet**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **OK**: a Azure Data Factory nem támogatja a parketta formátumát.

- **Javaslat**: Ellenőrizze a forrásadatokat. Tekintse meg a doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Hibakód: ParquetMissedDecimalPrecisionScale

- **Üzenet**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **OK**: próbálja meg elemezni a pontosságot és a skálázást, de nincs ilyen adat megadva.

- **Javaslat**: a forrás nem ad vissza pontos pontosságot és méretezést. A probléma oszlopban keresse meg a pontosságot és a méretezést.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Hibakód: ParquetInvalidDecimalPrecisionScale

- **Üzenet**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **OK**: a séma érvénytelen.

- **Javaslat**: a probléma oszlopban keresse meg a pontosságot és a méretezést.


### <a name="error-code--parquetcolumnnotfound"></a>Hibakód: ParquetColumnNotFound

- **Üzenet**: `Column %column; does not exist in Parquet file.`

- **OK**: a forrás sémája nem egyezik a fogadó sémával.

- **Javaslat**: a "tevékenység" the'mappings megkeresése. Győződjön meg arról, hogy a forrás oszlop képezhető le a megfelelő fogadó oszlopra.


### <a name="error-code--parquetinvaliddataformat"></a>Hibakód: ParquetInvalidDataFormat

- **Üzenet**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **OK**: az adattípusok nem alakíthatók át a leképezésekben megadott típusra. forrás

- **Javaslat**: Ellenőrizze a forrásadatokat, vagy adja meg a megfelelő adattípust ehhez az oszlophoz a másolási tevékenység oszlop-hozzárendelésében. Tekintse meg a doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Hibakód: ParquetDataCountNotMatchColumnCount

- **Üzenet**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **OK**: a forrás oszlopainak száma és a fogadó oszlopainak száma nem egyezik

- **Javaslat**: a dupla ellenőrzési forrás oszlopainak száma ugyanaz, mint a "leképezés" fogadó oszlopainak száma.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Hibakód: ParquetDataTypeNotMatchColumnType

- **Üzenet**: a (z)% srcType; adattípus nem felel meg a megadott típusú oszlopnak (% dstType;). a (z)% columnIndex; oszlopban.

- **OK**: a forrásból származó adatok nem alakíthatók át a fogadóban definiált típusra

- **Javaslat**: adjon meg egy megfelelő típust a Mapping. mosogatóban.


### <a name="error-code--parquetbridgeinvaliddata"></a>Hibakód: ParquetBridgeInvalidData

- **Üzenet**: `%message;`

- **OK**: az adatérték a korlátozáson felül

- **Javaslat**: próbálkozzon újra. Ha a probléma továbbra is fennáll, vegye fel velünk a kapcsolatot.


### <a name="error-code--parquetunsupportedinterpretation"></a>Hibakód: ParquetUnsupportedInterpretation

- **Üzenet**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **OK**: nem támogatott forgatókönyv

- **Javaslat**: a "ParquetInterpretFor" nem lehet "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Hibakód: ParquetUnsupportFileLevelCompressionOption

- **Üzenet**: `File level compression is not supported for Parquet.`

- **OK**: nem támogatott forgatókönyv

- **Javaslat**: távolítsa el a fájba a hasznos adatok között.


### <a name="error-code--usererrorjniexception"></a>Hibakód: UserErrorJniException

- **Üzenet**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **OK**: a JVM nem hozható létre, mert néhány tiltott (globális) argumentum be van állítva.

- **Javaslat**: Jelentkezzen be arra a gépre, amelyen a saját üzemeltetésű integrációs modul **egyes csomópontjai** futnak. Ellenőrizze, hogy a rendszerváltozó helyesen van-e beállítva, például: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Indítsa újra az összes IR-csomópontot, majd futtassa újra a folyamatot.


### <a name="arithmetic-overflow"></a>Aritmetikai túlcsordulás

- **Tünetek**: a Parquet-fájlok másolása során hibaüzenet jelenik meg: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **OK**: jelenleg csak a pontossági <= 38, és a <= 20 egész szám hosszúsága támogatott, ha az Oracle-ből a parkettára másolja a fájlokat. 

- **Megoldás**: az ilyen problémákkal rendelkező oszlopokat megkerülő megoldásként konvertálhatja a VARCHAR2.


### <a name="no-enum-constant"></a>Nem állandó Enum

- **Tünetek**: hiba történt az adatok parketta formátumba való másolása során: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , vagy: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **OK**: 

    A problémát a szóközök vagy a nem támogatott karakterek (például:;) okozhatja. {} () \n\t =) az oszlopnév esetében, mivel a parketta nem támogatja az ilyen formátumot. 

    Például a *contoso (test)* nevű oszlopnév a [kód](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) alapján zárójelben fogja elemezni a típust `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . A hiba akkor jelenik meg, ha nincs ilyen "teszt" típus.

    A támogatott típusok ellenőrzését [itt](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)tekintheti meg.

- **Megoldás**: 

    1. Ellenőrizze, hogy vannak-e szóközök a fogadó oszlop nevében.

    1. Ellenőrizze, hogy az első sor szóközökkel van-e felhasználva oszlopnév néven.

    1. Ellenőrizze, hogy a OriginalType típusa támogatott-e. Próbálja meg elkerülni ezeket a speciális szimbólumokat `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="unexpected-network-response-from-rest-connector"></a>Nem várt hálózati válasz a REST-összekötőtől

- **Tünetek**: a végpont időnként váratlan választ kap (400/401/403/500) a REST-összekötőből.

- **OK**: a REST forrás-összekötő URL-címet és http-metódust/fejlécet/törzset használ a társított szolgáltatás/adatkészlet/másolás forrása paraméterként a HTTP-kérések létrehozásakor. A problémát valószínűleg egy vagy több megadott paraméter hibája okozza.

- **Megoldás**: 
    - A cmd ablakban a "curl" használatával ellenőrizze, hogy az OK vagy a nem (**elfogadva** és **felhasználói ügynök** fejlécei mindig szerepeljenek-e):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Ha a parancs ugyanazt a váratlan választ adja vissza, javítsa a fenti paramétereket a "curl" értékkel, amíg vissza nem adja a várt választ. 

      Emellett használhatja a "curl--help" parancsot is a parancs fejlettebb használatához.

    - Ha csak az ADF REST-összekötő váratlan választ ad vissza, további hibaelhárításért forduljon a Microsoft támogatási szolgálatához.
    
    - Vegye figyelembe, hogy a "curl" nem lehet megfelelő az SSL-tanúsítvány érvényesítési hibájának reprodukálása érdekében. Bizonyos esetekben a "curl" parancs végrehajtása nem sikerült az SSL-tanúsítvány érvényesítési problémáinak elvégzése nélkül. Ha azonban ugyanazt az URL-címet a böngészőben hajtja végre, akkor a rendszer nem ad vissza SSL-tanúsítványt az első helyen az ügyfél számára a kiszolgálóval való megbízhatósági kapcsolat létrehozásához.

      A fenti esethez ajánlott eszközök, például a **Poster** és a **Hegedűs** .


## <a name="sftp"></a>SFTP

### <a name="invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>A "SSHPublicKey" hitelesítési típushoz megadott SFTP-hitelesítő adat érvénytelen.

- **Tünetek**: az SSH nyilvános kulcsú hitelesítés használatos, miközben a "SshPublicKey" hitelesítési típushoz érvénytelen SFTP hitelesítő adat van megadva.

- **OK**: ezt a hibát három lehetséges ok okozhatta:

    1. A titkos kulcs tartalmának beolvasása a AKV/SDK-ból történik, de helytelenül van kódolva.

    1. A kulcs tartalmának helytelen formátuma van kiválasztva.

    1. A hitelesítő adat vagy a titkos kulcs tartalma érvénytelen.

- **Megoldás**: 

    1. **1. ok**:

       Ha a titkos kulcs tartalma a AKV-ből származik, és az eredeti kulcsfájl képes működni, ha az ügyfél közvetlenül az SFTP-hez társított szolgáltatáshoz tölti fel.

       Tekintse meg https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication a privateKey tartalma Base64 kódolású titkos SSH-kulcs tartalmát.

       Kódolja **az eredeti titkos kulcsfájl teljes tartalmát** Base64 kódolással, és tárolja a kódolt karakterláncot a AKV. Az eredeti titkos kulcsfájl az, amely az SFTP-hez társított szolgáltatáson tud működni, ha a feltöltés fájlból lehetőségre kattint.

       Íme néhány példa a sztring generálásához:

       - C#-kód használata:
       ```
       byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
       string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
       ```

       - Python-kód használata:
       ```
       import base64
       rfd = open(r'{Private Key Path}', 'rb')
       keyContent = rfd.read()
       rfd.close()
       print base64.b64encode(Key Content)
       ```

       - Harmadik féltől származó Base64 átalakító eszköz használata

         Hasonló eszközök https://www.base64encode.org/ használata ajánlott.

    1. A **2. okból**:

       Ha a PKCS # 8 formátumú SSH titkos kulcs használatban van

       A PKCS # 8 formátum SSH titkos kulcsa (a "-----BEGIN ENCRYPTED PRIVATE KEY-----" kezdetű) jelenleg nem támogatott az SFTP-kiszolgáló az ADF-ben való eléréséhez. 

       Az alábbi parancsok futtatásával alakítsa át a kulcsot a hagyományos SSH-kulcs formátumára (Kezdje a következővel: "-----BEGIN RSA titkos kulcs-----"):

       ```
       openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
       chmod 600 traditional_format_key_file
       ssh-keygen -f traditional_format_key_file -p
       ```
    1. **3. ok**:

       Ha a kulcsfájl vagy a jelszó helyes, ellenőrizze, hogy van-e olyan eszköz, mint a megnyerő eszközzel.


### <a name="incorrect-linked-service-type-is-used"></a>Helytelen társított szolgáltatástípus van használatban

- **Tünetek**: az FTP-/SFTP-kiszolgáló nem érhető el.

- **OK**: helytelen társított szolgáltatástípus van használatban az FTP-vagy SFTP-kiszolgálóhoz, például az FTP-hez társított szolgáltatás használata egy SFTP-kiszolgálóhoz való kapcsolódáshoz vagy fordítva.

- **Megoldás**: Ellenőrizze a célkiszolgáló portját. Alapértelmezés szerint az FTP a 21-es portot használja, és az SFTP a 22-es portot használja.


### <a name="sftp-copy-activity-failed"></a>Az SFTP-másolási tevékenység nem sikerült

- **Tünetek**: hibakód: UserErrorInvalidColumnMappingColumnNotFound. Hibaüzenet: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **OK**: a forrás nem tartalmaz "AccMngr" nevű oszlopot.

- **Megoldás**: Ellenőrizze, hogy az adatkészlet hogyan lett konfigurálva úgy, hogy a cél adatkészlet oszlop hozzárendelésével erősítse meg, hogy van-e ilyen "AccMngr" oszlop.


### <a name="sftp-server-connection-throttling"></a>SFTP-kiszolgáló kapcsolatainak szabályozása

- **Tünetek**: a kiszolgáló válasza nem tartalmazza az ssh protokoll azonosítását, és nem sikerült a másolás.

- **OK**: Az ADF több kapcsolatot hoz létre az SFTP-kiszolgálóról párhuzamosan történő letöltésre, és időnként az SFTP-kiszolgáló szabályozását fogja érinteni. Gyakorlatilag a különböző kiszolgálók eltérő hibát adnak vissza a szabályozás során.

- **Megoldás**: 

    Adja meg az SFTP-adatkészlet maximális egyidejű összekapcsolását 1-re, majd futtassa újra a másolatot. Ha sikerrel jár, biztos lehet benne, hogy a szabályozás oka.

    Ha előléptetni szeretné az alacsony átviteli sebességet, forduljon az SFTP rendszergazdájához az egyidejű kapcsolatok számának növeléséhez, vagy adja hozzá az alábbi IP-címet az engedélyezési listához:

    - Ha felügyelt IR-t használ, vegye fel az [Azure Datacenter IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653).
      Vagy telepítheti a saját üzemeltetésű integrációs modult, ha nem kívánja az IP-címtartományok nagy listáját hozzáadni az SFTP-kiszolgáló engedélyezési listájához.

    - Ha saját üzemeltetésű integrációs modult használ, adja hozzá a számítógép IP-címét, amely telepítette a legördülő listát az engedélyezési listához.


### <a name="error-code-sftprenameoperationfail"></a>Hibakód: SftpRenameOperationFail

- **Tünetek**: a folyamat nem tudta átmásolni az adatait a blobból az SFTP-be a következő hiba miatt: `Operation on target Copy_5xe failed: Failure happened on 'Sink' side. ErrorCode=SftpRenameOperationFail,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException` .

- **OK**: az adatok másolásakor a useTempFileRename beállítás értéke TRUE (igaz). Ez lehetővé teszi, hogy a folyamat ideiglenes fájlokat használjon. A hiba akkor aktiválódik, ha egy vagy több ideiglenes fájl törlődött a teljes Adatmásolás előtt.

- **Megoldás**: állítsa a UseTempFileName beállítást hamis értékre.


## <a name="general-copy-activity-error"></a>Általános másolási tevékenység hibája

### <a name="error-code--jrenotfound"></a>Hibakód: JreNotFound

- **Üzenet**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **OK**: a saját üzemeltetésű Integration Runtime nem találja a Java futtatókörnyezetet. A Java-futtatókörnyezet szükséges az adott forrás olvasásához.

- **Javaslat**: az Integration Runtime-környezet, a dokumentációs dokumentum: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Hibakód: WildcardPathSinkNotSupported

- **Üzenet**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **OK**: a fogadó adatkészlet nem támogatja a helyettesítő karaktert.

- **Javaslat**: keresse meg a fogadó adatkészletet, és javítsa ki az elérési utat helyettesítő érték nélkül.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Hibakód: MappingInvalidPropertyWithEmptyValue

- **Üzenet**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Hibakód: MappingInvalidPropertyWithNamePathAndOrdinal

- **Üzenet**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Hibakód: MappingDuplicatedOrdinal

- **Üzenet**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Hibakód: MappingInvalidOrdinalForSinkColumn

- **Üzenet**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
