---
title: 'Oktatóanyag: Beszédfelismerési szándék felismerése a Speech SDK-valC#'
titleSuffix: Azure Cognitive Services
description: Ezen oktatóanyag segítségével megtanulhatja, hogyan ismerheti fel a szándékot beszédből a C#-hez készült Speech SDK használatával.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cf5bf3dfd7b6a408179bb267156433168e562a8e
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326831"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Oktatóanyag: A beszédfelismerési szándékok felismerése a Speech SDK használatávalC#

A Cognitive Services [SPEECH SDK](speech-sdk.md) a [Language Understanding szolgáltatással (Luis)](https://www.luis.ai/home) integrálódik a **szándék-felismerés**biztosításához. A szándék az, amit a felhasználó tenni szeretne: például repülőutat foglalni, megnézni az időjárást vagy telefonhívást indítani. A felhasználó bármilyen kifejezést használhat, amely számára természetes. A Machine learning használatával a LUIS leképezi a felhasználói kéréseket a definiált szándékokra.

> [!NOTE]
> A LUIS alkalmazás meghatározza a felismerni kívánt szándékokat és entitásokat. Az alkalmazás elkülönül a Speech Service szolgáltatást használó C#-alkalmazástól. Ebben a cikkben az „app” program kifejezés a LUIS-alkalmazásra, az „alkalmazás” kifejezés pedig a C#-kódra utal.

Ebben az oktatóanyagban a Speech SDK használatával fejleszthet ki egy olyan C#-konzolalkalmazást, amely az eszköz mikrofonján keresztül a felhasználó által kimondott szöveg alapján meghatározza a szándékot. A következőket fogja megtanulni:

> [!div class="checklist"]
> * A Speech SDK NuGet-csomagra vonatkozó Visual Studio-projekt létrehozása
> * Beszédfelismerési konfiguráció létrehozása és a szándék felismerésének beolvasása
> * A LUIS-app modelljének lekérése és a kívánt szándékok hozzáadása
> * A beszédfelismerés nyelvének megadása
> * Beszédfelismerés végrehajtása fájlból
> * Aszinkron, eseményvezérelt folyamatos felismerés használata

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

* Egy LUIS-fiók. Létrehozhat egy ingyenes fiókot a [LUIS portal](https://www.luis.ai/home) segítségével.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (bármely kiadás).

## <a name="luis-and-speech"></a>A LUIS és a beszéd

A LUIS integrálva van a Speech Services szolgáltatással, hogy felismerje a beszédfelismerési szándékokat. Nincs szüksége a Speech Services-előfizetésre, csak LUIS-ra.

A LUIS kétféle kulcsot kezel:

|Kulcstípus|Cél|
|--------|-------|
|Szerzői műveletek|Lehetővé teszi a LUIS-alkalmazások programozott módon történő létrehozását és módosítását|
|Végpont |Engedélyezi a hozzáférést egy adott LUIS-alkalmazáshoz|

Ebben az oktatóanyagban a végponti kulcs típusát kell megadnia. Az oktatóanyag a példa Home Automation LUIS alkalmazást használja, amelyet az [előre elkészített Home Automation-alkalmazás használata](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) című rövid útmutatóban hozhat létre. Ha saját LUIS-alkalmazást hozott létre, azt használhatja helyette.

LUIS-alkalmazás létrehozásakor a LUIS automatikusan létrehoz egy alapszintű kulcsot, amellyel szöveges lekérdezések használatával tesztelheti az alkalmazást. Ez a kulcs nem engedélyezi a beszédfelismerési szolgáltatások integrációját, és nem fog működni ezzel az Oktatóanyaggal. Hozzon létre egy LUIS-erőforrást az Azure irányítópulton, és rendelje hozzá a LUIS alkalmazáshoz. Az oktatóanyaghoz használhatja az ingyenes előfizetési szintet.

Miután létrehozta a LUIS-erőforrást az Azure-irányítópulton, jelentkezzen be a [Luis portálra](https://www.luis.ai/home), válassza ki az alkalmazást a **saját alkalmazások** lapon, majd váltson az alkalmazás **kezelése** lapra. Végül válassza a **kulcsok és végpontok** lehetőséget az oldalsávon.

![A LUIS portál kulcs- és a végpontbeállításai](media/sdk/luis-keys-endpoints-page.png)

A **kulcsok és végpont beállításai** lapon:

1. Görgessen le az **erőforrások és kulcsok** szakaszhoz, majd válassza az **erőforrás kiosztása**elemet.
1. A **kulcs kiosztása az alkalmazáshoz** párbeszédpanelen végezze el a következő módosításokat:

   * A **bérlő**területen válassza a **Microsoft**lehetőséget.
   * Az **előfizetés neve**területen válassza ki azt az Azure-előfizetést, amely a használni kívánt Luis-erőforrást tartalmazza.
   * A **kulcs**alatt válassza ki az alkalmazással használni kívánt Luis-erőforrást.

   Az új előfizetés hamarosan megjelenik a lap alján található táblázatban. 

1. Kattintson a kulcs melletti ikonra a vágólapra másoláshoz. (Bármelyik kulcsot használhatja.)

![A LUIS-app előfizetési kulcsai](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Beszédprojekt létrehozása Visual Studióban

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Ezután adja hozzá a projekthez egy kódot.

1. A **megoldáskezelő**nyissa meg a **program.cs**fájlt.

1. A következő deklarációkkal cserélje le a `using` utasítások blokkját a fájl elejére:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. A megadott `Main()` metóduson belül adja hozzá a következő kódot:

   ```csharp
   RecognizeIntentAsync().Wait();
   Console.WriteLine("Please press Enter to continue.");
   Console.ReadLine();
   ```

1. Hozzon létre egy üres aszinkron módszert @no__t – 0, ahogy az itt látható:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Az új metódus törzsében adja hozzá a következő kódot:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. A metódusban szereplő helyőrzőket cserélje le a LUIS előfizetési kulcsára, a régióra és az app azonosítójára az alább látható módon.

   |Helyőrző|Csere erre|
   |-----------|------------|
   |`YourLanguageUnderstandingSubscriptionKey`|A LUIS végpontkulcsa. Ezt az elemet újra be kell szereznie az Azure-irányítópultról, nem pedig "kezdő kulcs". A [Luis portálon](https://www.luis.ai/home)megtalálhatja az alkalmazás **kulcsai és végpontok** lapján (a **kezelés**alatt).|
   |`YourLanguageUnderstandingServiceRegion`|Annak a régiónak a rövid azonosítója, amelyben a LUIS-előfizetése van, például az USA nyugati régiója esetén `westus`. Lásd: [Régiók](regions.md).|
   |`YourLanguageUnderstandingAppId`|A LUIS-app azonosítója. A [Luis portálon](https://www.luis.ai/home)megtalálhatja az alkalmazás **Beállítások** lapján.|

Ezekkel a módosításokkal létrehozhatja (**vezérlő + SHIFT + B**) és futtathatja (**F5**) az oktatóanyag-alkalmazást. Ha a rendszer kéri, próbálja meg "a fények kikapcsolása" kifejezést a számítógép mikrofonjában. Az alkalmazás megjeleníti az eredményt a konzol ablakban.

A kód ismertetése a következő szakaszokban szerepel.

## <a name="create-an-intent-recognizer"></a>Szándékfelismerő létrehozása

Először létre kell hoznia egy beszédfelismerési konfigurációt a LUIS Endpoint kulcsból és régióból. A beszédfelismerési konfigurációk segítségével felismerőket hozhat létre a Speech SDK különböző képességeihez. A beszédfelismerési konfiguráció több módon is megadhatja a használni kívánt előfizetést. Itt a `FromSubscription` értéket használjuk, amely az előfizetési kulcsot és a régiót veszi igénybe.

> [!NOTE]
> Használja a LUIS-előfizetés kulcsát és régióját, nem pedig a Speech Services-előfizetést.

A következő lépés egy szándékfelismerő létrehozása a `new IntentRecognizer(config)` metódus használatával. Mivel a konfiguráció már tudja, hogy melyik előfizetést szeretné használni, nem kell újra megadnia az előfizetési kulcsot és a végpontot a felismerő létrehozásakor.

## <a name="import-a-luis-model-and-add-intents"></a>LUIS-modell importálása és szándékok hozzáadása

Most importálja a modellt a LUIS-appból a `LanguageUnderstandingModel.FromAppId()` használatával, majd adja hozzá azokat a LUIS-szándékokat, amelyeket a felismerő `AddIntent()` metódusával fel szeretne ismerni. Ezzel a két lépéssel növelheti a beszédfelismerés pontosságát, ha megadja azokat a szavakat, amelyeket a felhasználó a kérésekben nagy valószínűséggel használni fog. Nem kell hozzáadnia az összes alkalmazást, ha az alkalmazásban nem kell felismernie őket.

A leképezések hozzáadásához három argumentumot kell megadnia: a LUIS modellt (amelyet létrehoztak, és neve `model`), a cél neve és a szándék azonosítója. Az azonosító és a név közötti különbség a következő.

|`AddIntent()` @ no__t-1argument|Cél|
|--------|-------|
|intentName|A szándék LUIS-appban meghatározott neve. Ennek az értéknek pontosan egyeznie kell a LUIS-cél nevével.|
|intentID|A Speech SDK által felismert szándékhoz rendelt azonosító. Ez az érték lehet bármilyen hasonló; nem kell megegyeznie a cél nevével a LUIS alkalmazásban meghatározottak szerint. Ha például ugyanaz a kód több szándékot is kezel, használhatja hozzájuk ugyanazt az azonosítót.|

A Home Automation LUIS alkalmazásnak két célja van: egyet az eszköz bekapcsolásához, egy másikat pedig egy eszköz kikapcsolásához. A felismerő az alábbi sorokkal adható hozzá a felismerőhöz. Cserélje le a `RecognizeIntentAsync()` metódus három `AddIntent` sorát erre a kódra.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Az egyéni leképezések hozzáadása helyett használhatja a `AddAllIntents` metódust is, amellyel a modellben lévő összes leképezést hozzáadhatja a felismerőhöz.

## <a name="start-recognition"></a>Felismerés indítása

A felismerő létrehozása és a szándékok hozzáadása után elkezdődhet a felismerés. A Speech SDK az egyszeri és folyamatos felismerést is támogatja.

|Felismerési mód|Meghívandó metódusok|Eredmény|
|----------------|-----------------|---------|
|Egyszeri|`RecognizeOnceAsync()`|Egyszer kimondott szöveg alapján visszaadja a felismert szándékot (ha van).|
|Folytonos|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Több hosszúságú kimondott szöveg felismerése; eseményeket bocsát ki (például `IntermediateResultReceived`), ha az eredmények elérhetők.|

Az oktatóalkalmazás az egyszeri módot használja, ezért a felismerés megkezdéséhez a `RecognizeOnceAsync()` metódust alkalmazza. Az eredmény egy `IntentRecognitionResult` objektum, amely a felismert szándékra vonatkozó információkat tartalmaz. A LUIS JSON-választ kinyerheti a következő kifejezés használatával:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Az oktatóanyag-alkalmazás nem elemzi a JSON-eredményt. Csak a JSON-szöveget jeleníti meg a konzol ablakban.

![Egyetlen LUIS-felismerés eredményei](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Adja meg a felismerés nyelvét

A LUIS alapértelmezés szerint amerikai angol (`en-us`) nyelven végzi a szándékfelismerést. A területibeállítás-kódnak a beszédkonfiguráció `SpeechRecognitionLanguage` tulajdonságához való hozzárendelésével más nyelveken is végezhet szándékfelismerést. Például német nyelven végezhet szándékfelismerést, ha a felismerő létrehozása előtt az oktatóalkalmazáshoz hozzáadja a `config.SpeechRecognitionLanguage = "de-de";` argumentumot. További információ: [támogatott nyelvek](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Folyamatos felismerés fájlból

Az alábbi kód szemlélteti a Speech SDK-val való szándékfelismerés két további képességét. Az első a korábban említett folyamatos felismerés, amelynek esetén a felismerő eseményeket bocsát ki, ha eredmények érhetők el. Ezeket az események aztán feldolgozhatók az Ön által megadott eseménykezelőkkel. A folyamatos felismeréssel a felismerő `StartContinuousRecognitionAsync()` metódusának meghívásával elindíthatja az elismerést a `RecognizeOnceAsync()` helyett.

A másik képesség a feldolgozandó beszédet tartalmazó hangfelvétel leolvasása egy WAV-fájlból. A megvalósítás magában foglalja egy hangkonfiguráció létrehozását, amelyet a rendszer a szándék-felismerő létrehozásakor használhat. A fájlnak egycsatornásnak (mono) kell lennie, 16 kHz-es mintavételi aránnyal.

A funkciók kipróbálásához törölje vagy véleményezze a `RecognizeIntentAsync()` metódus törzsét, és adja hozzá a következő kódot a helyére.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Módosítsa a kódot úgy, hogy benne legyen a LUIS végpontkulcsa, a régió és az app azonosítója, illetve hogy az előzőekhez hasonlóan hozzáadhassa az otthonautomatizálási szándékokat. Módosítsa @no__t – 0 értéket a rögzített hangfájl nevére. Ezután hozza létre, másolja a hangfájlt a Build könyvtárba, és futtassa az alkalmazást.

Ha például a "fények kikapcsolása", a pause, majd a "fények bekapcsolása" lehetőséget választja a rögzített hangfájlban, a konzol kimenete az alábbihoz hasonló lehet:

![A LUIS hangfájl-felismerési eredményei](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg a kódot ebből a cikkből a **Samples/csharp/sharedcontent/Console** mappában.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszéd felismerése](quickstart-csharp-dotnetcore-windows.md)
