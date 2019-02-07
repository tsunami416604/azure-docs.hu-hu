---
title: Adatok áthelyezése az Azure Data Factory használatával az SFTP-kiszolgáló |} A Microsoft Docs
description: Útmutató az adatok áthelyezése helyszíni vagy felhőbeli SFTP-kiszolgáló Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c29cf0604e651902474d85dbd6edd3208e6b5944
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811296"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával az SFTP-kiszolgálóról
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-sftp-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sftp.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben SFTPconnector](../connector-sftp.md).

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factoryban az adatok áthelyezése helyszíni/felhőbeli SFTP-kiszolgálóra egy támogatott fogadó adattárba. Ez a cikk épül, amely a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk, amely a másolási tevékenység és a források/fogadóként támogatott adattárak listáját jeleníti meg az adatmozgatás általános áttekintést.

A Data factory jelenleg csak helyez át adatokat egy SFTP-kiszolgáló pedig más adattárakban, de amely adatokat helyez át más adattárakban SFTP-kiszolgálóra a nem támogatja. Támogatja mind a helyszíni és felhőbeli SFTP-kiszolgáló.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájl, miután sikerült a célhelyre másolja. Ha a forrásfájl törlése után a sikeres másolási van szüksége, hozzon létre egy egyéni tevékenységet, törölje a fájlt, és használja a tevékenységet a folyamat.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott esetek és hitelesítési típusok
Az SFTP-összekötő segítségével adatokat másolni **mind a felhő SFTP és a helyszíni SFTP kiszolgálók**. **Alapszintű** és **SshPublicKey** hitelesítési típusok támogatottak az SFTP-kiszolgálóhoz való kapcsolódás során.

Az adatok másolása helyszíni SFTP-kiszolgálóra, ha a helyszíni környezetben vagy az Azure virtuális gép adatkezelési átjárót kell telepítenie. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) az átjáró részleteiért. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) a cikk az átjáró beállítása és használatának részletes útmutatást.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez az SFTP forrásból származó különböző eszközök/API-k használatával.

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

- A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. Az SFTP-kiszolgáló az Azure Blob Storage-adatok másolása JSON-minták, lásd: [JSON-példa: Adatok másolása az SFTP-kiszolgáló az Azure blob](#json-example-copy-data-from-sftp-server-to-azure-blob) című szakaszát.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban a JSON-elemeket FTP-társított szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| type | A type tulajdonságot állítsa `Sftp`. |Igen |
| gazdagép | Az SFTP-kiszolgáló neve vagy IP-címe. |Igen |
| port |A port, amelyen az SFTP-kiszolgáló figyel. Az alapértelmezett érték a következő: 21 |Nem |
| authenticationType |Adja meg a hitelesítés típusát. Megengedett értékek: **Alapszintű**, **SshPublicKey**. <br><br> Tekintse meg [alapszintű hitelesítés használata](#using-basic-authentication) és [használatával SSH nyilvános kulcs alapú hitelesítés](#using-ssh-public-key-authentication) további tulajdonságok és JSON-minták részei. |Igen |
| skipHostKeyValidation | Adja meg, hogy állomáskulcsok ellenőrzésének kihagyása. | Nem. Az alapértelmezett érték: False (hamis) |
| hostKeyFingerprint | Adja meg a gazdagép-kulcs az ujjlenyomat. | Igen, ha a `skipHostKeyValidation` hamis értékre van állítva.  |
| átjáró neve |Az adatkezelési átjárót szeretne csatlakozni egy helyszíni SFTP-kiszolgáló neve. | Igen, ha az adatok másolása helyszíni SFTP-kiszolgálóra. |
| encryptedCredential | Titkosított hitelesítő adatokat az SFTP-kiszolgáló eléréséhez. Automatikusan létrehozott meghatározásakor az egyszerű hitelesítés (felhasználónév és jelszó) vagy SshPublicKey hitelesítési (felhasználónév és titkos kulcs elérési útja vagy tartalom) a másolás varázsló vagy a ClickOnce előugró párbeszédpanelen. | Nem. Csak akkor, ha az adatok másolása helyszíni SFTP-kiszolgálóra vonatkoznak. |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítés használata

Alapszintű hitelesítés használatához állítsa `authenticationType` , `Basic`, és adja meg az SFTP-összekötővel az előző szakaszban bemutatott általános eszközök mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév | SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó. |Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. | Igen |

#### <a name="example-basic-authentication"></a>Példa: Alapszintű hitelesítés
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: Alapszintű hitelesítés, a titkosított hitelesítő adatokat

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>SSH nyilvános kulcsos hitelesítés használatával

SSH nyilvános kulcsos hitelesítés használatához állítsa `authenticationType` , `SshPublicKey`, és adja meg az SFTP-összekötővel az előző szakaszban bemutatott általános eszközök mellett a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév |SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg, hogy az átjáró hozzáférhet a titkos kulcs fájlját abszolút elérési útját. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. <br><br> Csak akkor, ha az adatok másolása helyszíni SFTP-kiszolgálóra vonatkoznak. |
| privateKeyContent | A titkos kulcs tartalmát, szerializált karakterlánc. A másolás varázsló olvashatja a titkos kulcs fájlját, és csomagolja ki automatikusan a titkos kulcs tartalmát. Ha bármilyen más eszköz/SDK-t használ, használja a privateKeyPath tulajdonságot. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. |
| passPhrase | Adja meg a pass kifejezés/jelszót a titkos kulcs visszafejtésére, ha a kulcs fájlját egy hozzáférési kódot védi. | Igen, ha a titkos kulcs fájlját egy hozzáférési kódot védi. |

> [!NOTE]
> SFTP-összekötő támogatja az RSA/DSA OpenSSH-kulcs. Ellenőrizze, hogy a kulcsfájl tartalom "----BEGIN [RSA/DSA] PRIVATE KEY----" karakterlánccal kezdődik. Ha a titkos kulcs fájlját ppk formátumfájlt, használjon Putty eszközt .ppk átalakítása OpenSSH formátumban.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Példa: Titkos kulcs filePath SshPublicKey hitelesítéshez

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: Titkos kulcs tartalmát SshPublicKey hitelesítéshez

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében.

A **typeProperties** szakasz eltér az egyes adatkészlet. Az adatkészlet-típusra vonatkozó adatokat biztosít. A typeProperties szakasz egy adatkészlet típusú **FileShare** adatkészlet a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Tekintse meg a minta a társított szolgáltatás és adatkészlet-definíciók példákat.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt használt összes fájlja helyett a folderPath lévő fájlok egy adott sorkészletét jelölik ki.<br/><br/>Engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2014-1-?.txt"`<br/><br/> a bemeneti adatkészlethez FileShare fileFilter akkor. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath, az idősorozat-adatok filename partitionedBy használható. Ha például folderPath paraméteres az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli üzemmódot használja. A bináris módú és hamis értéket ASCII igaz. Alapértelmezett érték: Értéke TRUE. Ez a tulajdonság csak akkor használható, ha típusú társított társított szolgáltatás típusa: FTP-kiszolgáló. |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használatával
Az előző szakaszban ismertetett módon, a dinamikus folderPath, fájlnevét, partitionedBy idősorozat-adatokat is megadhat. A Data Factory makrók és a rendszer változó SliceStart, egy adott az adatszelethez tartozó logikai időszakot jelző SliceEnd teheti.

Time series adatkészleteket, az ütemezés és a szeletek kapcsolatos további információkért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md), [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md), és [folyamatok létrehozása](data-factory-create-pipelines.md) cikkeket.

#### <a name="sample-1"></a>1. példa:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} helyére a változó értékét, a Data Factory rendszer SliceStart (YYYYMMDDHH) formátumban megadva. Indítsa el a szelet időpontja a SliceStart hivatkozik. A folderPath eltér az egyes szeletekhez. Példa: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. példa:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Ebben a példában év, hónap, nap és SliceStart idején kinyert folderPath és a fileName tulajdonság által használt külön változókba.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el.

Mivel a rendelkezésre álló tulajdonságok a typeProperties szakasz a tevékenység a tevékenységek minden típusának számától függ. A másolási tevékenységhez a tulajdonságait a forrásként és fogadóként típusú változhat.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md) cikkben talál.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-példa: Adatok másolása az SFTP-kiszolgáló az Azure-blobba
Az alábbi példa mintául szolgáló JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával tartalmaz [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ezek bemutatják, hogyan SFTP forrásból származó adatok másolása az Azure Blob Storage. Azonban az adatok átmásolhatók **közvetlenül** bármelyik források a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

> [!IMPORTANT]
> Ez a példa JSON-kódrészletek biztosít. Nem tartalmaz részletes útmutató az adat-előállító létrehozásához. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) részletesen ismertető cikket.

A minta az alábbi data factory-entitások rendelkezik:

* A társított szolgáltatás típusa [sftp](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [FileShare](#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy SFTP-kiszolgáló Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**SFTP-beli társított szolgáltatás**

Ebben a példában az egyszerű hitelesítést használ, a felhasználónevet és jelszót a szövegként. A következő módszerek egyikét is használhatja:

* Alapszintű hitelesítés, a titkosított hitelesítő adatokat
* SSH nyilvános kulcsos hitelesítés

Lásd: [FTP társított szolgáltatás](#linked-service-properties) használható hitelesítési típust a következő szakaszban.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure Storage társított szolgáltatás**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Az SFTP bemeneti adatkészlet**

Ez az adatkészlet hivatkozik az SFTP-mappába `mysharedfolder` és a fájl `test.csv`. A folyamat a fájlt átmásolja a célhelyre.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure blobkimeneti adatkészlet**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **FileSystemSource** és **fogadó** típusa **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* [Másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) létrehoz egy folyamatot egy másolási tevékenységgel rendelkező kapcsolatos lépésenkénti útmutatót.
