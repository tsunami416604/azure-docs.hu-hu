---
title: Adatok másolása az Azure Data Factory használatával az SFTP-kiszolgáló |} A Microsoft Docs
description: Ismerje meg, amely lehetővé teszi az adatok másolása az SFTP-kiszolgálóra fogadóként támogatott adattárak az Azure Data Factory MySQL-összekötőjét.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 1a62e9e8377705af1a70e356f554cfa549c58f20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233464"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával az SFTP-kiszolgáló
> [!div class="op_single_selector" title1="Válassza ki a Data Factory szolgáltatás használ:"]
> * [1-es verzió](v1/data-factory-sftp-connector.md)
> * [Aktuális verzió](connector-sftp.md)

Ez a cikk ismerteti, hogyan adatok másolása az SFTP-kiszolgáló. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Az SFTP-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás/fogadó mátrix](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Az SFTP-összekötővel, a következőket támogatja:

- Másolás a fájlok **alapszintű** vagy **SshPublicKey** hitelesítést.
- Másolja a fájlokat,-elemzési a fájlokat, vagy a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott az SFTP-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SFTP-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Az Sftp**. |Igen |
| host | Az SFTP-kiszolgáló neve vagy IP-címe. |Igen |
| port | A port, amelyen az SFTP-kiszolgáló figyel.<br/>Engedélyezett értékek a következők: egész szám, alapértelmezett értéke **22-es**. |Nem |
| skipHostKeyValidation | Adja meg, hogy állomáskulcsok ellenőrzésének kihagyása.<br/>Engedélyezett értékek a következők: **igaz**, **hamis** (alapértelmezett).  | Nem |
| hostKeyFingerprint | Adja meg a gazdagép-kulcs az ujjlenyomat. | Igen, ha a "skipHostKeyValidation" hamis értékre van állítva.  |
| authenticationType | Adja meg a hitelesítés típusát.<br/>Engedélyezett értékek a következők: **Alapszintű**, **SshPublicKey**. Tekintse meg [alapszintű hitelesítés használata](#using-basic-authentication) és [használatával SSH nyilvános kulcs alapú hitelesítés](#using-ssh-public-key-authentication) további tulajdonságok és JSON-minták részei. |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítés használata

Alapszintű hitelesítés használatához állítsa "authenticationType" tulajdonságot **alapszintű**, és adja meg az SFTP-összekötővel az előző szakaszban bemutatott általános eszközök mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName | SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó. |Igen |
| password | A felhasználó (felhasználónév) jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

**Példa**

```json
{
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

SSH nyilvános kulcsos hitelesítés használatához állítsa a "authenticationType" tulajdonság **SshPublicKey**, és adja meg az SFTP-összekötővel az előző szakaszban bemutatott általános eszközök mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName | SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg a titkos kulcs fájlját, az Integration Runtime eléréséhez abszolút elérési útját. Csak akkor, ha a saját üzemeltetésű integrációs modulok típusának van megadva a "connectVia" vonatkozik. | Adja meg a `privateKeyPath` vagy `privateKeyContent`.  |
| privateKeyContent | Base64 kódolású SSH titkos kulcs tartalmát. Titkos SSH-kulcs OpenSSH formátumban kell lennie. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Adja meg a `privateKeyPath` vagy `privateKeyContent`. |
| passPhrase | Adja meg a pass kifejezés/jelszót a titkos kulcs visszafejtésére, ha a kulcs fájlját egy hozzáférési kódot védi. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen, ha a titkos kulcs fájlját egy hozzáférési kódot védi. |

> [!NOTE]
> SFTP-összekötő támogatja az RSA/DSA OpenSSH-kulcs. Ellenőrizze, hogy a kulcsfájl tartalom "----BEGIN [RSA/DSA] PRIVATE KEY----" karakterlánccal kezdődik. Ha a titkos kulcs fájlját ppk formátumfájlt, használjon Putty eszközt .ppk átalakítása OpenSSH formátumban. 

**1. példa: Titkos kulcs filePath SshPublicKey hitelesítéshez**

```json
{
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

**2. példa: Titkos kulcs tartalmát SshPublicKey hitelesítéshez**

```json
{
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. 

- A **Parquet és tagolt szövegformátum**, tekintse meg [Parquet és elválasztójellel tagolt szöveges formátumban adatkészlet](#parquet-and-delimited-text-format-dataset) szakaszban.
- Más formátumú hasonló **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú adatkészlet](#other-format-dataset) szakaszban.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet és elválasztójellel tagolt szöveges formátum adatkészlet

Adatok másolása az SFTP **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú adatkészlet című cikket, és a támogatott beállítások. A következő tulajdonságok támogatottak alatt SFTP `location` formátum-alapú adatkészlet beállításai:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A type tulajdonság alatt `location` adatkészlet értékre kell állítani **SftpLocation**. | Igen      |
| folderPath | Mappa elérési útját. Ha azt szeretné, helyettesítő karaktert tartalmazó szűrő mappába használandó, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha szeretné használni a helyettesítő karaktert tartalmazó fájlok szűrésére, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |

> [!NOTE]
> **Fájlmegosztás** továbbra is támogatja a Parquet vagy szöveges formátum a következő szakaszban említett adatkészlet típusa – a Másolás/keresési/GetMetadata tevékenység a visszamenőleges kompatibilitás érdekében. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
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

Adatok másolása az SFTP **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **FileShare** |Igen |
| folderPath | A mappa elérési útját. Helyettesítő karaktert tartalmazó szűrő támogatott, a helyettesítő karakterek engedélyezve vannak: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a fájl tényleges nevét helyettesítő elemet vagy a escape karaktere belül. <br/><br/>Példák: a gyökérmappa/almappa /, tekintse meg a további példákat [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). |Igen |
| fileName |  **Név vagy helyettesítő karaktert tartalmazó szűrő** az fájl(ok) a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. |Nem |
| modifiedDatetimeStart | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy általános teljesítményét adatáthelyezési érint ez a beállítás engedélyezésével, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet olyan nem fájlszűrő attribútum alkalmazandó az adatkészletet, amely a NULL.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy általános teljesítményét adatáthelyezési érint ez a beállítás engedélyezésével, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet olyan nem fájlszűrő attribútum alkalmazandó az adatkészletet, amely a NULL.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| format | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha meg szeretné elemezni az adott formátumú fájlok, formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** mappára vonatkozó részt a és **fileName** nevére.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** mappára vonatkozó részt a és **fileName** helyettesítő szűrővel.

>[!NOTE]
>Ha fájlt szűrő "fileFilter" tulajdonságot használja, továbbra is támogatott-van, miközben Ön a "fájlnevet" a jövőben hozzáadott új szűrő funkció használata javasolt.

**Példa**

```json
{
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SFTP-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sftp-as-source"></a>Az SFTP forrásként

- A Másolás **Parquet és tagolt szövegformátum**, tekintse meg [elválasztójellel tagolt szöveges formátum forrás- és Parquet](#parquet-and-delimited-text-format-source) szakaszban.
- A más formátumú, például a Másolás **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú forrás](#other-format-source) szakaszban.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet és elválasztójellel tagolt szöveges formátum forrása

Adatok másolása az SFTP **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység forrása a cikk és támogatott beállítások. A következő tulajdonságok támogatottak alatt SFTP `storeSettings` formátum-alapú másolási forrásaként beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **SftpReadSetting**. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | Mappa elérési útja a forrás mappák helyettesítő karakter. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. <br>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Nem                                            |
| wildcardFileName         | A szűrő forrásfájljaihoz megadott folderPath/wildcardFolderPath mellett helyettesítő karaktereket is tartalmazó fájl neve. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül.  További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Igen, ha `fileName` nincs megadva a következő adatkészlet |
| modifiedDatetimeStart    | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem                                            |

> [!NOTE]
> A Parquet vagy tagolt szövegformátum **FileSystemSource** továbbra is támogatott a következő szakaszban említett típusa másolási tevékenység forrása – az előző verziókkal való kompatibilitás. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSetting",
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

Adatok másolása az SFTP **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **FileSystemSource** |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. Megjegyzés: Ha a rekurzív értéke igaz, és a fogadó fájlalapú tároló, üres mappa/alárendelt-folder nem lesz másolva vagy hozható létre, a fogadó.<br/>Engedélyezett értékek a következők: **igaz** (alapértelmezett), **false (hamis)** | Nem |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

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

### <a name="folder-and-file-filter-examples"></a>Mappa és fájl szűrő példák

Ez a szakasz ismerteti a mappa elérési útját és nevét, az eredményül kapott viselkedéstől helyettesítő szűrőket.

| folderPath | fileName | recursive | Forrás-mappa szerkezete és szűrő eredmény (a fájlok **félkövér** adatok lekérése)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, használhatja az alapértelmezettet) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (üres, használhatja az alapértelmezettet) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
