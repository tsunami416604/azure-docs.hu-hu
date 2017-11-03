---
title: "Adatok áthelyezése az FTP-kiszolgáló Azure Data Factory használatával |} Microsoft Docs"
description: "Tudnivalók az adatok áthelyezése az Azure Data Factory használatával FTP-kiszolgálóhoz."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2bc5ec9b6396850f863c51288f86a99f42db726b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Adatok áthelyezése az FTP-kiszolgáló Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-ftp-connector.md)
> * [2. verzió – Előzetes verzió](../connector-ftp.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [FTP-összekötőt, a V2](../connector-ftp.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása az FTP-kiszolgálóhoz. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Adatok átmásolhatja az FTP-kiszolgáló bármely támogatott fogadó adattárolóhoz. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg támogatja áthelyezése adatok kizárólag az FTP-kiszolgáló az egyéb adattárakhoz, de nem adatok áthelyezését más adatokat tárolja, FTP-kiszolgálón. Az támogatja-e mind a helyszíni és felhőalapú FTP-kiszolgálók.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájl, miután sikerült átmásolni a cél. Ha a forrásfájl törlése után sikeres másolatot van szüksége, létrehozhat egy egyéni a fájl törlésére, és használja a tevékenységet a feldolgozási. 

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése
Ha áthelyezi adatait egy **helyszíni** adatokat (például az Azure Blob storage) tárolására, telepítése és használata az adatkezelési átjáró felhő FTP-kiszolgálót. Az adatkezelési átjáró egy olyan ügyfélügynök, a helyszíni számítógépre telepített, és lehetővé teszi a felhőszolgáltatások csatlakozás egy helyszíni erőforráshoz. További információkért lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md). A beállítás részletes utasításokat az átjáró össze, és használata, lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md). Az átjáró, FTP-kiszolgálóhoz való kapcsolódáshoz használja, akkor is, ha a kiszolgáló az az Azure-infrastruktúrák (IaaS) szolgáltatás virtuális gépként (VM).

Az átjáró telepíthető ugyanarra a helyi számítógépen vagy infrastruktúra-szolgáltatási virtuális gép az FTP-kiszolgálóként is. Azt javasoljuk azonban, hogy telepítse az átjáró, egy másik számítógépre, vagy az infrastruktúra-szolgáltatási virtuális gép az Erőforrásverseny elkerülése érdekében, és a jobb teljesítmény érdekében. Az átjáró egy külön számítógépen való telepítésekor a gép érhessék el az FTP-kiszolgálón kell lennie.

## <a name="get-started"></a>Bevezetés
A másolási tevékenység, amely FTP forrásból származó adatokat a különböző eszközök vagy API-k használatával helyezi át a feldolgozási sor hozhatja létre.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **Data Factory másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) gyors útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok.
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel.

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök vagy API-k (kivéve a .NET API-t) használ, amikor az a JSON formátum használatával adja meg a Data Factory entitások. Adatok másolása egy FTP-adattároló használt adat-előállító entitások JSON-definíciók minta, tekintse meg a [JSON-példa: adatok másolása az FTP-kiszolgáló az Azure blob](#json-example-copy-data-from-ftp-server-to-azure-blob) című szakaszát.

> [!NOTE]
> Támogatott tömörítési formátumú és használatával kapcsolatos részletekért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory entitások adott FTP-JSON-tulajdonságok.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat ismerteti a JSON-elemek szerepelnek az FTP-kapcsolódó szolgáltatásra vonatkozó.

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| type |Válassza az FTP-kiszolgáló. |Igen |&nbsp; |
| állomás |Adja meg a nevét vagy az FTP-kiszolgáló IP-címét. |Igen |&nbsp; |
| AuthenticationType |Adja meg a hitelesítés típusát. |Igen |Alapszintű, a névtelen |
| felhasználónév |Adja meg a felhasználót, aki hozzáfér az FTP-kiszolgálóhoz. |Nem |&nbsp; |
| jelszó |Adja meg a felhasználó (felhasználónév) jelszavát. |Nem |&nbsp; |
| encryptedCredential |Adja meg a titkosított hitelesítő adatokat, az FTP-kiszolgáló eléréséhez. |Nem |&nbsp; |
| gatewayName |Adja meg az átjáró nevét az adatkezelési átjáró helyszíni FTP-kiszolgálóhoz való kapcsolódáshoz. |Nem |&nbsp; |
| port |Adja meg a portot, amelyet az FTP-kiszolgáló figyel. |Nem |21 |
| enableSsl |Adja meg, hogy a TLS/SSL csatornán FTP használata. |Nem |Igaz |
| enableServerCertificateValidation |Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése a TLS/SSL csatornán keresztül FTP használata esetén. |Nem |Igaz |

### <a name="use-anonymous-authentication"></a>Névtelen hitelesítés

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Felhasználónevet és jelszót egyszerű szövegként használja az egyszerű hitelesítés

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Használja a portot, enableSsl, enableServerCertificateValidation

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>A hitelesítés és az átjáró encryptedCredential használata

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
Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében.

A **typeProperties** szakaszban nem egyezik az adatkészlet egyes típusú. A dataset típusra vonatkozó adatokat tartalmazza. A **typeProperties** szakasz egy adatkészlet típusú **fájlmegosztási** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Részleges azt a mappát. Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** mappa elérési utak alapján szelet kezdési és befejezési dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha **Fájlnév** nincs megadva egy kimeneti adatkészletet, a létrehozott fájl neve nem a következő formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amely használatával a fájlok egy részét jelölje ki a **folderPath**, ahelyett, hogy minden fájl.<br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa:`"fileFilter": "*.log"`<br/>2. példa:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** egy bemeneti fájlmegosztási adatkészlet esetében alkalmazható. Ez a tulajdonság nem támogatott a Hadoop elosztott fájlrendszerrel (HDFS). |Nem |
| partitionedBy |Használatával adja meg a dinamikus **folderPath** és **Fájlnév** idő adatsorozat adatok. Megadhat például egy **folderPath** , amely az adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha szeretné átmásolni a fájlokat, mivel ezek között a fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**, és a támogatott szintek a következők **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli mód használatára. Az értékek a következők igaz a bináris mód (Ez az alapértelmezett érték), és hamis értéket ASCII. A tulajdonság csak akkor használható, típusú a társított kapcsolódószolgáltatás-típus esetén: FTP-kiszolgáló. |Nem |

> [!NOTE]
> **Fájlnév** és **fileFilter** nem használható egyszerre.

### <a name="use-the-partionedby-property"></a>A partionedBy tulajdonsággal
Az előző szakaszban említett, megadhat egy dinamikus **folderPath** és **Fájlnév** idő adatsorozat adatokhoz a **partitionedBy** tulajdonság.

Idő adatsorozat adatkészleteket, az ütemezés és a szeletek kapcsolatos további tudnivalókért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md), [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md), és [folyamatok létrehozása](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} cseréli a Data Factory rendszer változó SliceStart, megadott formátumban (YYYYMMDDHH) értékét. A szelet kezdete a SliceStart hivatkozik. A mappa elérési útja eltér az egyes szeletek. (Például wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.)

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
Ebben a példában év, hónap, nap, és SliceStart idején ki kell olvasni a által használt külön változók a **folderPath** és **Fájlnév** tulajdonságait.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek érhetők el.

Tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység, másrészt a tevékenységek minden típusának eltérők lehetnek. A másolási tevékenységhez a típus tulajdonságokat. az adatforrások és mosdók függenek.

A másolási tevékenység, ha az adatforrás típusú **FileSystemSource**, a következő tulajdonság érhető el **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákat, vagy csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-példa: adatok másolása az FTP-kiszolgáló az Azure-Blobba
Ez a példa bemutatja, hogyan adatok másolása az FTP-kiszolgálóhoz az Azure Blob Storage tárolóban. Azonban adatok átmásolhatók közvetlenül a megadott mosdók bármelyikét a [adatokról és formátumok támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats), a másolási tevékenység során a Data Factory használatával.  

Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* A társított szolgáltatás típusa [FTP-kiszolgáló](#linked-service-properties)
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [fájlmegosztás](#dataset-properties)
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

A minta másol adatokat az FTP-kiszolgáló egy Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

### <a name="ftp-linked-service"></a>Kapcsolódó FTP-szolgáltatás

Ebben a példában a felhasználói nevet és jelszót egyszerű szövegként egyszerű hitelesítést használ. Használhatja a következő módszerek valamelyikével:

* A névtelen hitelesítés
* Egyszerű hitelesítés titkosított hitelesítő adatokkal
* FTP-keresztül SSL/TLS (ftps-t)

Tekintse meg a [FTP társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

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

Ez az adatkészlet hivatkozik az FTP-mappa `mysharedfolder` és fájl `test.csv`. A feldolgozási sor átmásolja a fájlt a cél.

Beállítás **külső** való **igaz** tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy adat-előállító tevékenység nem hozzák.

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

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB dinamikusan értékeli ki, az időpontnak a szelet által feldolgozott alapján. A mappa elérési útját használja, év, hónap, nap, és a kezdési idő órában részeit.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>A másolási tevékenység során a rendszer forrás- és a blob fájlgyűjtő egy folyamaton belül

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **FileSystemSource**, és a **fogadó** típusúra **BlobSink**.

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
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

* Című témakörben olvashat kulcsfontosságú szerepet játszik az adatátvitelt jelölik a (másolási tevékenység) a Data Factory és különböző módokon optimalizálása azt hatás teljesítmény, a [másolása tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md).

* A másolási tevékenység során a folyamat létrehozásának részletes leírása, tekintse meg a [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
