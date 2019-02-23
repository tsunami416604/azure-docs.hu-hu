---
title: Használatának első lépései a Microsoft Speech Recognition API-val a C#-szolgáltatási kódtára |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech recognition-szolgáltatási kódtára segítségével használja a beszélt nyelv szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f445d1fff48ee7a04c0b1c1d64c808f87d824b7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673164"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Gyors útmutató: A Bing Speech recognition szolgáltatás kódtára c.&#35; a .NET Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A szolgáltatási kódtár van fejlesztőknek szól, akik a saját felhőalapú szolgáltatás, és szeretnék beszédszolgáltatás meghívni a szolgáltatást. Ha szeretné, eszközhöz kötött alkalmazások hívja meg a speech recognition szolgáltatást, ne használja az SDK. (Használható egyéb ügyfélkódtárakat vagy a REST API-k, amelyek.)

A C#-szolgáltatási kódtára használatához telepítse a [NuGet-csomag Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). A szalagtár API-referencia, tekintse meg a [Microsoft Speech C# szolgáltatási kódtár](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

A következő szakaszok ismertetik, hogyan kell telepíteni, hozhat létre és futtassa a C# mintaalkalmazást a C# szolgáltatási kódtár használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Az alábbi példa a Windows 8 +, és a .NET 4.5-ös + kifejlesztett keretrendszer használatával [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>A mintaalkalmazás beolvasása

Klónozza a minta a [Speech C# service library minta](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) tárház.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a Speech Recognition API és a egy ingyenes próba-előfizetését kulcs lekérése

A beszédfelismerő API a Cognitive Services-(korábban Project Oxford) részét képezi. Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

> [!IMPORTANT]
> * Előfizetési kulcs lekérése. A beszédfelismerés klienskódtárak használata előtt rendelkeznie kell egy [előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
> * Az előfizetési kulcs használja. A megadott C# service library mintaalkalmazással meg kell adnia az előfizetési kulcs parancssori paraméterek egyikét. További információkért lásd: [futtassa a mintaalkalmazást](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1. lépés: A mintaalkalmazás telepítése

1. Indítsa el a Visual Studio 2015-öt, és válassza ki **fájl** > **nyílt** > **projekt/megoldás**.

2. Kattintson duplán a SpeechClient.sln nevű Visual Studio 2015-megoldás (.sln) fájl megnyitásához. A megoldás megnyílik a Visual Studióban.

## <a name="step-2-build-the-sample-application"></a>2. lépés: A mintaalkalmazás létrehozása

Nyomja le a Ctrl + Shift + B, vagy válassza ki **összeállítása** a menüszalag menü. Válassza ki **megoldás fordítása**.

## <a name="step-3-run-the-sample-application"></a>3. lépés: A mintaalkalmazás futtatása

1. A létrehozás befejezése után nyomja le az F5 billentyűt, vagy válasszon **Start** a menüszalag menü a példa futtatásához.

2. Nyissa meg a minta, például SpeechClientSample\bin\Debug kimeneti könyvtárat. Nyomja le a Shift + jobb gombbal, és válassza ki **Itt a megnyitott parancssori ablakban**.

3. Futtatás `SpeechClientSample.exe` a következő argumentumokkal:

   * [0] arg: Adjon meg egy bemeneti audio WAV-fájlt.
   * [1]. arg: Adja meg a hang területi beállítás.
   * Arg: [2]: Adja meg a felismerés mód: *Rövid* számára a `ShortPhrase` mód és *hosszú* számára a `LongDictation` mód.
   * [3]. arg: Adja meg az előfizetési kulcsot a speech recognition szolgáltatáshoz való hozzáféréshez.

## <a name="samples-explained"></a>A minták ismertetése

### <a name="recognition-modes"></a>Elismerés módok

* `ShortPhrase` mód: Az utterance (kifejezés), legfeljebb 15 másodperc hosszúságú. Adatokat küld a kiszolgálón, mert a az ügyfél több részleges és egy végső legjobb eredményt kap.
* `LongDictation` mód: Az utterance (kifejezés), akár 10 perc hosszú. Adatokat küld a kiszolgálón, mert a az ügyfél több részleges és több végső eredményt is, ahol azt jelzi, a kiszolgáló mondaton belüli szünetet alapján kap.

### <a name="supported-audio-formats"></a>Hangformátumok támogatott

A beszédfelismerő API a következő kodekek segítségével audio/WAV támogatja:

* A PCM egyetlen csatorna
* Siren
* SirenSR

### <a name="preferences"></a>Beállítások

Hozzon létre egy SpeechClient, először hozzon létre egy beállítások objektumot kell. A beállítások objektum paraméterek készletével, amely a speech service viselkedését konfigurálja. A következő mezőket áll:

* `SpeechLanguage`: A hang, a beszéd szolgáltatásnak küldött területi beállítása szerint.
* `ServiceUri`: A végpont hívja a speech service segítségével.
* `AuthorizationProvider`: Jogkivonatok beolvasni a beszédfelismerési szolgáltatás eléréséhez használt IAuthorizationProvider megvalósítása. Bár a minta lehetővé teszi a Cognitive Services-engedélyezési szolgáltató, erősen ajánlott, hogy hoz létre a saját implementációjához token-gyorsítótárazási kezelésére.
* `EnableAudioBuffering`: Egy speciális beállítás. Lásd: [kapcsolat kezelése](#connection-management).

### <a name="speech-input"></a>Bemeneti

A SpeechInput objektumot két mezőt tartalmaz:

* **Hang**: Egy Ön által választott, amelyről az SDK lekéri a hang stream megvalósítását. Ez lehet bármely [stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , amely támogatja az olvasó.
   > [!NOTE]
   > Az SDK-t a konce datového proudu észlel, amikor visszatér a stream **0** olvasási.

* **RequestMetadata**: A beszédfelismerés kérelem metaadatait. További információkért lásd: a [referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Beszéd kérelem

Miután egy SpeechClient és SpeechInput objektumokat kell példányosítani, RecognizeAsync használatával indítson egy beszédszolgáltatás.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

A kérelem befejezése után a RecognizeAsync által visszaadott feladat befejeződik. Az utolsó RecognitionResult elismerését vége. A feladat meghiúsulhat, ha a szolgáltatás vagy az SDK váratlanul meghibásodik.

### <a name="speech-recognition-events"></a>Speech recognition események

#### <a name="partial-results-event"></a>Részleges eredményeket esemény

Ez az esemény meghívása megtörténik, minden alkalommal, amikor beszédszolgáltatás előrejelzi, előfordulhat, hogy el véleményét, beszéd befejezése előtt is (Ha `MicrophoneRecognitionClient`), vagy fejezze be az adatok küldésének (használatakor `DataRecognitionClient`). Az eseményre feliratkozhat használatával `SpeechClient.SubscribeToPartialResult()`. Vagy használhatja az általános események előfizetés módszert `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Visszaadott formátum** | Leírás |
------|------
**LexicalForm** | Ez a képernyő akkor nyers, feldolgozatlan speech recognition eredmények igénylő alkalmazások számára optimális.
**Szöveg** | Más néven Inverz szöveg normalizálási, kis-és nagybetűk, írásjelek és cenzúrázása maszkolása a felismert kifejezést. Káromkodás csillagok karakterekkel van maszkolva után a kezdeti karakter, például "d x." Ez a képernyő akkor optimális alkalmazásokat, amelyek a speech recognition eredményeket megjeleníteni egy felhasználó általi használatra.
**magabiztosan** | Megbízhatósági szintjét a felismert kifejezés jelenti a társított Audio a speech recognition kiszolgáló által meghatározott módon.
**MediaTime** | Az aktuális idő (a 100 nanoszekundumos időegységben) audio-adatfolyam kezdetét viszonyítva.
**MediaDuration** | Az aktuális kifejezés időtartama vagy hosszúság képest a hang szegmens (az idő 100 nanoszekundumos egység).

#### <a name="result-event"></a>Esemény eredménye
Amikor befejezi a beszéd (a `ShortPhrase` módban), ez az esemény nevezzük. Az eredmény számára megadott van a legjobb n beállításokkal. A `LongDictation` módot, az esemény nem hívható meg többször is feldolgozza azt jelzi, ha a kiszolgáló mondaton belüli szünetet alapján. Az eseményre feliratkozhat használatával `SpeechClient.SubscribeToRecognitionResult()`. Vagy használhatja az általános események előfizetés módszert `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Visszaadott formátum** | Leírás |
------|------|
**RecognitionStatus** | Hogyan állította elő a felismerés állapota. Például azt eredményeként sikeres felismerése vagy állították eredményeként megszakítása a kapcsolat stb.
**Kifejezések** | Legjobb n felismert kifejezések a felismerés magabiztosan halmaza.

Felismerési eredményeket további információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Speech recognition válasz

Beszéd válasz. példa:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Kapcsolat kezelése

Az API-t használja a kérelmenként egyetlen WebSocket kapcsolaton keresztül. Az optimális felhasználói élmény az SDK megpróbálja Speech-szolgáltatáshoz történő újracsatlakozás, és indítsa el a kapott utolsó RecognitionResult elismerését. Például a audiókérést két perc hosszúságú, az SDK-t egy egy perces befejeződnie RecognitionEvent kapott, és egy hálózati hiba történt a öt másodperc után, ha az SDK-t, amely elindítja a perces jelre az új kapcsolat elindul.

>[!NOTE]
>Az SDK nem keresik térjen vissza az egy perces be van jelölve, mivel előfordulhat, hogy az adatfolyam nem támogatja a keresést. Ehelyett az SDK-t biztosítja, hogy egy belső puffer, hogy a hanganyag meghajtóin használja, és törli a puffer, RecognitionResult eseményt kap.

## <a name="buffering-behavior"></a>Pufferelés viselkedés

Alapértelmezés szerint az SDK-t hang puffereli, így azt helyreállíthatja, ha egy hálózati megszakítás történik. Egy forgatókönyvben, ahol célszerű során hálózati kapcsolata megszakadt a hanganyag elvetéséhez, és indítsa újra a kapcsolat, ajánlott letiltani a hang pufferelés beállításával `EnableAudioBuffering` a kívánt beállítások objektum `false`.

## <a name="related-topics"></a>Kapcsolódó témakörök

[Microsoft Speech C# szolgáltatási kódtár – referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
