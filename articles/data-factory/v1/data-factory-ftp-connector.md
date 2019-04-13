---
title: Adatok áthelyezése egy FTP-kiszolgálóról az Azure Data Factory használatával |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával az FTP-kiszolgálóról.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4aba7aadbe92b6c4f0ab417785e230bb6a6823df
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523426"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Adatok áthelyezése egy FTP-kiszolgálóról az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-ftp-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-ftp.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben FTP-összekötő](../connector-ftp.md).

Ez a cikk bemutatja, hogyan használható a másolási tevékenység az Azure Data Factoryban adatok áthelyezése egy FTP-kiszolgálóról. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Másolhat adatokat egy FTP-kiszolgáló bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data Factory jelenleg csak helyez át adatokat egy FTP-kiszolgáló pedig más adattárakban támogatja, de nem az adatok áthelyezését egyéb adatok tárolja az FTP-kiszolgálóhoz. Támogatja mind a helyszíni és felhőbeli FTP-kiszolgálókat.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájl, miután sikerült a célhelyre másolja. Ha a forrásfájl törlése után a sikeres másolási van szüksége, hozzon létre egy egyéni tevékenységet a fájl törléséhez, és használja a tevékenységet a folyamat.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése
Az adatok áthelyezéséhez az **helyszíni** egy felhőbeli adatok (például az Azure Blob storage) tárolására, telepítése és az adatkezelési átjáró használata az FTP-kiszolgáló. Az adatkezelési átjáró egy ügyfélügynök, amely a helyszíni gépen telepítve van, és lehetővé teszi a cloud services egy helyszíni erőforráshoz való kapcsolódáshoz. További információkért lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md). A beállítás lépésenként az átjáró beállítása, és használja, lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md). Az átjáró FTP-kiszolgálóhoz való kapcsolódáshoz használja, akkor is, ha a kiszolgáló az Azure infrastruktúra-szolgáltatás (IaaS) virtuális gépként (VM).

Telepítse az átjárót ugyanarra a helyszíni gépre vagy az FTP-kiszolgáló IaaS-alapú virtuális gép is. Azt javasoljuk azonban, hogy telepítve van az átjáró külön vagy IaaS virtuális Gépen az Erőforrásverseny elkerülése érdekében, valamint a jobb teljesítmény érdekében. Ha az átjáró egy külön számítógépen telepíti, a képesnek kell lennie az FTP-kiszolgáló eléréséhez.

## <a name="get-started"></a>Bevezetés
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez az FTP forrásból a különböző eszközök vagy API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **Data Factory másolás varázslójával**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **PowerShell**, **Azure Resource Manager-sablon**, **.NET API**, és **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy a következő lépésekkel hozzon létre egy folyamatot, amely helyez át adatokat egy forrásadattárból egy fogadó adattárba:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Amikor az eszközök vagy az API-k (kivéve a .NET API), meghatározhatja a Data Factory-entitások a JSON formátumban. A minta az adatok másolása az FTP-adattár használt Data Factory-entitások JSON-definíciói: a [JSON-példa: Adatok másolása az FTP-kiszolgálóhoz az Azure blob](#json-example-copy-data-from-ftp-server-to-azure-blob) című szakaszát.

> [!NOTE]
> Támogatott fájl- és tömörítési formátumok használatával kapcsolatos részletekért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md).

A következő szakaszok az FTP-adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat ismerteti a JSON-elemeket egy társított FTP-szolgáltatás.

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| type |Állítsa az FTP-kiszolgáló. |Igen |&nbsp; |
| gazdagép |Adja meg a nevét vagy az FTP-kiszolgáló IP-címét. |Igen |&nbsp; |
| authenticationType |Adja meg a hitelesítés típusát. |Igen |Alapszintű, a névtelen |
| felhasználónév |Adja meg a felhasználót, ki férhet hozzá az FTP-kiszolgáló. |Nem |&nbsp; |
| jelszó |Adja meg a jelszót a felhasználó (felhasználónév). |Nem |&nbsp; |
| encryptedCredential |Adja meg a titkosított hitelesítő adatokat, az FTP-kiszolgáló eléréséhez. |Nem |&nbsp; |
| átjáró neve |Az adatkezelési átjáró egy helyszíni FTP-kiszolgálóhoz való csatlakozáshoz adja meg az átjárója nevére. |Nem |&nbsp; |
| port |Adja meg a portot, amelyet az FTP-kiszolgáló figyel. |Nem |21 |
| enableSsl |Adja meg, hogy az FTP használja az SSL/TLS-csatorna-en keresztül. |Nem |true |
| enableServerCertificateValidation |Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése a TLS/SSL csatornán keresztül FTP használata esetén. |Nem |true |

>[!NOTE]
>Az FTP-összekötő támogatja a titkosítást vagy nincs explicit SSL/TLS titkosítás; elérésére FTP-kiszolgáló implicit SSL/TLS-titkosítás nem támogatott.

### <a name="use-anonymous-authentication"></a>Névtelen hitelesítés használata

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Felhasználónév és jelszó használata egyszerű szöveges az alapszintű hitelesítés

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Use port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>EncryptedCredential használja a hitelesítéshez és az átjáró

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében.

A **typeProperties** szakasz eltér az egyes adatkészlet. Az adatkészlet-típusra vonatkozó adatokat biztosít. A **typeProperties** szakasz egy adatkészlet típusú **FileShare** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Adatútvonalának a mappához. Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Tekintse meg a minta a társított szolgáltatás és adatkészlet-definíciók példákat.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** rendelkezik a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Amikor **fileName** nincs megadva a kimeneti adatkészlet esetén a létrehozott fájl neve a következő formátumban van: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adja meg a fájlok egy adott sorkészletét jelölik ki használandó szűrő a **folderPath**, ahelyett, hogy minden fájl.<br/><br/>Engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** a fájlmegosztás a bemeneti adatkészletek vonatkozik. Ez a tulajdonság nem támogatott a Hadoop elosztott fájlrendszer (HDFS). |Nem |
| partitionedBy |Adjon meg egy dinamikus használt **folderPath** és **fileName** az idősorozat-adatok. Meghatározhatja például, hogy egy **folderPath** , amely paraméteres van az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum ](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha másolja a fájlokat, mivel ezek közötti fájlalapú tárolók (bináris másolat) szeretné használni, hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**, és a támogatott szintek a következők **Optimal** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli üzemmódot használja-e. Az értékek a következők igaz a bináris mód (Ez az alapértelmezett érték), és hamis értéket az ASCII. Ez a tulajdonság csak akkor használható, ha a kapcsolódó társított szolgáltatás típusa típusa: FTP-kiszolgáló. |Nem |

> [!NOTE]
> **Fájlnév** és **fileFilter** nem használható egyszerre.

### <a name="use-the-partionedby-property"></a>PartionedBy tulajdonsággal
Az előző szakaszban ismertetett módon, megadhat egy dinamikus **folderPath** és **fileName** az idősorozat-adatokat a **partitionedBy** tulajdonság.

Time series adatkészleteket, az ütemezés és a szeletek kapcsolatos további információkért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md), [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md), és [folyamatok létrehozása](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} helyére a Data Factory rendszerváltozó SliceStart, a megadott formátumban (YYYYMMDDHH) értékét. Indítsa el a szelet időpontja a SliceStart hivatkozik. A mappa elérési útja eltér az egyes szeletekhez. (Például wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>2. példa

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
Ebben a példában az év, hónap, nap és SliceStart idején kibontotta a által használt külön változókba a **folderPath** és **fileName** tulajdonságait.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el.

A rendelkezésre álló tulajdonságok a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának, másrészt számától függ. A másolási tevékenység a tulajdonságait a forrásként és fogadóként típusú változhat.

A másolási tevékenység, ha a forrás típusa **FileSystemSource**, a következő tulajdonság megtalálható **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákat, vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-példa: Adatok másolása az FTP-kiszolgálóhoz az Azure Blob
Ez a példa bemutatja, hogyan másolhat adatokat egy FTP-kiszolgálóról az Azure Blob storage. Azonban az adatok közvetlenül a megadott fogadóként bármelyikét a átmásolhatók a [támogatott adattárak és formátumok](data-factory-data-movement-activities.md#supported-data-stores-and-formats), a másolási tevékenység használatával a Data Factoryban.

Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* A társított szolgáltatás típusa [FTP-kiszolgáló](#linked-service-properties)
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [fájlmegosztás](#dataset-properties)
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [Azure Blobba](data-factory-azure-blob-connector.md#dataset-properties)
* A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A minta adatokat másol egy FTP-kiszolgáló Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

### <a name="ftp-linked-service"></a>FTP-társított szolgáltatás

Ebben a példában alapszintű hitelesítés, a felhasználónevet és jelszót a szövegként használ. A következő módszerek egyikét is használhatja:

* A névtelen hitelesítés
* Alapszintű hitelesítés, a titkosított hitelesítő adatokat
* FTP over SSL/TLS (FTPS)

Tekintse meg a [FTP társított szolgáltatás](#linked-service-properties) használható hitelesítési típust a következő szakaszban.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

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
### <a name="ftp-input-dataset"></a>FTP-bemeneti adatkészlet

Ez az adatkészlet hivatkozik az FTP-mappába `mysharedfolder` és a fájl `test.csv`. A folyamat a fájlt átmásolja a célhelyre.

Beállítás **külső** való **igaz** a Data Factory szolgáltatás tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB dinamikusan kiértékeli, a feldolgozás alatt álló szelet kezdő időpontja alapján. A mappa elérési útját használja, az év, hónap, nap és óra részei a kezdési időpontot.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Fájl rendszer forrás- és blob fogadó rendelkező folyamatot egy másolási tevékenységgel

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és az óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **FileSystemSource**, és a **fogadó** típusa **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
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
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* Az adatáthelyezés (másolási tevékenységgel) a Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre legfontosabb tényezők kapcsolatos további információkért lásd: a [másolási tevékenységek teljesítményéhez és finomhangolási útmutató](data-factory-copy-activity-performance.md).

* Folyamat létrehozása másolási tevékenységgel rendelkező részletes ismertetését lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
