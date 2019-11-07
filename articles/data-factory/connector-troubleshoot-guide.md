---
title: Azure Data Factory összekötők hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory összekötői problémáit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 8cabc1031f9d0be772ba087109ae1dfc881ce163
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680084"
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

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

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

- **Megoldás**: futtassa ugyanazt a LEKÉRDEZÉST a SSMS, és ellenőrizze, hogy ugyanazt az eredményt látja-e. Ha igen, nyisson meg egy támogatási jegyet a Azure SQL Data Warehousehoz, és adja meg az SQL DW-kiszolgáló és az adatbázis nevét a további hibák megoldásához.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: a kérelem mérete túl nagy

- **Tünetek**: az adatmásolás Azure Cosmos DBba az alapértelmezett írási köteg méretével, és a *"**kérés mérete túl nagy**"* .

- **OK**: a Cosmos db egy kérés méretét 2 MB-ra korlátozza. A képlet:, kérelem mérete = egydokumentumos méret * írási köteg mérete. Ha a dokumentum mérete nagy, az alapértelmezett viselkedés túl nagy méretű kérések méretét eredményezi. Beállíthatja az írási köteg méretét.

- **Megoldás**: a másolási tevékenység fogadójában csökkentse a "batch méretének megírása" értéket (az alapértelmezett érték 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Hibaüzenet: egyedi index megkötésének megsértése

- **Tünetek**: az adatok Cosmos DBba való másolása során a következő hibaüzenetet kapta:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
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

## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory-blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)



