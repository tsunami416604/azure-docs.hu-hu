---
title: Azure Data Factory összekötők hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory összekötői problémáit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533153"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory összekötők hibáinak megoldása

Ez a cikk a Azure Data Factory összekötők gyakori hibaelhárítási módszereit vizsgálja.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

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

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: a kérelem mérete túl nagy

- **Tünetek**: az adatmásolás Azure Cosmos DBba az alapértelmezett írási köteg méretével, és a *"**kérés mérete túl nagy**"* .

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

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Hibaüzenet: érvénytelen SFTP-hitelesítő adat van megadva a (z) "SshPublicKey" hitelesítési típushoz.

- **Tünetek**: `SshPublicKey` hitelesítést használ, és a következő hibaüzenetet kapta:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **OK**: 3 lehetséges ok:

    1. Ha az ADF-szerzői felhasználói felületet használja az SFTP társított szolgáltatás létrehozásához, akkor ez a hiba azt jelenti, hogy a használni kívánt titkos kulcs formátuma helytelen. Használhatja az SSH titkos kulcs PKCS # 8 formátumát is, de vegye figyelembe, hogy az ADF csak a hagyományos SSH-kulcs formátumát támogatja. Pontosabban, a PKCS # 8 formátum és a hagyományos kulcs formátuma közötti különbség a PKCS # 8 fő tartalom, amely " *-----BEGIN ENCRYPTED Private key-----* " karakterlánccal kezdődik, míg a hagyományos kulcs formátuma a " *-----BEGIN RSA titkos kulcs-----* " karakterrel kezdődik.
    2. Ha Azure Key Vault használatával tárolja a titkos kulcs tartalmát, vagy az SFTP társított szolgáltatás létrehozásához programozott módszert használ, akkor ez a hiba azt jelenti, hogy a titkos kulcs tartalma helytelen, valószínűleg nem Base64 kódolású.
    3. A hitelesítő adat vagy a titkos kulcs tartalma érvénytelen.

- **Megoldás**: 

    - Az OK #1éhez futtassa a következő parancsokat a kulcs hagyományos formátumban való átalakításához, majd használja az ADF authoring felhasználói felületén.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - A következő okok miatt #2 a karakterlánc létrehozásához: az ügyfél 2 módon használhatja a következőt:
    - Harmadik féltől származó Base64 átalakító eszköz használata: illessze be a teljes titkos kulcs tartalmát olyan eszközökre, mint a [Base64 kódolása és](https://www.base64encode.org/)a dekódolása, kódolja egy Base64 formátumú karakterláncba, majd illessze be ezt a karakterláncot a Key vaultba, vagy használja ezt az értéket az SFTP társított szolgáltatás programozott módon történő létrehozásához.
    - Kód C# használata:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Ok #3 esetén ellenőrizze, hogy a kulcs fájlja vagy jelszava helyes-e a más eszközök használatával történő ellenőrzéséhez, ha az SFTP-kiszolgáló megfelelő eléréséhez használja.
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL Data Warehouse \ Azure SQL Database \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Hibakód: SqlFailedToConnect

- **Üzenet**: `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **OK**: Ha a hibaüzenet "SqlException"-t tartalmaz, az SQL Database eldönti, hogy egy bizonyos művelet sikertelen volt-e.

- **Javaslat**: a további részletekért tekintse meg a következő SQL-hibakódot ebben a dokumentációban: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure SQL támogatási szolgálatával.

- **OK**: Ha a hibaüzenetben szerepel az "ügyfél IP-címmel"... a kiszolgáló nem érhető el, és az Azure SQL Database-hez próbál csatlakozni, általában az Azure SQL Database-tűzfal okozza a problémát.

- **Javaslat**: az Azure SQL Server tűzfal konfigurációjában engedélyezze az "Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgálóhoz" lehetőséget. Dokumentáció: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Hibakód: SqlOperationFailed

- **Üzenet**: `A database operation failed. Please search error to get more details.`

- **OK**: Ha a hibaüzenet "SqlException"-t tartalmaz, az SQL Database eldönti, hogy egy bizonyos művelet sikertelen volt-e.

- **Javaslat**: a további részletekért tekintse meg a következő SQL-hibakódot ebben a dokumentációban: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure SQL támogatási szolgálatával.

- **OK**: Ha a hibaüzenet "PdwManagedToNativeInteropException"-t tartalmaz, általában a forrás-és a fogadó oszlop méretének eltérése okozta.

- **Javaslat**: Ellenőrizze a forrás-és a fogadó oszlopok méretét. Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure SQL támogatási szolgálatával.

- **OK**: Ha a hibaüzenet "InvalidOperationException"-t tartalmaz, általában érvénytelen bemeneti adatok okozzák.

- **Javaslat**: annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sort (ka) t a tárolóba. Dokumentáció: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Hibakód: SqlUnauthorizedAccess

- **Üzenet**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **OK**: a hitelesítő adatok helytelenek, vagy a bejelentkezési fiók nem fér hozzá az SQL Database-hez.

- **Javaslat**: Ellenőrizze, hogy a bejelentkezési fiók rendelkezik-e elegendő engedéllyel az SQL-adatbázis eléréséhez.


### <a name="error-code--sqlopenconnectiontimeout"></a>Hibakód: SqlOpenConnectionTimeout

- **Üzenet**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **OK**: az SQL Database átmeneti hibája lehet.

- **Javaslat**: próbálja meg újra frissíteni a társított szolgáltatás kapcsolati karakterláncát nagyobb kapcsolati időtúllépési értékkel.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Hibakód: SqlAutoCreateTableTypeMapFailed

- **Üzenet**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **OK**: az automatikus létrehozási tábla nem tudja kielégíteni a forrás követelményét.

- **Javaslat**: frissítse a "leképezések" oszlop típusát, vagy manuálisan hozza létre a fogadó táblát a célkiszolgálón.


### <a name="error-code--sqldatatypenotsupported"></a>Hibakód: SqlDataTypeNotSupported

- **Üzenet**: `A database operation failed. Please check the SQL errors.`

- **OK**: Ha a probléma az SQL-forráson történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a logikai típus tartományán (1/1/1753 12:00:00 – 12/31/9999 11:59:59 PM) van.

- **Javaslat**: írja be a típust karakterláncba a forrás SQL-lekérdezésben, vagy a másolási tevékenység oszlopban módosítsa az oszlop típusát a "string" értékre.

- **OK**: Ha a probléma az SQL-tárolón történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a fogadó tábla megengedett tartományán kívül esik.

- **Javaslat**: frissítse a megfelelő oszlopot "datetime2" típusra a fogadó táblában.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Hibakód: SqlInvalidDbStoredProcedure

- **Üzenet**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **OK**: a megadott tárolt eljárás érvénytelen. Ennek oka az lehet, hogy a tárolt eljárás nem ad vissza semmilyen adatforrást.

- **Javaslat**: Ellenőrizze az SQL-eszközök által tárolt eljárást. Győződjön meg arról, hogy a tárolt eljárás képes visszaadni az adatvesztést.


### <a name="error-code--sqlinvaliddbquerystring"></a>Hibakód: SqlInvalidDbQueryString

- **Üzenet**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **OK**: a megadott SQL-lekérdezés érvénytelen. Ennek oka az lehet, hogy a lekérdezés nem ad vissza semmilyen adatforrást.

- **Javaslat**: Ellenőrizze az SQL-eszközök SQL-lekérdezését. Győződjön meg arról, hogy a lekérdezés képes visszaadni az adatkérést.


### <a name="error-code--sqlinvalidcolumnname"></a>Hibakód: SqlInvalidColumnName

- **Üzenet**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **OK**: az oszlop nem található. A lehetséges konfiguráció helytelen.

- **Javaslat**: Ellenőrizze, hogy a lekérdezésben szerepel-e a "Structure" oszlop a (z) adatkészletben és a "leképezések" tevékenységben.


### <a name="error-code--sqlbatchwritetimeout"></a>Hibakód: SqlBatchWriteTimeout

- **Üzenet**: `Timeout in SQL write opertaion.`

- **OK**: az SQL Database átmeneti hibája lehet.

- **Javaslat**: Ha a probléma Reprodukálási, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-code--sqlbatchwriterollbackfailed"></a>Hibakód: SqlBatchWriteRollbackFailed

- **Üzenet**: `Timeout in SQL write operation and rollback also fail.`

- **OK**: az SQL Database átmeneti hibája lehet.

- **Javaslat**: próbálja meg újra frissíteni a társított szolgáltatás kapcsolati karakterláncát nagyobb kapcsolati időtúllépési értékkel.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Hibakód: SqlBulkCopyInvalidColumnLength

- **Üzenet**: `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **OK**: az SQL tömeges másolása nem sikerült, mert a BCP-ügyfél érvénytelen oszlopot kapott.

- **Javaslat**: annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sort (ka) t a tárolóba. Dokumentáció: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Hibakód: SqlConnectionIsClosed

- **Üzenet**: `The connection is closed by SQL database.`

- **OK**: az SQL-adatbázis lezárta az SQL-kapcsolatokat, ha a nagy egyidejű Futtatás és a kiszolgáló megszakítása a csatlakozást.

- **Javaslat**: a távoli kiszolgáló lezárta az SQL-kapcsolatokat. Próbálja újból. Ha probléma Reprodukálási, forduljon az Azure SQL támogatási szolgálatához.

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hibaüzenet: nem sikerült az átalakítás a karakterláncról uniqueidentifier való átalakításkor

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat a szakaszos másolással és a (z Azure SQL Data Warehouse) használatával, a következő hibaüzenet jelenik meg:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **OK**: Azure SQL Data Warehouse a Base nem tud üres karakterláncot konvertálni GUID-ra.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a "**alapértelmezett típus használata**" beállítást hamis értékre.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hibaüzenet: várt adattípus: decimális (x, x), jogsértő érték

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az SQL DW-be a szakaszos másolás és a kiindulási szolgáltatás használatával, a következő hibaüzenet jelenik meg:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **OK**: Azure SQL Data Warehouse a nem szúrható be üres karakterlánc (null érték) decimális oszlopba.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a "**alapértelmezett típus használata**" beállítást hamis értékre.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Hibaüzenet: Java-kivételt jelző üzenet: HdfsBridge:: CreateRecordReader

- **Tünetek**: az adatok a Azure SQL Data Warehouse használatával másolhatók a-ba, és a következő hibaüzenetet kaptak:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **OK**: a lehetséges ok az, hogy a séma (az oszlopszélesség teljes szélessége) túl nagy (1 MB-nál nagyobb). Az összes oszlop méretének hozzáadásával keresse meg a cél SQL DW-tábla sémáját:

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

- **Tünetek**: az SQL-lekérdezéssel az adatok lekérése Azure SQL Data Warehouseről, a következő hiba miatt:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **OK**: a Azure SQL Data Warehouse az Azure Storage-ban a külső tábla lekérdezésével kapcsolatos problémát észlelt.

- **Megoldás**: futtassa ugyanazt a LEKÉRDEZÉST a SSMS, és ellenőrizze, hogy ugyanazt az eredményt látja-e. Ha igen, nyisson egy támogatási jegyet az Azure SQL Data Warehouse-hoz kapcsolódóan, és adja meg az SQL DW-kiszolgáló és az adatbázis nevét a további hibaelhárításhoz.
            

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Hibakód: AzureBlobOperationFailed

- **Üzenet**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **OK**: a blob Storage-művelettel kapcsolatos probléma lépett fel.

- **Javaslat**: Tekintse meg a hibát a részletek között. Tekintse meg a blob súgóját: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Ha segítségre van szüksége, lépjen kapcsolatba a Storage csapatával.



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Hibakód: AdlsGen2OperationFailed

- **Üzenet**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **OK**: a ADLS Gen2 a művelet hibáját jelző hibaüzenetet nem sikerült.

- **Javaslat**: tekintse meg ADLS Gen2 által kiváltott részletes hibaüzenetet. Ha átmeneti hiba okozta, próbálkozzon újra. Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure Storage támogatási szolgálatával, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben.

- **OK**: Ha a hibaüzenet "tiltott" üzenetet tartalmaz, akkor előfordulhat, hogy az Ön által használt egyszerű szolgáltatásnév vagy felügyelt identitás nem rendelkezik elegendő engedéllyel a ADLS Gen2 eléréséhez.

- **Javaslat**: Tekintse meg a következő Súgó dokumentumot: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **OK**: Ha a hibaüzenetben a "InternalServerError" szöveg szerepel, a ADLS Gen2 a hibát adja vissza.

- **Javaslat**: Előfordulhat, hogy az átmeneti hiba okozta. próbálkozzon újra. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot az Azure Storage támogatási szolgálatával, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben.


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
            
