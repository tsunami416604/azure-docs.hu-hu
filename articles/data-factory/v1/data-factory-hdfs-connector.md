---
title: Adatok áthelyezése a helyszíni HDFS-ből |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával a helyszíni HDFS-ből.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f83b525a423ccb2e66d75032811a5f921238a06b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57893407"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával a helyszíni HDFS-ből
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-hdfs-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-hdfs.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [HDFS-összekötő a v2-ben](../connector-hdfs.md).

Ez a cikk bemutatja, hogyan kell használni a másolási tevékenység az Azure Data Factoryban az adatok áthelyezése egy helyszíni HDFS-ből. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Másolhat adatokat HDFS bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy helyszíni HDFS-ből pedig más adattárakban, de nem szükséges más adattárakból származó adatok áthelyezése egy helyszíni HDFS támogatja.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájl, miután sikerült a célhelyre másolja. Ha a forrásfájl törlése után a sikeres másolási van szüksége, hozzon létre egy egyéni tevékenységet, törölje a fájlt, és használja a tevékenységet a folyamat. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A Data Factory szolgáltatás támogatja a helyszíni HDFS az adatkezelési átjáró segítségével csatlakozik. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk további információt talál az adatkezelési átjáró-lépésenként az átjáró beállítása. Az átjáró használatára HDFS kapcsolódni, még akkor is, ha az Azure IaaS virtuális Gépekhez vannak tárolva.

> [!NOTE]
> Ellenőrizze, hogy az adatkezelési átjárót férhetnek hozzá **összes** a [name kiszolgáló]: [csomópont port name] és [csomópont]: [adatportot csomópont], a Hadoop-fürtön. Alapértelmezés szerint a [name csomópont port] 50070, és alapértelmezett [adatportot csomópont] 50075.

Átjáró telepíthető ugyanarra a helyszíni gépre vagy az Azure-alapú virtuális gép a HDFS, amíg azt javasoljuk, hogy telepítse az átjárót egy külön machine/Azure IaaS virtuális gép. Átjáró egy külön számítógépen csökkenti az erőforrás-versengés, és növeli a teljesítményt. Ha az átjáró egy külön számítógépen telepíti, a képesnek kell lennie a gépet, amelynek a HDFS eléréséhez.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely a különböző eszközök/API-k használatával helyez át adatokat a HDFS-forrásból.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A minta az adatok másolása a HDFS-tárolóban használt Data Factory-entitások JSON-definíciói: [JSON-példa: Adatok másolása a helyszíni HDFS-ből az Azure-Blobba](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) című szakaszát.

A következő szakaszok a HDFS adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A társított szolgáltatás egy adattárba hivatkozik, adat-előállító. Létrehoz egy társított szolgáltatást típusú **Hdfs** egy a helyszíni HDFS összekapcsolása a data factoryhoz. Az alábbi táblázatban a társított szolgáltatás JSON-elemeket HDFS leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Hdfs** |Igen |
| URL-cím |A HDFS URL-címe |Igen |
| authenticationType |Névtelen, vagy Windows. <br><br> Használandó **Kerberos-hitelesítés** HDFS-összekötőhöz, tekintse meg [ebben a szakaszban](#use-kerberos-authentication-for-hdfs-connector) , ennek megfelelően állítsa be a helyszíni környezetet. |Igen |
| Felhasználónév |Felhasználónév a Windows-hitelesítés. A Kerberos-hitelesítéshez, adja meg a `<username>@<domain>.com`. |Igen (a Windows-hitelesítés) |
| jelszó |Windows-hitelesítés jelszava. |Igen (a Windows-hitelesítés) |
| átjáró neve |Neve az átjáró, amely a Data Factory szolgáltatás csatlakozik a HDFS csatlakoznia kell. |Igen |
| encryptedCredential |[Új AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) a hozzáférési hitelesítő adatok kimenetét. |Nem |

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés használatával

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

### <a name="using-windows-authentication"></a>Windows-hitelesítés használatával

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
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **FileShare** a következő tulajdonságokkal rendelkezik (amely a HDFS-adatkészlet tartalmaz)

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A mappa elérési útját. Például: `myfolder`<br/><br/>Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Például: folder\subfolder, adja meg a mappa\\\\almappát, és a d:\samplefolder, adja meg a d:\\\\mappába.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha a fájlnév nincs megadva a kimeneti adatkészletek, a létrehozott fájl neve a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath, az idősorozat-adatok filename partitionedBy használható. Példa: folderPath paraméteres az adatok minden óra. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használatával
Az előző szakaszban ismertetett módon, megadhat egy dinamikus folderPath és fájlnevét, idősorozat-adatokat a **partitionedBy** tulajdonság, [Data Factory-függvények, és a rendszerváltozók](data-factory-functions-variables.md).

A time series adatkészleteket, az ütemezés és a szeletek kapcsolatos további információkért lásd: [adatkészletek létrehozása](data-factory-create-datasets.md), [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md), és [folyamatok létrehozása](data-factory-create-pipelines.md) cikkeket.

#### <a name="sample-1"></a>1. példa:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} helyére a változó értékét, a Data Factory rendszer SliceStart (YYYYMMDDHH) formátumban megadva. Indítsa el a szelet időpontja a SliceStart hivatkozik. A folderPath eltér az egyes szeletekhez. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. példa:

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
Ebben a példában év, hónap, nap és SliceStart idején kinyert folderPath és a fileName tulajdonság által használt külön változókba.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

A másolási tevékenység, ha a forrása típusa **FileSystemSource** typeProperties szakasz érhető el az alábbi tulajdonságokat:

**FileSystemSource** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákban vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md) cikkben talál.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-példa: Adatok másolása a helyszíni HDFS-ből az Azure Blobba
Ez a példa bemutatja, hogyan másolhat adatokat egy helyszíni HDFS-ből az Azure Blob Storage. Azonban az adatok átmásolhatók **közvetlenül** a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.  

A minta az alábbi Data Factory-entitások JSON-definíciói biztosít. Használhatja ezeket a definíciókat hozhat létre egy folyamatot az adatok másolása HDFS-ből az Azure Blob Storage használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md).

1. A társított szolgáltatás típusa [OnPremisesHdfs](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [FileShare](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy helyszíni HDFS-ből Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként, állítsa be az adatkezelési átjárót. A következő témakör utasításait a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**HDFS-beli társított szolgáltatást:** Ebben a példában a Windows-hitelesítést használ. Lásd: [HDFS társított szolgáltatás](#linked-service-properties) használható hitelesítési típust a következő szakaszban.

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

**Az Azure Storage társított szolgáltatást:**

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

**HDFS bemeneti adatkészlet:** Ez az adatkészlet hivatkozik a HDFS-mappa DataTransfer/UnitTest /. A folyamat a mappában lévő összes fájlt átmásolja a célhelyre.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

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

**Egy másolási tevékenységgel rendelkező fájlrendszer forrásaként és fogadó Blob folyamatot:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **FileSystemSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS-összekötő Kerberos-hitelesítés használata
A helyszíni környezet beállítása úgy, hogy a Kerberos-hitelesítés használatát a HDFS-összekötőben két lehetőség van. Kiválaszthatja, hogy a legjobban az esetet.
* Option 1: [Csatlakozás az átjárót tartalmazó számítógépen a Kerberos-tartomány](#kerberos-join-realm)
* Option 2: [Windows-tartomány és a Kerberos-tartomány közötti kölcsönös megbízhatósági kapcsolat engedélyezése](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>1. lehetőség: Csatlakozás az átjárót tartalmazó számítógépen a Kerberos-tartomány

#### <a name="requirement"></a>Követelmény:

* Az átjárót tartalmazó számítógépen kell a Kerberos-tartomány csatlakozni, és bármely Windows-tartomány nem tud csatlakozni.

#### <a name="how-to-configure"></a>Hogyan kell konfigurálni:

**Az átjárót tartalmazó számítógépen:**

1.  Futtassa a **Ksetup** segédprogram a Kerberos KDC-kiszolgáló és a kezdőtartomány konfigurálása.

    A gép egy munkacsoport tagjaként kell konfigurálni, mivel egy Kerberos-tartomány eltér egy Windows-tartományhoz. Ez a beállítás a Kerberos-tartomány és a KDC-kiszolgáló hozzáadása a következő megvalósítható. Cserélje le *REALM.COM* a saját megfelelő terület szükséges.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Indítsa újra a** 2 parancsok végrehajtása után a gép.

2.  Ellenőrizze a konfigurációt a **Ksetup** parancsot. A kimenet hasonló lesz:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Az Azure Data Factoryban:**

* Konfigurálás a HDFS összekötő használatával **Windows-hitelesítés** együtt a Kerberos egyszerű neve és a jelszót a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszát.

### <a name="kerberos-mutual-trust"></a>2. lehetőség: Windows-tartomány és a Kerberos-tartomány közötti kölcsönös megbízhatósági kapcsolat engedélyezése

#### <a name="requirement"></a>Követelmény:
*   Az átjárót tartalmazó számítógépen egy Windows-tartományhoz kell csatlakoztatni.
*   A tartományvezérlő-beállítások frissítése engedéllyel is kell rendelkeznie.

#### <a name="how-to-configure"></a>Hogyan kell konfigurálni:

> [!NOTE]
> Cserélje le REALM.COM és AD.COM a következő oktatóanyagban a saját megfelelő tartomány és a tartományvezérlő igény szerint.

**KDC-kiszolgálón:**

1. A KDC-konfiguráció szerkesztése **krb5.conf** lehetővé teszik a KDC-fájlt a következő konfigurációs sablon hivatkozó Windows-tartományt. Alapértelmezés szerint a konfiguráció a következő helyen található **/etc/krb5.conf**.

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

   **Indítsa újra a** a KDC-szolgáltatás konfigurálása után.

2. Készítse elő a nevű egyszerű **krbtgt/REALM.COM\@AD.COM** KDC-kiszolgálón a következő paranccsal:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. A **hadoop.security.auth_to_local** HDFS-szolgáltatás konfigurációs fájlt, adja hozzá `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Tartományvezérlő:**

1.  Futtassa a következő **Ksetup** parancsok egy kezdőtartomány-bejegyzés hozzáadásához:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  A Kerberos-tartomány Windows-tartomány megbízhatósági kapcsolatot hoz létre. [jelszó] az a jelszó a rendszerbiztonsági tag **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberos használt titkosítási algoritmus.

    1. Nyissa meg a Kiszolgálókezelő > csoportházirend-kezelés > tartomány > csoportházirend-objektumok > alapértelmezett vagy az aktív tartományi házirend, és a szerkesztése.

    2. A a **Csoportházirendkezelés-szerkesztő** előugró ablakban nyissa meg a számítógép konfigurációja > házirendek > Windows-beállítások > biztonsági beállítások > helyi házirend > biztonsági beállítások, és konfigurálása **hálózati biztonság: A Kerberos használható titkosítási típusok konfigurálása**.

    3. Válassza ki a titkosítási algoritmus szeretne használni, amikor szeretne csatlakozni a KDC. Gyakran egyszerűen választhatja lehetőségekről.

        ![A Kerberos konfiguráció titkosítási típusok](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Használat **Ksetup** parancs használatával adja meg a titkosítási algoritmus az adott tartomány esetében használható.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Hozzon létre a tartományi fiók és a Kerberos egyszerű közötti leképezést, és a Kerberos egyszerű használata Windows-tartományban.

    1. Indítsa el a felügyeleti eszközök > **Active Directory – felhasználók és számítógépek**.

    2. A speciális szolgáltatások konfigurálása kattintva **nézet** > **speciális szolgáltatások**.

    3. Keresse meg a fiókot, amelyhez létre szeretné hozni a leképezéseket, és a jobb gombbal kattintva **a felhasználónév-leképezések** > kattintson **Kerberos-nevek** fülre.

    4. Rendszerbiztonsági tag hozzáadása a tartományt.

        ![Térkép biztonsági identitás](media/data-factory-hdfs-connector/map-security-identity.png)

**Az átjárót tartalmazó számítógépen:**

* Futtassa a következő **Ksetup** parancsok egy kezdőtartomány-bejegyzés hozzáadására.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Az Azure Data Factoryban:**

* Konfigurálás a HDFS összekötő használatával **Windows-hitelesítés** együtt a tartományi fiók vagy a Kerberos egyszerű a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszát.

> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
