---
title: Ismerkedés a Bing Speech Recognition API-val a C# asztali kódtár használatával |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech Recognition API használatával a beszélt hangot képes szöveggé alakítani alapszintű Windows alkalmazásokat fejleszthet.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: f79b148558e7881f852ccd57916b0b0f31a98219
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342330"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Gyors útmutató: A Bing beszédfelismerés API c. használata&#35; .NET Windows-es

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Ezen a lapon látható, hogyan hozhat létre egy egyszerű Windows-alkalmazás, amely a Speech Recognition API-t használja a beszélt hangot képes szöveggé alakítani. Az ügyféloldali kódtár használatával lehetővé teszi, hogy a valós idejű streamelési, ami azt jelenti, hogy ha az ügyfélalkalmazás elküldi a szolgáltatás az audio, párhuzamosan és aszinkron módon kap vissza a részleges felismerési eredményeket.

A fejlesztők számára Speech Service használata az alkalmazások futtatása bármely eszközön használhatja a C# asztali tár. A könyvtár használatához telepítse a [NuGet-csomag Microsoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) egy 32 bites platform és a [NuGet-csomag Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) a egy 64 bites platform. Az ügyféloldali kódtár API-referencia, lásd: [Microsoft Speech C# asztali tár](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

A következő szakaszok ismertetik, hogyan kell telepíteni, hozhat létre és futtassa a C# mintaalkalmazást a C# asztali könyvtár használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

A következő minta használatával a Windows 8 +, és a .NET-keretrendszer 4.5-ös + célja [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>A mintaalkalmazás beolvasása

Klónozza a minta a [Speech C# minta asztali tár](https://github.com/microsoft/cognitive-speech-stt-windows) tárház.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a Speech Recognition API és a egy ingyenes próba-előfizetését kulcs lekérése

A beszédfelismerő API a Cognitive Services-(korábban Project Oxford) részét képezi. Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

> [!IMPORTANT]
> * Előfizetési kulcs lekérése. A beszédfelismerés klienskódtárak használatához rendelkeznie kell egy [előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
> * Az előfizetési kulcs használja. A megadott C# asztali mintaalkalmazással illessze be az előfizetési kulcs a szövegmezőbe írja be a minta futtatásakor. További információkért lásd: [futtassa a mintaalkalmazást](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1. lépés: A mintaalkalmazás telepítése

1. Indítsa el a Visual Studio 2015-öt, és válassza ki **fájl** > **nyílt** > **projekt/megoldás**.

2. Keresse meg a mappát, ahová mentette a Speech Recognition API letöltött fájlokat. Válassza ki **Speech** > **Windows**, majd válassza ki a minta-WP mappát.

3. Kattintson duplán a SpeechToText-WPF-Samples.sln nevű Visual Studio 2015-megoldás (.sln) fájl megnyitásához. A megoldás megnyílik a Visual Studióban.

## <a name="step-2-build-the-sample-application"></a>2. lépés: A minta-alkalmazás létrehozása

1. Ha a használni kívánt *szándékával felismerése*, először létre kell regisztrálni a [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). A végpont URL-címét a LUIS-alkalmazás használatával a kulcs értékét állítsa `LuisEndpointUrl` az app.config fájlban a samples/SpeechRecognitionServiceExample mappában. A LUIS alkalmazás a végpont URL-címének további információkért lásd: [tegye közzé alkalmazását](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Cserélje le a `&` karaktert a LUIS végponti URL-címet `&amp;` annak érdekében, hogy az URL-cím helyesen értelmezi az XML-elemzőben.

2. Nyomja le a Ctrl + Shift + B, vagy válassza ki **összeállítása** a menüszalag menü. Válassza ki **megoldás fordítása**.

## <a name="step-3-run-the-sample-application"></a>3. lépés: A mintaalkalmazás futtatása

1. A létrehozás befejezése után nyomja le az F5 billentyűt, vagy válasszon **Start** a menüszalag menü a minta futtatásához.

2. Nyissa meg a **Project Oxford Speech to Text minta** ablak. Illessze be az előfizetési kulcs a **illessze be ide az előfizetési kulcs elindításához** szövegmezőben látható módon. Az előfizetési kulcs a számítógépen vagy laptopon megőrizni, válassza ki **kulcs mentése**. A rendszer az előfizetési kulcs törléséhez válassza ki **kulcs törlése** eltávolítása a számítógépen vagy laptopon.

   ![Speech recognition illessze be a kulcs](../Images/SpeechRecog_paste_key.PNG)

3. A **Speech Recognition forrás**, válassza ki a hat speech forrásokból, amelyek két fő bemeneti kategóriába sorolhatók:

   * A számítógép hangsávról vagy egy csatolt mikrofon használatával beszédfelismerési rögzítése.
   * Hang lejátszása.

   Az egyes kategóriákhoz felismerés három mód:

    * **ShortPhrase mód**: az utterance (kifejezés), legfeljebb 15 másodperc, mennyi ideig. Adatokat küld a kiszolgálón, mert a az ügyfél több részleges és több végső eredményt, több legjobb n választási lehetőség a kap.
    * **LongDictation mód**: az utterance (kifejezés), akár két perc hosszú. Adatokat küld a kiszolgálón, mert a az ügyfél több részleges és több végső eredményt is, ahol azt jelzi, a kiszolgáló mondaton belüli szünetet alapján kap.
    * **Leképezési észlelési**: A kiszolgáló a beszéd, adjon meg további strukturált információt ad vissza. Leképezési észlelési használatához először betanítja a modellt használatával kell [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Ez a mintaalkalmazás minta hangfájlok használata. Ebben a példában a samples/SpeechRecognitionServiceExample mappában letöltött adattár találja a fájlokat. Ezen minta hangfájlok automatikus futtatásra, ha nincsenek más fájlok közül választ kiválasztásakor **Shortphrase mód használata wav-fájl** vagy **wav-fájl használata Longdictation mód** , a beszéd, adjon meg. Jelenleg csak WAV hangformátum használata támogatott.

![Beszéd szöveges felülethez](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>A minták ismertetése

### <a name="recognition-events"></a>Elismerés események

* **Részleges eredményeket események**: Ez az esemény meghívása megtörténik, minden alkalommal, amikor beszédszolgáltatás előrejelzi, előfordulhat, hogy el véleményét, beszéd befejezése előtt is (Ha `MicrophoneRecognitionClient`), vagy fejezze be az adatok küldésének (használatakor `DataRecognitionClient`).
* **Hibaesemények**: meghívva, ha a szolgáltatás hibát észlel.
* **Leképezési események**: nevű ügyfeleken "WithIntent" (csak a ShortPhrase üzemmódban) után a végső felismerés eredménye egy strukturált JSON-leképezés szűrőtulajdonság.
* **Események eredményeként**:
  * A `ShortPhrase` mód, ez az esemény nevezik, és a legjobb n eredményeket ad vissza, és beszéljen befejezése után.
  * A `LongDictation` üzemmód, az eseménykezelő neve többször, ahol a szolgáltatás azonosítja a mondaton belüli szünetet alapján.
  * **A legjobb n lehetőségek mindegyike**, egy megbízhatósági érték és néhány másik formája a felismert szöveget adja vissza. További információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

Eseménykezelők ki már emelni a kód megjegyzések formájában a kódban.

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Microsoft Speech asztali kódtár – referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Java Android rendszeren a Microsoft Speech Recognition API használatának első lépései](GetStartedJavaAndroid.md)
* [IOS-eszközökön a Microsoft Speech Recognition API Objective-C nyelven használatának első lépései](Get-Started-ObjectiveC-iOS.md)
* [A Microsoft Speech Recognition API a JavaScript használatának első lépései](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft Speech Recognition API REST-en keresztül](GetStartedREST.md)
