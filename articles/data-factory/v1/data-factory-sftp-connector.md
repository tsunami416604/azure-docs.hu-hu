---
title: Adatok áthelyezése az SFTP-kiszolgálóról az Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhetát át az adatokat egy helyszíni vagy egy felhős SFTP-kiszolgálóról az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265804"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Adatok áthelyezése SFTP-kiszolgálóról az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sftp-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sftp.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [SFTPconnector in V2 című témakört.](../connector-sftp.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni/felhős SFTP-kiszolgáló egy támogatott fogadó adattárba. Ez a cikk az [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkre épül, amely általános áttekintést nyújt a másolási tevékenységgel rendelkező adatáthelyezésről és a forrásként/fogadóként támogatott adattárak listájáról.

A Data Factory jelenleg csak az SFTP-kiszolgálóról más adattárolókba való átmozgatását támogatja, más adattárakból nem áthelyező adatokat pedig SFTP-kiszolgálóra. Támogatja mind a helyszíni, mind a felhőalapú SFTP-kiszolgálókat.

> [!NOTE]
> A Másolási tevékenység nem törli a forrásfájlt, miután sikeresen másolta a célhelyre. Ha a sikeres másolás után törölnie kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez és a folyamatban lévő tevékenység használatához.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott forgatókönyvek és hitelesítési típusok
Ezzel az SFTP-összekötővel adatokat másolhat **a felhős SFTP-kiszolgálókról és a helyszíni SFTP-kiszolgálókról is.** Az SFTP-kiszolgálóhoz való csatlakozáskor az **alapszintű** és **az SshPublicKey** hitelesítési típusok támogatottak.

Adatok másolásakor egy helyszíni SFTP-kiszolgálóról, telepítenie kell egy adatkezelési átjáróa a helyszíni környezetben/Azure virtuális gép. Az átjáró részleteiről az [Adatkezelési átjáró ban](data-factory-data-management-gateway.md) talál részleteket. Tekintse meg [az adatok áthelyezése a helyszíni helyszínek és a felhőbeli](data-factory-move-data-between-onprem-and-cloud.md) cikk az átjáró beállításával és használatával kapcsolatos részletes útmutatásért.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy SFTP-forrásból.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

- A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához. JSON-minták az SFTP-kiszolgálóról az Azure Blob Storage-ba történő másolásához lásd: [JSON-példa: Adatok másolása Az SFTP-kiszolgálóról](#json-example-copy-data-from-sftp-server-to-azure-blob) az Azure blob szakasza ebben a cikkben.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az FTP-kapcsolattal összekapcsolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type | A típustulajdonságnak a `Sftp`beállítását kell állítani. |Igen |
| gazda | Az SFTP-kiszolgáló neve vagy IP-címe. |Igen |
| port |Az a port, amelyen az SFTP-kiszolgáló figyel. Az alapértelmezett érték: 21 |Nem |
| authenticationType |Adja meg a hitelesítés típusát. Engedélyezett értékek: **Alapszintű**, **SshPublicKey**. <br><br> Lásd: [Az alapszintű hitelesítés és](#using-basic-authentication) az [SSH nyilvános kulcsú hitelesítésszakaszainak használata](#using-ssh-public-key-authentication) további tulajdonságokon, illetve JSON-mintákon. |Igen |
| skipHostKeyValidation | Adja meg, hogy kihagyja-e az állomáskulcs-érvényesítést. | Nem. Az alapértelmezett érték: false |
| hostKeyFingerprint | Adja meg az állomáskulcs ujjlenyomatát. | Igen, `skipHostKeyValidation` ha a beállítás hamis.  |
| átjárónév |A helyszíni SFTP-kiszolgálóhoz való csatlakozáshoz való adatkezelési átjáró neve. | Igen, ha adatokat másol egy helyszíni SFTP-kiszolgálóról. |
| titkosított hitelesítő adatok | Titkosított hitelesítő adat az SFTP-kiszolgáló eléréséhez. Az alapfokú hitelesítés (felhasználónév + jelszó) vagy a SshPublicKey hitelesítés (felhasználónév + titkos kulcs elérési útja vagy tartalma) megadásakor jön létre a másolási varázslóban vagy a ClickOnce előugró ablakban. | Nem. Csak akkor alkalmazható, ha adatokat másol egy helyszíni SFTP-kiszolgálóról. |

### <a name="using-basic-authentication"></a>Egyszerű hitelesítés használata

Az alapfokú hitelesítés `authenticationType` `Basic`használatához állítsa be a at, és adja meg a következő tulajdonságokat az utolsó szakaszban bevezetett SFTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| felhasználónév | Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó. |Igen |
| jelszó | A felhasználó jelszava (felhasználónév). | Igen |

#### <a name="example-basic-authentication"></a>Példa: Alapfokú hitelesítés
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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: Egyszerű hitelesítés titkosított hitelesítő adatokkal

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

### <a name="using-ssh-public-key-authentication"></a>Az SSH nyilvános kulcsos hitelesítésének használata

Az SSH nyilvános kulcsú `authenticationType` `SshPublicKey`hitelesítésének használatához állítsa be a at, és adja meg a következő tulajdonságokat az utolsó szakaszban bevezetett SFTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| felhasználónév |Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg az átjáró által elérhető személyes kulcsfájl abszolút elérési útját. | Adja meg `privateKeyPath` `privateKeyContent`a vagy a . <br><br> Csak akkor alkalmazható, ha adatokat másol egy helyszíni SFTP-kiszolgálóról. |
| privateKeyContent | A személyes kulcs tartalmának szerializált karakterlánca. A Másolás varázsló képes olvasni a személyes kulcsfájlt, és automatikusan kibontani a személyes kulcs tartalmát. Ha bármilyen más eszközt/SDK-t használ, használja inkább a privateKeyPath tulajdonságot. | Adja meg `privateKeyPath` `privateKeyContent`a vagy a . |
| Jelszót | Adja meg a személyes kulcs visszafejtéséhez használt jelszót/jelszót, ha a kulcsfájlt jelmondat védi. | Igen, ha a személyes kulcsfájlt jelmondat védi. |

> [!NOTE]
> Az SFTP csatlakozó támogatja az RSA/DSA OpenSSH kulcsot. Győződjön meg arról, hogy a kulcsfájl tartalma a következővel kezdődik: "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Ha a személyes kulcsfájl ppk formátumú fájl, kérjük, a Putty eszközzel konvertáljon .ppk formátumból OpenSSH formátumba.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Példa: SshPublicKey hitelesítés személyes kulcsfájlhasználatávalPath

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: SshPublicKey hitelesítés titkos kulcstartalommal

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
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A JSON adatkészletek szerkezete, rendelkezésre állása és házirendje minden adatkészlettípushoz hasonlóak.

A **typeProperties** szakasz az adatkészlet egyes típusaiesetében eltérő. Az adatkészlet típusára jellemző információkat tartalmaz. A **FileShare** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alelérési útja. A karakterlánc speciális karaktereihez használja a ' \ ' escape karaktert. Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumban lesz: <br/><br/>`Data.<Guid>.txt`(Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| fileFilter |Adjon meg egy szűrőt, amelyet a mappapatikus ban lévő fájlok egy részhalmazának kijelölésére, nem pedig az összes fájlhoz kell használni.<br/><br/>Az engedélyezett `*` értékek a következők: (több karakter) és `?` (egy karakter).<br/><br/>1. példa:`"fileFilter": "*.log"`<br/>2. példa:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter bemeneti FileShare adatkészletesetén alkalmazható. Ez a tulajdonság nem támogatott a HDFS. |Nem |
| particionáltby |partitionedBy használható dinamikus folderPath, fájlnév az idősorozat adatait. Például folderPath paraméterezett minden óra az adatok. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |
| useBinaryTransfer |Adja meg, hogy bináris átviteli módot használ-e. Igaz a bináris mód és a hamis ASCII. Alapértelmezett érték: Igaz. Ez a tulajdonság csak akkor használható, ha a csatolt szolgáltatás típusa FtpServer típusú. |Nem |

> [!NOTE]
> a fájlnév és a fájlszűrő nem használható egyszerre.

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használata
Ahogy az előző szakaszban is említettük, megadhat egy dinamikus mappátPath, fájlnév az idősorozat-adatok partitionedBy. Ezt megteheti a Data Factory makrókkal és a SliceStart, SliceEnd rendszerváltozóval, amely egy adott adatszelet logikai időszakát jelzi.

Az idősorozat-adatkészletekről, az ütemezésről és a szeletekről az [Adatkészletek létrehozása](data-factory-create-datasets.md), [& végrehajtás ütemezése](data-factory-scheduling-and-execution.md)és [A folyamatok létrehozása](data-factory-create-pipelines.md) című témakörben olvashat.

#### <a name="sample-1"></a>1. minta:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában a ({Slice} ) a megadott formátumban a SliceStart Data Factory rendszerváltozó értékére cserélődik. A SliceStart a szelet kezdési időpontjára utal. A folderPath minden szeletesetében más. Példa: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. minta:

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
Ebben a példában a SliceStart évét, hónapját, napját és idejét a folderPath és a fileName tulajdonságai által használt különálló változókba bontják ki.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Az olyan tulajdonságok, mint a név, a leírás, a bemeneti és kimeneti táblák és a házirendek minden típusú tevékenységhez elérhetők.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. A Másolási tevékenység esetében a típustulajdonságok a források és a fogadók típusától függően változnak.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Tekintse meg [a fájl- és tömörítési formátumokat az Azure Data Factory cikkében](data-factory-supported-file-and-compression-formats.md) a részletekről.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-példa: Adatok másolása Az SFTP-kiszolgálóról az Azure blobba
A következő példa minta JSON-definíciókat tartalmaz, amelyek segítségével létrehozhat egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatják, hogyan másolhatja az adatokat Az SFTP-forrásból az Azure Blob Storage. Azonban az adatok **közvetlenül** másolhatók bármelyik forrásból bármelyik fogadók [az itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott az Azure Data Factory másolási tevékenység használatával.

> [!IMPORTANT]
> Ez a minta JSON-kódrészleteket biztosít. Nem tartalmazza az adat-előállító létrehozásának lépésenkénti útmutatóit. Tekintse meg [az adatok áthelyezését a helyszíni helyek és](data-factory-move-data-between-onprem-and-cloud.md) a felhőalapú cikk között, és részletes útmutatást talál.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

* [Sftp](#linked-service-properties)típusú összekapcsolt szolgáltatás .
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [FileShare](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* [A Fájlrendszerforrást](#copy-activity-properties) és a [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként adatokat másol egy SFTP-kiszolgálóról egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**SFTP-kapcsolt szolgáltatás**

Ez a példa az egyszerű hitelesítést használja egyszerű szövegben a felhasználónévvel és jelszóval. Az alábbi módszerek közül választhat:

* Alapfokú hitelesítés titkosított hitelesítő adatokkal
* SSH nyilvános kulcsos hitelesítés

Az [FTP-alapú szolgáltatás](#linked-service-properties) részben a különböző típusú hitelesítéseket használhatja.

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
**Azure Storage-hoz csatolt szolgáltatás**

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

Ez az adatkészlet az SFTP `mysharedfolder` mappára és fájlra `test.csv`hivatkozik. A folyamat a fájlt a célhelyre másolja.

"külső" beállítás: az "igaz" tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Folyamat másolási tevékenységgel**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A json-definícióban a **forrástípus** **FileSystemSource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**.

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

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

* [A Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) lépésenkénti útmutatást tartalmaz egy másolási tevékenységgel rendelkező folyamat létrehozásához.
