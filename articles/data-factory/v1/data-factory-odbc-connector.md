---
title: Adatok áthelyezése ODBC adattárolókhoz |} Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával ODBC adattárolókhoz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 200b3c36c28cd61ca34e57875d030bf308c387ec
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049281"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Helyezze át a ODBC adattárolókhoz Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-odbc-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-odbc.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [ODBC-összekötőt, a V2](../connector-odbc.md).


Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factoryben az adatok mozgatása egy helyszíni ODBC adattároló. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

Az ODBC-adattár adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Adat-előállító jelenleg csak áthelyezése adatait egy ODBC-adattároló egyéb adattárakhoz, de nem az egyéb adattárakhoz adatok áthelyezése egy ODBC-adattároló. 

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
Data Factory szolgáltatásnak a helyszíni ODBC adatforrások az adatkezelési átjáró használatával történő csatlakozást támogatja. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikkben tájékozódhat az adatkezelési átjáró és az átjáró beállításával kapcsolatos részletes útmutatás. Az átjáró használatával kapcsolódhat egy ODBC-adattároló, még akkor is, ha egy Azure IaaS virtuális gép helyezkedik el.

Az átjárót telepítheti az ugyanabban a helyi számítógépen, vagy az Azure virtuális gép ODBC adattárként. Azt javasoljuk azonban, hogy telepítse az átjáró, egy különálló számítógép vagy az Azure infrastruktúra-szolgáltatási virtuális gép az Erőforrásverseny elkerülése érdekében, és a jobb teljesítmény érdekében. Az átjáró egy külön számítógépen való telepítésekor a gép kell tudni hozzáférni a gépet, amelynek a ODBC-tárolót.

Az adatkezelési átjáró, leszámítva is szeretne telepíteni az ODBC-illesztőprogram az adattároló az átjárót működtető gépen.

> [!NOTE]
> Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolati/átjáró hibaelhárítási tippek a kapcsolódó problémákat.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok egy ODBC-adattároló különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása egy ODBC-adattároló használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az ODBC-adatok tárolásához Azure-blobba](#json-example-copy-data-from-odbc-data-store-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások ODBC adattárolóhoz JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a társított szolgáltatás JSON-elemek szerepelnek ODBC jellemző leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesOdbc** |Igen |
| connectionString |A kapcsolati karakterlánc és egy opcionális titkosított hitelesítő adat nem hozzáférési hitelesítő adatok része. Példák az alábbi szakaszokban található. <br/><br/>A kapcsolati karakterlánc megadhatja például a mintával `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, vagy használja a rendszer az átjáró gépen beállította DSN (adatforrás neve) `"DSN=<name of the DSN>;"` (kell továbbra is megadhatja a hitelesítő adatok részében hivatkozott szolgáltatásban található ennek megfelelően). |Igen |
| hitelesítő adat |A hozzáférési hitelesítő adatok része illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolódási karakterlánc. Példa: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nem |
| authenticationType |Az ODBC-adattár eléréséhez használt hitelesítés típusa. Lehetséges értékek a következők: névtelen és alapvető. |Igen |
| felhasználónév |Ha egyszerű hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia az ODBC-adattárolóhoz neve. |Igen |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítést használó

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Alapszintű hitelesítést használ, a titkosított hitelesítő adatokkal
A hitelesítő adatokat titkosíthatja a [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (Azure PowerShell 1.0-ás verziója) parancsmag vagy [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 vagy korábbi verzióját az Azure PowerShell).  

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés segítségével

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```


## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **RelationalTable** (amely tartalmazza a ODBC dataset) a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |Az ODBC-tárolóban a tábla neve. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek érhetők el.

Tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység viszont eltérőek a tevékenységek minden típusának. A másolási tevékenység során két érték források és mosdók típusától függően.

A másolási tevékenység során típusú forrás esetén **RelationalSource** (amely tartalmazza a ODBC), a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: Válasszon * from tábla. |Igen |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-példa: adatok másolása az ODBC-adatok tárolásához Azure-blobba
Ebben a példában a JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Azt illusztrálja, hogyan ODBC forrásból származó adatok másolása az Azure Blob Storage tárolóban. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

A minta a következő data factory entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesOdbc](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat az ODBC-tárolóban egy lekérdezés eredményét blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

Első lépésként, állítsa be az adatkezelési átjáró. Az utasítások szerepelnek a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**ODBC társított szolgáltatás** ebben a példában az egyszerű hitelesítést használ. Lásd: [ODBC társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage társított szolgáltatás**

```json
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

**ODBC bemeneti adatkészlet**

A példa azt feltételezi, hogy létrehozott egy "MyTable" tábla egy ODBC-adatbázis és a "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz.

"External" beállítása: "true" arról tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet külső data factoryval való és adat-előállító tevékenység nem hozzák.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure blobkimeneti adatkészlet**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Másolási tevékenység során a folyamat az ODBC-adatforrás (RelationalSource) és a Blob fogadó (BlobSink)**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **RelationalSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Az ODBC leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység az eseményforrás-típusnak a következő kétlépéses módszert típusok gyűjtése automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Adatok ODBC adatok áruházakból áthelyezésekor ODBC adattípusok típusú képezi le .NET említetteknek megfelelően a [ODBC adattípus-leképezések alkalmazását](https://msdn.microsoft.com/library/cc668763.aspx) témakör.

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források ismételhető Olvasás
Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="ge-historian-store"></a>GE történész tároló
A hivatkozás ODBC társított szolgáltatás létrehozása a [GE Proficy történész (most GE történész)](http://www.geautomation.com/products/proficy-historian) adatokat az Azure data factory tárolja el az alábbi példában látható módon:

```json
{
    "name": "HistorianLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "connectionString": "DSN=<name of the GE Historian store>;",
            "gatewayName": "<gateway name>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": "<password>"
        }
    }
}
```

Az adatkezelési átjáró telepíthető egy helyszíni gépre, és regisztrálja az átjárót a portál. A helyszíni számítógépre telepített átjárót az ODBC illesztőprogram GE történész GE történész adattároló való kapcsolódáshoz használ. Ezért telepítenie az illesztőprogramot, ha még nincs telepítve az átjárót működtető gépen. Lásd: [kapcsolat engedélyezése](#enabling-connectivity) című szakaszban talál információt.

A Data Factory-megoldásban GE történész tároló használata előtt győződjön meg arról, hogy az átjáró képes kapcsolódni a következő szakaszban található utasításokat követve adattárba.

A cikk részletes ismertetése az elejétől ODBC adatok használatával tárolja a másolási műveletek a forrás-adattároló olvasható.  

## <a name="troubleshoot-connectivity-issues"></a>Csatlakozási problémák
Kapcsolódási problémák elhárításához, használja a **diagnosztika** lapján **az adatkezelési átjáró konfigurációkezelőjének**.

1. Indítsa el **az adatkezelési átjáró konfigurációkezelőjének**. Futtatásával "C:\Program Files\Microsoft Data felügyeleti Gateway\1.0\Shared\ConfigManager.exe" közvetlenül (vagy) keresés a **átjáró** mutató hivatkozás található **Microsoft adatkezelési átjáró** az alkalmazás a következő ábrán látható módon.

    ![Keresési átjáró](./media/data-factory-odbc-connector/search-gateway.png)
2. Váltás a **diagnosztika** fülre.

    ![Átjáró diagnosztika](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Válassza ki a **típus** adatok tárolására (társított szolgáltatás).
4. Adja meg **hitelesítési** , és írja be **hitelesítő adatok** (vagy) megadása **kapcsolati karakterlánc** csatlakozás az adattárolóhoz használandó.
5. Kattintson a **tesztkapcsolat** az adattár a kapcsolat ellenőrzéséhez.

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
