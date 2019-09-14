---
title: Ismerkedés a Microsoft Speech Recognition API-val a C# Service Library használatával | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A beszélt nyelv szöveggé alakításához használja a Bing Speech felismerési szolgáltatás könyvtárát.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965856"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Gyors útmutató: A C&#35; Bing Speech felismerési szolgáltatás könyvtárának használata a .net Windows rendszerben

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A szolgáltatás könyvtára olyan fejlesztők számára készült, akik saját felhőalapú szolgáltatással rendelkeznek, és a beszédfelismerési szolgáltatást szeretnék hívni a szolgáltatástól. Ha az eszközhöz kötött alkalmazásokból is meg szeretné hívni a beszédfelismerési szolgáltatást, ne használja ezt az SDK-t. (Használjon más ügyféloldali kódtárakat vagy REST API-kat.)

A C# szolgáltatás könyvtárának használatához telepítse a [Microsoft. Bing. Speech NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Bing.Speech/). A Library API-referenciát a [Microsoft Speech C# Service Library webhelyén](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)tekintheti meg.

A következő szakaszok azt ismertetik, hogyan telepítheti, építheti C# le és futtathatja a C# minta alkalmazást a Service Library használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

A következő példa a Windows 8 + és a .NET 4.5 + keretrendszerhez készült a [Visual Studio 2015, a Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)használatával.

### <a name="get-the-sample-application"></a>A minta alkalmazás beszerzése

A minta klónozása a [Speech C# Service Library minta](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) adattárból.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Feliratkozás a Speech Recognition API-ra, és ingyenes próbaverziós előfizetési kulcs beszerzése

A Speech API Cognitive Services (korábban Project Oxford) része. A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

> [!IMPORTANT]
> * Előfizetési kulcs beszerzése. A Speech Client kódtárak használata előtt rendelkeznie kell egy [előfizetési kulccsal](https://azure.microsoft.com/try/cognitive-services/).
>
> * Használja az előfizetési kulcsot. A szolgáltatási függvénytár C# megadott alkalmazásával a parancssori paraméterek egyikének kell megadnia az előfizetési kulcsot. További információ: [a minta alkalmazás futtatása](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1\. lépés: A minta alkalmazás telepítése

1. Indítsa el a Visual Studio 2015 alkalmazást, és válassza a **fájl** > **nyitott** > **projekt/megoldás**elemet.

2. Kattintson duplán a SpeechClient. SLN nevű Visual Studio 2015 Solution (. SLN) fájl megnyitásához. A megoldás megnyílik a Visual Studióban.

## <a name="step-2-build-the-sample-application"></a>2\. lépés: A minta alkalmazás összeállítása

Nyomja le a CTRL + SHIFT + B billentyűkombinációt, vagy válassza a **Létrehozás** elemet a menüszalag menüben. Ezután válassza a **megoldás létrehozása**lehetőséget.

## <a name="step-3-run-the-sample-application"></a>3\. lépés: A mintaalkalmazás futtatása

1. A létrehozás befejezése után nyomja le az F5 billentyűt, vagy válassza a menüszalag menü **Start** parancsát a példa futtatásához.

2. Nyissa meg a minta kimeneti könyvtárát, például SpeechClientSample\bin\Debug. Nyomja le a SHIFT billentyűt, kattintson a jobb gombbal, majd válassza a **parancssorablak megnyitása**lehetőséget.

3. Futtassa `SpeechClientSample.exe` a következő argumentumokat:

   * ARG [0]: Adja meg a bemeneti hang WAV-fájlját.
   * ARG [1]: Határozza meg a hang területi beállítását.
   * ARG [2]: A felismerési módok meghatározása: *Rövid* a `ShortPhrase` mód esetében, és *hosszú* a `LongDictation` mód.
   * ARG [3]: Az előfizetési kulcs megadásával férhet hozzá a beszédfelismerési szolgáltatáshoz.

## <a name="samples-explained"></a>Minták magyarázata

### <a name="recognition-modes"></a>Felismerési módok

* `ShortPhrase`mód A teljes érték 15 másodperc. Ahogy a rendszer elküldi az adatküldés eredményeit a kiszolgálónak, az ügyfél több részleges eredményt kap, és egy utolsó legjobb eredményt kapja.
* `LongDictation`mód Akár 10 percet is igénybe vehet. Az adatküldés során az ügyfél több részleges eredményt és több végső eredményt kap, attól függően, hogy a kiszolgáló hol jelzi a mondat szüneteltetését.

### <a name="supported-audio-formats"></a>Támogatott hangformátumok

A Speech API a következő kodekekkel támogatja a hang/WAV használatát:

* PCM – egyetlen csatorna
* Sziréna
* SirenSR

### <a name="preferences"></a>Beállítások

SpeechClient létrehozásához először létre kell hoznia egy Preferences objektumot. A Preferences objektum olyan paraméterek összessége, amelyek a beszédfelismerési szolgáltatás viselkedését konfigurálja. A következő mezőkből áll:

* `SpeechLanguage`: A beszédfelismerési szolgáltatásnak eljuttatott hang területi beállítása.
* `ServiceUri`: A beszédfelismerési szolgáltatás meghívásához használt végpont.
* `AuthorizationProvider`: Egy IAuthorizationProvider implementáció, amely a jogkivonatok beolvasására szolgál a beszédfelismerési szolgáltatás eléréséhez. Bár a minta Cognitive Services engedélyezési szolgáltatót biztosít, javasoljuk, hogy hozzon létre saját implementációt a jogkivonat-gyorsítótárazás kezeléséhez.
* `EnableAudioBuffering`: Speciális beállítás. Lásd: a [kapcsolatok kezelése](#connection-management).

### <a name="speech-input"></a>Beszédfelismerési bemenet

A SpeechInput objektum két mezőből áll:

* **Hang**: A kívánt stream-implementáció, amelyből az SDK lekéri a hangot. Az olvasást támogató bármely [adatfolyam](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) lehet.
   > [!NOTE]
   > Az SDK észleli a stream végét, ha az adatfolyam **0** értéket ad vissza olvasáskor.

* **RequestMetadata**: A beszédfelismerési kérelemmel kapcsolatos metaadatok. További információ: [hivatkozás](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Beszédfelismerési kérelem

Miután létrehozta a SpeechClient és a SpeechInput objektumokat, a RecognizeAsync használatával kérheti a beszédfelismerési szolgáltatást.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

A kérés befejeződése után a RecognizeAsync által visszaadott feladat befejeződött. Az utolsó RecognitionResult az elismerés vége. A feladat sikertelen lehet, ha a szolgáltatás vagy az SDK váratlanul leáll.

### <a name="speech-recognition-events"></a>Beszédfelismerési események

#### <a name="partial-results-event"></a>Részleges eredmény esemény

Ez az esemény minden alkalommal megjelenik, amikor a Speech Service előre jelzi, hogy mit mond, még mielőtt elvégezte a beszédet (ha használja `MicrophoneRecognitionClient`), vagy befejezi az adatok küldését (ha használja `DataRecognitionClient`). Előfizethet az eseményre a használatával `SpeechClient.SubscribeToPartialResult()`. Vagy használhatja az általános események előfizetési módszert `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Visszatérési formátum** | Leírás |
------|------
**LexicalForm** | Ez az űrlap optimálisan használható olyan alkalmazások számára, amelyek nyers, feldolgozatlan beszédfelismerési eredményeket igényelnek.
**Szöveg** | A felismert kifejezés az inverz szöveg normalizálása, a tőkésítés, a központozás és a káromkodás maszkolása. A káromkodás a kezdeti karakter után csillagokkal van ellátva, például: "d * * *". Ez az űrlap optimálisan használható olyan alkalmazások számára, amelyek a beszédfelismerés eredményeit egy felhasználó számára jelenítik meg.
**Megbízhatósági** | A felismert kifejezés megbízhatósági szintje a beszédfelismerési kiszolgáló által meghatározott társított hangra vonatkozik.
**MediaTime** | Az aktuális idő az audio stream elejéhez képest (100 ns egységben).
**MediaDuration** | Az aktuális kifejezés időtartama/hossza a hangszegmenshez képest (100 – ns egységben).

#### <a name="result-event"></a>Eredmény esemény
Ha befejezte a beszédet `ShortPhrase` (mód), ez az esemény a következő:. Az eredményhez az n-legjobb választási lehetőségek állnak rendelkezésre. A `LongDictation` módban az esemény többször is hívható, attól függően, hogy a kiszolgáló hol jelzi a mondat szüneteltetését. Előfizethet az eseményre a használatával `SpeechClient.SubscribeToRecognitionResult()`. Vagy használhatja az általános események előfizetési módszert `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Visszatérési formátum** | Leírás |
------|------|
**RecognitionStatus** | Az elismerés előállításának állapota. Például a sikeres felismerés vagy a kapcsolatok megszakítása eredményeként történt, stb.
**Kifejezések** | Az n-legjobb felismert mondatok halmaza, az elismerési megbízhatósággal.

Az eredmények felismerésével kapcsolatos további információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Beszédfelismerési válasz

Beszéd-válasz példája:
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

## <a name="connection-management"></a>Kapcsolatok kezelése

Az API igény szerint egyetlen WebSocket-kapcsolattal rendelkezik. Az SDK az optimális felhasználói élmény érdekében megpróbál újracsatlakozni a beszédfelismerési szolgáltatáshoz, és elindítja az elismerést a kapott utolsó RecognitionResult. Ha például a hangkérelem két percet vesz igénybe, az SDK egy RecognitionEvent kapott, és egy hálózati hiba történt öt másodperc elteltével, az SDK egy új, az egyperces jellel kezdődő kapcsolatokat indít el.

>[!NOTE]
>Az SDK nem keresi vissza az egyperces jelölést, mert előfordulhat, hogy az adatfolyam nem támogatja a keresést. Ehelyett az SDK egy belső puffert tart fenn, amelyet a hang puffereléséhez használ, és törli a puffert, mivel RecognitionResult eseményeket fogad.

## <a name="buffering-behavior"></a>Pufferelési viselkedés

Alapértelmezés szerint az SDK-pufferek hang, hogy a rendszer képes legyen helyreállítani a hálózati megszakítások bekövetkezésekor. Olyan helyzetekben, ahol érdemes eldobni a hanganyagot a hálózat leválasztása közben, és újra kell indítani a kapcsolatot, a Preferences `EnableAudioBuffering` `false`objektum beállításával ajánlott letiltani a hangpufferelést.

## <a name="related-topics"></a>Kapcsolódó témakörök

[Microsoft Speech C# Service Library – dokumentáció](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
