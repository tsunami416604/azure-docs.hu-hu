---
title: "Adatok másolása az Azure Data Factory használatával webes tábla |} Microsoft Docs"
description: "További információk a webes tábla összekötő az Azure Data Factory, amely lehetővé teszi egy webes tábla mosdók adat-előállító által támogatott adattárolókhoz adatok másolása."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: affe0d7da4b6e40da3b7fdb10d53b9e793ec19bd
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával webes táblából
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-web-table-connector.md)
> * [2. verzió – Előzetes verzió](connector-web-table.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni egy webes tábla adatbázis. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.


> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [webes tábla-összekötőt a V1](v1/data-factory-web-table-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Webes tábla adatbázis adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a webes tábla összekötő támogatja az **HTML-lapon tábla tartalom kibontására sor kerül**. A HTTP/s végpont adatok lekéréséhez használja [HTTP összekötő](connector-http.md) helyette.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](create-self-hosted-integration-runtime.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való webes tábla összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Webes table csatolt szolgáltatás a következő tulajdonságok esetén támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **webes** |Igen |
| URL-címe | A webes forrás URL-címe |Igen |
| AuthenticationType | Az érték engedélyezett: **névtelen**. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör a webes tábla a dataset által támogatott tulajdonságokról.

Adatok másolása webes tábla, az adatkészlet típus tulajdonságának beállítása **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **Webtábla** | Igen |
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
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör a webes tábla forrás által támogatott tulajdonságokról.

### <a name="web-table-as-source"></a>Webes táblázat forrásaként

Adatok másolása webes tábla, állítsa be a forrás típusa a másolási tevékenység **WebSource**, további tulajdonságok nem támogatottak.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Egy tábla indexének lekérése egy HTML-weblap

1. Indítsa el **Excel 2016** majd átváltása a **adatok** fülre.
2. Kattintson a **új lekérdezés** eszköztárán mutasson **egyéb forrásokból származó** kattintson **a webes**.

    ![A Power Query menü](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. A a **a webes** párbeszédpanelen adja meg a **URL-cím** használható a társított szolgáltatás JSON (például: https://en.wikipedia.org/wiki/) elérési út lehet megadni az adatkészlet együtt (például: AFI % 27s_100_Years 100_Movies), és kattintson a **OK**.

    ![Webes párbeszédpanelről](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Ebben a példában használt URL-cím: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Ha látja **hozzáférés webes tartalom** párbeszédpanelen jelölje ki a jobb **URL-cím**, **hitelesítési**, kattintson **Connect**.

   ![Webes tartalom párbeszédpanel](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kattintson egy **tábla** tekintse meg a tábla tartalmát, és kattintson a fanézetben elem **szerkesztése** panel alján.  

   ![Navigator párbeszédpanel](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Az a **Lekérdezésszerkesztő** ablak, kattintson a **speciális szerkesztő** gomb az eszköztáron.

    ![Speciális szerkesztő gomb](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. A speciális szerkesztése párbeszédpanelen az mellett látható "Forrás" érték az index.

    ![Speciális szerkesztő - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Ha az Excel 2013 használ, [Microsoft Power Query az Excel programhoz](https://www.microsoft.com/download/details.aspx?id=39379) lekérni az index. Lásd: [weblapon kapcsolódás](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) cikkben alább. A lépések hasonlóak használata [Microsoft Power BI Desktop az](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).