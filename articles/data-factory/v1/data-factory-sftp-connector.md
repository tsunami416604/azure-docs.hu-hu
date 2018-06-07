---
title: Adatok áthelyezése az Azure Data Factory használatával SFTP kiszolgálóról |} Microsoft Docs
description: További tudnivalók az adatok mozgatása egy helyszíni vagy egy Azure Data Factory használatával felhő SFTP-kiszolgáló.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9efb385e96e77b3cfc69556406bbc9777338b5d7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623468"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával SFTP-kiszolgálóról
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-sftp-connector.md)
> * [2. verzió – Előzetes verzió](../connector-sftp.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [a V2 SFTPconnector](../connector-sftp.md).

Ez a cikk ismerteti, hogyan Azure Data Factory a másolási tevékenység használatával helyezze át az adatok áttelepítését egy helyszíni/felhőbeli SFTP támogatott fogadó adattárat. Ez a cikk épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikket, amely megadja az adatmozgás általános áttekintést a másolási tevékenység és a támogatott adatforrások/mosdók adattárolókhoz listáját.

Adat-előállító jelenleg csak áthelyezése adatainak áttelepítését egy SFTP más adattárolókhoz, de nem adatok egyéb adattárakhoz egy SFTP kiszolgálóra helyezi át. Az támogatja-e mind a helyszíni és felhőalapú SFTP kiszolgálók.

> [!NOTE]
> Másolási tevékenység nem törli a forrásfájl, miután sikerült átmásolni a cél. Ha a forrásfájl törlése után sikeres másolatot van szüksége, létrehozhat egy egyéni törölje a fájlt, és az adatcsatorna használja a tevékenységet. 

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott esetek és hitelesítési típusok
Az SFTP-összekötő segítségével adatokat másolni **mind a felhőalapú SFTP és a helyszíni SFTP kiszolgálók**. **Alapszintű** és **SshPublicKey** hitelesítési típusok támogatottak a SFTP-kiszolgálóhoz való csatlakozáskor.

Amikor adatokat másol egy helyszíni SFTP kiszolgáló, a helyszíni környezetben vagy az Azure virtuális gép adatkezelési átjárót kell telepítenie. Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) talál részletes információt az átjárót. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk részletes ismertetése az átjáró beállítása és használja azt.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat SFTP forrásból származó különböző eszközök/API-k használatával hozhatja létre egy folyamatot.

- Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

- Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. Adatok másolása az SFTP server az Azure Blob Storage a JSON-minták, lásd: [JSON-példa: adatok másolása az SFTP server az Azure blob](#json-example-copy-data-from-sftp-server-to-azure-blob) című szakaszát.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat vonatkozó FTP-társított szolgáltatás JSON-elemeket leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| type | A type tulajdonságot meg kell `Sftp`. |Igen |
| gazdagép | Az SFTP-kiszolgáló neve vagy IP-címét. |Igen |
| port |Port, amelyen az SFTP kiszolgáló figyel. Az alapértelmezett érték: 21. |Nem |
| authenticationType |Adja meg a hitelesítés típusát. Megengedett értékek: **alapvető**, **SshPublicKey**. <br><br> Tekintse meg [használja az egyszerű hitelesítés](#using-basic-authentication) és [használatával SSH nyilvános kulcsos hitelesítés](#using-ssh-public-key-authentication) további tulajdonságokat és JSON-minták szakasz. |Igen |
| skipHostKeyValidation | Adja meg, hogy a gazdagép kulcs ellenőrzésének kihagyására. | Nem. Az alapértelmezett érték: hamis |
| hostKeyFingerprint | Adja meg a gazdagép kulcs az ujjlenyomat. | Igen, ha a `skipHostKeyValidation` hamis értékre van állítva.  |
| gatewayName |Az adatkezelési átjáró egy helyszíni SFTP-kiszolgálóhoz való csatlakozáshoz neve. | Igen, ha az adatok másolása egy helyszíni SFTP-kiszolgálón. |
| encryptedCredential | Titkosított hitelesítő adatokat a SFTP-kiszolgálóhoz való hozzáféréshez. Automatikusan létrehozott Ha megadja az egyszerű hitelesítés (felhasználónév + jelszó) vagy az SshPublicKey hitelesítési (felhasználónév + titkos kulcs elérési útja vagy tartalom) másolása varázsló vagy a ClickOnce előugró párbeszédpanelen. | Nem. Csak akkor, ha az adatok másolása egy helyszíni SFTP kiszolgáló alkalmazni. |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítést használó

Egyszerű hitelesítést használ, állítsa be `authenticationType` , `Basic`, és adja meg az SFTP összekötő általános néhányat a meglévők közül az utolsó szakaszban bemutatott mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév | Felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. | Igen |

#### <a name="example-basic-authentication"></a>Példa: Az egyszerű hitelesítés
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: Alapszintű hitelesítési titkosított hitelesítő adat

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

SSH nyilvános kulcsos hitelesítés használatához állítsa `authenticationType` , `SshPublicKey`, és adja meg az SFTP összekötő általános néhányat a meglévők közül az utolsó szakaszban bemutatott mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- | --- |
| felhasználónév |SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adjon meg abszolút elérési útját a titkos kulcs fájlját, hogy az átjáró férhetnek hozzá. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. <br><br> Csak akkor, ha az adatok másolása egy helyszíni SFTP kiszolgáló alkalmazni. |
| privateKeyContent | A titkos kulcs tartalmát, mivel a szerializált karakterlánc. A varázsló a titkos kulcsfájl olvashatja, és automatikusan bontsa ki a titkos kulcs tartalmát. Ha minden egyéb eszköz/SDK használja, használja a privateKeyPath tulajdonságot. | Adja meg a `privateKeyPath` vagy `privateKeyContent`. |
| hozzáférési kód | Adja meg a pass kifejezést/jelszót a titkos kulcs visszafejtésére, ha a kulcs fájlját egy hozzáférési kódot védi. | Igen, ha a titkos kulcsfájl védik a hozzáférési kód. |

> [!NOTE]
> SFTP-összekötő támogatja az RSA/DSA OpenSSH-kulcsot. Ellenőrizze, hogy a kulcsot tartalmazó fájlt tartalom "---BEGIN [RSA/DSA] titkos kulcsot---" kezdődik. Ha a titkos kulcsfájl ppk formátumfájlt, használjon Putty eszközt .ppk átalakítása OpenSSH formátumban.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Példa: Az SshPublicKey hitelesítés használata a titkos kulcs fájl elérési útja

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: Az SshPublicKey hitelesítés használata a titkos kulcs tartalmát

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
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében.

A **typeProperties** szakaszban nem egyezik az adatkészlet egyes típusú. A dataset típusra vonatkozó adatokat tartalmazza. A typeProperties szakasz egy adatkészlet típusú **fájlmegosztási** adatkészlet tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Sub mappa elérési útját. Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál.<br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter is alkalmazható egy bemeneti fájlmegosztási az adatkészlethez. Ez a tulajdonság a HDFS nem támogatott. |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath idő adatsor fájlnevét partitionedBy használható. Például folderPath adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli mód használata. A bináris mód és a hamis értéket ASCII igaz. Alapértelmezett érték: igaz. A tulajdonság csak akkor használható, típusú társított kapcsolódószolgáltatás-típus esetén: FTP-kiszolgáló. |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használatával
Az előző szakaszban említett, megadhat egy dinamikus folderPath idő adatsorozat adatok partitionedBy fájlnevét. A Data Factory makrók és a rendszer változó SliceStart, egy adott adatszelet logikai időtartamnak jelző SliceEnd azt is megteheti.

Idő adatsorozat adatkészleteket, az ütemezés és a szeletek kapcsolatos további tudnivalókért lásd: [létrehozása adatkészletek](data-factory-create-datasets.md), [ütemezés & végrehajtási](data-factory-scheduling-and-execution.md), és [létrehozása folyamatok](data-factory-create-pipelines.md) cikkeket.

#### <a name="sample-1"></a>1. példa:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} adat-előállító rendszer változó SliceStart (YYYYMMDDHH) formátumban megadott érték helyére. A szelet kezdete a SliceStart hivatkozik. A folderPath nem azonos az egyes szeletek. Példa: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

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
Ebben a példában év, hónap, nap és SliceStart idején ki kell olvasni a külön változókat, amelyek folderPath és a fájlnév tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek érhetők el.

Mivel a rendelkezésre álló tulajdonságok a tevékenység typeProperties szakaszában tevékenységek minden típusának függenek. A másolási tevékenység során a típus tulajdonságokat. az adatforrások és mosdók függenek.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Támogatott formátumú és tömörítés
Lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikk részletei.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-NÁ. példa: Adatok másolása az SFTP server az Azure blob
Az alábbi példa minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). SFTP forrásból származó adatok másolása az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** a forrásokban, sem a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

> [!IMPORTANT]
> Ez a minta JSON kódtöredékek biztosít. Nem tartalmazza az adat-előállítóban létrehozásának részletes leírása. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk lépéseit.

A minta a következő data factory entitások rendelkezik:

* A társított szolgáltatás típusa [sftp](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [fájlmegosztási](#dataset-properties).
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat az SFTP-kiszolgáló egy Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Kapcsolódó SFTP szolgáltatás**

Ebben a példában az egyszerű hitelesítést használ, a felhasználónevet és jelszót egyszerű szövegként. Használhatja a következő módszerek valamelyikével:

* Egyszerű hitelesítés titkosított hitelesítő adatokkal
* SSH nyilvános kulcsos hitelesítés

Lásd: [FTP társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

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
**SFTP bemeneti adatkészlet**

Ehhez az adatkészlethez az SFTP mappára hivatkozik `mysharedfolder` és fájl `test.csv`. A feldolgozási sor átmásolja a fájlt a cél.

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

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

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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

**A másolási tevékenység-feldolgozási folyamat**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **FileSystemSource** és **fogadó** típusúra **BlobSink**.

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

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

* [Másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) való a másolási tevékenység során a folyamat létrehozásának lépéseit.
