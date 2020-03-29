---
title: 'A beszédbeszédből származó szándékok felismerése a Beszéd SDK C használatával #'
titleSuffix: Azure Cognitive Services
description: Ebben az útmutatóban megtudhatja, hogyan ismerheti fel a beszédből származó szándékokat a C# beszédstióc sdk használatával.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5d3c77c307739f9014010a592aa496a1cc83b333
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77120039"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>A beszédbeszédből származó szándékok felismerése a C beszédsti-sdk használatával #

A Cognitive Services [speech SDK](speech-sdk.md) integrálja a [language understanding szolgáltatás (LUIS)](https://www.luis.ai/home) a **szándék felismerése.** A szándék az, amit a felhasználó tenni szeretne: például repülőutat foglalni, megnézni az időjárást vagy telefonhívást indítani. A felhasználó bármilyen kifejezést használhat, amely számára természetes. A gépi tanulás használatával a LUIS leképezi a felhasználói kérelmeket a megadott szándékokhoz.

> [!NOTE]
> A LUIS alkalmazás meghatározza a felismerni kívánt szándékokat és entitásokat. Az alkalmazás elkülönül a Speech Service szolgáltatást használó C#-alkalmazástól. Ebben a cikkben az „app” program kifejezés a LUIS-alkalmazásra, az „alkalmazás” kifejezés pedig a C#-kódra utal.

Ebben az útmutatóban a beszédfelismerési SDK használatával fejleszthet egy C# konzolalkalmazást, amely az eszköz mikrofonján keresztül a felhasználói utterances-ből származó leképezéseket eredményez. A következőket fogja megtanulni:

> [!div class="checklist"]
>
> - A Speech SDK NuGet-csomagra vonatkozó Visual Studio-projekt létrehozása
> - Beszédkonfiguráció létrehozása és szándékfelismerő beolvasása
> - A LUIS-app modelljének lekérése és a kívánt szándékok hozzáadása
> - A beszédfelismerés nyelvének megadása
> - Beszédfelismerés végrehajtása fájlból
> - Aszinkron, eseményvezérelt folyamatos felismerés használata

## <a name="prerequisites"></a>Előfeltételek

Az útmutató megkezdése előtt győződjön meg arról, hogy a következő elemeket kapta:

- Egy LUIS-fiók. Létrehozhat egy ingyenes fiókot a [LUIS portal](https://www.luis.ai/home) segítségével.
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (bármely kiadás).

## <a name="luis-and-speech"></a>A LUIS és a beszéd

A LUIS integrálható a Speech Service szolgáltatással a beszédből való szándékfelismerés céljából. Ehhez nincs szükség Speech Service-előfizetésre, csak a LUIS-ra.

A LUIS háromféle kulcsot használ:

| Kulcs típusa  | Cél                                               |
| --------- | ----------------------------------------------------- |
| Tartalomkészítés | Lehetővé teszi a LUIS-alkalmazások programozási programozással történő létrehozását és módosítását |
| Kezdő   | Lehetővé teszi a LUIS-alkalmazás tesztelését csak szöveggel   |
| Végpont  | Engedélyezi a hozzáférést egy adott LUIS-alkalmazáshoz            |

Ehhez az útmutatóhoz szükség van a végpontkulcs-típusra. Ez az útmutató a példát használja a Home Automation LUIS alkalmazás, amely hozhat létre az előre [összeállított otthoni automatizálási alkalmazás](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) rövid útmutató használata. Ha saját LUIS-alkalmazást hozott létre, használhatja helyette.

Luis-alkalmazás létrehozásakor a LUIS automatikusan létrehoz egy kezdőkulcsot, így szöveges lekérdezések használatával tesztelheti az alkalmazást. Ez a kulcs nem engedélyezi a beszédszolgáltatás integrációját, és nem működik ezzel az útmutatóval. Hozzon létre egy LUIS-erőforrást az Azure-irányítópulton, és rendelje hozzá a LUIS alkalmazáshoz. Ehhez az útmutatóhoz használhatja az ingyenes előfizetési szintet.

Miután létrehozta a LUIS-erőforrást az Azure-irányítópulton, jelentkezzen be a [LUIS-portálra,](https://www.luis.ai/home)válassza ki az alkalmazást a **Saját alkalmazások** lapon, majd váltson át az alkalmazás **Kezelése** lapjára. Végül válassza a **Kulcsok és végpontok elemet** az oldalsávon.

![A LUIS portál kulcs- és a végpontbeállításai](media/sdk/luis-keys-endpoints-page.png)

A **Kulcsok és végpontbeállításai** lapon:

1. Görgessen le az **Erőforrások és kulcsok** szakaszhoz, és válassza az Erőforrás **hozzárendelése lehetőséget.**
1. A **Kulcs hozzárendelése az alkalmazáshoz** párbeszédpanelen hajtsa végre a következő módosításokat:

   - A **Bérlő csoportban**válassza a **Microsoft**lehetőséget.
   - Az **Előfizetés neve csoportban**válassza ki a használni kívánt LUIS-erőforrást tartalmazó Azure-előfizetést.
   - A **Kulcs csoportban**válassza ki az alkalmazással használni kívánt LUIS-erőforrást.

   Az új előfizetés hamarosan megjelenik a lap alján található táblázatban.

1. A billentyű melletti ikonra jelölve másolja azt a vágólapra. (Bármelyik kulcsot használhatja.)

![A LUIS-app előfizetési kulcsai](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Beszédprojekt létrehozása Visual Studióban

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Ezután kódot ad hozzá a projekthez.

1. A **Megoldáskezelőben**nyissa meg a fájlt **Program.cs.**

1. Cserélje le `using` az akta elején lévő kimutatásblokkot a következő nyilatkozatokra:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Cserélje ki `Main()` a mellékelt módszert a következő aszinkron megfelelőre:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Hozzon létre egy üres `RecognizeIntentAsync()`aszinkron metódust, ahogy az itt látható:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Az új módszer törzsében adja hozzá ezt a kódot:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. A metódusban szereplő helyőrzőket cserélje le a LUIS előfizetési kulcsára, a régióra és az app azonosítójára az alább látható módon.

   | Helyőrző | Csere erre |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | A LUIS végpontkulcsa. Ismét be kell szereznie ezt az elemet az Azure-irányítópultról, nem pedig egy "kezdő kulcsot". Az alkalmazás **kulcsai és végpontjai** lapján **(a Kezelés**területen) megtalálhatja a [LUIS portálon.](https://www.luis.ai/home) |
   | `YourLanguageUnderstandingServiceRegion` | Annak a régiónak a rövid azonosítója, amelyben a LUIS-előfizetése van, például az USA nyugati régiója esetén `westus`. Lásd: [Régiók](regions.md). |
   | `YourLanguageUnderstandingAppId` | A LUIS-app azonosítója. Az alkalmazás **Beállítások** lapján, a [LUIS portálon](https://www.luis.ai/home)találhatja meg. |

Ezekkel a módosításokkal létrehozhatja (**Control+Shift+B**) és futtathatja **(F5**) az alkalmazást. Amikor a rendszer kéri, próbálja meg a számítógép mikrofonjába bekapcsolni a "Kapcsolja ki a villanyt" kifejezést. Az alkalmazás megjeleníti az eredményt a konzol ablakban.

A kód ismertetése a következő szakaszokban szerepel.

## <a name="create-an-intent-recognizer"></a>Szándékfelismerő létrehozása

Először létre kell hoznia egy beszédfelismerési konfigurációt a LUIS-végpontkulcsból és régióból. A beszédfelismerési konfigurációk segítségével a beszédska k. A beszédfelismerési konfiguráció nak többféle módja van a használni kívánt előfizetés megadására; itt használjuk, `FromSubscription`amely az előfizetési kulcsot és a régiót veszi igénybe.

> [!NOTE]
> A LUIS-előfizetés kulcsát és régióját használja, ne a Beszédszolgáltatás-előfizetést.

A következő lépés egy szándékfelismerő létrehozása a `new IntentRecognizer(config)` metódus használatával. Mivel a konfiguráció már tudja, hogy melyik előfizetést kell használnia, nem kell újra megadnia az előfizetési kulcsot és a végpontot a felismerő létrehozásakor.

## <a name="import-a-luis-model-and-add-intents"></a>LUIS-modell importálása és szándékok hozzáadása

Most importálja a modellt a LUIS-appból a `LanguageUnderstandingModel.FromAppId()` használatával, majd adja hozzá azokat a LUIS-szándékokat, amelyeket a felismerő `AddIntent()` metódusával fel szeretne ismerni. Ezzel a két lépéssel növelheti a beszédfelismerés pontosságát, ha megadja azokat a szavakat, amelyeket a felhasználó a kérésekben nagy valószínűséggel használni fog. Nem kell hozzáadnia az alkalmazás összes szándékát, ha nem kell felismerni őket az alkalmazásban.

Leképezések hozzáadásához meg kell adnia három argumentumot: a `model`LUIS-modell (amely et létrehoztak és elnevezett ), a szándék nevét és egy szándékazonosítót. Az azonosító és a név közötti különbség a következő.

| `AddIntent()`&nbsp;Argumentum | Cél |
| --------------------------- | ------- |
| `intentName` | A szándék LUIS-appban meghatározott neve. Ennek az értéknek pontosan meg kell egyeznie a LUIS-leképezés nevével. |
| `intentID` | A Speech SDK által felismert szándékhoz rendelt azonosító. Ez az érték lehet, amit akarsz; nem kell, hogy megfeleljen a leképezés neve a LUIS-alkalmazásban meghatározott. Ha például ugyanaz a kód több szándékot is kezel, használhatja hozzájuk ugyanazt az azonosítót. |

Az Otthoni automatizálás LUIS alkalmazásnak két leképezése van: az egyik az eszköz bekapcsolásához, a másik pedig az eszköz kikapcsolásához. A felismerő az alábbi sorokkal adható hozzá a felismerőhöz. Cserélje le a `RecognizeIntentAsync()` metódus három `AddIntent` sorát erre a kódra.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Ahelyett, hogy egyedi leképezések `AddAllIntents` hozzáadása, a módszer segítségével a modell összes leképezése a felismerő.

## <a name="start-recognition"></a>Felismerés indítása

A felismerő létrehozása és a szándékok hozzáadása után elkezdődhet a felismerés. A Speech SDK az egyszeri és folyamatos felismerést is támogatja.

| Felismerési mód | Meghívandó metódusok | Eredmény |
| ---------------- | --------------- | ------ |
| Egyszeri | `RecognizeOnceAsync()` | Egyszer kimondott szöveg alapján visszaadja a felismert szándékot (ha van). |
| Folyamatos | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Több kimondott szövegfelismerést ismer fel; eseményeket (például) `IntermediateResultReceived`bocsát ki, amikor az eredmények rendelkezésre állnak. |

Az alkalmazás egylövéses módot `RecognizeOnceAsync()` használ, ezért a felismerés megkezdéséhez kéri a hívásokat. Az eredmény egy `IntentRecognitionResult` objektum, amely a felismert szándékra vonatkozó információkat tartalmaz. A LUIS JSON-választ a következő kifejezéssel bonthatja ki:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Az alkalmazás nem elemzi a JSON-eredményt. Csak a JSON-szöveget jeleníti meg a konzolablakban.

![Egyetlen LUIS-felismerési eredmények](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Adja meg a felismerés nyelvét

A LUIS alapértelmezés szerint amerikai angol (`en-us`) nyelven végzi a szándékfelismerést. A területibeállítás-kódnak a beszédkonfiguráció `SpeechRecognitionLanguage` tulajdonságához való hozzárendelésével más nyelveken is végezhet szándékfelismerést. Például adja `config.SpeechRecognitionLanguage = "de-de";` hozzá az alkalmazásunkba, mielőtt létrehozza a felismerő felismerni szándékok német nyelven. További információ: [LUIS language support](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Folyamatos felismerés fájlból

Az alábbi kód szemlélteti a Speech SDK-val való szándékfelismerés két további képességét. Az első a korábban említett folyamatos felismerés, amelynek esetén a felismerő eseményeket bocsát ki, ha eredmények érhetők el. Ezeket az események aztán feldolgozhatók az Ön által megadott eseménykezelőkkel. Folyamatos felismerés esetén a felismerés elindításához hívja meg a felismerő metódusát `StartContinuousRecognitionAsync()` a helyett. `RecognizeOnceAsync()`

A másik képesség a feldolgozandó beszédet tartalmazó hangfelvétel leolvasása egy WAV-fájlból. A megvalósítás magában foglalja egy hangkonfiguráció létrehozását, amely a szándékfelismerő létrehozásakor használható. A fájlnak egycsatornásnak (mono) kell lennie, 16 kHz-es mintavételi aránnyal.

Ezeket a funkciókat ki szeretné próbálni, `RecognizeIntentAsync()` törölje vagy megjegyzéssel fűzze hozzá a metódus törzsét, és adja hozzá a helyére a következő kódot.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Módosítsa a kódot úgy, hogy benne legyen a LUIS végpontkulcsa, a régió és az app azonosítója, illetve hogy az előzőekhez hasonlóan hozzáadhassa az otthonautomatizálási szándékokat. Váltás `whatstheweatherlike.wav` a felvett hangfájl nevére. Ezután építse nát, másolja a hangfájlt a buildkönyvtárba, és futtassa az alkalmazást.

Ha például a "Kapcsolja ki a lámpákat" kimondja, hogy "Kapcsolja ki a lámpákat", majd a rögzített hangfájlban a "Fények bekapcsolása" kimondva, a konzol kimenete a következőhöz hasonló lehet:

![Audiofájl LUIS-felismerési eredményei](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg a cikkben szereplő kódot a **samples/csharp/sharedcontent/console** mappában.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Beszéd felismerése mikrofonból](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
