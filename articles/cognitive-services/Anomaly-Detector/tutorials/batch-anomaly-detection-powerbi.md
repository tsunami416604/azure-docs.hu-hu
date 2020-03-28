---
title: 'Oktatóanyag: Anomáliák megjelenítése kötegelt észleléssel és A Power BI-val'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az Anomália-detektor API-t és a Power BI-t az anomáliák megjelenítésére az idősorozat-adatok ban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402664"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Oktatóanyag: Anomáliák megjelenítése kötegelt észleléssel és A Power BI-val

Az oktatóanyag segítségével az idősorozat-adatkészleten belüli anomáliákat kötegként keresheti meg. A Power BI desktop használatával excelfájlt készít, előkészíti az adatokat az Anomáliadetektor API-hoz, és statisztikai anomáliákat jelenít meg benne.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Idősorozat-adatkészlet importálása és átalakítása a Power BI Desktop használatával
> * A Power BI Desktop integrálása az Anomaly Detector API-val a kötegelt anomáliadetektáláshoz
> * Vizualizálja az adatokban található rendellenességeket, beleértve a várt és a látott értékeket, valamint az anomáliadetektálási határokat.

## <a name="prerequisites"></a>Előfeltételek
* [Azure-előfizetés](https://azure.microsoft.com/free/)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), ingyenesen elérhető.
* Idősorozat-adatpontokat tartalmazó excel-fájl (.xlsx). A rövid útmutató példaadatai a [GitHubon](https://go.microsoft.com/fwlink/?linkid=2090962) találhatók.
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez. 
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. Ezt később a rövid útmutatóban fogja megtenni.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Az idősorozat-adatok betöltése és formázása

Első lépésekhez nyissa meg a Power BI Desktopot, és töltse be az előfeltételekből letöltött idősorozat-adatokat. Ez az excel fájl egy sor koordinált világidő (UTC) időbélyeget és értékpárokat tartalmaz.  

> [!NOTE]
> A Power BI számos forrásból, például .csv-fájlokból, SQL-adatbázisokból, Azure blobstorageból stb.  

A Power BI Asztali főablakában kattintson a **Kezdőlap** menüszalagra. A menüszalag **Külső adatok** csoportjában nyissa meg az **Adatok bekésése** legördülő menüt, és kattintson az **Excel**parancsra.

![Az "Adatok bekerülése" gomb képe a Power BI-ban](../media/tutorials/power-bi-get-data-button.png)

Miután a párbeszédpanel megjelenik, keresse meg azt a mappát, amelybe a példát .xlsx fájlt töltötte le, és jelölje ki. A **Navigátor** párbeszédének megjegedése után kattintson **a Lap1**gombra, majd **a Szerkesztés gombra.**

![A Power BI "Navigator" adatforrásképernyőjének képe](../media/tutorials/navigator-dialog-box.png)

A Power BI az első oszlopban lévő `Date/Time` időbélyegeket adattípussá alakítja. Ezeket az időbélyegeket szöveggé kell konvertálni ahhoz, hogy az Anomáliadetektor API-ba küldhetők. Ha a Power Query-szerkesztő nem nyílik meg automatikusan, kattintson a kezdőlap **lekérdezések szerkesztése** gombjára. 

Kattintson a Power Query Editor **Átalakítás** menüszalagjára. A **Bármely oszlop** csoportban nyissa meg az **Adattípus:** legördülő menüt, és válassza a **Szöveg parancsot.**

![A Power BI "Navigator" adatforrásképernyőjének képe](../media/tutorials/data-type-drop-down.png)

Amikor értesítést kap az oszloptípus módosításáról, kattintson az **Aktuális cseréje gombra.** Ezután kattintson a Kezdőlap **menüszalag** **On & Apply** vagy Az Alkalmaz **gombra.** 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Hozzon létre egy függvényt az adatok elküldéséhez és a válasz formázásához

Az adatfájl formázásához és az Anomáliadetektor API-ba való elküldéséhez meghívhat egy lekérdezést a fent létrehozott táblában. A Power Query Editor **Kezdőlap** menüszalagján nyissa meg az **Új forrás** legördülő menüt, és kattintson az Üres **lekérdezés parancsra.**

Győződjön meg arról, hogy az új lekérdezés ki van jelölve, majd kattintson a **Speciális szerkesztő gombra.** 

![A Power BI "Speciális szerkesztő" gombjának képe](../media/tutorials/advanced-editor-screen.png)

A Speciális szerkesztőben használja a következő Power Query M kódrészletet az oszlopok kibontásához a táblából, és küldje el az API-nak. Ezt követően a lekérdezés létrehoz egy táblát a JSON-válaszból, és visszaadja azt. Cserélje `apiKey` le a változót az érvényes Anomália-detektor API-kulcsra és `endpoint` a végpontra. Miután beírta a lekérdezést a Speciális szerkesztőbe, kattintson a **Kész**gombra.

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

Az adatlapon lévő lekérdezés `Sheet1` meghívása az **Enter Parameter (Paraméter megadása)** lehetőséget, majd az **Invoke**parancsot. 

![Az "Advanced Editor" gomb képe](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Adatforrások adatvédelme és hitelesítése

> [!NOTE]
> Legyen tisztában a szervezet adatvédelmi és hozzáférési irányelveivel. További információt a [Power BI Desktop adatvédelmi szintjei](https://docs.microsoft.com/power-bi/desktop-privacy-levels) című témakörben talál.

A lekérdezés futtatásakor figyelmeztető üzenet jelenhet meg, mivel külső adatforrást használ. 

![A Power BI által létrehozott figyelmeztetést megjelenítő kép](../media/tutorials/blocked-function.png)

A probléma megoldásához kattintson a **Fájl**gombra, majd **a Beállítások és beállítások gombra.** Ezután kattintson **a Beállítások gombra.** Az **Aktuális fájl**csoportban válassza az **Adatvédelem**lehetőséget, majd hagyja **figyelmen kívül az adatvédelmi szinteket, és növelje a teljesítményt.** 

Emellett előfordulhat, hogy egy üzenet arra kéri, hogy adja meg, hogyan szeretne csatlakozni az API-hoz.

![Hozzáférési hitelesítő adatok megadására irányuló kérelmet megjelenítő kép](../media/tutorials/edit-credentials-message.png)

A probléma megoldásához kattintson a **Hitelesítő adatok szerkesztése gombra** az üzenetben. A párbeszédablak után válassza a **Névtelen** lehetőséget az API-hoz névtelenül való csatlakozáshoz. Ezután kattintson a **Csatlakozás** gombra. 

Ezután kattintson a Módosítások & **Bezárás** gombra a **Kezdőlap** menüszalagon.

## <a name="visualize-the-anomaly-detector-api-response"></a>Az Anomáliadetektor API-válaszának megjelenítése

A Power BI fő képernyőjén kezdje el használni a fent létrehozott lekérdezéseket az adatok megjelenítéséhez. Először jelölje be **a Vonaldiagram lehetőséget** a **képi megjelenítések ben.** Ezután adja hozzá az időbélyeget a meghívott függvényből a vonaldiagram **tengelyéhez.** Kattintson rá a jobb gombbal, és válassza **az Időbélyeg parancsot.** 

![Az Időbélyeg értékére kattintva a jobb gombbal](../media/tutorials/timestamp-right-click.png)

Adja hozzá a következő mezőket az **Invoked függvényből** a diagram **Értékek** mezőjéhez. Az alábbi képernyőkép segítségével készítheti el a diagramot.

    * Érték
    * Felsőmargók
    * Alsómargók
    * Várt értékek

![Az új gyorsmérő képernyő képe](../media/tutorials/chart-settings.png)

A mezők hozzáadása után kattintson a diagramra, és méretezze át az összes adatpont megjelenítéséhez. A diagram az alábbi képernyőképhez hasonlóan fog kinézni:

![Az új gyorsmérő képernyő képe](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Anomáliaadat-pontok megjelenítése

A Power BI ablakának jobb oldalán, a **FIELDS** ablaktábla alatt kattintson a jobb gombbal az **Érték** elemre az **Invoked Function lekérdezés**alatt, majd kattintson az **Új gyorsmérték parancsra.**

![Az új gyorsmérő képernyő képe](../media/tutorials/new-quick-measure.png)

A megjelenő képernyőn válassza a **Szűrt érték** lehetőséget számításként. Állítsa az `Sum of Value` **Alapértéket** a értékre. Ezután `IsAnomaly` húzza a programot a **Meghív függvény** mezőkből a **Szűrő gombra.** Válasszon `True` a **Szűrő** legördülő menüből.

![Az új gyorsmérő képernyő képe](../media/tutorials/new-quick-measure-2.png)

Az **Ok**gombra kattintás `Value for True` után a mezők listájának alján lesz egy mező. Kattintson rá a jobb gombbal, és nevezze át **Anomáliára.** Adja hozzá a diagram **Értékei**hez. Ezután jelölje ki a **Formátum** eszközt, és állítsa az X-tengely típusát **Kategorikus**ra .

![Az új gyorsmérő képernyő képe](../media/tutorials/format-x-axis.png)

Színeket alkalmazhat a diagramra a **Formátum** eszközre és az **Adatszínek**gombra kattintva. A diagramnak a következőre kell hasonlítania:

![Az új gyorsmérő képernyő képe](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Anomáliaészlelés streamelése az Azure Databricks segítségével](anomaly-detection-streaming-databricks.md)
