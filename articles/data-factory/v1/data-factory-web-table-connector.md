---
title: Adatok áthelyezése az Azure Data Factory használatával Webtábla |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával egy táblából.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ba8db3ebe2caf4c37d147f744326b6e631cb556
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022053"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Adatok áthelyezése egy webes táblázat a forrás Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-web-table-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-web-table.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [webes táblázat összekötő a v2-ben](../connector-web-table.md).

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok áthelyezése egy weblapon egy táblából egy támogatott fogadó adattárba. Ez a cikk épül, amely a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk, amely a másolási tevékenység és a források/fogadóként támogatott adattárak listáját jeleníti meg az adatmozgatás általános áttekintést.

A Data factory jelenleg csak helyez át adatokat egy webes táblázat pedig más adattárakban támogatja, de nem adatok áthelyezése más adatokat tárolja egy webes táblázat célhelyre.

> [!IMPORTANT]
> A webes összekötő jelenleg támogatja kinyerésekor tábla tartalmat csak egy HTML-oldalt. Egy HTTP-vagy HTTPS-végpont-adatokat lekérni, használja a [HTTP-összekötő](data-factory-http-connector.md) helyette.

## <a name="prerequisites"></a>Előfeltételek

A webes táblázat összekötő használatához kell egy helyi Integration Runtime (más néven az adatkezelési átjáró) telepítése és konfigurálása a `gatewayName` tulajdonságot a fogadó társított szolgáltatás. Például a webtábla másolása az Azure Blob storage-konfigurálja az Azure Storage társított szolgáltatás a következők:

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
Egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a helyszíni Cassandra adattárolókból más eszközök/API-k használatával is létrehozhat. 

- A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával. 
- A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját. 

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. 
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy webes táblázat használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása az Azure Blob webtábla](#json-example-copy-data-from-web-table-to-azure-blob) című szakaszát. 

Az alábbi szakaszok nyújtanak, amelyek meghatározzák egy webes táblázat az adott Data Factory-entitások JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a JSON-elemeket társított webszolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Web** |Igen |
| URL-cím |A webes forrás URL-címe |Igen |
| authenticationType |Névtelen. |Igen |

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés használatával

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
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **Webtábla** tulajdonságai a következők

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |Az adatkészlet típusa. meg kell **Webtábla** |Igen |
| elérési út |Az erőforrás, amely tartalmazza a tábla relatív URL-CÍMÉT. |Nem. Ha nincs megadva elérési út, csak az URL-címet a társított szolgáltatás definíciójában megadott szolgál. |
| index |Az erőforrás a tábla indexe. Lásd: [egy tábla egy HTML-oldalt a Get-index](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon. |Igen |

**Példa**

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
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a másolási tevékenység a forrás jelenleg típusú **WebSource**, nincs további tulajdonságok támogatottak.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-példa: Webtábla adatokat másol az Azure Blobba
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [webes](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [Webtábla](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [WebSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy webes táblázat Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

A következő minta bemutatja, hogyan másolhat adatokat egy webes táblázat az Azure-blobba. Azonban az adatok közvetlenül a megadott fogadóként bármelyikét a átmásolhatók a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikk az Azure Data Factoryban a másolási tevékenység használatával.

**A társított szolgáltatás webes** ebben a példában a társított webszolgáltatás a névtelen hitelesítést. Lásd: [webes társított szolgáltatás](#linked-service-properties) használható hitelesítési típust a következő szakaszban.

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

**Azure Storage társított szolgáltatás**

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

**Webtábla bemeneti adatkészlet** beállítás **külső** való **igaz** a Data Factory szolgáltatás tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

> [!NOTE]
> Lásd: [egy tábla egy HTML-oldalt a Get-index](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon.  
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

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1.).

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



**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **WebSource** és **fogadó** típusa **BlobSink**.

Lásd: [WebSource tulajdonságait](#copy-activity-type-properties) a WebSource által támogatott tulajdonságok listáját.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Egy tábla indexének lekérése egy HTML-lap
1. Indítsa el a **Excel 2016-ban** , és váltson át a **adatok** fülre.  
2. Kattintson a **új lekérdezés** az eszköztáron, mutasson a **egyéb forrásokból származó** kattintson **webes tartalomból**.

    ![A Power Query menü](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Az a **webes tartalomból** párbeszédpanelen adja meg **URL-cím** , melyeket használhat, a társított szolgáltatás JSON (például: https://en.wikipedia.org/wiki/) elérési utat kell megadni az adatkészlet mellett (például: A(z) % 27s_100_Years AFI... 100_Movies), és kattintson a **OK**.

    ![Webes párbeszédpanelről](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Ebben a példában használt URL-cím: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha látja **hozzáférés webes tartalom** párbeszédpanelen jelölje ki a jobb **URL-cím**, **hitelesítési**, kattintson **Connect**.

   ![Webes tartalom párbeszédpanel](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kattintson egy **tábla** tekintse meg a tábla tartalmát, majd kattintson a faszerkezetes nézetben elem **szerkesztése** gombra a lap alján.  

   ![A kezelő párbeszédpanel](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Az a **Lekérdezésszerkesztő** ablakban kattintson a **speciális szerkesztő** gombra az eszköztáron.

    ![Speciális szerkesztő gomb](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. A speciális szerkesztő párbeszédpanel mellett a "Forrás" száma érték az index.

    ![Speciális szerkesztő - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Az Excel 2013 használatakor használjon [Excelhez készült Microsoft Power Query](https://www.microsoft.com/download/details.aspx?id=39379) az indexet beolvasni. Lásd: [csatlakozás egy weblaphoz](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) részleteivel. A lépések hasonlóak, ha használ [Microsoft Power BI Desktop-](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
