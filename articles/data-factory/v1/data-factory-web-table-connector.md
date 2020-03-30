---
title: Adatok áthelyezése a webtáblából az Azure Data Factory használatával
description: Megtudhatja, hogy miként helyezhet át adatokat egy weblapon lévő táblából az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265713"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Adatok áthelyezése webtábla-forrásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-web-table-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-web-table.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Webtábla-összekötő a V2](../connector-web-table.md)alkalmazásban című témakört.

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy táblázatból egy weblapon egy támogatott fogadó adattárba. Ez a cikk az [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkre épül, amely általános áttekintést nyújt a másolási tevékenységgel rendelkező adatáthelyezésről és a forrásként/fogadóként támogatott adattárak listájáról.

Az adatelények jelenleg csak a webtáblából más adattárolókba való adatáthelyezést támogatják, más adattárakból nem a webtábla célhelyére történő átmozgatását.

> [!IMPORTANT]
> Ez a webösszekötő jelenleg csak a táblázattartalom HTML-lapról történő kibontását támogatja. Http/s-végpontból történő adatok beolvasásához használja inkább a [HTTP-összekötőt.](data-factory-http-connector.md)

## <a name="prerequisites"></a>Előfeltételek

A webtábla-összekötő használatához be kell állítania egy saját üzemeltetésű integrációs futásidejűt `gatewayName` (más néven Adatkezelési átjárót), és konfigurálnia kell a tulajdonságot a fogadóhoz csatolt szolgáltatásban. Ha például a webtáblából az Azure Blob storage-ba szeretne másolni, konfigurálja az Azure Storage-hoz csatolt szolgáltatást a következőképpen:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat egy helyszíni Cassandra-adattárból. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához. 
- A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. 
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. 

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A minta JSON-definíciók data factory entitások, amelyek adatok másolására szolgálnak egy webtábla, lásd: [JSON példa: Adatok másolása webtáblából az Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) szakasza ebben a cikkben. 

A következő szakaszok a Webtábla data factory entitásai definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a webkapcsolattal ellátott szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következőre kell állítania: **Web** |Igen |
| URL-cím |A webes forrás URL-címe |Igen |
| authenticationType |Névtelen. |Igen |

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **WebTable** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |az adatkészlet típusát. **webtable-re** kell állítani |Igen |
| path |A táblát tartalmazó erőforrás relatív URL-címe. |Nem. Ha nincs megadva elérési út, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. |
| Index |Az erőforrás ban lévő tábla indexe. A [HTML-lapok szakaszban lévő táblázat indexének beszerzése](#get-index-of-a-table-in-an-html-page) című témakörben a HTML-lapokban lévő táblázat indexindexének beszerzése című témakört találja. |Igen |

**Példa:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Jelenleg, ha a másolási tevékenység forrása **WebSource**típusú, a további tulajdonságok nem támogatottak.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-példa: Adatok másolása webtáblából az Azure Blobba
A következő minta a következőket mutatja:

1. [Web](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [WebTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [WebForrást](#copy-activity-properties) és [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként adatokat másol egy webtáblából egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Az alábbi minta bemutatja, hogyan másolhat adatokat egy webtáblából egy Azure blobba. Azonban az adatok közvetlenül másolhatók az [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkben az Azure Data Factory másolási tevékenység használatával szereplő egyik fogadóba.

**Webkapcsolattal összekapcsolt szolgáltatás** Ez a példa a webkapcsolattal rendelkező szolgáltatást használja névtelen hitelesítéssel. A [webcsatolt szolgáltatás](#linked-service-properties) szakaszban a különböző típusú hitelesítéseket használhatja.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**WebTable bemeneti adatkészlet** A **külső** **true** beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

> [!NOTE]
> A [HTML-lapok szakaszban lévő táblázat indexének beszerzése](#get-index-of-a-table-in-an-html-page) című témakörben a HTML-lapokban lévő táblázat indexindexének beszerzése című témakört találja.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure blobkimeneti adatkészlet**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Folyamat másolási tevékenységgel**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **WebSource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**.

A WebSource által támogatott tulajdonságok listájának WebSource-típustulajdonságai.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Táblázat indexének beszereznie HTML-lapon
1. Indítsa el **az Excel 2016-ot,** és váltson az **Adatok** lapra.  
2. Kattintson az eszköztár **Új lekérdezés gombjára,** mutasson **a Más forrásokból pontra,** és kattintson a **Webről**parancsra.

    ![A Power Query menü](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. A **Forrásból** párbeszédpanelen adja meg a JSON csatolt szolgáltatásban https://en.wikipedia.org/wiki/) használt **URL-címet** (például az adatkészlethez megadott elérési úttal együtt (például: AFI%27s_100_Years... 100_Movies), majd kattintson **az OK gombra.**

    ![Webről párbeszédpanel](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    A példában használt URL-cím:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha megjelenik **az Access webes tartalom** párbeszédpanele, válassza ki a megfelelő **URL-címet**, **hitelesítést,** majd kattintson a **Csatlakozás gombra.**

   ![Az Access webes tartalom párbeszédpanelje](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kattintson egy **táblázatelemre** a fa nézetben a táblázat tartalmának **megtekintéséhez,** majd kattintson az alsó Szerkesztés gombra.  

   ![Kezelő párbeszédpanel](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. A **Lekérdezésszerkesztő** ablakban kattintson az eszköztár **Speciális szerkesztő** gombjára.

    ![Speciális szerkesztő gomb](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. A Speciális szerkesztő párbeszédpanelen a "Forrás" melletti szám az index.

    ![Speciális szerkesztő - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Excel 2013 használata esetén az index lekérdezéséhez használja [az Excelhez készült Microsoft Power Queryt.](https://www.microsoft.com/download/details.aspx?id=39379) A részleteket a [Csatlakozás weblaphoz](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) című cikkben találja. A lépések hasonlóak az [asztali Microsoft Power BI](https://powerbi.microsoft.com/desktop/)használata esetén is.

> [!NOTE]
> Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
