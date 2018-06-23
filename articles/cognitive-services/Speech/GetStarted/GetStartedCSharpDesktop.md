---
title: A C# asztali könyvtár használatával a Microsoft beszéd felismerés API használatába |} Microsoft Docs
description: A Microsoft beszéd felismerés API használatával szóbeli hang szöveggé alakítani alapvető Windows-alkalmazások fejlesztéséhez.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347447"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>Első lépések a beszéd-felismerési API C&#35; a .NET-hez Windows rendszeren

Ezen a lapon látható, hogyan fejleszthet egy alapvető Windows-alkalmazás, amely a beszédfelismerés felismerés API használatával szóbeli hang szöveggé alakítani. Az ügyféloldali kódtár használata lehetővé teszi a valós idejű streamelési, ami azt jelenti, ha az ügyfélalkalmazást küld a szolgáltatás hang, hogy egyidejűleg és aszinkron módon kap részleges felismerési eredmények vissza.

A fejlesztők számára beszéd szolgáltatás használatát bármely olyan eszközről, a futó alkalmazásokból használhatja a C# asztali könyvtárban. A könyvtár használatához telepítenie a [NuGet-csomag Microsoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) egy 32 bites platform és a [NuGet-csomag Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) a egy 64 bites platformon. Az ügyféloldali kódtár API-referencia, lásd: [Microsoft beszéd C# asztali tár](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Az alábbi szakaszok azt ismertetik, hogyan telepítse, felépítéséhez, és futtassa a C# mintaalkalmazást a C# asztali könyvtár használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

A következő minta Windows 8 + és kifejlesztett .NET-keretrendszer 4.5-ös + használatával [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>A mintaalkalmazás beolvasása

A mintát klónozza a [beszéd C# asztali tár minta](https://github.com/microsoft/cognitive-speech-stt-windows) tárház.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>A beszédfelismerés felismerés API előfizetni, és egy ingyenes próba-előfizetés kulcs beszerzése

A Diktálásfelismerési API (korábban projekt Oxford) kognitív szolgáltatások részét képezi. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

> [!IMPORTANT]
> * Egy előfizetés kulcs beszerzése. A beszédfelismerés klienskódtárak segítségével használatához rendelkeznie kell egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
> * Az előfizetés-kulcsot használ. A megadott C# asztali mintaalkalmazást illessze be az Előfizetés kulcs szöveges minta futtatásakor. További információkért lásd: [futtassa a mintaalkalmazást](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1. lépés: A mintaalkalmazás telepítése

1. Indítsa el a Visual Studio 2015-öt, és válassza **fájl** > **nyitott** > **projekt/megoldás**.

2. Keresse meg a mappát, ahová menteni szeretné a beszédfelismerés felismerés API letöltött fájlokat. Válassza ki **beszéd** > **Windows**, majd válassza ki a minta-WP mappát.

3. Kattintson duplán a Visual Studio 2015 megoldás (.sln) fájl SpeechToText-WPF-Samples.sln nevű megnyitásához. A megoldás a Visual Studio nyílik meg.

## <a name="step-2-build-the-sample-application"></a>2. lépés: A minta-alkalmazás létrehozása

1. Ha a használni kívánt *biztonsági mentés felismerés*, először be kell jelentkeznie a a [nyelvi ismertetése intelligens szolgáltatás (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). A végponti URL-cím az LUIS alkalmazás segítségével a kulcs értékét állítsa `LuisEndpointUrl` az app.config fájlban a minták/SpeechRecognitionServiceExample mappában. A LUIS alkalmazás a végpont URL-címének további információkért lásd: [az alkalmazás közzététele](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Cserélje le a `&` karaktert a LUIS végpont URL-cím elé `&amp;` annak érdekében, hogy az URL-címet az XML-elemző megfelelően értelmezi.

2. Nyomja le a Ctrl + Shift + B, vagy válasszon **Build** a menüszalag menü. Válassza ki **megoldás fordítása**.

## <a name="step-3-run-the-sample-application"></a>3. lépés: A mintaalkalmazás futtatása

1. A létrehozás befejezése után nyomja le az F5 billentyűt, vagy válasszon **Start** a menüszalag menü a minta futtatásához.

2. Lépjen a **projekt Oxford beszédfelismerés szöveges mintavételi** ablak. Illessze be az Előfizetés kulcs azokat a **illessze be ide az Előfizetés kulcs elindításához** szövegmezőben látható módon. Az Előfizetés kulcs a számítógépen vagy hordozható megőrizni, válassza ki **kulcs mentése**. A rendszer az Előfizetés-kulcs törléséhez válassza ki **kulcs törlése** eltávolítása a PC- vagy hordozható.

   ![Beszéd felismerés illessze be a kulcs](../Images/SpeechRecog_paste_key.PNG)

3. A **Beszéd felismerés forrás**, válassza ki a hat beszéd forrásokból, amely két fő bemeneti kategóriába sorolhatók:

   * A számítógép mikrofon vagy egy csatlakoztatott mikrofon segítségével rögzítheti a beszédfelismerés.
   * Hangfájl lejátszása.

   Minden egyes kategória három felismerés módot:

    * **ShortPhrase mód**: egy utterance legfeljebb 15 másodperc hosszú. Mivel adatokat küld a kiszolgáló, az ügyfél megkapja a több részleges eredményt és egy végeredményt több n-legjobb választást.
    * **LongDictation mód**: egy utterance akár két perc hosszú. Mivel adatokat küld a kiszolgáló, az ügyfél megkapja a több részleges eredményt, és több végső eredmény azt jelzi, ha a kiszolgáló mondat szünet alapján.
    * **Leképezési észlelési**: A kiszolgáló a beszédfelismerés bemeneti további strukturált információt ad vissza. Leképezési észlelési használatához először használatával a modell betanításához szüksége [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Minta hangfájlok használata a mintaalkalmazáshoz. A tárházban letöltött ebben a példában a minták/SpeechRecognitionServiceExample mappában találja a fájlokat. A minta hangfájlok automatikus futtatása, ha nincs más fájlok közül választ, amikor kiválaszt **Shortphrase mód használata wav-fájl** vagy **Longdictation mód használata wav-fájl** , a beszéd adjon meg. Jelenleg csak WAV audioformátum használata támogatott.

![Beszédfelismerés szöveg felület](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Minták alapján

### <a name="recognition-events"></a>Felismerés események

* **Részleges eredmények események**: Ez az esemény minden alkalommal, amikor a beszédfelismerés szolgáltatás előrejelzi mi, előfordulhat, hogy kell arról, még mielőtt beszéd menüelemnek (használatakor `MicrophoneRecognitionClient`) vagy a Befejezés adatküldés (használatakor `DataRecognitionClient`).
* **Hibaesemények**: hívható meg, ha a szolgáltatás hibát észlel.
* **Leképezési események**: nevű ügyfeleken "WithIntent" (csak a ShortPhrase üzemmódban) után utolsó felismerési eredmények strukturált JSON-leképezés van értelmezni.
* **Események eredménye**:
  * A `ShortPhrase` módot, ez az esemény neve és az n-legjobb eredményt ad vissza, és beszéljen befejezése után.
  * A `LongDictation` üzemmód, az eseménykezelő neve többször, ahol a szolgáltatás azonosítja a mondatok szünet alapján.
  * **Az egyes az n-legjobb lehetőségeket**, abban, hogy az érték, és néhány más űrlapok a felismert szöveg adott vissza. További információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

Az eseménykezelők ki már emelni a kód megjegyzések formájában a kódban.

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Microsoft Speech asztali kódtár – dokumentáció](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Ismerkedés a Microsoft beszéd felismerés API-nak Java Android rendszeren](GetStartedJavaAndroid.md)
* [IOS rendszerű eszközökön a Microsoft beszéd-felismerési API Objective-C az első lépései](Get-Started-ObjectiveC-iOS.md)
* [A Microsoft beszéd felismerés API a JavaScript beolvasása használatába](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft beszéd felismerés API REST-en keresztül](GetStartedREST.md)
