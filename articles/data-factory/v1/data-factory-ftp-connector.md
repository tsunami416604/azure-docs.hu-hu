---
title: Adatok áthelyezése FTP-kiszolgálóról az Azure Data Factory használatával
description: További információ arról, hogyan helyezhet át adatokat FTP-kiszolgálóról az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992045"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Adatok áthelyezése FTP-kiszolgálóról az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-ftp-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-ftp.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [FTP-összekötő a V2 alkalmazásban című témakört.](../connector-ftp.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory-ban az FTP-kiszolgálóról történő átmozgatáshoz. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok mozgásáról a másolási tevékenységgel.

Az FTP-kiszolgálóról bármely támogatott fogadóadattárba másolhat adatokat. A másolási tevékenység által fogadóként támogatott adattárak listáját a [támogatott adattárolók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak az FTP-kiszolgálóról más adattárolókba való adatáthelyezést támogatja, más adattárakból nem áthelyező adatokat FTP-kiszolgálóra. Támogatja mind a helyszíni, mind a felhőbeli FTP-kiszolgálókat.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájlt, miután sikeresen másolta a célhelyre. Ha a sikeres másolás után törölnie kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez, és használja a folyamatban lévő tevékenységet.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése
Ha adatokat helyez át egy **helyszíni FTP-kiszolgálóról** egy felhőalapú adattárba (például az Azure Blob storage-ba), telepítse és használja az Adatkezelési átjárót. Az adatkezelési átjáró egy ügyfélügynök, amely telepítve van a helyszíni gépen, és lehetővé teszi a felhőszolgáltatások számára, hogy csatlakozzanak egy helyszíni erőforráshoz. További információt az [Adatkezelési átjáró ban talál.](data-factory-data-management-gateway.md) Az átjáró beállításával és használatával kapcsolatos részletes útmutatást az [Adatok áthelyezése a helyszíni helyek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md)című témakörben találja. Az átjáró segítségével csatlakozhat egy FTP-kiszolgálóhoz, még akkor is, ha a kiszolgáló egy Azure-infrastruktúrán szolgáltatásként (IaaS) virtuális gépként (VM) található.

Lehetőség van az átjáró telepítésére ugyanazon a helyszíni gépen vagy IaaS virtuális gép, mint az FTP-kiszolgáló. Azonban azt javasoljuk, hogy telepítse az átjárót egy külön gépre vagy IaaS virtuális gépre az erőforrások versengésének elkerülése és a jobb teljesítmény érdekében. Ha az átjárót külön számítógépre telepíti, a számítógépnek képesnek kell lennie az FTP-kiszolgáló elérésére.

## <a name="get-started"></a>Bevezetés
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök vagy API-k használatával áthelyezi az adatokat egy FTP-forrásból.

A folyamat létrehozásának legegyszerűbb módja a **Data Factory Copy wizard .** [Lásd: Hozzon létre egy folyamatot a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) egy gyors útmutatóhoz.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio**, **PowerShell,** **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Akár az eszközöket, akár api-kat használja, hajtsa végre az alábbi lépéseket egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök vagy API-k használataesetén (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az FTP-adattólimából adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta ebben a cikkben a [JSON-példa: Adatok másolása FTP-kiszolgálóról az Azure blob](#json-example-copy-data-from-ftp-server-to-azure-blob) szakaszában.

> [!NOTE]
> A használható támogatott fájl- és tömörítési formátumokról az [Azure Data Factory fájl- és tömörítési formátumok című témakörben](data-factory-supported-file-and-compression-formats.md)talál.

A következő szakaszok az FTP-re jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az FTP-kapcsolattal összekapcsolt szolgáltatásra jellemző JSON-elemeket ismerteti.

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| type |Állítsa ezt FtpServer-re. |Igen |&nbsp; |
| gazda |Adja meg az FTP-kiszolgáló nevét vagy IP-címét. |Igen |&nbsp; |
| authenticationType |Adja meg a hitelesítés típusát. |Igen |Alapszintű, Névtelen |
| felhasználónév |Adja meg azt a felhasználót, akinek hozzáférése van az FTP-kiszolgálóhoz. |Nem |&nbsp; |
| jelszó |Adja meg a felhasználó jelszavát (felhasználónév). |Nem |&nbsp; |
| titkosított hitelesítő adatok |Adja meg az FTP-kiszolgáló eléréséhez szükséges titkosított hitelesítő adatokat. |Nem |&nbsp; |
| átjárónév |Adja meg az átjáró nevét az Adatkezelési átjáróban, hogy egy helyszíni FTP-kiszolgálóhoz csatlakozzon. |Nem |&nbsp; |
| port |Adja meg azt a portot, amelyen az FTP-kiszolgáló figyel. |Nem |21 |
| enableSsl |Adja meg, hogy az FTP-t SSL/TLS-csatornán keresztül szeretné-e használni. |Nem |igaz |
| enableServerCertificateValidation |Adja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL tanúsítványának érvényesítését, ha SSL/TLS csatornán keresztül imkeresztül i. |Nem |igaz |

>[!NOTE]
>Az FTP-összekötő támogatja az FTP-kiszolgáló elérését titkosítás vagy explicit SSL/TLS titkosítás nélkül; nem támogatja az implicit SSL/TLS titkosítást.

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

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Felhasználónév és jelszó használata egyszerű szövegben az egyszerű hitelesítéshez

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

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Port használata, enableSsl, enableServerCertificateValidation

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

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Titkosított hitelesítő adat használata hitelesítéshez és átjáróhoz

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
Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása című témakörben található.](data-factory-create-datasets.md) A JSON adatkészletek szerkezete, rendelkezésre állása és házirendje minden adatkészlettípushoz hasonlóak.

A **typeProperties** szakasz az adatkészlet egyes típusaiesetében eltérő. Az adatkészlet típusára jellemző információkat tartalmaz. A **FileShare** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa algörbéje. A karakterlánc speciális karaktereihez használja a ' \ ' escape karaktert. Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő és záró dátuma alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú: <br/><br/>`Data.<Guid>.txt`(Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amelyet a **mappapatikus ban**lévő fájlok egy részhalmazának kijelölésére, nem pedig az összes fájlkijelölésére.<br/><br/>Az engedélyezett `*` értékek a következők: (több karakter) és `?` (egy karakter).<br/><br/>1. példa:`"fileFilter": "*.log"`<br/>2. példa:`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** bemeneti FileShare adatkészletesetén alkalmazható. Ez a tulajdonság nem támogatott a Hadoop distributed file system (HDFS) rendszerrel. |Nem |
| particionáltby |Dinamikus **mappa megadásáraPath** és **fileName** az idősorozat-adatokhoz. Megadhatja például azt a **folderPath mappát,** amely minden adatórára paraméterezve van. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Ajson formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)és [Parketta formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat úgy szeretné másolni, ahogy azok a fájlalapú tárolók között vannak (bináris másolat), hagyja ki a formátum szakaszt a bemeneti és kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a **következők: GZip**, **Deflate,** **BZip2**és **ZipDeflate**, és a támogatott szintek **optimálisak** és **a leggyorsabbak.** További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |
| useBinaryTransfer |Adja meg, hogy használni szeretné-e a bináris átviteli módot. Az értékek a bináris módra (ez az alapértelmezett érték) és az ASCII értékei hamisak. Ez a tulajdonság csak akkor használható, ha a társított csatolt szolgáltatás típusa: FtpServer. |Nem |

> [!NOTE]
> **A fileName** és **a fileFilter** nem használható egyszerre.

### <a name="use-the-partionedby-property"></a>A partionedBy tulajdonság használata
Ahogy az előző szakaszban is említettük, dinamikus **folderPath** és **fileName** mappát adhat meg a **partitionedBy** tulajdonsággal rendelkező idősorozat-adatokhoz.

Az idősorozat-adatkészletekről, az ütemezésről és a szeletekről az [Adatkészletek létrehozása](data-factory-create-datasets.md), [Ütemezés és végrehajtás,](data-factory-scheduling-and-execution.md)valamint [A folyamatok létrehozása című](data-factory-create-pipelines.md)témakörben olvashat.

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában a ({Slice} ) a (YYYYMMDDHH) megadott formátumban lecseréli a (Slice} ) értékre a Slice Start Data Factory rendszerváltozó értékére. A SliceStart a szelet kezdési időpontjára utal. A mappa elérési útja minden szeletnél más. (Például wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.)

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
Ebben a példában a SliceStart éve, hónapja, napja és ideje külön változókba kerül, amelyeket a **folderPath** és **fileName** tulajdonságok használnak.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása című témakörben található.](data-factory-create-pipelines.md) Az olyan tulajdonságok, mint a név, a leírás, a bemeneti és kimeneti táblák és a házirendek minden típusú tevékenységhez elérhetők.

A tevékenység **típustulajdonságai** szakaszában elérhető tulajdonságok viszont az egyes tevékenységtípusoktól függően változnak. A másolási tevékenység esetében a típustulajdonságok a források és a fogadók típusától függően változnak.

A másolási tevékenységben, ha a forrás **fájlforrás,** a következő tulajdonság érhető el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból, vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-példa: Adatok másolása FTP-kiszolgálóról az Azure Blobba
Ez a minta bemutatja, hogyan másolhat adatokat egy FTP-kiszolgálóról az Azure Blob storage. Az adatok azonban közvetlenül átmásolhatók a [támogatott adattárakban és formátumokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats)megadott fogadók bármelyikébe a Data Factory másolási tevékenységének használatával.

Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)vagy a [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával:

* [FtpServer](#linked-service-properties) típusú csatolt szolgáltatás
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú kapcsolt szolgáltatás
* [Fájlmegosztás](#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
* [A FileSystemSource](#copy-activity-properties) és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) programot használó másolási tevékenységet használó [folyamat](data-factory-create-pipelines.md)

A minta óránként átmásolja az adatokat egy FTP-kiszolgálóról egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

### <a name="ftp-linked-service"></a>FTP-kapcsolattal összekapcsolt szolgáltatás

Ez a példa egyszerű hitelesítést használ, a felhasználónév és a jelszó egyszerű szövegben. Az alábbi módszerek közül választhat:

* Névtelen hitelesítés
* Alapfokú hitelesítés titkosított hitelesítő adatokkal
* FTP SSL/TLS-en (FTPS)

Az [FTP-alapú szolgáltatás](#linked-service-properties) részben a különböző típusú hitelesítéseket használhatja.

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

Ez az adatkészlet az FTP `mysharedfolder` mappára és fájlra `test.csv`utal. A folyamat a fájlt a célhelyre másolja.

A **külső** **true** beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Másolási tevékenység egy folyamatban, amely nek fájlrendszer-forrása és blob-fogadója van

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A json-definícióban a **forrástípus** **Fájlrendszerforrás,** a **fogadó** típusa pedig **BlobSink**lesz.

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
> Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* Ha többet szeretne megtudni az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről és optimalizálási lehetőségekről, olvassa el a [Tevékenység teljesítményének másolása és hangolása útmutatót.](data-factory-copy-activity-performance.md)

* A másolási tevékenységgel rendelkező folyamat létrehozásáról a [Másolási tevékenység oktatóanyagcímű témakörben](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)talál részletes útmutatást.
