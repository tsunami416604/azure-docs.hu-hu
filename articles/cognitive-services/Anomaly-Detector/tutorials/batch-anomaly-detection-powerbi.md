---
title: Rendellenességek batch észlelése és a Power BI használatával jelenítheti meg
titlesuffix: Azure Cognitive Services
description: Az Anomáliadetektálási detector használatával API és a Power BI használatával rendellenességek során az idősorozat-adatok megjelenítése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 48257782bd71f6145adc7107a5fe70b4cc4f8305
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790447"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Oktatóanyag: Rendellenességek batch észlelése és a Power BI használatával jelenítheti meg

Ebben az oktatóanyagban használja kötegként time series adatkészlet belül előforduló rendellenességek felderítéséhez. Power BI desktop használatával, Ön lesz igénybe vehet egy Excel-fájl, az adatok előkészítése az Anomáliadetektálási detector használatával API és jelenítheti meg, egész statisztikai rendellenességek.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A Power BI Desktop használatával importálja, és a egy time series adatkészlet átalakítása
> * A Power BI Desktop integrálása a Anomáliadetektálási detector használatával batch anomáliadetektálás API
> * Megjelenítheti a rendellenességeket, többek között a várt és látott értékeket, és a rendellenességek észlelése határok belül.

## <a name="prerequisites"></a>Előfeltételek

* [A Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), elérhető ingyenes.
* Az excel (.xlsx) fájlt tartalmazó idősorozat-adatok mutat. A példaadatokat ebben a rövid útmutatóban található [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Betölteni, és az idősorozat-adatok formázása

Első lépésként nyissa meg a Power BI Desktopban, és a letöltött a Előfeltételek idősorozat-adatok betöltése. Az excel-fájlban található tartalmazza az egyezményes világidő (UTC) időbélyegző-érték párokat.  

> [!NOTE]
> A Power BI számos különböző forrásból, mint például a .csv-fájlok, SQL adatbázisok, az Azure blob storage-bA és további adatokat használhatja.  

A Power BI Desktop fő ablakban kattintson a **kezdőlap** menüszalagon. Az a **külső adatok** csoportba, a menüszalag, nyissa meg a **adatok lekérése** legördülő menüben, majd kattintson **Excel**.

![A Power BI-ban "Adatok lekérése" gomb képe](../media/tutorials/power-bi-get-data-button.png)

Miután a párbeszédpanel megjelenik, lépjen abba a mappába, ahová letöltötte a példa .xlsx-fájlt, és jelölje ki. Miután a **kezelő** párbeszédpanelen megjelenik, kattintson a **Munka1**, majd **szerkesztése**.

![Az adatforrás "Kezelő" – Power BI-ban képe](../media/tutorials/navigator-dialog-box.png)

A Power BI átalakítja az időbélyegeket az első oszlopban egy `Date/Time` adattípus. Ezek az időbélyegek konvertálni kell szöveggé annak érdekében, hogy az Anomáliadetektálási detector használatával API-hoz küldhető. Ha a Power Query-szerkesztő nem nyílik meg automatikusan, kattintson a **lekérdezések szerkesztése** kezdőlap lapján. 

Kattintson a **átalakítása** menüszalagján a Power Query-szerkesztőben. Az a **minden oszlop** csoportjában nyissa meg a **adattípus:** legördülő menüből, és válassza ki **szöveg**.

![Az adatforrás "Kezelő" – Power BI-ban képe](../media/tutorials/data-type-drop-down.png)

Ha az oszlop típusának a módosításával kapcsolatos értesítést kap, kattintson az **cserélje le az aktuális**. Ezt követően kattintson a **Bezárás és alkalmazás** vagy **alkalmaz** a a **kezdőlap** menüszalagon. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Hozzon létre egy függvényt, hogy elküldje az adatokat, és a válasz formázása

Formázása, és küldje el a data-fájlt az Anomáliadetektálási detector használatával API-hoz, hívhat meg egy lekérdezést a fent létrehozott táblában. A Power Query-szerkesztőben, az a **kezdőlap** menüszalagon, nyissa meg a **új forrás** legördülő menüben, majd kattintson **üres lekérdezés**.

Győződjön meg arról, hogy az új lekérdezés ki van jelölve, majd kattintson a **speciális szerkesztő**. 

![A Power bi-ban a "Speciális szerkesztő" gomb képe](../media/tutorials/advanced-editor-screen.png)

A speciális szerkesztő belül a következő Power Query M kódrészletet használja az oszlopok kigyűjtése a táblában, és küldje el az API-hoz. Ezt követően a lekérdezés fog hozzon létre egy táblát a JSON-válaszból, és küldje vissza. Cserélje le a `apiKey` érvényes Anomáliadetektálási detector használatával API-kulcsát, a változó és `endpoint` a végponthoz. Miután megadta a lekérdezést, a speciális szerkesztő, kattintson a **kész**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect,
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

A lekérdezés: adatok oszlopfejléc meghívása kiválasztásával `Sheet1` alábbi **paraméter megadása**, kattintson **Invoke**. 

![A "Speciális szerkesztő" gomb képe](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Forrás adatvédelem és hitelesítés

> [!NOTE]
> Vegye figyelembe az adatok védelmében, és a hozzáférés a szervezet szabályzatait. Lásd: [Power BI Desktop adatvédelmi szintjeinek](https://docs.microsoft.com/power-bi/desktop-privacy-levels) további információt.

Figyelmeztető üzenetet kaphat, amikor megpróbálja futtatni a lekérdezést, mert azt már használja egy külső adatforrást. 

![Egy Power BI által létrehozott figyelmeztetés képe](../media/tutorials/blocked-function.png)

Ennek kijavításához kattintson **fájl**, és **lehetőségek és beállítások**. Kattintson a **beállítások**. Alább **aktuális fájl**válassza **adatvédelmi**, és **teljesítmény lehetséges javítása az adatvédelmi szintek figyelmen kívül hagyásával**. 

Ezenkívül előfordulhat, hogy kap egy üzenetben kéri, adja meg, hogyan csatlakozhat az API-hoz való.

![Egy kérelem eléréséhez szükséges hitelesítő adatokat adja meg a kép](../media/tutorials/edit-credentials-message.png)

Ennek kijavításához kattintson **hitelesítő adatok szerkesztése** az üzenetben. Miután a párbeszédpanel megjelenik, válassza ki a **névtelen** kapcsolódásának az API-hoz. Ezután kattintson a **Csatlakozás** gombra. 

Ezt követően kattintson a **Bezárás és alkalmazás** a a **kezdőlap** menüszalagján a módosítások életbe léptetéséhez.

## <a name="visualize-the-anomaly-detector-api-response"></a>Az Anomáliadetektálási detector használatával API-válasz megjelenítése

A fő Power BI-képernyőn jelenítheti meg az adatokat a fent létrehozott lekérdezések használatának megkezdéséhez. Először válasszon **vonaldiagram** a **Vizualizációk**. Majd adja hozzá az időbélyeg a meghívott függvény a vonaldiagram **tengely**. Kattintson rá a jobb gombbal, és válassza ki **időbélyeg**. 

![Kattintson a jobb gombbal az időbélyegző-érték](../media/tutorials/timestamp-right-click.png)

Adja hozzá a következő mezőket a **meghívott függvény** a diagramhoz **értékek** mező. Használja az alábbi képernyőfelvétel a diagram létrehozása érdekében.

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Az új gyorsmérő képernyő képe](../media/tutorials/chart-settings.png)

Miután hozzáadta a mezőket, kattintson a diagramra, majd méretezze át, és megjeleníti az összes adatponttal. A diagram hasonlóan néz ki az alábbi képernyőképet:

![Az új gyorsmérő képernyő képe](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Anomáliadetektálási adatpontok megjelenítése

A Power BI-ablak jobb oldalán alább a **MEZŐK** ablaktáblán kattintson a jobb gombbal a **érték** alatt a **függvény meghívása lekérdezésben**, kattintson **új gyors mérték**.

![Az új gyorsmérő képernyő képe](../media/tutorials/new-quick-measure.png)

A megjelenő képernyőn jelölje be a **szűrt érték** a számítási. Állítsa be **érték kiinduló** való `Sum of Value`. Ezután húzza `IsAnomaly` a a **meghívott függvény** mező a **szűrő**. Válassza ki `True` származó a **szűrő** legördülő menüből.

![Az új gyorsmérő képernyő képe](../media/tutorials/new-quick-measure-2.png)

Kattintás után **Ok**, hogy egy `Value for True` mezőt, a mezők listájának alján. Kattintson a jobb gombbal, és nevezze át, hogy **Anomáliadetektálási**. Adja hozzá a diagram **értékek**. Válassza ki a **formátum** , és állítsa be az x tengely típusát eszközzel **Kategorikus**.

![Az új gyorsmérő képernyő képe](../media/tutorials/format-x-axis.png)

Színek alkalmazása a diagramra kattintva a **formátum** eszköz és **adatszínek**. A diagram a következőhöz a következőhöz hasonlóan kell kinéznie:

![Az új gyorsmérő képernyő képe](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Az Azure Databricks streamelési anomáliadetektálás](anomaly-detection-streaming-databricks.md)
