---
title: "Adatok másolása az Azure Data Factory SFTP-kiszolgáló |} Microsoft Docs"
description: "Ismerje meg, amely lehetővé teszi az adatok másolása az SFTP-kiszolgáló az adattároló utasításként támogatott, a fogadó az Azure Data Factory MySQL-összekötőjét."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 1d90bafebcd942454b31d0f62b0daf3f01ad6926
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory SFTP-kiszolgáló
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-sftp-connector.md)
> * [2. verzió – Előzetes verzió](connector-sftp.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatok másolása az SFTP-kiszolgáló. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [SFTP-összekötőt a V1](v1/data-factory-sftp-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

SFTP-kiszolgáló adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez SFTP az összekötő támogatja:

- Fájlokat másol használatával **alapvető** vagy **SshPublicKey** hitelesítés.
- Mint a fájlok másolása-fájlok elemzése, vagy a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják az SFTP adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Kapcsolódó SFTP szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Sftp**. |Igen |
| gazdagép | Az SFTP-kiszolgáló neve vagy IP-címét. |Igen |
| port | Port, amelyen az SFTP kiszolgáló figyel.<br/>Két érték engedélyezett: egész szám, alapértelmezett értéke **22**. |Nem |
| skipHostKeyValidation | Adja meg, hogy a gazdagép kulcs ellenőrzésének kihagyására.<br/>Két érték engedélyezett: **igaz**, **hamis** (alapértelmezett).  | Nem |
| hostKeyFingerprint | Adja meg a gazdagép kulcs az ujjlenyomat. | Igen, ha a "skipHostKeyValidation" hamis értékre van állítva.  |
| authenticationType | Adja meg a hitelesítés típusát.<br/>Két érték engedélyezett: **alapvető**, **SshPublicKey**. Tekintse meg [használja az egyszerű hitelesítés](#using-basic-authentication) és [használatával SSH nyilvános kulcsos hitelesítés](#using-ssh-public-key-authentication) további tulajdonságokat és JSON-minták szakasz. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítést használó

Az egyszerű hitelesítést használ, állítsa "authenticationType" tulajdonságot **alapvető**, és adja meg az SFTP összekötő általános néhányat a meglévők közül az utolsó szakaszban bemutatott mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| Felhasználónév | Felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |

**Példa**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>SSH nyilvános kulcsos hitelesítés használatával

SSH nyilvános kulcsos hitelesítés használatához állítsa "authenticationType" tulajdonság **SshPublicKey**, és adja meg az SFTP összekötő általános néhányat a meglévők közül az utolsó szakaszban bemutatott mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| Felhasználónév | SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg a titkos kulcsfájl integrációs futásidejű hozzáférő abszolút elérési útja. Csak akkor, ha önálló üzemeltetett integrációs futásidejű van megadva a "connectVia" vonatkozik. | Adja meg a `privateKeyPath` vagy `privateKeyContent`.  |
| privateKeyContent | Base64 kódolású SSH titkos kulcs tartalmát. Titkos SSH-kulcsot OpenSSH formátumban kell lennie. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Adja meg a `privateKeyPath` vagy `privateKeyContent`. |
| hozzáférési kód | Adja meg a pass kifejezést/jelszót a titkos kulcs visszafejtésére, ha a kulcs fájlját egy hozzáférési kódot védi. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen, ha a titkos kulcsfájl védik a hozzáférési kód. |

> [!NOTE]
> SFTP-összekötő csak a protokoll OpenSSH-kulcsot támogatja. Győződjön meg arról, hogy a fájl nem megfelelő formátumú. Használhatja a Putty eszközt .ppk átalakítása OpenSSH formátumban.

**1. példa: Az SshPublicKey hitelesítés használata a titkos kulcs fájl elérési útja**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: Az SshPublicKey hitelesítés használata a titkos kulcs tartalmát**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az SFTP dataset által támogatott tulajdonságokról.

Adatok másolása az SFTP, állítsa be a type tulajdonságot az adathalmaz **fájlmegosztási**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útját. Például: mappát vagy almappát / |Igen |
| fileName | Adja meg a fájl nevét a **folderPath** Ha át kívánja másolni a megadott fájlt. Ha nem ad meg ehhez a tulajdonsághoz értéket, a DataSet adatkészlet mutat a mappában lévő összes fájl forrásaként. |Nem |
| fileFilter | Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál. Érvényes, csak ha nincs megadva fájlnév. <br/><br/>Helyettesítő karakterek engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/>-1. példa:`"fileFilter": "*.log"`<br/>– 2. példa:`"fileFilter": 2017-09-??.txt"` |Nem |
| Formátumban | Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretne elemezni egy adott formátumú fájlok, a következő formátumban típusú támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

**Példa**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az SFTP forrás által támogatott tulajdonságokról.

### <a name="sftp-as-source"></a>SFTP forrásaként

Adatok másolása SFTP, állítsa be a forrás típusa a másolási tevékenység **FileSystemSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **FileSystemSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. Megjegyzés: Ha a rekurzív értéke true, és a fogadó fájlalapú tároló, üres mappa/alterület-folder nem lesz másolva vagy hozható létre a fogadó.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
