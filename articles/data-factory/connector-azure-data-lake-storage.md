---
title: Adatok másolása Azure Data Lake Storage Gen2 a Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok Azure Data Lake Storage Gen2ba és onnan a Azure Data Factory használatával.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: e21ae2f8eda4521effa5b7db686fe72241aa4cdb
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276283"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Adatok másolása Azure Data Lake Storage Gen2 a Azure Data Factory használatával

A Azure Data Lake Storage Gen2 (ADLS Gen2) az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ba beépített Big Data-elemzésekhez rendelt képességek összessége. Az adataihoz a fájlrendszer és az objektum tárolási paradigma segítségével is felhasználhatja a felületet.

Ez a cikk az adatok Azure Data Lake Storage Gen2ba való másolásának módját ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen2-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-vagy fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban, ezzel az összekötővel a következőket teheti:

- Adatmásolás a fiók kulcsa, a szolgáltatásnév vagy a felügyelt identitások használatával az Azure-erőforrások hitelesítéséhez.
- Fájlok másolása másként, vagy a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md)rendelkező fájlok elemzése vagy készítése.

>[!TIP]
>Ha engedélyezi a hierarchikus névteret, a blob és a Data Lake Storage Gen2 API-k között jelenleg nem áll rendelkezésre együttműködési képesség. Ha a "ErrorCode = FilesystemNotFound" hibaüzenet jelenik meg a "a megadott fájlrendszer nem létezik" üzenettel, azt a rendszer a blob API-n keresztül létrehozott megadott fogadó fájlrendszer okozta, máshol nem Data Lake Storage Gen2 API-t. A probléma megoldásához adjon meg egy olyan nevű új fájlrendszert, amely nem létezik blob-tároló neveként. Ezután Data Factory automatikusan létrehozza az adott fájlrendszert az Adatmásolás során.

>[!NOTE]
>Ha engedélyezi a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz** lehetőséget az Azure Storage-tűzfal beállításainál, az Azure Integration Runtime nem csatlakozik Data Lake Storage Gen2hoz, és megjelenít egy tiltott hibát. A hibaüzenet akkor jelenik meg, ha Data Factory nem megbízható Microsoft-szolgáltatásként van kezelve. Ehelyett a saját üzemeltetésű integrációs modult használja a csatlakozáshoz.

## <a name="get-started"></a>Bevezetés

>[!TIP]
>Az Data Lake Storage Gen2-összekötő használatáról a következő témakörben olvashat: az [adatAzure Data Lake Storage Gen2ba való betöltés](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban információt talál a Data Lake Storage Gen2ra vonatkozó Data Factory entitások definiálásához használt tulajdonságokról.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Data Lake Storage Gen2-összekötő a következő hitelesítési típusokat támogatja. A részletekért tekintse meg a megfelelő részeket:

- [Fiók kulcsos hitelesítés](#account-key-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások Azure-erőforrások hitelesítéshez](#managed-identity)

>[!NOTE]
>Ha az adatok SQL Data Warehouseba való betöltéséhez a forrás-Data Lake Storage Gen2 Virtual Network végponttal van konfigurálva, akkor a következőt kell használnia a felügyelt identitások hitelesítéséhez:. Tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt további konfigurálási előfeltételekkel.

### <a name="account-key-authentication"></a>Fiók kulcsos hitelesítés

A storage-fiók kulcsos hitelesítést használ, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureBlobFS**. |Igen |
| url | Data Lake Storage Gen2 végpontja a mintázatával `https://<accountname>.dfs.core.windows.net`. | Igen |
| accountKey | Data Lake Storage Gen2hoz tartozó fiók kulcsa. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

>[!NOTE]
>A másodlagos ADLS-fájlrendszer végpontja nem támogatott a fiók kulcsos hitelesítésének használatakor. Más hitelesítési típusokat is használhat.

**Példa:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket.

1. Az alkalmazás [regisztrálása Azure ad-Bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című cikk lépéseit követve regisztrálja az alkalmazás entitásait Azure Active Directoryban (Azure ad). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatásnév megfelelő engedélyét. További információ arról, hogy az engedély hogyan működik Data Lake Storage Gen2 a [fájlok és könyvtárak hozzáférés-vezérlési listájáról](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Forrásként**: A Storage Explorerban legalább **végrehajtási** engedélyt kell megadnia a forrásfájl-rendszertől kezdve, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - **Fogadóként:** A Storage Explorer a fogadó fájlrendszertől kezdődően legalább **végrehajtási** engedélyt adjon meg, valamint **írási** engedéllyel a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!NOTE]
>A fiók szintjétől kezdődő vagy a kapcsolatok tesztelésére szolgáló mappák listázásához be kell állítania az egyszerű szolgáltatásnév számára a **"Storage blob Adatolvasó" engedéllyel rendelkező Storage-fiók engedélyét a iam-ban**. Ez akkor igaz, ha a következőt használja:
>- **Adatmásolási eszköz** másolása másolási folyamat létrehozásához.
>- **Data Factory felhasználói felület** a kapcsolatok teszteléséhez és a mappák navigálásához a szerzői műveletek során. 
>Ha problémái vannak az engedélyek megadásával kapcsolatban a fiók szintjén, a szerzői műveletek, a tesztelési kapcsolatok kihagyása és a megadott engedéllyel rendelkező szülő elérési út megadása után válassza a megadott elérési útról való Tallózás lehetőséget. A másolási tevékenység akkor működik, ha az egyszerű szolgáltatásnév megfelelő engedélyekkel rendelkezik a másolandó fájlok számára.

A társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureBlobFS**. |Igen |
| url | Data Lake Storage Gen2 végpontja a mintázatával `https://<accountname>.dfs.core.windows.net`. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. A mező megjelölése `SecureString` úgy, hogy biztonságosan tárolja Data Factoryban. Vagy [hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Lekéri a Azure Portal jobb felső sarkában lévő egér fölé. | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md), amely az adott adat-előállító jelöli. Ezt a felügyelt identitást közvetlenül használhatja Data Lake Storage Gen2 hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához is. Lehetővé teszi a kijelölt gyár számára az adatok elérését és másolását a Data Lake Storage Gen2.

Az Azure erőforrás-hitelesítéshez használt felügyelt identitások használatához kövesse az alábbi lépéseket.

1. [Kérje le a Data Factory felügyelt identitás adatait](data-factory-service-identity.md#retrieve-managed-identity) úgy, hogy átmásolja a **szolgáltatás identitás-alkalmazásának azonosítóját** a gyárával együtt.

2. Adja meg a felügyelt identitás megfelelő engedélyét. További információ arról, hogy az engedély hogyan működik Data Lake Storage Gen2 a [fájlok és könyvtárak hozzáférés-vezérlési listájáról](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Forrásként**: A Storage Explorerban legalább **végrehajtási** engedélyt kell megadnia a forrásfájl-rendszertől kezdve, valamint **olvasási** engedéllyel kell rendelkeznie a másolandó fájlokhoz. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob-Adatolvasó** szerepkört.
    - **Fogadóként:** A Storage Explorer a fogadó fájlrendszertől kezdődően legalább **végrehajtási** engedélyt adjon meg, valamint **írási** engedéllyel a fogadó mappához. Másik lehetőségként a hozzáférés-vezérlés (IAM) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!NOTE]
>A fiók szintjétől kezdődő vagy a kapcsolatok teszteléséhez szükséges mappák listázásához be kell állítania a felügyelt identitás számára a **"Storage blob Adatolvasó" engedéllyel rendelkező Storage-fiók engedélyét a iam-ban**. Ez akkor igaz, ha a következőt használja:
>- **Adatmásolási eszköz** másolása másolási folyamat létrehozásához.
>- **Data Factory felhasználói felület** a kapcsolatok teszteléséhez és a mappák navigálásához a szerzői műveletek során. 
>Ha problémái vannak az engedélyek megadásával kapcsolatban a fiók szintjén, a szerzői műveletek, a tesztelési kapcsolatok kihagyása és a megadott engedéllyel rendelkező szülő elérési út megadása után válassza a megadott elérési útról való Tallózás lehetőséget. A másolási tevékenység akkor működik, ha az egyszerű szolgáltatásnév megfelelő engedélyekkel rendelkezik a másolandó fájlok számára.

>[!IMPORTANT]
>Ha a Data Lake Storage Gen2 SQL Data Warehouseba való betöltéséhez használja a-alapú adatkészletet, akkor a Data Lake Storage Gen2 felügyelt identitások hitelesítésének használatakor ügyeljen arra, hogy az [útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. és 2. lépését is végrehajtsa 1.) a SQL Database-kiszolgáló Azure Active Directory (Azure AD) és 2) rendelje hozzá a Storage blob adatközreműködői szerepkört a SQL Database-kiszolgálóhoz; a REST-et a Data Factory kezeli. Ha a Data Lake Storage Gen2 egy Azure Virtual Network-végponttal van konfigurálva, akkor az adatoknak a használatával történő betöltéséhez használja a felügyelt identitás-hitelesítést.

A társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureBlobFS**. |Igen |
| url | Data Lake Storage Gen2 végpontja a mintázatával `https://<accountname>.dfs.core.windows.net`. | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

**Példa**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

- A **parketta, a tagolt szöveg, a Avro és a bináris formátum**esetében lásd a [parketta, tagolt szöveg és bináris formátum adatkészlet](#format-based-dataset) szakaszt.
- Más formátumok, például az **ork/JSON formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, Avro és bináris formátum adatkészlet

Ha a **parketta, a tagolt szöveg, a Avro vagy a bináris formátum**között szeretne adatokat másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú adatkészleten és a támogatott beállításokon. A következő tulajdonságok támogatottak Data Lake Storage Gen2 `location` a Format-alapú adatkészlet beállítások területén:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben `location` található Type tulajdonságot **AzureBlobFSLocation**értékre kell állítani. | Igen      |
| fileSystem | A Data Lake Storage Gen2 fájlrendszer neve.                              | Nem       |
| folderPath | Az adott fájlrendszerben található mappa elérési útja. Ha a mappák szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |
| fileName   | A fájl neve a megadott fileSystem + folderPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |

> [!NOTE]
> A következő szakaszban említett, a Parquet vagy szöveges formátumot tartalmazó **AzureBlobFSFile** -típus adatkészlete továbbra is támogatott a visszamenőleges kompatibilitás céljából történő másolási, keresési vagy GetMetaData tevékenység esetén. Ez azonban nem működik a leképezési adatfolyam-szolgáltatással. Javasoljuk, hogy ezt az új modellt használja tovább. Az Data Factory szerzői felhasználói felület ezeket az új típusokat hozza létre.

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Egyéb formátumú adatkészlet

Az adatoknak az **ork/JSON formátumban**Data Lake Storage Gen2ba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **AzureBlobFSFile**. |Igen |
| folderPath | A Data Lake Storage Gen2 mappájának elérési útja. Ha nincs megadva, a legfelső szintű mutat. <br/><br/>A helyettesítő karakteres szűrő használata támogatott. Az `*` engedélyezett helyettesítő karakterek (nulla vagy több karakternek felelnek `?` meg) és (nulla vagy egyetlen karakternek felelnek meg). Ha `^` a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter be van zárva, használja a Escape-nevet. <br/><br/>Példák: fájlrendszer/mappa/. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. |Nem |
| fileName | A megadott "folderPath" alatt lévő fájlok neve vagy helyettesítő karakteres szűrője. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>A Filter `*` (szűrő) esetében a megengedett helyettesítő karakterek (nulla vagy több karakternek `?` felelnek meg) és (a nulla vagy egyetlen karakternek felel meg).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Ha `^` a tényleges fájlnév helyettesítő karakterrel rendelkezik, vagy a escape-karakter be van zárva, használja a Escape-nevet.<br/><br/>Ha nincs megadva a fájlnév egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*Az adatgyűjtés. [tevékenység futtatásának azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" az "adat. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz". Ha egy táblázatos forrásból másol egy lekérdezés helyett egy tábla nevét, a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és `modifiedDatetimeStart` `modifiedDatetimeEnd`a közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` a dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` a dátum datetime értékkel `modifiedDatetimeStart` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és `modifiedDatetimeStart` `modifiedDatetimeEnd`a közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` a dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` a dátum datetime értékkel `modifiedDatetimeStart` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| format | Ha szeretné másolni a fájlokat, a fájlalapú tárolók (bináris másolat) közötti, hagyja ki a format szakaszban, mind a bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs.md#parquet-format) . |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** mappát, és nevezze el a **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **fájlnevet** egy helyettesítő szűrővel kell megadnia. 

**Példa:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a [másolási tevékenységek konfigurációi](copy-activity-overview.md#configuration) és a [folyamatok és tevékenységek](concepts-pipelines-activities.md)című részben találja. Ez a szakasz a Data Lake Storage Gen2 forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 a forrás típusaként

- A **parketta, a tagolt szöveg, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork/JSON formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, Avro és bináris formátum forrása

Ha a **parketta, a tagolt szöveg, a Avro vagy a bináris formátum**adatait szeretné átmásolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a formátum-alapú másolási tevékenység forrására . A következő tulajdonságok támogatottak Data Lake Storage Gen2 formátum- `storeSettings` alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A Type tulajdonságot `storeSettings` a **AzureBlobFSReadSetting**értékre kell állítani. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott fájlrendszerben helyettesítő karakterekkel. <br>Az `*` engedélyezett helyettesítő karakterek (nulla vagy több karakternek felelnek `?` meg) és (nulla vagy egyetlen karakternek felelnek meg). Akkor `^` használja a-et, ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott fájlrendszer + folderPath/wildcardFolderPath helyettesítő karakterekkel rendelkező fájlnév. <br>Az `*` engedélyezett helyettesítő karakterek (nulla vagy több karakternek felelnek `?` meg) és (nulla vagy egyetlen karakternek felelnek meg). Akkor `^` használja a-et, ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a belsejében. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és `modifiedDatetimeStart` `modifiedDatetimeEnd`a közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` a dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` a dátum datetime értékkel `modifiedDatetimeStart` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

> [!NOTE]
> A parketta vagy a tagolt szöveg formátuma esetén a következő szakaszban említett **AzureBlobFSSource** -típus másolási tevékenysége továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy ezt az új modellt használja tovább. Az Data Factory szerzői felhasználói felület ezeket az új típusokat hozza létre.

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Egyéb formátum forrása

Ha az Data Lake Storage Gen2t az **ork/JSON formátumban**szeretné másolni, a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **AzureBlobFSSource**. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát.<br/>Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Az Azure Data Lake Storage Gen2 a fogadó típusa

- A **parketta, a tagolt szöveg, a Avro vagy a bináris formátum**másolásához tekintse meg a következőt: [parketta, tagolt szöveg és bináris formátum](#format-based-sink) fogadó szakasz.
- Más formátumokba, például az **ork/JSON formátumba**való másoláshoz tekintse meg a [más Format mosogató](#other-format-sink) szakaszt.

#### <a name="format-based-sink"></a>Parketta, tagolt szöveg, Avro és bináris formátumú fogadó

Az adatmásoláshoz a **parketta, a tagolt szöveg, a Avro vagy a bináris formátum**a [parketta formátuma](format-parquet.md), a [tagolt szöveg formátuma](format-delimited-text.md), a [Avro formátum](format-avro.md) és a [bináris formátum](format-binary.md) cikk a Format-alapú másolási tevékenység fogadója és a támogatott beállítások alapján. A következő tulajdonságok támogatottak Data Lake Storage Gen2 `storeSettings` a Format-alapú másolási fogadó beállításaiban:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A Type tulajdonságot `storeSettings` a **AzureBlobFSWriteSetting**értékre kell állítani. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a fájl hierarchiáját a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: A forrás mappából származó összes fájl a célmappa első szintjén található. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egyesít minden fájlt a forrás mappájából egy fájlba. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

> [!NOTE]
> A parketta vagy a tagolt szöveg formátuma esetén a következő szakaszban említett **AzureBlobFSSink** -típus másolási tevékenység fogadója továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy ezt az új modellt használja tovább. Az Data Factory szerzői felhasználói felület ezeket az új típusokat hozza létre.

**Példa:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Egyéb formátumú fogadó

Ha az Data Lake Storage Gen2t az **ork/JSON formátumban**szeretné átmásolni, a **fogadó szakaszban a** következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **AzureBlobFSSink**. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a fájl hierarchiáját a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: A forrás mappából származó összes fájl a célmappa első szintjén található. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egyesít minden fájlt a forrás mappájából egy fájlba. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | recursive | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, alapértelmezett használata) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Üres, alapértelmezett használata) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>A rekurzív és copyBehavior példákat

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + File5 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>ACL-ek megőrzése Data Lake Storage Gen1

>[!TIP]
>Az adatok Azure Data Lake Storage Gen1ból a Gen2-be való másolásához lásd: [adatok másolása Azure Data Lake Storage Gen1ról a Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) -be a Azure Data Factory használatával a lépésenkénti és ajánlott eljárásokhoz.

Ha Azure Data Lake Storage Gen1ról másol fájlokat a Gen2, megadhatja, hogy a POSIX hozzáférés-vezérlési listákat (ACL-eket) az adatokkal együtt őrizze meg. A hozzáférés-vezérléssel kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) és [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../storage/blobs/data-lake-storage-access-control.md).

A következő ACL-típusok megtekinthetők az Azure Data Factory másolási tevékenység használatával. Kiválaszthat egy vagy több típust is:

- **ACL**: A POSIX hozzáférés-vezérlési listáját másolja és őrizze meg a fájlokon és könyvtárakon. A teljes meglévő ACL-eket a forrásról a fogadóra másolja. 
- **Tulajdonos**: A fájlok és könyvtárak tulajdonos felhasználójának másolása és megőrzése. A felhasználók a fogadó Data Lake Storage Gen2hoz való hozzáférésre van szükség.
- **Csoport**: A fájlok és könyvtárak tulajdonosi csoportjának másolása és megőrzése. A felhasználó hozzáférése a fogadó Data Lake Storage Gen2hoz vagy a tulajdonos felhasználóhoz (ha a tulajdonos felhasználó a célcsoport tagja is) szükséges.

Ha egy mappából történő másolást ad meg, Data Factory replikálja a megadott mappára és a benne lévő fájlokra és könyvtárakra vonatkozó ACL `recursive` -eket, ha az igaz értékre van állítva. Ha egyetlen fájlból történő másolást ad meg, a rendszer átmásolja a fájl ACL-jeit.

>[!IMPORTANT]
>Ha úgy dönt, hogy megőrzi az ACL-eket, győződjön meg arról, hogy elég magas engedélyekkel rendelkezik ahhoz, hogy a Data Factory a fogadó Data Lake Storage Gen2-fiókjával működjenek. Használhatja például a fiók kulcsának hitelesítését, vagy hozzárendelheti a Storage blob-adatok tulajdonosi szerepkörét az egyszerű szolgáltatásnév vagy a felügyelt identitás számára.

Ha a forrást Data Lake Storage Gen1ként konfigurálja a bináris másolási lehetőséggel vagy bináris formátummal, a fogadó pedig Data Lake Storage Gen2ként a bináris másolási lehetőséggel vagy bináris formátummal, akkor a **megőrzés** lehetőséget a **adatok másolása eszköz beállításai** lapon vagy a tevékenység-létrehozás **másolási tevékenység** > **beállításainak** lapja.

![Data Lake Storage Gen1 a Gen2 megőrzése ACL-hez](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Íme egy példa a JSON-konfigurációra ( `preserve`lásd:): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

További információ a [forrás-átalakításról](data-flow-source.md) és a fogadó [transzformációról](data-flow-sink.md) a leképezési adatfolyam funkcióban.

## <a name="next-steps"></a>További lépések

A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
