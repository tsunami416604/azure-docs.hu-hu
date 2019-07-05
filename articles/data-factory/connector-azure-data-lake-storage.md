---
title: Adatok másolása, illetve az Azure Data Lake Storage Gen2 a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat és az Azure Data Lake Storage Gen2 Azure Data Factory használatával.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 9f60c6258da77c0aaa99d16e178f4b3531ce90d9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509249"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Data Lake Storage Gen2 Azure Data Factory használatával

Az Azure Data Lake Storage Gen2 dedikált beépített big data-elemzési képességeket [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Használhatja kommunikáljon az adatok a mindkét fájl rendszer és a objektum tárolási paradigmákat használatával.

Ez a cikk ismerteti, hogyan másolhat adatokat, és az Azure Data Lake Storage Gen2. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure Data Lake Storage Gen2 összekötő támogatott a következő tevékenységeket:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás és fogadó mátrix](copy-activity-overview.md)
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban az összekötő a következőket teheti:

- Adatok másolása az Azure-erőforrások hitelesítések fiókkulcs, szolgáltatásnevet vagy felügyelt identitások használatával.
- Másolja a fájlokat, vagy elemezni, vagy hozzon létre fájlokat [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Ha engedélyezi a hierarchikus névtér, jelenleg nincs műveletek Blob és a Data Lake Storage Gen2 API-k közötti együttműködés. Ha eléri a hiba "ErrorCode = FilesystemNotFound" "a megadott fájlrendszer nem létezik" üzenettel okozza a megadott fogadó fájlrendszer létrehozott Data Lake Storage Gen2 API helyett a Blob API-n keresztül máshol. A probléma megoldásához, adjon meg egy új fájlrendszer, amelynek neve nem létezik a blobtároló nevét. Majd a Data Factory automatikusan létrehozza a fájlrendszer adatmásolás során.

>[!NOTE]
>Ha engedélyezi a **engedélyezése megbízható Microsoft-szolgáltatások a tárfiók** lehetőség az Azure Storage-tűzfal beállításait, az Azure-beli integrációs modul nem csatlakozhat a Data Lake Storage Gen2, és bemutatja a rendszer tiltott hibát észlelt. A hibaüzenet jelenik meg, mert a Data Factory egy megbízható Microsoft-szolgáltatás nem számít. A saját üzemeltetésű integrációs modul használatával kapcsolódhat helyette.

## <a name="get-started"></a>Bevezetés

>[!TIP]
>A Data Lake Storage Gen2-összekötő használatára a lépésenkénti útmutatóért lásd: [adatok betöltése az Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok ismertetik a Data Lake Storage Gen2-re adott Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Data Lake Storage Gen2-összekötő a következő hitelesítési típusokat támogatja. Tekintse meg a megfelelő szakaszokra kapcsolatos részletek:

- [Fiók kulcsos hitelesítés](#account-key-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások Azure-erőforrások hitelesítéshez](#managed-identity)

>[!NOTE]
>A PolyBase az adatok betöltése az SQL Data Warehouse, ha a Data Lake Storage Gen2 forrás beállítása a virtuális hálózati végpont, ha szükséges, a PolyBase által felügyelt identitás hitelesítést kell használnia. Tekintse meg a [felügyelt identitás hitelesítési](#managed-identity) további konfigurációs az Előfeltételek szakaszban.

### <a name="account-key-authentication"></a>Fiók kulcsos hitelesítés

A storage-fiók kulcsos hitelesítést használ, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureBlobFS**. |Igen |
| url | A Data Lake Storage Gen2 végpont minta `https://<accountname>.dfs.core.windows.net`. | Igen |
| accountKey | Data Lake Storage Gen2-fiók kulcsát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha az adattár egy magánhálózaton lévő használhatja az Azure-beli integrációs modul és a egy saját üzemeltetésű integrációs modult. Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

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

Egyszerű szolgáltatásnév hitelesítése használatához kövesse az alábbi lépéseket.

1. A lépéseket követve az Azure Active Directoryban (Azure AD) application entitás regisztrálása [regisztrálja az alkalmazást az Azure AD-bérlő](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatás egyszerű megfelelő engedéllyel. Engedély a Data Lake Storage Gen2 a működéséről további [hozzáférés-vezérlési listák a fájlok és könyvtárak](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Forrásként**: A Storage Explorerben, adjon meg legalább **Execute** kezdve a forrásrendszerben fájlt, az engedély **olvasási** engedéllyel a fájlok másolásához. Másik lehetőségként a hozzáférés-vezérlés (IAM), adja meg legalább az **Storage-Blobadatok olvasója** szerepkör.
    - **Fogadóként**: A Storage Explorerben, adjon meg legalább **Execute** kezdve a fogadó fájlrendszerből mentén engedély **írási** a fogadó mappa engedéllyel. Másik lehetőségként a hozzáférés-vezérlés (IAM), adja meg legalább az **Storage-Blobadatok Közreműködője** szerepkör.

>[!NOTE]
>A lista mappákat a fiók szintjén, vagy a kapcsolat teszteléséhez, be kell állítania a szolgáltatásnév az engedély megadása az engedély **tárfiók IAM a "Storage-Blobadatok olvasója" engedéllyel rendelkező**. Ez igaz, ha használja a:
>- **Másolás eszköz** a szerző másolási folyamat.
>- **Data Factory felhasználói felülete** tesztelheti a kapcsolatot, és lépjen a mappák létrehozása alatt. 
>Ha kétségei vannak, létrehozása alatt a fiók szintjén engedély megadására vonatkozó hagyja ki a kapcsolat és a bemeneti engedély megadása a szülő elérési utat a Tallózás gombra, majd válassza a megadott elérési útja. Másolja a tevékenység működése, mindaddig, amíg az egyszerű szolgáltatás átmásolni a fájlokat a megfelelő engedéllyel rendelkező kapnak.

Ezek a Tulajdonságok támogatottak a társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureBlobFS**. |Igen |
| url | A Data Lake Storage Gen2 végpont minta `https://<accountname>.dfs.core.windows.net`. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy `SecureString` tárolja biztonságos helyen a Data Factoryban. Is [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha az adattár egy magánhálózaton lévő használhatja az Azure-beli integrációs modul és a egy saját üzemeltetésű integrációs modult. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md), amely az adott adat-előállító jelöli. Használhatja a felügyelt identitást közvetlenül a Data Lake Storage Gen2-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatás használatával. A kijelölt factory való eléréséhez és másolásához-adatok a Data Lake Storage Gen2 lehetővé teszi.

Azure-erőforrás-hitelesítéshez használandó felügyelt identitások, kövesse az alábbi lépéseket.

1. [A Data Factory által felügyelt azonosító információkat lekérni](data-factory-service-identity.md#retrieve-managed-identity) értékét másolásával az **szolgáltatás identitás Alkalmazásazonosítója** az előállító együtt jönnek létre.

2. Adja meg a felügyelt identitás megfelelő engedéllyel. Engedély a Data Lake Storage Gen2 a működéséről további [hozzáférés-vezérlési listák a fájlok és könyvtárak](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Forrásként**: A Storage Explorerben, adjon meg legalább **Execute** kezdve a forrásrendszerben fájlt, az engedély **olvasási** engedéllyel a fájlok másolásához. Másik lehetőségként a hozzáférés-vezérlés (IAM), adja meg legalább az **Storage-Blobadatok olvasója** szerepkör.
    - **Fogadóként**: A Storage Explorerben, adjon meg legalább **Execute** kezdve a fogadó fájlrendszerből mentén engedély **írási** a fogadó mappa engedéllyel. Másik lehetőségként a hozzáférés-vezérlés (IAM), adja meg legalább az **Storage-Blobadatok Közreműködője** szerepkör.

>[!NOTE]
>Listára mappákat a fiók szintjén, vagy a kapcsolat teszteléséhez, be kell állítania az engedély az engedély megadása a felügyelt identitás **IAM a "Storage-Blobadatok olvasója" engedéllyel rendelkező tárfiók**. Ez igaz, ha használja a:
>- **Másolás eszköz** a szerző másolási folyamat.
>- **Data Factory felhasználói felülete** tesztelheti a kapcsolatot, és lépjen a mappák létrehozása alatt. 
>Ha kétségei vannak, létrehozása alatt a fiók szintjén engedély megadására vonatkozó hagyja ki a kapcsolat és a bemeneti engedély megadása a szülő elérési utat a Tallózás gombra, majd válassza a megadott elérési útja. Másolja a tevékenység működése, mindaddig, amíg az egyszerű szolgáltatás átmásolni a fájlokat a megfelelő engedéllyel rendelkező kapnak.

>[!IMPORTANT]
>Ha a PolyBase használatával adatok betöltése az Data Lake Storage Gen2 az SQL Data Warehouse, a Data Lake Storage Gen2 felügyelt identitás-hitelesítés használata esetén, győződjön meg arról is lépéseket 1. és 2 a [Ez az útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1) regisztrálja az SQL Adatbázis-kiszolgáló az Azure Active Directoryval (Azure AD) és 2) a Storage-Blobadatok Közreműködője szerepkör hozzárendelése az SQL Database-kiszolgáló; a Data Factory kezeli, a többi. Ha a Data Lake Storage Gen2 van konfigurálva az Azure Virtual Network-végponttal, az adatok betöltése a PolyBase használatával kell használnia felügyelt identitás hitelesítési PolyBase igényeinek megfelelően.

Ezek a Tulajdonságok támogatottak a társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureBlobFS**. |Igen |
| url | A Data Lake Storage Gen2 végpont minta `https://<accountname>.dfs.core.windows.net`. | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha az adattár egy magánhálózaton lévő használhatja az Azure-beli integrációs modul és a egy saját üzemeltetésű integrációs modult. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

- A parquet eszközökben és tagolt szövegformátum, tekintse meg a [Parquet és elválasztójellel tagolt szöveges formátumban adatkészlet](#parquet-and-delimited-text-format-dataset) szakaszban.
- Más formátumokhoz, például ORC, Avro, JSON vagy bináris formátum, tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszban.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet és elválasztójellel tagolt szöveges formátum adatkészlet

Adatok másolása és a Data Lake Storage Gen2 a parquet vagy tagolt szövegformátum, tekintse meg a [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) cikkek formátum-alapú adatkészlet és a támogatott beállítások. A következő tulajdonságok támogatottak a Data Lake Storage Gen2 alatt `location` formátum-alapú adatkészlet beállításai:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A type tulajdonság alatt `location` az adatkészletben lévő értékre kell állítani **AzureBlobFSLocation**. | Igen      |
| fileSystem | A Data Lake Storage Gen2 fájlrendszer neve.                              | Nem       |
| folderPath | A megadott fájl rendszerben mappába elérési útja. Ha szeretne egy helyettesítő karakter segítségével szűrő mappákat, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |
| fileName   | A fájl neve, az adott fájlrendszer + folderPath alatt. Ha szeretne egy helyettesítő karakter használatával szűrhetők a fájlok, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |

> [!NOTE]
> A **AzureBlobFSFile** parquet vagy szöveges formátum a következő szakaszban említett adatkészlet típusa, másolása, a keresési és a GetMetadata tevékenység a visszamenőleges kompatibilitás érdekében továbbra is támogatott. De a leképezés data flow szolgáltatással nem működik. Azt javasoljuk, hogy használja-e az új modell, a jövőben. A Data Factory felhasználói felületén szerzői állít elő, hogy ezek a típusok.

**Példa**

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

### <a name="other-format-dataset"></a>Más formátumú adatkészlet

A Data Lake Storage Gen2 ORC, Avro, JSON vagy bináris formátumot az adatok másolását, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **AzureBlobFSFile**. |Igen |
| folderPath | A Data Lake Storage Gen2 a mappa elérési útját. Ha nincs megadva, a legfelső szintű mutat. <br/><br/>Helyettesítő karaktert tartalmazó szűrő használata támogatott. A helyettesítő karakterek engedélyezettek `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter). Használat `^` elkerülésére, ha az aktuális mappa neve szerepel egy helyettesítő karakter, vagy a escape karaktere belül. <br/><br/>Példák: fájlrendszer/mappa /. További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). |Nem |
| fileName | Név vagy helyettesítő karaktert tartalmazó szűrő a fájlok a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>A szűrő a helyettesítő karakterek engedélyezve vannak `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges neve szerepel egy helyettesítő karakter, vagy a escape karaktere belül.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a másolási tevékenység létrehozza a fájl neve a következő mintának: "*Adatokat. [a tevékenység futtatása azonosító GUID]. [GUID Ha FlattenHierarchy]. [Ha a konfigurált formátum]. [Ha konfigurálta a tömörítés]* ", például"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Ha valamely tábla neve helyett egy lekérdezést a táblázatos forrásból másol, van-e a névminta " *[táblanév]. [ formátum]. [Ha konfigurálta a tömörítés]* ", például"MyTable.csv". |Nem |
| modifiedDatetimeStart | Fájlok szűrése az utolsó módosítás attribútum alapján. A fájlok ki van jelölve, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Adatáthelyezés általános teljesítményét befolyásolja a beállítás engedélyezése, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet null értékű, ami azt jelenti, hogy nincs fájl attribútum szűrő alkalmazása az adatkészlethez. Amikor `modifiedDatetimeStart` egy datetime értéket tartalmaz, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a DateTime típusú érték van kijelölve. Amikor `modifiedDatetimeEnd` egy datetime értéket tartalmaz, de `modifiedDatetimeStart` má hodnotu NULL, ez azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútuma kisebb, mint a dátum/idő érték kiválasztva.| Nem |
| modifiedDatetimeEnd | Fájlok szűrése az utolsó módosítás attribútum alapján. A fájlok ki van jelölve, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Adatáthelyezés általános teljesítményét befolyásolja a beállítás engedélyezése, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet null értékű, ami azt jelenti, hogy nincs fájl attribútum szűrő alkalmazása az adatkészlethez. Amikor `modifiedDatetimeStart` egy datetime értéket tartalmaz, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a DateTime típusú érték van kijelölve. Amikor `modifiedDatetimeEnd` egy datetime értéket tartalmaz, de `modifiedDatetimeStart` má hodnotu NULL, ez azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútuma kisebb, mint a dátum/idő érték kiválasztva.| Nem |
| format | Ha szeretné másolni a fájlokat, a fájlalapú tárolók (bináris másolat) közötti, hagyja ki a format szakaszban, mind a bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretné elemezni, vagy hozzon létre egy adott formátumú fájlok formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ORC formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum ](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** egy mappára vonatkozó részt a és **fileName** a fájl nevét.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** egy mappára vonatkozó részt a és **fileName** egy helyettesítő szűrővel. 

**Példa**

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: [tevékenységkonfigurációkhoz másolása](copy-activity-overview.md#configuration) és [folyamatok és tevékenységek](concepts-pipelines-activities.md). Ez a szakasz a Data Lake Storage Gen2 forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Az Azure Data Lake Storage Gen2 a forrás típusa

- Másolja át a parquet vagy tagolt szövegformátum, tekintse meg a [elválasztójellel tagolt szöveges formátum forrás- és Parquet](#parquet-and-delimited-text-format-source) szakaszban.
- Más tartalomfolyamokat ORC, az avro-hoz, a JSON és a bináris formátum másolása, tekintse meg a [más formátumú forrás](#other-format-source) szakaszban.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet és elválasztójellel tagolt szöveges formátum forrása

Data Lake Storage Gen2 adatokat másol parquet vagy elválasztójellel tagolt szöveges formátumban, tekintse meg a [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység forrás- és a támogatott beállítások című cikket. A következő tulajdonságok támogatottak a Data Lake Storage Gen2 alatt `storeSettings` formátum-alapú másolási forrásaként beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AzureBlobFSReadSetting**. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha rekurzív értéke igaz, és a fogadó egy fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | A megadott fájl rendszerben konfigurált adatforrás mappákba szűrő adatkészlet helyettesítő karaktereket is tartalmazó mappa elérési. <br>A helyettesítő karakterek engedélyezettek `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter). Használat `^` elkerülésére, ha a tényleges mappanevet helyettesítő karakter, vagy a escape karaktere belül. <br>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Nem                                            |
| wildcardFileName         | A helyettesítő karakterek az adott fájlrendszer + folderPath/wildcardFolderPath szűrő forrásfájlok a fájl neve. <br>A helyettesítő karakterek engedélyezettek `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter). Használat `^` elkerülésére, ha a tényleges mappanevet helyettesítő karakter, vagy a escape karaktere belül. További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Igen, ha `fileName` nincs megadva a adatkészlet |
| modifiedDatetimeStart    | Fájlok szűrése az utolsó módosítás attribútum alapján. A fájlok ki van jelölve, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami azt jelenti, hogy nincs fájl attribútum szűrő alkalmazása az adatkészlethez. Amikor `modifiedDatetimeStart` egy datetime értéket tartalmaz, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosításának attribútum nagyobb vagy egyenlő a DateTime típusú érték van kijelölve. Amikor `modifiedDatetimeEnd` egy datetime értéket tartalmaz, de `modifiedDatetimeStart` má hodnotu NULL, az azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútuma a kisebb, mint a dátum/idő érték van kiválasztva. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | Szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem                                            |

> [!NOTE]
> A parquet vagy tagolt szövegformátum a **AzureBlobFSSource** típusa másolási tevékenység forrása az alábbi szakaszban ismertetett továbbra is támogatott, mert a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja-e az új modell, a jövőben. A Data Factory felhasználói felületén szerzői állít elő, hogy ezek a típusok.

**Példa**

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

#### <a name="other-format-source"></a>Más formátumú forrás

Adatok másolása a Data Lake Storage Gen2 ORC, Avro, JSON vagy bináris formátum, a következő tulajdonságokat a másolási tevékenység által támogatott **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **AzureBlobFSSource**. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha rekurzív értéke igaz, és a fogadó egy fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó.<br/>Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozhatnak száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

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

- Másolja a parquet vagy tagolt szövegformátum, tekintse meg a [Parquet és elválasztójellel tagolt szöveges formátum fogadó](#parquet-and-delimited-text-format-sink) szakaszban.
- Más tartalomfolyamokat ORC, az avro-hoz, a JSON és a bináris formátum másolása, tekintse meg a [más formátumú fogadó](#other-format-sink) szakaszban.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet és elválasztójellel tagolt szöveges formátum fogadó

Adatok másolása a parquet vagy tagolt szövegformátum Data Lake Storage Gen2-re, lásd: a [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység fogadó és a támogatott beállítások cikkeket. A következő tulajdonságok támogatottak a Data Lake Storage Gen2 alatt `storeSettings` formátum-alapú másolási fogadó beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AzureBlobFSWriteSetting**. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a hierarchiája a célmappában. A relatív elérési útját a forrásfájl, a megadott forrásmappához megegyezik a célmappában cél fájl relatív elérési útját.<br/><b>-FlattenHierarchy</b>: Minden fájl a forrásmappából szerepelnek, az első szintjét a célmappában. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozhatnak száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem       |

> [!NOTE]
> A parquet vagy tagolt szövegformátum a **AzureBlobFSSink** típusa másolási tevékenység fogadó az alábbi szakaszban ismertetett továbbra is támogatott, mert a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja-e az új modell, a jövőben. A Data Factory felhasználói felületén szerzői állít elő, hogy ezek a típusok.

**Példa**

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

#### <a name="other-format-sink"></a>Más formátumú fogadó

Adatok másolása a Data Lake Storage Gen2 ORC, Avro, JSON vagy bináris formátum, a következő tulajdonságok támogatottak a **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **AzureBlobFSSink**. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a hierarchiája a célmappában. A relatív elérési útját a forrásfájl, a megadott forrásmappához megegyezik a célmappában cél fájl relatív elérési útját.<br/><b>-FlattenHierarchy</b>: Minden fájl a forrásmappából szerepelnek, az első szintjét a célmappában. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozhatnak száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

**Példa**

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

### <a name="folder-and-file-filter-examples"></a>Mappa és fájl szűrő példák

Ez a szakasz ismerteti a mappa elérési útját és nevét, az eredményül kapott viselkedéstől helyettesítő szűrőket.

| folderPath | fileName | recursive | Forrás-mappa szerkezete és szűrő eredmény (a fájlok **félkövér** adatok lekérése)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, használhatja az alapértelmezettet) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Üres, használhatja az alapértelmezettet) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>A rekurzív és copyBehavior példákat

Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél szerkezete ugyanaz, mint a forrás mappa1 hozzon létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + File5 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 mértékének nem. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 mértékének nem. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 mértékének nem. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Hozzáférés-vezérlési listák a Data Lake Storage Gen1 megőrzése

>[!TIP]
>Adatok másolása az Azure Data Lake Storage Gen1 Gen2 az általános, lásd: [adatok másolása az Azure Data Lake Storage Gen1 az Azure Data factoryval Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) a lépésenkénti és ajánlott eljárások.

Amikor fájlokat másol az Azure Data Lake Storage Gen1 Gen2-re, kiválaszthatja, megőrizheti a POSIX hozzáférés-vezérlési listák (ACL) adataival együtt. További információ a hozzáférés-vezérlés: [hozzáférés-vezérlés az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) és [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

A következő típusú hozzáférés-vezérlési listák megőrizhetőek a az Azure Data Factory másolási tevékenység használatával. Válasszon ki egy vagy több típusa:

- **ACL**: Másolja, és őrizze meg a POSIX hozzáférés-vezérlési listák a fájlok és könyvtárak. A forrás és fogadó-, a teljes meglévő ACL-ek másolja. 
- **Tulajdonos**: Másolja, és megőrizheti fájljait és könyvtárait a tulajdonos. Data Lake Storage Gen2 fogadó felügyelő hozzáférésre szükség.
- **Csoport**: Másolja, és őrizze meg a tulajdonoscsoport fájlok és könyvtárak. A Data Lake Storage Gen2 vagy a tulajdonos felhasználó fogadó-(Ha a tulajdonos felhasználó szintén tagja a csoportnak) felügyelő hozzáférésre szükség.

Ha megad egy mappát másol, adat-előállító replikálja ACL-EK, hogy a megadott mappában, és a fájlokat és könyvtárakat, ha `recursive` értéke igaz. Ha megad egy fájl másolása, a fájl hozzáférés-vezérlési listák másolódnak.

>[!IMPORTANT]
>Ha hozzáférés-vezérlési listák megőrzése lehetőséget választja, győződjön meg arról, magas a Data Factory ellen a fogadó Data Lake Storage Gen2-fiók megfelelő engedélyeket biztosítson. Például a fiók-kulcsos hitelesítést, vagy a tárolási Blob adatok tulajdonosi szerepkör hozzárendelése a szolgáltatás egyszerű vagy felügyelt identitás.

Annak konfigurálásakor forrás, a Data Lake Storage Gen1 bináris másolat lehetőséget vagy bináris formátumú és fogadó, Data Lake Storage Gen2 a bináris másolat lehetőséggel vagy a bináris formátum, a **megőrzése** beállítást a **másolása Eszköz beállításai** lap vagy az a **másolási tevékenység** > **beállítások** lapon találja authoring tevékenység.

![Gen2 Preserve ACL-t a Data Lake Storage Gen1](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Íme egy példa JSON konfigurációjának (lásd: `preserve`): 

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

## <a name="mapping-data-flow-properties"></a>Data flow tulajdonságai

Tudjon meg többet [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) a leképezési adatok flow szolgáltatásban.

## <a name="next-steps"></a>További lépések

A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
