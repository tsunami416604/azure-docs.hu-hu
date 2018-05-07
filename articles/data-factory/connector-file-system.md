---
title: Adatok másolása a fájlrendszer vagy Azure Data Factory használatával |} Microsoft Docs
description: Ismerje meg az adatok másolása támogatott fogadó adattárolókhoz fájlrendszer (vagy) a támogatott forráshierarchiából adattárolókhoz fájlrendszerre Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: jingwang
ms.openlocfilehash: d3db05b0c71c9bd355fcacb5554fd8e6a5b128e1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Adatok másolása, vagy a Windows operációs rendszer Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-onprem-file-system-connector.md)
> * [2. verzió – Előzetes verzió](connector-file-system.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és a fájlrendszerhez. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [fájlrendszer-összekötőt a V1](v1/data-factory-onprem-file-system-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása fájlrendszer bármely támogatott fogadó adattár, vagy bármely támogatott forrás adattár fájlrendszerre adatokat másolni. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a fájl rendszer összekötő támogatja:

- Fájlok másolása a/helyi géphez vagy hálózati fájlmegosztás. Linux fájlmegosztás használatához telepítenie [Samba](https://www.samba.org/) a Linux-kiszolgálón.
- Fájlokat másol használatával **Windows** hitelesítés.
- Mint a fájlok másolása-fájlok elemzése/létrehozásakor, vagy a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

Az adatok másolása az vagy egy fájlrendszer, amely nincs nyilvánosan elérhető, akkor be kell állítania egy Self-hosted integrációs futásidejű. Lásd: [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben alább.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják fájlrendszerre adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok fájl kapcsolódó rendszerszolgáltatás támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **fájlkiszolgáló**. | Igen |
| gazdagép | Adja meg a legfelső szintű mappa elérési útját, amelyet szeretne másolni. Az escape-karakter használata "\" speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. | Igen |
| felhasználói azonosítóját | Adja meg a felhasználó, aki hozzáfér a kiszolgáló Azonosítóját. | Igen |
| jelszó | Adja meg a felhasználó (userid) jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és a dataset definíciók

| Forgatókönyv | a társított szolgáltatás definíciójának "állomás" | Az adatkészlet-definícióban "folderPath" |
|:--- |:--- |:--- |
| Helyi mappa integrációs futásidejű gépen: <br/><br/>Példák: D:\\ \* vagy D:\folder\subfolder\\* |A JSON-ban: `D:\\`<br/>A felhasználói felület: `D:\` |A JSON: `.\\` vagy `folder\\subfolder`<br>A felhasználói felület: `.\` vagy `folder\subfolder` |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\myserver\\megosztása\\ \* vagy \\ \\myserver\\megosztása\\mappa\\almappa\\* |A JSON-ban: `\\\\myserver\\share`<br/>A felhasználói felület: `\\myserver\share` |A JSON: `.\\` vagy `folder\\subfolder`<br/>A felhasználói felület: `.\` vagy `folder\subfolder` |

>[!NOTE]
>Szerzői felhasználói felületén keresztül, ha nem kell a felhasználótól, dupla fordított perjel (`\\`) karaktert, mint amikor JSON keresztül, adjon meg egy fordított perjel.

**Példa**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a fájlt a rendszer dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása/fájlrendszerre, az adatkészlet típus tulajdonságának beállítása **fájlmegosztási**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útját. Helyettesítő karakter szűrő nem támogatott. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. |Igen |
| fileName | **Név vagy helyettesítő karakter szűrő** az alatt a megadott "folderPath" (oka) t. Ha nem adja meg egy értéket ehhez a tulajdonsághoz a DataSet adatkészlet mutat, a mappában lévő összes fájlt. <br/><br/>Szűrő, az engedélyezett a helyettesítő karaktereket: `*` (több karaktert) és `?` (egyetlen karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészlet és **preserveHierarchy** nincs meghatározva a tevékenység a fogadó, a másolási tevékenység során automatikusan létrehozza a fájl nevét a következő formátumban: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Példa: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Nem |
| Formátumban | Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Szeretne elemezni, vagy egy adott formátumú fájlok létrehozása, ha a következő fájl formátuma típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja az összes fájlt egy mappában, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** mappa megadó és **Fájlnév** fájlnévvel.<br>Másol egy mappát a fájlok egy részét, adja meg a **folderPath** mappa megadó és **Fájlnév** helyettesítő szűrővel.

>[!NOTE]
>Ha a fájl szűrő használt "fileFilter" tulajdonság, az továbbra is támogatott-van, amíg a "fájlnevet" továbbítja a hozzáadandó új szűrő funkció használatához javasoltak.

**Példa**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a fájl rendszer forrás és a fogadó által támogatott tulajdonságokról listáját tartalmazza.

### <a name="file-system-as-source"></a>Fájlrendszer forrásaként

Adatok másolása fájlrendszer, állítsa be a forrás típusa a másolási tevékenység **FileSystemSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **FileSystemSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon almappáiban, illetve csak a megadott mappát. Megjegyzés: Ha a rekurzív értéke true, és a fogadó fájlalapú tároló, üres mappa/alterület-folder nem lesz másolva vagy hozható létre a fogadó.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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

### <a name="file-system-as-sink"></a>Fájlrendszer, a fogadó

Adatok másolása fájlrendszer, állítsa be a fogadó típusa a másolási tevékenység **FileSystemSink**. A következő tulajdonságok támogatottak a **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **FileSystemSink** |Igen |
| copyBehavior | Meghatározza a másolási viselkedését, ha az adatforrás fájlok fájlalapú adattárolóból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában első szintjét is. A fájlok céljaként automatikusan létrehozott nevet adni. <br/><b>-Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl/Blob neve meg van adva, az egyesített neve legyen a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz ismerteti az eredményül kapott viselkedéstől rekurzív és copyBehavior kombinációk a másolási művelet.

| Rekurzív | copyBehavior | Forrás mappaszerkezet | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 forrásaként azonos struktúrájú jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájl automatikusan létrehozott fájlnévvel egyesülnek |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).