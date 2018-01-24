---
title: "Adatok áthelyezése a helyszíni HDFS |} Microsoft Docs"
description: "További tudnivalók az Azure Data Factory használatával a helyszíni HDFS áthelyezni az adatokat."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 87acbe81d20e0f2b209565eace16de1b979b1d96
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával a helyszíni HDFS
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-hdfs-connector.md)
> * [2. verzió – Előzetes verzió](../connector-hdfs.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [HDFS-összekötőt, a V2](../connector-hdfs.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása egy helyszíni HDFS. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

HDFS adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg csak áthelyezése adatait egy helyszíni HDFS egyéb adattárakhoz, de nem az egyéb adattárakhoz adatok áthelyezése egy helyszíni HDFS.

> [!NOTE]
> Másolási tevékenység nem törli a forrásfájl, miután sikerült átmásolni a cél. Ha a forrásfájl törlése után sikeres másolatot van szüksége, létrehozhat egy egyéni törölje a fájlt, és az adatcsatorna használja a tevékenységet. 

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
Data Factory szolgáltatásnak a helyszíni HDFS az adatkezelési átjáró használatával történő csatlakozást támogatja. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikkben tájékozódhat az adatkezelési átjáró és az átjáró beállításával kapcsolatos részletes útmutatás. Az átjáró használatával kapcsolódhat HDFS, még akkor is, ha egy Azure IaaS virtuális gép helyezkedik el.

> [!NOTE]
> Győződjön meg arról, hogy az adatkezelési átjáró férhetnek hozzá **összes** [csomópont névkiszolgálót]: [csomópont port name] és [adatok működő kiszolgálók]: [adatok csomópont port] a Hadoop-fürt. Alapértelmezés szerint a [name csomópont port] 50070, és alapértelmezett [adatok csomópont port] 50075.

Amíg az átjáró telepíthető az ugyanabban a helyi számítógépen, vagy az Azure virtuális Gépen, mint a HDFS, azt javasoljuk, hogy az átjáró telepítése egy különálló számítógép vagy az Azure infrastruktúra-szolgáltatási virtuális gép. Átjáró egy külön számítógépen Erőforrásverseny csökkenti, és javítja a teljesítményt. Az átjáró egy külön számítógépen való telepítésekor a gép kell tudni hozzáférni a gépet, amelynek a HDFS.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat HDFS forrásból származó különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok.
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel.

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása a HDFS-tárolóban használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az helyszíni HDFS az Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) című szakaszát.

A következő szakaszok részletesen bemutatják való HDFS adat-előállító tartozó entitások meghatározásához használt JSON tulajdonságokat:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A társított szolgáltatás adattárat egy adat-előállító hivatkozásokat tartalmaz. Típusú társított szolgáltatás létrehozása **Hdfs** egy helyszíni HDFS összekapcsolása a data factory. A következő táblázat a társított szolgáltatás JSON-elemek szerepelnek HDFS jellemző leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Hdfs** |Igen |
| URL-cím |A HDFS URL-címe |Igen |
| authenticationType |Névtelen, vagy a Windows. <br><br> Használandó **Kerberos-hitelesítés** HDFS-összekötőhöz, tekintse meg [ebben a szakaszban](#use-kerberos-authentication-for-hdfs-connector) ennek megfelelően a helyszíni környezet beállítása. |Igen |
| Felhasználónév |Felhasználónév a Windows-hitelesítést. A Kerberos-hitelesítés meg `<username>@<domain>.com`. |Igen (a Windows-hitelesítés) |
| jelszó |A Windows-hitelesítés jelszót. |Igen (a Windows-hitelesítés) |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia a HDFS a neve. |Igen |
| encryptedCredential |[Új AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) kimenetét a hozzáférési hitelesítő adatok. |Nem |

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés segítségével

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
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **fájlmegosztási** (amely tartalmazza a HDFS dataset) a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |A mappa elérési útját. Példa:`myfolder`<br/><br/>Használja az escape-karakter "\" a speciális karakterek a karakterláncban. Például: folder\subfolder, adja meg a mappa\\\\almappa és d:\samplefolder, adja meg a d:\\\\mappába.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet, a létrehozott fájl nevét a következő lenne ebben a formátumban: <br/><br/>Adatok. <Guid>.txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nem |
| partitionedBy |Adjon meg egy dinamikus folderPath idő adatsor fájlnevét partitionedBy használható. Példa: folderPath adatok óránkénti paraméteres. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> fájlnév és fileFilter nem használható egyszerre.

### <a name="using-partionedby-property"></a>PartionedBy tulajdonság használatával
Az előző szakaszban említett, megadhat egy dinamikus folderPath és a fájlnév idő adatsorozat adatokhoz a **partitionedBy** tulajdonság, [adat-előállító funkciók és a rendszer változók](data-factory-functions-variables.md).

Idő adatsorozat adatkészleteket, az ütemezés és a szeletek kapcsolatos további információkért lásd: [létrehozása adatkészletek](data-factory-create-datasets.md), [ütemezés & végrehajtási](data-factory-scheduling-and-execution.md), és [létrehozása folyamatok](data-factory-create-pipelines.md) cikkeket.

#### <a name="sample-1"></a>1. példa:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában {szelet} adat-előállító rendszer változó SliceStart (YYYYMMDDHH) formátumban megadott érték helyére. A szelet kezdete a SliceStart hivatkozik. A folderPath nem azonos az egyes szeletek. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

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
Ebben a példában év, hónap, nap és SliceStart idején ki kell olvasni a külön változókat, amelyek folderPath és a fájlnév tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek érhetők el.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

A másolási tevékenység, ha a forrás típusa nem **FileSystemSource** typeProperties szakaszában érhetők a következő tulajdonságokkal:

**FileSystemSource** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |

## <a name="supported-file-and-compression-formats"></a>Támogatott formátumú és tömörítés
Lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikk részletei.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-példa: adatok másolása az helyszíni HDFS az Azure-Blobba
Ez a példa bemutatja, hogyan egy helyszíni HDFS adatok másolása az Azure Blob Storage. Azonban az adatok átmásolhatók **közvetlenül** bármely, a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.  

A minta a következő Data Factory-entitásokhoz JSON-definíciókat biztosítja. E definíciókat hozhat létre egy folyamatot, az adatok másolása az HDFS az Azure Blob Storage használatával [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. A társított szolgáltatás típusa [OnPremisesHdfs](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [fájlmegosztási](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat egy helyszíni HDFS egy Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

Első lépésként, állítsa be az adatkezelési átjáró. Utasításait a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**HDFS társított szolgáltatás:** ebben a példában a Windows-hitelesítést használ. Lásd: [HDFS társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

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

**Az Azure tárolás társított szolgáltatásának:**

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

**HDFS bemeneti adatkészletéből:** Ez az adatkészlet hivatkozik a HDFS mappa DataTransfer/UnitTest /. A feldolgozási sor másolja a fájlokat ebben a mappában a cél.

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

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

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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

**A másolási tevékenység során a fájlrendszer és a Blob fogadó folyamat:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **FileSystemSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>A HDFS-összekötő Kerberos-hitelesítés használata
A helyszíni környezet beállítása úgy, hogy a Kerberos-hitelesítés használatát a HDFS connector két lehetőség áll rendelkezésre. Kiválaszthatja a legjobban az esethez.
* 1. lehetőség: [illesztési átjárót működtető gépen, a Kerberos-tartomány](#kerberos-join-realm)
* 2. lehetőség: [kölcsönös, a Windows-tartomány és a Kerberos-tartomány közötti megbízhatósági kapcsolat engedélyezése](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>1. lehetőség: Illesztés átjárót működtető gépen, Kerberos-tartomány

#### <a name="requirement"></a>Követelmény:

* Az átjáró számítógépe a Kerberos-tartomány csatlakozni kell, és nem tud csatlakozni a Windows-tartományhoz.

#### <a name="how-to-configure"></a>Hogyan kell konfigurálni:

**Az átjáró számítógépén:**

1.  Futtassa a **Ksetup** segédprogram a Kerberos Kulcsszolgáltató kiszolgáló és a tartomány beállításához.

    A számítógép egy munkacsoport tagjaként kell konfigurálni óta egy Kerberos-tartomány eltér a Windows-tartományhoz. Ez a Kerberos-tartomány és a KDC-kiszolgáló hozzáadása az alábbiak szerint elérhető. Cserélje le *REALM.COM* a saját megfelelő terület szükséges.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Indítsa újra a** 2 parancsok végrehajtása után a gép.

2.  A konfiguráció ellenőrzése a **Ksetup** parancsot. A kimeneti kell lennie, például:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Az Azure Data Factoryben:**

* Konfigurálhatja a HDFS összekötő segítségével **Windows-hitelesítés** együtt a Kerberos egyszerű neve és a jelszót a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszban.

### <a name="kerberos-mutual-trust"></a>2. lehetőség: A Windows-tartomány és a Kerberos-tartomány közötti kölcsönös megbízhatósági engedélyezése

#### <a name="requirement"></a>Követelmény:
*   Az átjáró számítógépe Windows-tartományhoz kell csatlakoztatni.
*   A tartományvezérlő-beállítások frissítése engedéllyel kell rendelkezni.

#### <a name="how-to-configure"></a>Hogyan kell konfigurálni:

> [!NOTE]
> Cserélje le REALM.COM és AD.COM az alábbi oktatóanyag saját megfelelő tartomány és a tartományvezérlő igény szerint.

**KDC-kiszolgálón:**

1.  A KDC konfigurációjának szerkesztése **krb5.conf** fájlt, hogy a KDC se hivatkozzon a következő konfigurációs Windows-tartományt. Alapértelmezés szerint a konfigurációja itt található: **/etc/krb5.conf**.

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

2.  Készítse elő a rendszerbiztonsági tag nevű  **krbtgt/REALM.COM@AD.COM**  a KDC-kiszolgáló a következő paranccsal:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  A **hadoop.security.auth_to_local** HDFS-szolgáltatás konfigurációs fájlt, adja hozzá `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**A tartományvezérlőn:**

1.  Futtassa a következő **Ksetup** parancsok tartomány bejegyzés hozzáadása:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Windows-tartomány Kerberos-tartomány bizalmi kapcsolat létrehozásához. [jelszó] pedig a jelszót a rendszerbiztonsági tag  **krbtgt/REALM.COM@AD.COM** .

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberos használt titkosítási algoritmus.

    1. Nyissa meg a Kiszolgálókezelő > csoportházirend-kezelés > tartomány > csoportházirend-objektumok > alapértelmezett vagy az aktív tartományi házirend és a Szerkesztés.

    2. A a **Csoportházirendkezelés-szerkesztő** előugró ablakban, keresse fel a számítógép konfigurációja > házirendek > Windows-beállítások > biztonsági beállítások > helyi házirend > biztonsági beállítások, és konfigurálja **hálózati biztonság: konfigurálása titkosítási típusok engedélyezett Kerberos**.

    3. Válassza ki a titkosítási algoritmust szeretné használni, ha a KDC csatlakozni. Gyakran egyszerűen kiválaszthatja a beállításokat.

        ![A Kerberos config titkosítási típusok](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Használjon **Ksetup** parancs használatával adja meg a titkosítási algoritmus az adott tartomány kell használni.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  A tartományi fiókot és a Kerberos egyszerű közötti leképezést létrehozása a Windows-tartomány Kerberos egyszerű használatához.

    1. Indítsa el a felügyeleti eszközök > **Active Directory – felhasználók és számítógépek**.

    2. Kattintson a speciális szolgáltatások konfigurálása **nézet** > **speciális funkciók**.

    3. Keresse meg a fiókot, amelyhez hozzárendelések létrehozásához, és a jobb gombbal kattintva **a felhasználónév-leképezések** > kattintson **Kerberos-nevek** fülre.

    4. Adja hozzá a tartományi rendszerbiztonsági tag.

        ![Térkép biztonsági azonosító](media/data-factory-hdfs-connector/map-security-identity.png)

**Az átjáró számítógépén:**

* Futtassa a következő **Ksetup** parancs használatával adja hozzá egy tartományi bejegyzést.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Az Azure Data Factoryben:**

* Konfigurálhatja a HDFS összekötő segítségével **Windows-hitelesítés** együtt tartományi fiók vagy a Kerberos egyszerű a HDFS-adatforráshoz való kapcsolódáshoz. Ellenőrizze [HDFS társított szolgáltatás Tulajdonságok](#linked-service-properties) konfiguráció részletei című szakaszban.

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
