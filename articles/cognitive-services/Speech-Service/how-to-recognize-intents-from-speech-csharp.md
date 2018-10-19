---
title: 'Oktatóanyag: Szándék felismerése beszédből a C#-hez készült Speech SDK használatával'
titleSuffix: Microsoft Cognitive Services
description: >
  Ezen oktatóanyag segítségével megtanulhatja, hogyan ismerheti fel a szándékot beszédből a C#-hez készült Speech SDK használatával.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: Speech
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 9210aaf2ddfa917ff480f4126c02137f46788dc3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884341"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Oktatóanyag: Szándék felismerése beszédből a C#-hez készült Speech SDK használatával

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

A Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)-ja integrálható a [Language Understanding szolgáltatással (LUIS)](https://www.luis.ai/home) **szándékfelismerés** céljából. A szándék az, amit a felhasználó tenni szeretne: például repülőutat foglalni, megnézni az időjárást vagy telefonhívást indítani. A felhasználó bármilyen kifejezést használhat, amely számára természetes. A gépi tanulással a LUIS az Ön által meghatározott szándékokhoz rendeli a felhasználói kéréseket.

> [!NOTE]
> A LUIS alkalmazás meghatározza a felismerni kívánt szándékokat és entitásokat. Az alkalmazás elkülönül a Speech Service szolgáltatást használó C#-alkalmazástól. Ebben a cikkben az „app” program kifejezés a LUIS-alkalmazásra, az „alkalmazás” kifejezés pedig a C#-kódra utal.

Ebben az oktatóanyagban a Speech SDK használatával fejleszthet ki egy olyan C#-konzolalkalmazást, amely az eszköz mikrofonján keresztül a felhasználó által kimondott szöveg alapján meghatározza a szándékot. A következőket fogja megtanulni:

> [!div class="checklist"]
> * A Speech SDK NuGet-csomagra vonatkozó Visual Studio-projekt létrehozása
> * Beszédkonfiguráció létrehozása és szándékfelismerő beszerzése
> * A LUIS-app modelljének lekérése és a kívánt szándékok hozzáadása
> * A beszédfelismerés nyelvének megadása
> * Beszédfelismerés végrehajtása fájlból
> * Aszinkron, eseményvezérelt folyamatos felismerés használata

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie.

* Egy LUIS-fiók. Létrehozhat egy ingyenes fiókot a [LUIS portal](https://www.luis.ai/home) segítségével.
* Visual Studio 2017 (bármely kiadás).

## <a name="luis-and-speech"></a>A LUIS és a beszéd

A LUIS integrálható a Speech Service szolgáltatással a beszédből való szándékfelismerés céljából. Ehhez nincs szükség Speech Service-előfizetésre, csak a LUIS-ra.

A LUIS kétféle kulcsot kezel: 

|Kulcs típusa|Cél|
|--------|-------|
|létrehozás|LUIS-appok programozott létrehozása és módosítása|
|endpoint |engedélyezi a hozzáférést egy adott LUIS-apphoz|

A jelen oktatóanyaghoz szükséges végpontkulcs a LUIS-kulcs lesz. Ebben az oktatóanyag az otthonautomatizálási LUIS-példaappot használjuk, amelyet az [Előre összeállított otthonautomatizálási alkalmazás használata](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) című rövid útmutató lépéseit követve hozhat létre. Ha saját LUIS-appot hozott létre, használja azt.

A LUIS-app elkészítésekor a rendszer automatikusan létrehoz egy indítókulcsot, hogy szöveges lekérdezésekkel tesztelhesse az appot. Ez a kulcs nem teszi lehetővé a Speech Service szolgáltatás integrációját, és nem használható ebben az oktatóanyagban. Hozzon létre egy új LUIS-erőforrást az Azure-irányítópulton, és rendelje hozzá a LUIS-apphoz. Az oktatóanyaghoz használhatja az ingyenes előfizetési szintet. 

Miután létrehozta a LUIS-erőforrást az Azure-irányítópulton, jelentkezzen be a [LUIS portálra](https://www.luis.ai/home), válassza ki az alkalmazást a Saját alkalmazások oldalon, majd váltson az app Manage (Kezelés) oldalára. Végül kattintson a **Keys and Endpoints** (Kulcsok és végpontok) elemre az oldalsávon.

![A LUIS portál kulcs- és a végpontbeállításai](media/sdk/luis-keys-endpoints-page.png)

A Keys and Endpoint (Kulcsok és végpontok) beállítási oldalán:

1. Görgessen le a Resources and Keys (Erőforrások és kulcsok) szakaszig, majd kattintson az **Assign resource** (Erőforrás hozzárendelése) elemre.
1. Az **Assign a key to your app** (Az API-kulcs hozzáadása az alkalmazáshoz) párbeszédablakban válassza a következőket:

    * Bérlőként válassza a Microsoftot.
    * Az előfizetés neve legyen a használni kívánt LUIS-erőforrást tartalmazó Azure-előfizetés.
    * A kulcs menüpontnál válassza azt a LUIS-erőforrást, amelyet az appal használni szeretne.

Az új előfizetés hamarosan megjelenik a lap alján található táblázatban. A kulcs a mellette lévő ikonra kattintva másolható a vágólapra. (Bármelyik kulcsot használhatja.)

![A LUIS-app előfizetési kulcsai](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Beszédprojekt létrehozása Visual Studióban

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Nyissa meg a `Program.cs` fájlt a Visual Studio-projektben, és cserélje le a fájl elején található `using`-utasításokból álló blokkot az alábbi deklarációkra.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

A megadott `Main()` metóduson belül adja hozzá a következő kódot.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Hozzon létre egy üres `RecognizeIntentAsync()` aszinkron metódust az ábrán látható módon.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Adja hozzá ezt a kódot az új metódust törzséhez.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

A metódusban szereplő helyőrzőket cserélje le a LUIS előfizetési kulcsára, a régióra és az app azonosítójára az alább látható módon.

|Helyőrző|Csere erre|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|A LUIS végpontkulcsa. Amint azt korábban is említettük, ennek az Azure-irányítópultból beszerzett kulcsnak kell lennie, nem pedig indulókulcsnak. A kulcs az app Keys and Endpoints (Kulcsok és végpontok) oldalán (a Manage (Kezelés) területen) található a [LUIS portálon](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|Annak a régiónak a rövid azonosítója, amelyben a LUIS-előfizetése van, például az USA nyugati régiója esetén `westus`. Lásd: [Régiók](regions.md).|
|`YourLanguageUnderstandingAppId`|A LUIS-app azonosítója. Az app Settings (Beállítások) oldalán található a [LUIS portálon](https://www.luis.ai/home).|

Miután elvégezte ezeket a módosításokat, összeállíthatja (Ctrl+Shift+B) és futtathatja (F5) az oktatóalkalmazást. Amikor a rendszer kéri, mondja bele a következő mondatot a számítógép mikrofonjába: „Turn off the lights” (A világítás kikapcsolása). Az eredmény a konzolablakban jelenik meg.

A kód ismertetése a következő szakaszokban szerepel.


## <a name="create-an-intent-recognizer"></a>Szándékfelismerő létrehozása

A beszédbeli szándékok felismerésének első lépése egy beszédkonfiguráció létrehozása a LUIS végpontkulcsából és a régióból. A beszédkonfigurációk használhatók a Speech SDK különféle képességeihez tartozó felismerők létrehozására. A beszédkonfigurációban többféle módon is megadható az előfizetés, amelyet használni szeretne. Jelen esetben a `FromSubscription` metódust használjuk, amely az előfizetés kulcsát és a régiót veszi fel.

> [!NOTE]
> Használja a LUIS-előfizetés kulcsát és régióját, a Speech Service előfizetés most nem használható.

A következő lépés egy szándékfelismerő létrehozása a `new IntentRecognizer(config)` metódus használatával. Mivel a konfigurációban már szerepel a használni kívánt előfizetés, a felismerő létrehozásakor nem kell ismét megadnia az előfizetési kulcsot és a végpontot.

## <a name="import-a-luis-model-and-add-intents"></a>LUIS-modell importálása és szándékok hozzáadása

Most importálja a modellt a LUIS-appból a `LanguageUnderstandingModel.FromAppId()` használatával, majd adja hozzá azokat a LUIS-szándékokat, amelyeket a felismerő `AddIntent()` metódusával fel szeretne ismerni. Ezzel a két lépéssel növelheti a beszédfelismerés pontosságát, ha megadja azokat a szavakat, amelyeket a felhasználó a kérésekben nagy valószínűséggel használni fog. Nem kell az appban szereplő összes szándékot hozzáadnia, ha nem szükséges, hogy az alkalmazás mindegyiket felismerje.

A szándékok hozzáadásához három argumentumra van szükség: a LUIS-modellre (amelyet `model` néven épp most hozott létre), a szándék nevére és a szándék azonosítójára. Az azonosító és a név közötti különbség a következő.

|`AddIntent()` argumentum|Cél|
|--------|-------|
|intentName |A szándék LUIS-appban meghatározott neve. Pontosan egyeznie kell a LUIS-szándék nevével.|
|intentID    |A Speech SDK által felismert szándékhoz rendelt azonosító. Tetszés szerint megadható, nem kell megfelelnie a LUIS-appban megadott szándék nevének. Ha például ugyanaz a kód több szándékot is kezel, használhatja hozzájuk ugyanazt az azonosítót.|

Az otthonautomatizálási LUIS-app két szándékot tartalmaz: egyet az eszközök bekapcsolásához, egyet pedig a kikapcsoláshoz. A felismerő az alábbi sorokkal adható hozzá a felismerőhöz. Cserélje le a `RecognizeIntentAsync()` metódus három `AddIntent` sorát erre a kódra.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

## <a name="start-recognition"></a>Felismerés indítása

A felismerő létrehozása és a szándékok hozzáadása után elkezdődhet a felismerés. A Speech SDK az egyszeri és folyamatos felismerést is támogatja.

|Felismerési mód|Meghívandó metódusok|Eredmény|
|----------------|-----------------|---------|
|Egyszeri|`RecognizeOnceAsync()`|Egyszer kimondott szöveg alapján visszaadja a felismert szándékot (ha van).|
|Folyamatos|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Több alkalommal kimondott szöveg felismerése. Eseményeket bocsát ki (például `IntermediateResultReceived`), ha vannak elérhető eredmények.|

Az oktatóalkalmazás az egyszeri módot használja, ezért a felismerés megkezdéséhez a `RecognizeOnceAsync()` metódust alkalmazza. Az eredmény egy `IntentRecognitionResult` objektum, amely a felismert szándékra vonatkozó információkat tartalmaz. A LUIS JSON-választ az alábbi kifejezés váltja ki:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Az oktatóalkalmazás nem elemzi az eredményként kapott JSON-t, csak megjeleníti azt a konzolalkalmazásban.

![LUIS-felismerés eredményei](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Adja meg a felismerés nyelvét

A LUIS alapértelmezés szerint amerikai angol (`en-us`) nyelven végzi a szándékfelismerést. A területibeállítás-kódnak a beszédkonfiguráció `SpeechRecognitionLanguage` tulajdonságához való hozzárendelésével más nyelveken is végezhet szándékfelismerést. Például német nyelven végezhet szándékfelismerést, ha a felismerő létrehozása előtt az oktatóalkalmazáshoz hozzáadja a `config.SpeechRecognitionLanguage = "de-de";` argumentumot. Lásd: [Támogatott nyelvek](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Folyamatos felismerés fájlból

Az alábbi kód szemlélteti a Speech SDK-val való szándékfelismerés két további képességét. Az első a korábban említett folyamatos felismerés, amelynek esetén a felismerő eseményeket bocsát ki, ha eredmények érhetők el. Ezeket az események aztán feldolgozhatók az Ön által megadott eseménykezelőkkel. Folyamatos felismerés esetén a `RecognizeOnceAsync()` helyett a felismerő `StartContinuousRecognitionAsync()` metódusát hívja meg a felismerés elindításához.

A másik képesség a feldolgozandó beszédet tartalmazó hangfelvétel leolvasása egy WAV-fájlból. Ez magában foglalja egy olyan audiokonfiguráció létrehozását is, amely a szándékfelismerő létrehozásakor használható. A fájlnak egycsatornásnak (mono) kell lennie, 16 kHz-es mintavételi aránnyal.

Az említett funkciók kipróbálásához cserélje le a `RecognizeIntentAsync()` metódus törzsét a következő kódra. 

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Módosítsa a kódot úgy, hogy benne legyen a LUIS végpontkulcsa, a régió és az app azonosítója, illetve hogy az előzőekhez hasonlóan hozzáadhassa az otthonautomatizálási szándékokat. Írja át a `whatstheweatherlike.wav` elemet a saját hangfájljának nevére. Ha ezzel megvan, állítsa össze és futtassa az alkalmazást.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Nézze meg, hogy a cikkben szereplő kód szerepel-e a samples/csharp/sharedcontent/console mappában.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszéd felismerése](how-to-recognize-speech-csharp.md)
