---
title: Adatok áthelyezése az ODBC-adattárak |} A Microsoft Docs
description: Adatok áthelyezése az Azure Data Factory használatával ODBC-adattárak tudnivalók.
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
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1039cde45824491bcc82f324c05e4819e66355e0
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975992"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Helyezze át az adatok Azure Data Factory használatával az ODBC-adattárak
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-odbc-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-odbc.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [ODBC-összekötő a v2-ben](../connector-odbc.md).


Ez a cikk bemutatja, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok áthelyezése a helyszíni ODBC adattárolókból. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Másolhat adatokat egy ODBC data store bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy ODBC data store pedig más adattárakban, de nem az adatok áthelyezését más adattárakban egy ODBC data store támogatja. 

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A Data Factory szolgáltatás támogatja a helyszíni ODBC adatforrások az adatkezelési átjáró segítségével csatlakozik. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk további információt talál az adatkezelési átjáró-lépésenként az átjáró beállítása. Az átjáró használatával kapcsolódhat egy ODBC data store, még akkor is, ha az Azure IaaS virtuális Gépekhez vannak tárolva.

Telepítheti az átjárót ugyanarra a helyszíni gépre vagy az Azure virtuális gép ODBC adattárként. Azt javasoljuk azonban, hogy az átjárót telepít egy külön machine/Azure IaaS virtuális gép az Erőforrásverseny elkerülése érdekében, és a jobb teljesítmény érdekében. Ha az átjáró egy külön számítógépen telepíti, a képesnek kell lennie a gépet, amelynek az ODBC-adattár eléréséhez.

Az adatkezelési átjárót akkor is is telepítenie kell az ODBC-illesztő az adattár az átjárót tartalmazó számítógépen.

> [!NOTE]
> Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.

## <a name="getting-started"></a>Első lépések
Egy folyamatot egy másolási tevékenységgel, amely helyez át adatokat egy ODBC adatokat az adattárból más eszközök/API-k használatával is létrehozhat.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

-Folyamatok létrehozására is használhatja az alábbi eszközöket: **az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani: 

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy ODBC data store használt JSON-definíciói egy minta: [példa JSON: ODBC data az adatok másolása az Azure-Blobba tárolására](#json-example-copy-data-from-odbc-data-store-to-azure-blob) című szakaszát. 

A következő szakaszok és az ODBC data store adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletek:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban a társított szolgáltatás JSON-elemeket ODBC leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonság értékre kell állítani: **OnPremisesOdbc** |Igen |
| kapcsolati Sztringje |A kapcsolati karakterláncot, és a egy nem kötelező titkosított hitelesítő adatokat nem eléréséhez szükséges hitelesítő adatokat része. Lásd az alábbi szakaszokban található példákat. <br/><br/>Például a minta segítségével adható meg a kapcsolati karakterlánc `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, vagy használja a rendszer DSN (adatforrás neve), állítsa be az átjárót tartalmazó számítógépen `"DSN=<name of the DSN>;"` (meg kell továbbra is adja meg a hitelesítő adatok rész társított szolgáltatásban ennek megfelelően). |Igen |
| hitelesítő adat |A hozzáférési hitelesítő adatok része, a kapcsolati karakterláncot a megadott illesztőprogram-specifikus tulajdonság-érték formátuma. Példa: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nem |
| authenticationType |Az ODBC-adattárban való kapcsolódáshoz használt hitelesítés típusa. Lehetséges értékek a következők: névtelen és alapszintű. |Igen |
| felhasználónév |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjáró neve |Az átjáró, amely a Data Factory szolgáltatás használatával kell kapcsolódni az ODBC-adattár neve. |Igen |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítés használata

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
A hitelesítő adatok használatával titkosíthatók a [New-AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) (az Azure PowerShell 1.0-ás verziójú) parancsmaggal vagy [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9-es vagy korábbi verzióját a következő, Azure (PowerShell).  

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

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés használatával

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
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **RelationalTable** (amely tartalmazza az ODBC-adatkészlet) a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla az ODBC-adattár neve. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el.

A rendelkezésre álló tulajdonságok a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának másrészről számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

A másolási tevékenység, ha a forrása típusa **RelationalSource** (amely tartalmazza a ODBC), a következő tulajdonságok typeProperties szakasz érhető el:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla. |Igen |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-példa: adatok másolása az ODBC-adatokból tárolja az Azure Blobba
Ebben a példában biztosít, amelyek segítségével hozzon létre egy folyamat JSON-definíciók [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Azt mutatja, hogyan másolhat adatokat egy ODBC-forrás egy Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

A minta az alábbi data factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OnPremisesOdbc](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy lekérdezés eredménye egy ODBC data store-ban egy blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként, állítsa be az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**ODBC-beli társított szolgáltatást** ebben a példában az alapszintű hitelesítést használ. Lásd: [ODBC társított szolgáltatás](#linked-service-properties) használható hitelesítési típust a következő szakaszban.

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

A minta azt feltételezi, létrehozott egy "MyTable" tábla egy ODBC-adatbázis és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

Adatok írása egy új blob minden órában (frequency: óra, az interval: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

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


**Az ODBC-adatforrás (RelationalSource) és a Blob fogadó (BlobSink) a folyamat másolási tevékenysége**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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
### <a name="type-mapping-for-odbc"></a>ODBC-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Adatok áthelyezése az ODBC-adattárak, amikor a ODBC adattípusok vannak leképezve .NET Tulajdonságtípusokat a [ODBC adattípus-leképezések alkalmazását](https://msdn.microsoft.com/library/cc668763.aspx) témakör.

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Kapcsolódási problémák elhárításához
A kapcsolati hibák elhárításához, használja a **diagnosztikai** lapján **Data Management Gateway Configuration Manager**.

1. Indítsa el a **Data Management Gateway Configuration Manager**. Ezt futtathatja "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" közvetlenül (vagy) keresési a **átjáró** mutató hivatkozás található **Microsoft Data Management Gateway** az alkalmazás a következő képen látható módon.

    ![Keresés átjáró](./media/data-factory-odbc-connector/search-gateway.png)
2. Váltson a **diagnosztikai** fülre.

    ![Átjáró-diagnosztikát](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Válassza ki a **típus** adatok tárolására (társított szolgáltatás).
4. Adja meg **hitelesítési** , és adja meg **hitelesítő adatok** (vagy) adja meg **kapcsolati karakterlánc** az adattárban való kapcsolódáshoz használt.
5. Kattintson a **kapcsolat tesztelése** az adattár a kapcsolat ellenőrzéséhez.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
