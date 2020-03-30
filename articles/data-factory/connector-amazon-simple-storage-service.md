---
title: Adatok másolása az Amazon Simple Storage Service (S3) szolgáltatásból
description: Ismerje meg, hogyan másolhat adatokat az Amazon Simple Storage Service (S3) szolgáltatásból a támogatott fogadó adattárakba az Azure Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 56cc7425eea184cd26010cde48e42e38b27e68a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893296"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Adatok másolása az Amazon Simple Storage Szolgáltatásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuális verzió](connector-amazon-simple-storage-service.md)

Ez a cikk bemutatja, hogyan másolhat adatokat az Amazon Simple Storage Service (Amazon S3) szolgáltatásból. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

>[!TIP]
>Az Amazon S3-ról az Azure Storage-ra vonatkozó adatáttelepítési forgatókönyvről további információ az [Azure Data Factory használatával az Amazon S3-ból az Azure Storage-ba való áttelepítéséről.](data-migration-guidance-s3-azure-storage.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Amazon S3 csatlakozó a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban, ez az Amazon S3 csatlakozó támogatja a fájlok másolását, mint-van, vagy elemzi a fájlokat a [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md). A [fájl metaadatainak megőrzése másolás közben](#preserve-metadata-during-copy)is dönthet. Az összekötő [az AWS Signature Version 4-et](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) használja az S3-ra vonatkozó kérelmek hitelesítéséhez.

>[!TIP]
>Ezzel az Amazon S3 csatlakozóval adatokat másolhat **bármely S3-kompatibilis tárhelyszolgáltatóról,** pl. [a Google Cloud Storage tárhelyről.](connector-google-cloud-storage.md) Adja meg a megfelelő szolgáltatás URL-címét a csatolt szolgáltatás konfigurációjában.

## <a name="required-permissions"></a>Szükséges engedélyek

Ha adatokat szeretne másolni az Amazon S3-ból, győződjön meg arról, hogy megkapta a következő engedélyeket:

- **Másolási tevékenység végrehajtásához:**: `s3:GetObject` és `s3:GetObjectVersion` amazon S3 objektumműveletekhez.
- A Data Factory GUI `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` **authoring:** és az Amazon S3 Bucket Operations engedélyek et is szükség van műveletek, mint a teszt kapcsolat és a böngészési / navigálás fájl elérési utak. Ha nem szeretné megadni ezeket az engedélyeket, hagyja ki a tesztkapcsolatot a csatolt szolgáltatás létrehozási lapján, és adja meg az elérési utat közvetlenül az adatkészlet beállításaiban.

Az Amazon S3 engedélyek teljes listájáról az [Engedélyek megadása a házirendben](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)című témakörben talál további részleteket.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

A következő szakaszok az Amazon S3-ra jellemző Data Factory-entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Amazon S3 kapcsolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **AmazonS3**-ra kell állítani. | Igen |
| accessKeyId azonosító | A titkos hozzáférési kulcs azonosítója. |Igen |
| secretAccessKey | Maga a titkos hozzáférési kulcs. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen |
| serviceUrl | Adja meg az egyéni S3-végpontot, ha a hivatalos Amazon S3 szolgáltatástól eltérő S3-kompatibilis tárolószolgáltatótól másolja az adatokat. Ha például adatokat szeretne másolni `https://storage.googleapis.com`a Google Cloud Storage szolgáltatásból, adja meg a értéket. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!TIP]
>Adja meg az egyéni S3 szolgáltatás URL-címét, ha a hivatalos Amazon S3 szolgáltatástól eltérő S3-kompatibilis tárolóból másol adatokat.

>[!NOTE]
>Ehhez az összekötőhöz hozzáférési kulcsok szükségesek az IAM-fiókhoz az Amazon S3-ból történő adatok másolásához. [Az ideiglenes biztonsági hitelesítő adat](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nem támogatott.
>

Például:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Amazon S3 a következő `location` tulajdonságokat támogatja a formátumalapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` típustulajdonságát **AmazonS3Location**beállítással kell beállítani. | Igen      |
| bucketName (vödörneve) | Az S3 gyűjtő neve.                                          | Igen      |
| folderPath | Az adott gyűjtő alatti mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |
| fileName   | A fájl neve alatt a megadott vödör + folderPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |
| version | Az S3 objektum verziója, ha az S3 verziószámozás engedélyezve van. Ha nincs megadva, a legújabb verzió lesz beolvasása. |Nem |

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Amazon S3 forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-s3-as-source"></a>Amazon S3 forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Amazon S3 a következő `storeSettings` tulajdonságokat támogatja a formátumalapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` **AmazonS3ReadSettings (AmazonS3ReadSettings )** tulajdonságra kell állítani. | Igen                                                         |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ne feledje, hogy ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                                          |
| Előtag                   | Az S3 objektumkulcs előtagja az adatkészletben a forrásobjektumok szűrésére konfigurált adott gyűjtő alatt. Azok az objektumok, amelyek billentyűi ezzel az előtaggal kezdődnek, ki vannak jelölve. <br>Csak akkor `wildcardFolderPath` `wildcardFileName` érvényes, ha nincsenek megadva tulajdonságok. | Nem                                                          |
| helyettesítő mappaelérési út       | Az adatkészletben a forrásmappák szűrésére konfigurált, az adott gyűjtő alatti helyettesítő karakterekkel rendelkező mappaelérési út. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                                          |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a megadott gyűjtő + folderPath/wildcardFolderPath alatt. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne.  További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Igen, `fileName` ha az `prefix` adatkészletben van, és nincs megadva |
| modifiedDatetimeStart    | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve. | Nem                                                          |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                                          |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                                          |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Példák mappa- és fájlszűrőre

Ez a szakasz a mappa elérési útjának és a helyettesítő szűrőkkel rendelkező fájlnévnek a viselkedését ismerteti.

| Vödör | kulcs | Rekurzív | A forrásmappa szerkezete és a szűrő eredménye (a félkövérrel szedett fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| Vödör | `Folder*/*` | hamis | Vödör<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| Vödör | `Folder*/*` | igaz | Vödör<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| Vödör | `Folder*/*.csv` | hamis | Vödör<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| Vödör | `Folder*/*.csv` | igaz | Vödör<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése másolás közben

Amikor fájlokat másol az Amazon S3-ról az Azure Data Lake Storage Gen2/Azure Blob szolgáltatásba, megőrizheti a fájl metaadatait az adatokkal együtt. További információ: [Metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez jelölje be a [GetMetadata tevékenység](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenységtulajdonságok törlése

A tulajdonságok részleteinek megismeréséhez jelölje be a [Tevékenység törlése jelölőnégyzetet.](delete-activity.md)

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak, mint a visszamenőleges kompatibilitás. Javasoljuk, hogy a fenti szakaszokban említett új modellt használja, és az ADF szerzői felhasználói felülete áttért az új modell létrehozására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészletmodell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítania: **AmazonS3Object** |Igen |
| bucketName (vödörneve) | Az S3 gyűjtő neve. A helyettesítő karakter szűrő nem támogatott. |Igen másolási/kereshető tevékenységesetén, Nem a GetMetadata tevékenységhez |
| kulcs | Az S3 objektumkulcs **neve vagy helyettesítő karaktere** a megadott gyűjtő alatt. Csak akkor érvényes, ha az "előtag" tulajdonság nincs megadva. <br/><br/>A helyettesítő karakter szűrő a mapparész és a fájlnév része konkretizálva is támogatott. Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg).<br/>- 1. példa:`"key": "rootfolder/subfolder/*.csv"`<br/>- 2. példa:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>További példák a [Mappa- és fájlszűrő-példákban.](#folder-and-file-filter-examples) A `^` menekülésre használható, ha a tényleges mappa/fájlnév helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. |Nem |
| Előtag | Az S3 objektumkulcs előtagja. Azok az objektumok, amelyek billentyűi ezzel az előtaggal kezdődnek, ki vannak jelölve. Csak akkor érvényes, ha a "key" tulajdonság nincs megadva. |Nem |
| version | Az S3 objektum verziója, ha az S3 verziószámozás engedélyezve van. Ha nincs megadva, a legújabb verzió lesz beolvasása. |Nem |
| modifiedDatetimeStart | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| modifiedDatetimeEnd | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| Formátum | Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha meghatározott formátumú fájlokat szeretne elemezni vagy létrehozni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [a Json formátum,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [az Avro formátum,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [az Orc Formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [Parkettaformátum](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című szakaszban talál. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**.<br/>Támogatott szintek: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Ha egy mappa alá szeretné másolni az összes fájlt, adja meg a **gyűjtőhöz** a gyűjtő nevét és **előtagja** nevet a mapparészhez.<br>Ha egyetlen fájlt szeretne másolni egy adott névvel, adja meg a **gyűjtőhöz és** a **kulcshoz** a mapparészhez és a fájlnévhez.<br>Ha a fájlok egy részhalmazát egy mappába szeretné másolni, adja meg a **gyűjtőhöz mező nevét** és **a** kulcs a mapparészhez és a helyettesítő karakteres szűrőhöz.

**Példa: előtag használata**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**Példa: kulcs és verzió használata (nem kötelező)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **FileSystemSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. Megjegyzés: Ha a rekurzív érték igaz, a fogadó pedig fájlalapú tároló, az üres mappa/almappa nem lesz másolva/hozva a fogadóban.<br/>Az engedélyezett értékek a következők: **true** (alapértelmezett), **hamis** | Nem |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
Az Azure Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
