---
title: Adatok másolása az Azure Data Factory használatával Webtábla |} A Microsoft Docs
description: 'További tudnivalók: Web tábla összekötő az Azure Data Factory, amely lehetővé teszi egy webes táblázat a Data Factory által fogadóként támogatott adattárak adatok másolása.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 3e62dbc31976187f4bd37a3567169da2ffa0909b
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807652"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Web-táblából
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-web-table-connector.md)
> * [Aktuális verzió](connector-web-table.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy webes táblázat adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

A webes táblázat összekötő a különbséget a [REST közötti összekötő](connector-rest.md) és a [HTTP-összekötő](connector-http.md) vannak:

- **Webes tábla összekötő** kivonatok tábla egy HTML-weblap tartalmat.
- **REST-összekötő** kifejezetten az adatok másolása a RESTful API-k támogatása.
- **HTTP-összekötő** általános adatokat lekérni bármilyen HTTP-végpontot, például fájl letöltéséhez. 

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat webes táblaadatbázisához bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a webes táblázat összekötő támogatja az **táblát tartalom beolvasása egy HTML-oldalt a**.

## <a name="prerequisites"></a>Előfeltételek

A webes táblázat összekötő használatához, be kell állítania egy helyi Integration Runtime. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások webes táblázat összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a webes tábla társított szolgáltatás:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Web** |Igen |
| url | A webes forrás URL-címe |Igen |
| authenticationType | Megengedett értéke: **Névtelen**. |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**Példa**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a webes table adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Webtábla adatmásolás, állítsa be a type tulajdonság, az adatkészlet **Webtábla**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **Webtábla** | Igen |
| elérési út |Az erőforrás, amely tartalmazza a tábla relatív URL-CÍMÉT. |Nem. Ha nincs megadva elérési út, csak az URL-címet a társított szolgáltatás definíciójában megadott szolgál. |
| index |Az erőforrás a tábla indexe. Lásd: [egy tábla egy HTML-oldalt a Get-index](#get-index-of-a-table-in-an-html-page) szakasz lépéseit egy tábla indexének első HTML-lapon. |Igen |

**Példa**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a webes tábla forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="web-table-as-source"></a>Webtábla forrásként

Adatok másolása a webtábla, állítsa be a forrás típusaként a másolási tevékenység **WebSource**, nincs további tulajdonságok támogatottak.

**Példa**

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Egy tábla indexének lekérése egy HTML-lap

Egy táblázatban, amely a konfigurálnia kell a indexét beolvasásához [adatkészlet tulajdonságai](#dataset-properties), használhatja például az Excel 2016-ban az eszköz a következő:

1. Indítsa el a **Excel 2016-ban** , és váltson át a **adatok** fülre.
2. Kattintson a **új lekérdezés** az eszköztáron, mutasson a **egyéb forrásokból származó** kattintson **webes tartalomból**.

    ![A Power Query menü](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Az a **webes tartalomból** párbeszédpanelen adja meg **URL-cím** , melyeket használhat, a társított szolgáltatás JSON (például: https://en.wikipedia.org/wiki/) elérési utat kell megadni az adatkészlet mellett (például: A(z) % 27s_100_Years AFI... 100_Movies), és kattintson a **OK**.

    ![Webes párbeszédpanelről](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Ebben a példában használt URL-cím: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha látja **hozzáférés webes tartalom** párbeszédpanelen jelölje ki a jobb **URL-cím**, **hitelesítési**, kattintson **Connect**.

   ![Webes tartalom párbeszédpanel](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kattintson egy **tábla** tekintse meg a tábla tartalmát, majd kattintson a faszerkezetes nézetben elem **szerkesztése** gombra a lap alján.  

   ![A kezelő párbeszédpanel](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Az a **Lekérdezésszerkesztő** ablakban kattintson a **speciális szerkesztő** gombra az eszköztáron.

    ![Speciális szerkesztő gomb](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. A speciális szerkesztő párbeszédpanel mellett a "Forrás" száma érték az index.

    ![Speciális szerkesztő - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Az Excel 2013 használatakor használjon [Excelhez készült Microsoft Power Query](https://www.microsoft.com/download/details.aspx?id=39379) az indexet beolvasni. Lásd: [csatlakozás egy weblaphoz](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) részleteivel. A lépések hasonlóak, ha használ [Microsoft Power BI Desktop-](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
