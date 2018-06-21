---
title: Adatok másolása az FTP-kiszolgáló Azure Data Factory használatával |} Microsoft Docs
description: 'Útmutató: adatok másolása az FTP-kiszolgáló egy támogatott fogadó adattár egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
ms.openlocfilehash: 71933c4356c9fd8b050656b197b18e200fb1104f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284821"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Adatok másolása az FTP-kiszolgáló Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-ftp-connector.md)
> * [2. verzió – Előzetes verzió](connector-ftp.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az FTP-kiszolgálóhoz. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [FTP-összekötőt a V1](v1/data-factory-ftp-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

FTP-kiszolgáló adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az FTP-összekötő támogatja:

- Fájlokat másol használatával **alapvető** vagy **névtelen** hitelesítés.
- Mint a fájlok másolása-fájlok elemzése, vagy a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory entitások adott FTP-tulajdonságok.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Kapcsolódó FTP-szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **FTP-kiszolgáló**. | Igen |
| gazdagép | Adja meg a nevét vagy az FTP-kiszolgáló IP-címét. | Igen |
| port | Adja meg a portot, amelyet az FTP-kiszolgáló figyel.<br/>Két érték engedélyezett: egész szám, alapértelmezett értéke **21**. | Nem |
| enableSsl | Adja meg, hogy a TLS/SSL csatornán FTP használata.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis**. | Nem |
| enableServerCertificateValidation | Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése a TLS/SSL csatornán keresztül FTP használata esetén.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis**. | Nem |
| authenticationType | Adja meg a hitelesítés típusát.<br/>Két érték engedélyezett: **alapvető**, **névtelen** | Igen |
| Felhasználónév | Adja meg a felhasználót, aki hozzáfér az FTP-kiszolgálóhoz. | Nem |
| jelszó | Adja meg a felhasználó (felhasználónév) jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

>[!NOTE]
>Az FTP-összekötő támogatja elérése során FTP-kiszolgáló nincs titkosítás vagy az SSL/TLS titkosítás explicit; implicit SSL/TLS titkosítás nem támogatja.

**1. példa: a névtelen hitelesítés használatával**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: egyszerű hitelesítést használ.**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az FTP-adatkészlet által támogatott tulajdonságokról.

Adatok másolása az FTP, állítsa be a type tulajdonságot az adathalmaz **fájlmegosztási**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útját. Helyettesítő karakter szűrő nem támogatott. Például: mappát vagy almappát / |Igen |
| fileName | **Név vagy helyettesítő karakter szűrő** az alatt a megadott "folderPath" (oka) t. Ha nem adja meg egy értéket ehhez a tulajdonsághoz a DataSet adatkészlet mutat, a mappában lévő összes fájlt. <br/><br/>Szűrő, az engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több karakter megegyezik) és `?` (nulla megegyezik vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használjon `^` -e a tényleges fájlnév helyettesítő vagy a escape karaktere belül karaktert. |Nem |
| Formátumban | Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretne elemezni egy adott formátumú fájlok, a következő formátumban típusú támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |
| useBinaryTransfer | Adja meg, hogy a bináris átviteli mód használatára. Igaz a bináris mód (alapértelmezett), és hamis értéket az ASCII értékei. |Nem |

>[!TIP]
>Másolja az összes fájlt egy mappában, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** mappa megadó és **Fájlnév** fájlnévvel.<br>Másol egy mappát a fájlok egy részét, adja meg a **folderPath** mappa megadó és **Fájlnév** helyettesítő szűrővel.

>[!NOTE]
>Ha a fájl szűrő használt "fileFilter" tulajdonság, az továbbra is támogatott-van, amíg a "fájlnevet" továbbítja a hozzáadandó új szűrő funkció használatához javasoltak.

**Példa**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az FTP-forrás által támogatott tulajdonságokról.

### <a name="ftp-as-source"></a>FTP-forrásként

Adatok másolása FTP, állítsa be a forrás típusa a másolási tevékenység **FileSystemSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **FileSystemSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. Megjegyzés: Ha a rekurzív értéke true, és a fogadó fájlalapú tároló, üres mappa/alterület-folder nem lesz másolva vagy hozható létre a fogadó.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
