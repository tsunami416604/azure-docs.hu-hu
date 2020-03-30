---
title: Adatok áthelyezése az ODBC-adattárakból
description: Ismerje meg, hogyan helyezheti át az adatokat az ODBC-adattárakból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1735c2d2ed107f7ec65d68a6826267ee83a93f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281391"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Adatok áthelyezése az ODBC adattárakból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-odbc-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-odbc.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [ODBC-összekötő t a V2 alkalmazásban.](../connector-odbc.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni ODBC adattárból. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

Az ODBC-adattárból adatokat bármely támogatott fogadó adattárba másolhat. A másolási tevékenység által fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában láthatja. A Data Factory jelenleg csak az ODBC-adattárakból más adattárolókba való adatáthelyezést támogatja, de nem támogatja az adatok más adattárakból odbc-adattárolóba való áthelyezését.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Csatlakozás engedélyezése
A Data Factory szolgáltatás támogatja a helyszíni ODBC-forrásokhoz való csatlakozást az adatkezelési átjáró használatával. Tekintse meg [az adatok áthelyezését a helyszíni helyek és a felhőalapú](data-factory-move-data-between-onprem-and-cloud.md) cikk között, és ismerje meg az Adatkezelési átjárót és az átjáró beállításának lépésenkénti útmutatóját. Az átjáró segítségével csatlakozhat egy ODBC-adattárhoz, még akkor is, ha az egy Azure IaaS virtuális gépen található.

Az átjáró ugyanazon a helyszíni gépen vagy az Azure virtuális gép, mint az ODBC-adattártelepítheti. Azonban azt javasoljuk, hogy telepítse az átjárót egy külön gépre/Azure IaaS virtuális gépre az erőforrások versengésének elkerülése és a jobb teljesítmény érdekében. Ha az átjárót egy külön számítógépre telepíti, a gépnek képesnek kell lennie a számítógép elérésére az ODBC adattárral.

Az adatkezelési átjárón kívül telepítenie kell az odbc-illesztőprogramot is az átjárógépen lévő adattárhoz.

> [!NOTE]
> A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy ODBC-adattárból.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az ODBC-adattárból adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása az ODBC-adattárból](#json-example-copy-data-from-odbc-data-store-to-azure-blob) az Azure Blob szakaszba ebben a cikkben.

A következő szakaszok az ODBC-adattárra jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az ODBC-hez csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következő re van állítva: **OnPremisesOdbc** |Igen |
| connectionString (kapcsolati karakterlánc) |A kapcsolati karakterlánc nem-hozzáférési hitelesítő adatok és egy opcionális titkosított hitelesítő adat. Tekintse meg a következő szakaszokban található példákat. <br/><br/>Megadhatja a kapcsolati karakterláncot a mintához hasonló mintával, vagy `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`használhatja az átjárógépen beállított rendszeradatforrás-nevet (adatforrásnév) `"DSN=<name of the DSN>;"` (ennek megfelelően meg kell adnia a csatolt szolgáltatás hitelesítő adatrészét). |Igen |
| hitelesítő adat |Az illesztőprogram-specifikus tulajdonságérték-formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő adatrésze. Példa: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nem |
| authenticationType |Az ODBC-adattárhoz való csatlakozáshoz használt hitelesítés típusa. Lehetséges értékek: Névtelen és Alapszintű. |Igen |
| userName (Felhasználónév) |Adja meg a felhasználónevet, ha alapfokú hitelesítést használ. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak az ODBC-adattárhoz való csatlakozáshoz használnia kell. |Igen |

### <a name="using-basic-authentication"></a>Egyszerű hitelesítés használata

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
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Egyszerű hitelesítés használata titkosított hitelesítő adatokkal
A hitelesítő adatokat a [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (Az Azure PowerShell 1.0-s verziója) parancsmag vagy a [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (az Azure PowerShell 0.9-es vagy korábbi verziója) használatával titkosíthatja.

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

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata

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
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable** típusú adatkészlet typeProperties szakasza (amely az ODBC adatkészletet is tartalmazza) a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve az ODBC adattárban. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Az olyan tulajdonságok, mint a név, a leírás, a bemeneti és kimeneti táblák és a házirendek minden típusú tevékenységhez elérhetők.

A tevékenység **típustulajdonságai** szakaszában elérhető tulajdonságok viszont az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

A másolási tevékenységben, ha a forrás **relationalSource** típusú (amely magában foglalja az ODBC-t is), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: válassza a * lehetőséget a MyTable táblából. |Igen |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-példa: Adatok másolása az ODBC-adattárból az Azure Blobba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével létrehozhat egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatja, hogyan másolhatja az adatokat egy ODBC-forrásból egy Azure Blob Storage.It shows how to copy data from a ODBC source to a Azure Blob Storage. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.

A minta a következő adatfeldolgozó entitásokkal rendelkezik:

1. [OnPremisesOdbc](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [RelationalTable](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [Relációsforrást](#copy-activity-properties) és [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta adatokat másol egy lekérdezés eredménye egy ODBC adattárban egy blob óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

**ODBC-hez csatolt szolgáltatás** Ez a példa az alapfokú hitelesítést használja. Az [ODBC-hez csatolt szolgáltatás](#linked-service-properties) szakaszban a különböző típusú hitelesítéseket használhatja.

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

**Azure Storage-hoz csatolt szolgáltatás**

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

A minta feltételezi, hogy létrehozott egy "MyTable" táblát egy ODBC-adatbázisban, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz.

"külső" beállítás: az "igaz" tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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

**Tevékenység másolása odbc-forrással (RelationalSource) és Blob-fogadóval (BlobSink) rendelkező folyamatban**

A folyamat tartalmaz egy másolási tevékenységet, amely úgy van beállítva, hogy használja ezeket a bemeneti és kimeneti adatkészletek, és az ütemezés szerint óránként fut. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource** lesz állítva, **a fogadó** típusa pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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
### <a name="type-mapping-for-odbc"></a>Az ODBC típusleképezése
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át az ODBC adattárakból, az ODBC adattípusok az [ODBC adattípus-leképezések](https://msdn.microsoft.com/library/cc668763.aspx) témakörben említettek szerint .NET-típusokhoz vannak rendelve.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-read-from-relational-sources"></a>Relációs forrásokból ismételhető olvasmony
Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Kapcsolódási problémák elhárítása
A csatlakozási problémák elhárításához használja az **Adatkezelési átjáró konfigurációkezelőjének** **Diagnosztika lapját.**

1. Indítsa el **az Adatkezelési átjáró konfigurációkezelőjét**. A "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" (vagy) közvetlenül futtatható a "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" (vagy) az **Átjáró** kifejezéssel a **Microsoft Data Management Gateway** alkalmazásra mutató hivatkozás megkereséséhez az alábbi képen látható módon.

    ![Átjáró keresése](./media/data-factory-odbc-connector/search-gateway.png)
2. Váltson a **Diagnosztika** lapra.

    ![Átjáródiagnosztika](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Válassza ki az adattár **típusát** (csatolt szolgáltatás).
4. Adja meg a **hitelesítést,** és adja meg a **hitelesítő adatokat** (vagy) adja meg az adattárhoz való csatlakozáshoz használt **kapcsolati karakterláncot.**
5. Kattintson a **Kapcsolat tesztelése** elemre az adattárhoz való csatlakozás teszteléséhez.

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
