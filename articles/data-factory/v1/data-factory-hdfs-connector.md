---
title: Adatok áthelyezése a helyszíni HDFS-ből
description: Ismerje meg, hogyan helyezheti át az adatokat a helyszíni HDFS-ből az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924344"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Adatok áthelyezése a helyszíni HDFS-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-hdfs-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-hdfs.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [HDFS-összekötő t a V2 alkalmazásban.](../connector-hdfs.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése a helyszíni HDFS.This article explain how to use the Copy Activity in Azure Data Factory to move data from an on-premises HDFS. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

A HDFS-ből adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak a helyszíni HDFS-ből más adattárolókba való átmozgatását támogatja, de nem támogatja az adatok más adattárakból a helyszíni HDFS-be való áthelyezését.

> [!NOTE]
> A Másolási tevékenység nem törli a forrásfájlt, miután sikeresen másolta a célhelyre. Ha a sikeres másolás után törölnie kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez és a folyamatban lévő tevékenység használatához. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Csatlakozás engedélyezése
A Data Factory szolgáltatás támogatja a helyszíni HDFS-hez való csatlakozást az adatkezelési átjáró használatával. Tekintse meg [az adatok áthelyezését a helyszíni helyek és a felhőalapú](data-factory-move-data-between-onprem-and-cloud.md) cikk között, és ismerje meg az Adatkezelési átjárót és az átjáró beállításának lépésenkénti útmutatóját. Az átjáró használatával akkor is csatlakozhat a HDFS-hez, ha az egy Azure IaaS virtuális gépen található.

> [!NOTE]
> Győződjön meg arról, hogy az adatkezelési átjáró hozzáfér a Hadoop-fürt **összes** [name node kiszolgálójához]:[névcsomópont-port] és a Hadoop-fürt [adatcsomópont-kiszolgálók] számára. Az alapértelmezett [névcsomópont-port] 50070, az alapértelmezett [adatcsomópont-port] pedig 50075.

Bár telepítheti az átjárót ugyanazon a helyszíni gépen vagy az Azure virtuális gép, mint a HDFS, azt javasoljuk, hogy telepítse az átjárót egy külön gépen/Azure IaaS virtuális gép. Átjáró egy külön gépen csökkenti az erőforrás-versengés, és javítja a teljesítményt. Ha az átjárót külön gépre telepíti, a gépnek képesnek kell lennie a hdfs-sel való hozzáférésre.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy HDFS-forrásból.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Azure Portal**, **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager sablon**, **.NET API**és REST **API**. Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A minta JSON-definíciók data factory entitások, amelyek adatok másolására egy HDFS-adattárból, lásd: [JSON példa: Adatok másolása a helyszíni HDFS az Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) szakasz ebben a cikkben.

A következő szakaszok a HDFS-re jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
A csatolt szolgáltatás adattaránttárolót kapcsol össze egy adat-előállítóval. Hozzon létre egy **hdfs** típusú összekapcsolt szolgáltatást, amely összeköti a helyszíni HDFS-t az adat-előállítóval. Az alábbi táblázat a HDFS-hez csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **Hdfs** |Igen |
| url |A HDFS URL-címe |Igen |
| authenticationType |Névtelen vagy Windows. <br><br> **Kerberos-hitelesítés** hdfs-összekötő használatához tekintse meg ezt a [szakaszt](#use-kerberos-authentication-for-hdfs-connector) a helyszíni környezet ennek megfelelő beállításához. |Igen |
| userName (Felhasználónév) |Felhasználónév a Windows-hitelesítéshez. Kerberos-hitelesítésesetén `<username>@<domain>.com`adja meg a mezőt. |Igen (Windows-hitelesítéshez) |
| jelszó |Jelszó a Windows-hitelesítéshez. |Igen (Windows-hitelesítéshez) |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a HDFS-hez való csatlakozáshoz használnia kell. |Igen |
| titkosított hitelesítő adatok |A hozzáférési hitelesítő adatok [új-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) kimenete. |Nem |

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>A Windows-hitelesítés használata

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A FileShare típusú adatkészlet typeProperties **szakasza** (amely hdfs adatkészletet is tartalmaz) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa elérési útja. Például: `myfolder`<br/><br/>A karakterlánc speciális karaktereihez használja a ' \ ' escape karaktert. Például: mappa\almappa esetén\\\\adja meg a mappa almappáját,\\\\a d:\samplefolder esetében pedig adja meg a d: mintamappát.<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumban lesz: <br/><br/>`Data.<Guid>.txt`(például: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| particionáltby |partitionedBy használható dinamikus folderPath, fájlnév az idősorozat adatait. Példa: folderPath paraméterezve minden óra adathoz. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

> [!NOTE]
> a fájlnév és a fájlszűrő nem használható egyszerre.

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használata
Ahogy az előző szakaszban is említettük, dinamikus folderPath és fájlnév adható meg az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, a [Data Factory függvényekkel és a rendszerváltozókkal.](data-factory-functions-variables.md)

Az idősorozat-adatkészletekről, az ütemezésről és a szeletekről az [Adatkészletek létrehozása](data-factory-create-datasets.md), [& végrehajtás ütemezése](data-factory-scheduling-and-execution.md)és [A folyamatok létrehozása](data-factory-create-pipelines.md) című cikkek című témakörben olvashat bővebben.

#### <a name="sample-1"></a>1. minta:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában a ({Slice} ) a megadott formátumban a SliceStart Data Factory rendszerváltozó értékére cserélődik. A SliceStart a szelet kezdési időpontjára utal. A folderPath minden szeletesetében más. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. minta:

```JSON
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

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

A Másolási tevékenység esetében, ha a forrás **fájlforrás,** a következő tulajdonságok érhetők el a typeProperties szakaszban:

**A FileSystemSource** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Tekintse meg [a fájl- és tömörítési formátumokat az Azure Data Factory cikkében](data-factory-supported-file-and-compression-formats.md) a részletekről.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-példa: Adatok másolása a helyszíni HDFS-ből az Azure Blobba
Ez a minta bemutatja, hogyan másolhat adatokat egy helyszíni HDFS-ből az Azure Blob Storage-ba. Azonban az adatok **közvetlenül** másolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.  

A minta JSON-definíciókat biztosít a következő Data Factory entitásokhoz. Ezekkel a definíciókkal létrehozhat egy folyamatot a HDFS-ből az Azure Blob Storage-ba a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával.

1. [OnPremisesHdfs](#linked-service-properties)típusú összekapcsolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [FileShare](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [A Fájlrendszerforrást](#copy-activity-properties) és a [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként adatokat másol egy helyszíni HDFS-ből egy Azure blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. A helyszíni helyek és a felhőalapú cikk [közötti átmozgatási adatok](data-factory-move-data-between-onprem-and-cloud.md) utasításai.

**HDFS-kapcsolt szolgáltatás:** Ez a példa a Windows-hitelesítést használja. A [hdfs-alapú szolgáltatásszakaszban](#linked-service-properties) a különböző típusú hitelesítéseket használhatja.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage-hoz kapcsolódó szolgáltatás:**

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

**HDFS bemeneti adatkészlet:** Ez az adatkészlet a DataTransfer/UnitTest/ HDFS mappára utal. A folyamat a mappában lévő összes fájlt a célhelyre másolja.

"külső" beállítás: az "igaz" tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység egy folyamatban, amelynek fájlrendszerforrása és blobfogadója van:**

A folyamat tartalmaz egy másolási tevékenységet, amely úgy van beállítva, hogy használja ezeket a bemeneti és kimeneti adatkészletek, és az ütemezés szerint óránként fut. A json-definícióban a **forrástípus** **FileSystemSource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-hitelesítés használata A HDFS-összekötőhöz
Két lehetőség van a helyszíni környezet beállítására, hogy a Kerberos-hitelesítést használhassa a HDFS-összekötőben. Kiválaszthatja azt, aki jobban illik az ön esetéhez.
* 1. lehetőség: [Csatlakozás átjárógéphez a Kerberos-birodalomban](#kerberos-join-realm)
* 2. lehetőség: [A Windows-tartomány és a Kerberos tartomány közötti kölcsönös bizalom engedélyezése](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>1. lehetőség: Csatlakozás átjárógéphez a Kerberos-birodalomban

#### <a name="requirement"></a>Követelmény:

* Az átjárógépnek csatlakoznia kell a Kerberos tartományhoz, és nem tud csatlakozni egyetlen Windows-tartományhoz sem.

#### <a name="how-to-configure"></a>Konfigurálás:

**Átjárógépen:**

1.  Futtassa a **Ksetup** segédprogramot a Kerberos KDC-kiszolgáló és a birodalom konfigurálásához.

    A számítógépet munkacsoport tagjaként kell konfigurálni, mivel a Kerberos tartomány eltér a Windows tartománytól. Ez a Kerberos-birodalom beállításával és a KDC-kiszolgáló hozzáadásával érhető el az alábbiak szerint. Cserélje *ki REALM.COM* a saját birodalmába, ha szükséges.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **A** 2 parancs végrehajtása után indítsa újra a számítógépet.

2.  Ellenőrizze a konfigurációt a **Ksetup** paranccsal. A kimenetnek a következőnek kell lennie:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Az Azure Data Factoryban:**

* Konfigurálja a HDFS-összekötőt **a Windows-hitelesítés** használatával a Kerberos egyszerű nevével és jelszavával együtt a HDFS adatforráshoz való csatlakozáshoz. Ellenőrizze [a HDFS csatolt szolgáltatás tulajdonságai](#linked-service-properties) szakaszt a konfigurációs részletekről.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. lehetőség: A Windows-tartomány és a Kerberos tartomány közötti kölcsönös bizalom engedélyezése

#### <a name="requirement"></a>Követelmény:
*   Az átjárógépnek csatlakoznia kell egy Windows-tartományhoz.
*   A tartományvezérlő beállításainak frissítéséhez engedély szükséges.

#### <a name="how-to-configure"></a>Konfigurálás:

> [!NOTE]
> Cserélje le REALM.COM és AD.COM a következő oktatóanyagban a saját saját tartományára és tartományvezérlőjére, ha szükséges.

**KDC-kiszolgálón:**

1. A KDC-konfiguráció szerkesztése a **krb5.conf** fájlban, hogy a KDC megbízzon a következő konfigurációs sablonra hivatkozó Windows-tartományban. Alapértelmezés szerint a konfiguráció az **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log

           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true

           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }

           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM

           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **A** konfigurálás után indítsa újra a KDC szolgáltatást.

2. Készítsen elő egy **\@krbtgt/REALM.COM AD.COM** nevű megbízót a KDC kiszolgálón a következő paranccsal:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. A **hadoop.security.auth_to_local** HDFS szolgáltatás `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`konfigurációs fájljában adja hozzá a t.

**Tartományvezérlőn:**

1.  A következő **Ksetup-parancsok** futtatásával adjon hozzá egy béna bejegyzést:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Bizalmi kapcsolat létrehozása a Windows tartományból a Kerberos-tartományba. [jelszó] a jelszó a fő **\@krbtgt/REALM.COM AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberos-ban használt titkosítási algoritmust.

    1. Nyissa meg a Kiszolgálókezelő > csoportházirend-kezelés > a tartomány> csoportházirend-objektumok at > alapértelmezett vagy az aktív tartományi házirendet, és szerkessze.

    2. A **Csoportházirend kezelése szerkesztő** előugró ablakban nyissa meg a Számítógép konfigurációs > házirendek > A Windows beállításai > a helyi házirendek > > a biztonsági beállítások ablakot, és konfigurálja a **Hálózati biztonságot: A Kerberos számára engedélyezett titkosítási típusok konfigurálása**.

    3. Válassza ki a kdc-hez való csatlakozáskor használni kívánt titkosítási algoritmust. Általában egyszerűen kiválaszthatja az összes lehetőséget.

        ![Konfigurációs titkosítási típusok a Kerberos hoz](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. A **Ksetup** paranccsal adja meg az adott REALM-en használandó titkosítási algoritmust.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Hozza létre a leképezést a tartományi fiók és a Kerberos egyszerű felhasználó között a Kerberos egyszerű felhasználó használata érdekében a Windows-tartományban.

    1. Indítsa el az **Active Directory – felhasználók és számítógépek beépülő modul**> felügyeleti eszközöket.

    2. A speciális szolgáltatások konfigurálása a**Speciális funkciók** **megtekintése** > gombra kattintva.

    3. Keresse meg azt a fiókot, amelyhez leképezéseket szeretne létrehozni, és kattintson a jobb gombbal a **Név-hozzárendelések** megtekintéséhez > a **Kerberos-nevek** fülre kattintva.

    4. Adjon hozzá egy megbízóakat a bazsúrából.

        ![Biztonsági identitás leképezése](media/data-factory-hdfs-connector/map-security-identity.png)

**Átjárógépen:**

* A következő **Ksetup-parancsok** futtatásával adjon hozzá egy béna bejegyzést.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Az Azure Data Factoryban:**

* Konfigurálja a HDFS-összekötőt **a Windows-hitelesítés** használatával a tartományi fiókkal vagy a Kerberos Egyszerű Kerberos-fiókkal együtt a HDFS adatforráshoz való csatlakozáshoz. Ellenőrizze [a HDFS csatolt szolgáltatás tulajdonságai](#linked-service-properties) szakaszt a konfigurációs részletekről.

> [!NOTE]
> Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)


## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
