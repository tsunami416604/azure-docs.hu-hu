---
title: "Adatok áthelyezése az Azure Data Factory használatával webes táblából |} Microsoft Docs"
description: "Tudnivalók az adatok áthelyezése egy táblából egy Azure Data Factory használatával weblapon."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aaf11636484fb6295c9522795b069839d2783ba9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával webes tábla forrásból
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-web-table-connector.md)
> * [2. verzió – Előzetes verzió](../connector-web-table.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [webes tábla összekötőt, a V2](../connector-web-table.md).

Ez a cikk ismerteti a másolási tevékenység használata az Azure Data Factory tárolt adatok mozgatása egy tábla egy weblapon támogatott fogadó adattárat. Ez a cikk épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikket, amely megadja az adatmozgás általános áttekintést a másolási tevékenység és a támogatott adatforrások/mosdók adattárolókhoz listáját.

Adat-előállító jelenleg csak áthelyezése egy webes tábla adatai más adattárolókhoz, de egy webes tábla célra nem adatok áthelyezését más adatokat tárolja.

> [!IMPORTANT]
> A webalkalmazás-összekötőjének jelenleg csak kibontása tábla tartalma HTML-lapon. A HTTP/s végpont adatok lekéréséhez használja [HTTP összekötő](data-factory-http-connector.md) helyette.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység, mely az adatok egy helyszíni Cassandra adattároló különböző eszközök/API-k használatával létrehozhat egy folyamatot. 

- Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást. 
- Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása egy webes tábla használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az webes tábla az Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások webes táblához JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek jellemző csatolt webszolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **webes** |Igen |
| URL-cím |A webes forrás URL-címe |Igen |
| AuthenticationType |Névtelen. |Igen |

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés segítségével

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
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **Webtábla** tulajdonságai a következők

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A dataset típusa. meg kell **Webtábla** |Igen |
| Elérési út |Az erőforrás, amely tartalmazza a tábla relatív URL-CÍMÉT. |Nem. Ha nincs megadva, csak a megadott URL-cím a társított szolgáltatás definíciójának használja. |
| Index |Annak az erőforrás a táblának az indexe. Lásd: [Get index egy tábla egy HTML-lapon](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon. |Igen |

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
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a forrás, a másolási tevékenység jelenleg típusú **WebSource**, további tulajdonságok nem támogatottak.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-példa: adatok másolása az webes tábla az Azure-Blobba
A következő példában:

1. A társított szolgáltatás típusa [webes](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [Webtábla](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [WebSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat egy webes tábla egy Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

A következő példa bemutatja, hogyan adatok másolása az Azure blob egy webes táblából. Azonban adatok átmásolhatók közvetlenül a megadott mosdók bármelyikét a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk a másolási tevékenység során az Azure Data Factory használatával.

**Webes társított szolgáltatás** a példában a kapcsolódó webszolgáltatás névtelen hitelesítést. Lásd: [webes társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Webtábla bemeneti adatkészlet** beállítás **külső** való **igaz** tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

> [!NOTE]
> Lásd: [Get index egy tábla egy HTML-lapon](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon.  
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


**Az Azure Blob kimeneti adatkészlet**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1).

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



**A másolási tevékenység-feldolgozási folyamat**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **WebSource** és **fogadó** típusúra **BlobSink**.

Lásd: [WebSource típustulajdonságokat](#copy-activity-type-properties) a WebSource által támogatott tulajdonságok listája.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Egy tábla indexének lekérése egy HTML-weblap
1. Indítsa el **Excel 2016** majd átváltása a **adatok** fülre.  
2. Kattintson a **új lekérdezés** eszköztárán mutasson **egyéb forrásokból származó** kattintson **a webes**.

    ![A Power Query menü](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. A a **a webes** párbeszédpanelen adja meg a **URL-cím** használható a társított szolgáltatás JSON (például: https://en.wikipedia.org/wiki/) elérési út lehet megadni az adatkészlet együtt (például: AFI % 27s_100_Years 100_Movies), és kattintson a **OK**.

    ![Webes párbeszédpanelről](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Ebben a példában használt URL-cím: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha látja **hozzáférés webes tartalom** párbeszédpanelen jelölje ki a jobb **URL-cím**, **hitelesítési**, kattintson **Connect**.

   ![Webes tartalom párbeszédpanel](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kattintson egy **tábla** tekintse meg a tábla tartalmát, és kattintson a fanézetben elem **szerkesztése** panel alján.  

   ![Navigator párbeszédpanel](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Az a **Lekérdezésszerkesztő** ablak, kattintson a **speciális szerkesztő** gomb az eszköztáron.

    ![Speciális szerkesztő gomb](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. A speciális szerkesztése párbeszédpanelen az mellett látható "Forrás" érték az index.

    ![Speciális szerkesztő - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Ha az Excel 2013 használ, [Microsoft Power Query az Excel programhoz](https://www.microsoft.com/download/details.aspx?id=39379) lekérni az index. Lásd: [weblapon kapcsolódás](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) cikkben alább. A lépések hasonlóak használata [Microsoft Power BI Desktop az](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
