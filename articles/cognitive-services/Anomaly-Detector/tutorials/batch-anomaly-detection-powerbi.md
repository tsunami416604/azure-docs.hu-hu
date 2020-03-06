---
title: 'Oktatóanyag: rendellenességek megjelenítése a Batch észlelésével és Power BIával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja az anomália-Kiderítő API-t és a Power BI az idősorozat-adatvesztések megjelenítéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402664"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Oktatóanyag: rendellenességek megjelenítése a Batch észlelésével és Power BIával

Ez az oktatóanyag egy idősorozat-adatkészletben lévő anomáliák megkeresésére használható kötegként. A Power BI Desktop használatával egy Excel-fájlt fog készíteni, előkészíti az adatvesztési detektor API-ját, és megjeleníti a statisztikai rendellenességeket az egészben.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Idősorozat-adatkészletek importálása és átalakítása Power BI Desktop használatával
> * Power BI Desktop integrálása a Batch-anomáliák észleléséhez tartozó anomáliák Kiderítő API-val
> * Megjelenítheti az adatokban talált rendellenességeket, beleértve a várt és látható értékeket, valamint a rendellenességek észlelési határait.

## <a name="prerequisites"></a>Előfeltételek
* [Azure-előfizetés](https://azure.microsoft.com/free/)
* A [Microsoft Power bi Desktop](https://powerbi.microsoft.com/get-started/)ingyenesen elérhető.
* Az idősorozat adatpontjait tartalmazó Excel-fájl (. xlsx). A rövid útmutatóhoz tartozó példa a [githubon](https://go.microsoft.com/fwlink/?linkid=2090962) érhető el
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hozzon létre egy Text Analytics-erőforrást,"  target="_blank">hozzon létre egy Text Analytics erőforrás <span class="docon docon-navigate-external x-hidden-focus"></span> -</a> a Azure Portal a kulcs és a végpont beszerzéséhez. 
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. Ezt később is megteheti a rövid útmutatóban.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Az idősorozat-adatértékek betöltése és formázása

Első lépésként nyissa meg Power BI Desktop és töltse be az előfeltételekből letöltött idősorozat-adatok betöltését. Ez az Excel-fájl az egyezményes világidő (UTC) és az érték párok sorozatát tartalmazza.  

> [!NOTE]
> A Power BI számos különböző forrásból, például. csv-fájlokból, SQL-adatbázisokból, Azure Blob Storage-ból és egyebekből származó adatok használatával használható.  

A fő Power BI Desktop ablakban kattintson a **Kezdőlap** menüszalagra. A menüszalag **külső** adatcsoportjában nyissa meg az **adatlekérdezés** legördülő menüt, és kattintson az **Excel**elemre.

![Az "adatok lekérése" gomb képe Power BI](../media/tutorials/power-bi-get-data-button.png)

A párbeszédpanel megjelenése után Navigáljon arra a mappára, ahová letöltötte a example. xlsx fájlt, majd válassza ki. Miután a **navigátor** párbeszéd megjelenik, kattintson a **Munka1**elemre, majd a **Szerkesztés**gombra.

![A "navigátor" adatforrás képe Power BI](../media/tutorials/navigator-dialog-box.png)

A Power BI az első oszlop időbélyegeit `Date/Time` adattípusra konvertálja. Ezeket az időbélyegeket szöveggé kell alakítani, hogy el lehessen elküldeni az anomália-detektor API-nak. Ha a Power Query-szerkesztő nem nyílik meg automatikusan, kattintson a kezdőlapon a **lekérdezések szerkesztése** elemre. 

Kattintson az **átalakítás** menüszalagra a Power Query-szerkesztőben. A **bármely oszlop** csoportban nyissa meg az **adattípus:** legördülő menüt, és válassza a **text (szöveg**) lehetőséget.

![A "navigátor" adatforrás képe Power BI](../media/tutorials/data-type-drop-down.png)

Amikor értesítést kap az oszlop típusának módosításáról, kattintson az **aktuális cseréje**elemre. Ezt követően kattintson a **Bezárás gombra & alkalmazása** vagy az **alkalmazás** a **Kezdőlap** menüszalagon. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Függvény létrehozása az adat elküldéséhez és a válasz formázásához

Az adatfájlnak a rendellenesség-érzékelő API-hoz való formázásához és elküldéséhez egy lekérdezést hívhat meg a fent létrehozott táblán. A Power Query szerkesztőben, a **Kezdőlap** menüszalagján nyissa meg az **új forrás** legördülő menüt, és kattintson az **üres lekérdezés**elemre.

Győződjön meg arról, hogy az új lekérdezés van kiválasztva, majd kattintson a **speciális szerkesztő**elemre. 

![A "Speciális szerkesztő" gomb képe a Power BI](../media/tutorials/advanced-editor-screen.png)

A Speciális szerkesztőon belül a következő Power Query M kódrészlettel bontsa ki az oszlopokat a táblából, és küldje el az API-nak. Ezt követően a lekérdezés létrehoz egy táblát a JSON-válaszból, és visszaküldi azt. Cserélje le az `apiKey` változót az érvényes anomália-érzékelő API-kulcsára, és `endpoint` a végponttal. Miután megadta a lekérdezést a Speciális szerkesztőban, kattintson a **kész**gombra.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Hívja meg a lekérdezést az adatlapon úgy, hogy kiválasztja `Sheet1` az **ENTER paramétert**, majd kattintson a **meghívás**gombra. 

![A "Speciális szerkesztő" gomb képe](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Adatforrás-adatvédelem és-hitelesítés

> [!NOTE]
> Ügyeljen arra, hogy a szervezet szabályzatai adatvédelmet és hozzáférést biztosítson. További információ: [Power bi Desktop adatvédelmi szintek](https://docs.microsoft.com/power-bi/desktop-privacy-levels) .

Előfordulhat, hogy a lekérdezés futtatására tett kísérlet során figyelmeztető üzenet jelenik meg, mivel külső adatforrást használ. 

![A Power BI által létrehozott figyelmeztetést ábrázoló kép](../media/tutorials/blocked-function.png)

A probléma megoldásához kattintson a **fájl**, majd a beállítások **és beállítások**elemre. Ezután kattintson a **Beállítások**elemre. Az **aktuális fájl**alatt válassza az **Adatvédelem**lehetőséget, és **hagyja figyelmen kívül az adatvédelmi szinteket, és javítsa a teljesítményt**. 

Emellett üzenet jelenik meg, amely arra kéri, hogy határozza meg, hogyan kíván csatlakozni az API-hoz.

![A hozzáférési hitelesítő adatok megadására vonatkozó kérést megjelenítő kép](../media/tutorials/edit-credentials-message.png)

A probléma megoldásához kattintson a **hitelesítő adatok szerkesztése** elemre az üzenetben. Ha a párbeszédablak megjelenik, akkor a **Névtelen** API-hoz való csatlakozáshoz válassza a névtelen lehetőséget. Ezután kattintson a **Csatlakozás** gombra. 

Ezt követően kattintson a **Bezárás gombra & alkalmazás** a **Kezdőlap** menüszalagján a módosítások alkalmazásához.

## <a name="visualize-the-anomaly-detector-api-response"></a>Az anomália-érzékelő API-válaszának megjelenítése

A fő Power BI képernyőn kezdje el használni a fent létrehozott lekérdezéseket az adatmegjelenítéshez. Először válassza ki a **diagramot** a **vizualizációkban**. Ezután adja hozzá a meghívott függvény időbélyegét a vonal diagramjának **tengelyéhez**. Kattintson rá a jobb gombbal, és válassza az **időbélyeg**lehetőséget. 

![Kattintson a jobb gombbal az időbélyeg értékére](../media/tutorials/timestamp-right-click.png)

Adja hozzá a következő mezőket a **meghívott függvényből** a diagram **értékek** mezőjébe. A diagram létrehozásához használja az alábbi képernyőképet.

    * Érték
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Az új gyors mérték képernyő képe](../media/tutorials/chart-settings.png)

A mezők hozzáadása után kattintson a diagramra, és méretezze át az összes adatpont megjelenítéséhez. A diagram az alábbi képernyőképhez hasonlóan fog kinézni:

![Az új gyors mérték képernyő képe](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Az anomália adatpontjainak megjelenítése

A Power BI ablak jobb oldalán, a **mezők** ablaktábla alatt kattintson a jobb gombbal az **értékre** a **meghívott függvény lekérdezésében**, és kattintson az **új gyors mérték**elemre.

![Az új gyors mérték képernyő képe](../media/tutorials/new-quick-measure.png)

A megjelenő képernyőn válassza a **szűrt érték** kiszámítása lehetőséget. Adja meg az **alapértéket** `Sum of Value`. Ezután húzza `IsAnomaly` a **meghívott függvény** mezőiből a **szűréshez**. Válassza a `True` lehetőséget a **szűrő** legördülő menüből.

![Az új gyors mérték képernyő képe](../media/tutorials/new-quick-measure-2.png)

Miután rákattintott **az OK gombra**, a mezők listájának alján egy `Value for True` mező jelenik meg. Kattintson rá a jobb gombbal, és nevezze át az **anomáliára**. Adja hozzá a diagram **értékeit**. Ezután válassza ki a **formázó** eszközt, és állítsa az X tengely típusát **kategorikus**értékre.

![Az új gyors mérték képernyő képe](../media/tutorials/format-x-axis.png)

Színeket alkalmazhat a diagramra úgy, hogy a **Formátum** eszközre és az **adatszínekre**kattint. A diagramnak a következőhöz hasonlóan kell kinéznie:

![Az új gyors mérték képernyő képe](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Adatfolyam-rendellenességek észlelése Azure Databricks](anomaly-detection-streaming-databricks.md)
