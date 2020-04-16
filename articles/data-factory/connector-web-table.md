---
title: Adatok másolása webtáblából az Azure Data Factory használatával
description: Ismerje meg az Azure Data Factory webtábla-összekötőjét, amely lehetővé teszi az adatok másolását egy webtáblából a Data Factory által támogatott adattárolókba fogadóként.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0eb4d37342685c13027a69bb6cb85f618fa63f20
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410216"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Adatok másolása webtáblából az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-web-table-connector.md)
> * [Jelenlegi verzió](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy webtábla-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

A különbség a webtábla-összekötő, a [REST-összekötő](connector-rest.md) és a [HTTP-összekötő](connector-http.md) között a következők:

- **A webtábla-összekötő** html weblapról nyeri ki a táblázat tartalmát.
- **A REST-összekötő** kifejezetten támogatja a RESTful API-kból származó adatok másolását.
- **A HTTP-összekötő** általános fontosságú, ha bármely HTTP-végpontról lekéri az adatokat, például fájl letöltéséhez. 

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a webtábla-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A webtábla-adatbázisból adatokat másolhat bármely támogatott fogadóadattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez a webtábla-összekötő támogatja a **táblázattartalom HTML-lapból történő kibontását.**

## <a name="prerequisites"></a>Előfeltételek

A webtábla-összekötő használatához be kell állítania egy saját üzemeltetésű integrációs futásidejűt. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Data Factory-entitások webtábla-összekötőre jellemző definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A webtábla-csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **Web** |Igen |
| url | A webes forrás URL-címe |Igen |
| authenticationType | Megengedett érték: **Névtelen**. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

**Példa:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a webes tábla adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a webtáblából, állítsa az adatkészlet típustulajdonságát **WebTable**tulajdonságra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **WebTable** | Igen |
| path |A táblát tartalmazó erőforrás relatív URL-címe. |Nem. Ha nincs megadva elérési út, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. |
| Index |Az erőforrás ban lévő tábla indexe. A [HTML-lapok szakaszban lévő táblázat indexének beszerzése](#get-index-of-a-table-in-an-html-page) című témakörben a HTML-lapokban lévő táblázat indexindexének beszerzése című témakört találja. |Igen |

**Példa:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a webtábla forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="web-table-as-source"></a>Webtábla forrásként

Ha adatokat szeretne másolni a webtáblából, állítsa be a forrástípust a másolási tevékenységben a **WebSource programba,** és a további tulajdonságok nem támogatottak.

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Táblázat indexének beszereznie HTML-lapon

Az [adatkészlet tulajdonságaiban](#dataset-properties)konfigurálandó tábla indexének leéséhez például az Excel 2016-ot használhatja eszközként az alábbiak szerint:

1. Indítsa el **az Excel 2016-ot,** és váltson az **Adatok** lapra.
2. Kattintson az eszköztár **Új lekérdezés gombjára,** mutasson **a Más forrásokból pontra,** és kattintson a **Webről**parancsra.

    ![A Power Query menü](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. A **Forrásból** párbeszédpanelen adja meg a JSON csatolt szolgáltatásban https://en.wikipedia.org/wiki/) használt **URL-címet** (például az adatkészlethez megadott elérési úttal együtt (például: AFI%27s_100_Years... 100_Movies), majd kattintson **az OK gombra.**

    ![Webről párbeszédpanel](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    A példában használt URL-cím:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha megjelenik **az Access webes tartalom** párbeszédpanele, válassza ki a megfelelő **URL-címet**, **hitelesítést,** majd kattintson a **Csatlakozás gombra.**

   ![Az Access webes tartalom párbeszédpanelje](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kattintson egy **táblázatelemre** a fa nézetben a táblázat tartalmának **megtekintéséhez,** majd kattintson az alsó Szerkesztés gombra.  

   ![Kezelő párbeszédpanel](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. A **Lekérdezésszerkesztő** ablakban kattintson az eszköztár **Speciális szerkesztő** gombjára.

    ![Speciális szerkesztő gomb](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. A Speciális szerkesztő párbeszédpanelen a "Forrás" melletti szám az index.

    ![Speciális szerkesztő - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Excel 2013 használata esetén az index lekérdezéséhez használja [az Excelhez készült Microsoft Power Queryt.](https://www.microsoft.com/download/details.aspx?id=39379) A részleteket a [Csatlakozás weblaphoz](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) című cikkben találja. A lépések hasonlóak az [asztali Microsoft Power BI](https://powerbi.microsoft.com/desktop/)használata esetén is.


## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
