---
title: Ismerkedés a Bing Speech-felismerési API-val az C# asztali kódtár használatával | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech felismerési API-t használó alapszintű Windows-alkalmazások fejlesztése a beszélt hang szöveggé alakításához.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966904"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Gyors útmutató: A Windows rendszerben a .NET-&#35; hez készült Bing Speech felismerési API használata

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Ez a lap bemutatja, hogyan fejleszthet egy alapszintű Windows-alkalmazást, amely a Speech Recognition API-t használja a beszélt hang szöveggé alakításához. Az ügyféloldali kódtár használata lehetővé teszi a valós idejű folyamatos átvitelt, ami azt jelenti, hogy amikor az ügyfélalkalmazás hangot küld a szolgáltatásnak, akkor egyidejűleg és aszinkron módon kapja vissza a részleges felismerési eredményeket.

Azok a fejlesztők, akik a beszédfelismerési szolgáltatást olyan alkalmazásokból szeretnék használni, amelyek C# bármely eszközön futnak, használhatják az asztali könyvtárat. A könyvtár használatához telepítse a [Microsoft. ProjectOxford. SpeechRecognition-x86 NuGet-csomagot](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) egy 32 bites platformra és a [Microsoft. ProjectOxford. SpeechRecognition-x64 NuGet](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) -csomagra egy 64 bites platformon. Az ügyféloldali kódtár API-referenciája: [Microsoft Speech C# Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

A következő szakaszok azt ismertetik, hogyan telepítheti, építheti C# és futtathatja a minta C# alkalmazást az asztali kódtár használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

A következő minta a Windows 8 + és a .NET-keretrendszer 4.5 + verziójára lett kifejlesztve a [Visual Studio 2015, a Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)használatával.

### <a name="get-the-sample-application"></a>A minta alkalmazás beszerzése

A minta klónozása a [Speech C# Desktop Library minta](https://github.com/microsoft/cognitive-speech-stt-windows) adattárból.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Feliratkozás a Speech Recognition API-ra, és ingyenes próbaverziós előfizetési kulcs beszerzése

A Speech API Cognitive Services (korábban Project Oxford) része. A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

> [!IMPORTANT]
> * Előfizetési kulcs beszerzése. A beszédfelismerési ügyféloldali kódtárak használata előtt rendelkeznie kell egy [előfizetési kulccsal](https://azure.microsoft.com/try/cognitive-services/).
>
> * Használja az előfizetési kulcsot. A megadott C# asztali minta alkalmazással illessze be az előfizetési kulcsot a szövegmezőbe a minta futtatásakor. További információ: [a minta alkalmazás futtatása](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1\. lépés: A minta alkalmazás telepítése

1. Indítsa el a Visual Studio 2015 alkalmazást, és válassza a **fájl** > **nyitott** > **projekt/megoldás**elemet.

2. Tallózással keresse meg azt a mappát, ahová a letöltött Speech Recognition API-fájlokat mentette. Válassza a **beszédfelismerési** > **ablakok**lehetőséget, majd válassza ki a minta-WP mappát.

3. Kattintson duplán a SpeechToText-WPF-Samples. SLN nevű Visual Studio 2015 Solution (. SLN) fájl megnyitásához. A megoldás megnyílik a Visual Studióban.

## <a name="step-2-build-the-sample-application"></a>2\. lépés: A minta alkalmazás összeállítása

1. Ha az *elismerést*szeretné használni a szándéktal, először regisztrálnia kell a [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Ezután a Luis-alkalmazás végpont URL-címével állíthatja be a kulcs `LuisEndpointUrl` értékét a Samples/SpeechRecognitionServiceExample mappában található app. config fájlban. További információ a LUIS-alkalmazás végponti URL-címéről: [az alkalmazás közzététele](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Cserélje le `&` a Luis-végpont `&amp;` URL-címének karakterét az értékre, hogy az URL-cím helyesen legyen értelmezve az XML-elemző által.

2. Nyomja le a CTRL + SHIFT + B billentyűkombinációt, vagy válassza a **Létrehozás** elemet a menüszalag menüben. Ezután válassza a **megoldás létrehozása**lehetőséget.

## <a name="step-3-run-the-sample-application"></a>3\. lépés: A mintaalkalmazás futtatása

1. A létrehozás befejezése után nyomja le az F5 billentyűt, vagy válassza a menüszalag menü **Start** parancsát a minta futtatásához.

2. Nyissa meg a **Project Oxford Speech-t** a mintaszöveg ablakba. Illessze be az előfizetési kulcsot az **előfizetési kulcs beillesztése ide** a szövegmezőbe az ábrán látható módon. Ha meg szeretné őrizni az előfizetési kulcsot a SZÁMÍTÓGÉPén vagy a laptopján, válassza a **kulcs mentése**lehetőséget. Ha törölni szeretné az előfizetési kulcsot a rendszerből, válassza a **kulcs törlése** lehetőséget a számítógép vagy a laptop eltávolításához.

   ![Beszédfelismerési beillesztési kulcs](../Images/SpeechRecog_paste_key.PNG)

3. A **beszédfelismerési forrás**területen válassza ki a hat beszéd forrása közül az egyiket, amelyek két fő bemeneti kategóriába sorolhatók:

   * A beszédfelismerés rögzítéséhez használja a számítógép mikrofonját vagy egy csatlakoztatott mikrofont.
   * Hangfájl lejátszása.

   Mindegyik kategória három felismerési módot tartalmaz:

    * **ShortPhrase mód**: A teljes érték 15 másodperc. Az adatküldés során az ügyfél több részleges eredményt kap, és egy végeredmény több n-legjobb lehetőség közül.
    * **LongDictation mód**: Akár két percet is igénybe vehet. Az adatküldés során az ügyfél több részleges eredményt és több végső eredményt kap, attól függően, hogy a kiszolgáló hol jelzi a mondat szüneteltetését.
    * **Szándék észlelése**: A kiszolgáló további strukturált adatokat ad vissza a beszédfelismerési bemenetről. A szándék-észlelés használatához először a [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)használatával kell betanítania a modellt.

Minta hangfájlok használata ehhez a minta alkalmazáshoz. Keresse meg a minta/SpeechRecognitionServiceExample mappában található, a mintában letöltött fájlokat a tárházban. Ezek a hangfájlok automatikusan futnak, ha a **WAV-fájl használata Shortphrase módban** lehetőséget választja, vagy ha a **Longdictation mód wav-fájlt használ** a beszédfelismerési bemenetként. Jelenleg csak a WAV hangformátum támogatott.

![Beszéd – szöveg illesztőfelület](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Minták magyarázata

### <a name="recognition-events"></a>Felismerési események

* **Részleges eredmények eseményei**: Ez az esemény minden alkalommal megjelenik, amikor a Speech Service előre jelzi, hogy mit mond, még mielőtt elvégezte a beszédet (ha használja `MicrophoneRecognitionClient`), vagy befejezi az adatok küldését (ha használja `DataRecognitionClient`).
* **Hiba eseményei**: Hívása, ha a szolgáltatás hibát észlel.
* **Leképezési események**: A rendszer a végső felismerési eredmény elemzése után a "WithIntent" ügyfeleket (csak ShortPhrase módban) hívja meg egy strukturált JSON-szándékba.
* **Eredmény eseményei**:
  * Ebben `ShortPhrase` az esetben a rendszer ezt az eseményt hívja meg, és a beszéd befejezése után az n-legjobb eredményt adja vissza.
  * A `LongDictation` módban az eseménykezelőt többször kell meghívni, attól függően, hogy a szolgáltatás hogyan azonosítja a mondatot.
  * **Minden n-legjobb választás esetében**a rendszer egy megbízhatósági értéket és a felismert szöveg néhány különböző formáját adja vissza. További információ: [kimeneti formátum](../Concepts.md#output-format).

Az eseménykezelők már szerepelnek a kódban a kód megjegyzései formájában.

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Microsoft Speech Desktop Library – dokumentáció](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Ismerkedés a Microsoft Speech Recognition API-val Java Androidon](GetStartedJavaAndroid.md)
* [Ismerkedés a Microsoft Speech Recognition API-val a Objective-C-ben iOS rendszeren](Get-Started-ObjectiveC-iOS.md)
* [Ismerkedés a Microsoft Speech Recognition API-val a JavaScriptben](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft Speech Recognition API-val REST használatával](GetStartedREST.md)
