---
title: A C# szolgáltatás könyvtár használatával a Microsoft beszéd felismerés API használatába |} Microsoft Docs
description: A Microsoft beszéd felismerés service library segítségével szóbeli nyelvi szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/17/2017
ms.author: zhouwang
ms.openlocfilehash: 0320f41658a7ac4d6bf9e88ed998c853b665d485
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347470"
---
# <a name="get-started-with-the-speech-recognition-service-library-in-c35-for-net-windows"></a>Ismerkedjen meg a beszédfelismerés felismerés service könyvtár c.&#35; .NET Windows

A szolgáltatás könyvtárban van, a fejlesztők számára, akik a saját felhőalapú szolgáltatás, és szeretné, hogy beszéd szolgáltatás hívásához a szolgáltatás. Ha azt szeretné, a beszédfelismerés felismerési szolgáltatás hívásához eszköz adathoz kötött alkalmazásokból, ne használja az SDK. (Használni más ügyfélkódtárai vagy a REST API-k, amelyek.)

A C# service könyvtár használatához telepítse a [NuGet-csomag Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). A szalagtár API-referencia, tekintse meg a [Microsoft beszéd C# service könyvtár](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Az alábbi szakaszok azt ismertetik, hogyan telepítse, felépítéséhez, és futtassa a C# mintaalkalmazást a C# szolgáltatás könyvtár használatával.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

A következő példa a Windows 8 +, és a .NET 4.5 + kifejlesztett keretrendszer használatával [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>A mintaalkalmazás beolvasása

A mintát klónozza a [beszéd C# service library minta](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) tárház.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>A beszédfelismerés felismerés API előfizetni, és egy ingyenes próba-előfizetés kulcs beszerzése

A Diktálásfelismerési API (korábban projekt Oxford) kognitív szolgáltatások részét képezi. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

> [!IMPORTANT]
> * Egy előfizetés kulcs beszerzése. A beszédfelismerés klienskódtárak segítségével használata előtt rendelkeznie kell egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
> * Az előfizetés-kulcsot használ. A megadott C# service library mintaalkalmazást meg kell adnia az Előfizetés kulcs rendelkezésre álló parancssori paraméterek. További információkért lásd: [futtassa a mintaalkalmazást](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1. lépés: A mintaalkalmazás telepítése

1. Indítsa el a Visual Studio 2015-öt, és válassza **fájl** > **nyitott** > **projekt/megoldás**.

2. Kattintson duplán a Visual Studio 2015 megoldás (.sln) fájl SpeechClient.sln nevű megnyitásához. A megoldás a Visual Studio nyílik meg.

## <a name="step-2-build-the-sample-application"></a>2. lépés: A minta-alkalmazás létrehozása

Nyomja le a Ctrl + Shift + B, vagy válasszon **Build** a menüszalag menü. Válassza ki **megoldás fordítása**.

## <a name="step-3-run-the-sample-application"></a>3. lépés: A mintaalkalmazás futtatása

1. A létrehozás befejezése után nyomja le az F5 billentyűt, vagy válasszon **Start** a menüszalag menü a példa futtatásához.

2. Nyissa meg a minta, például SpeechClientSample\bin\Debug kimeneti könyvtár. Nyomja le a Shift + jobb gombbal, és válassza ki **Itt a nyitott parancsablakot**.

3. Futtatás `SpeechClientSample.exe` a következő argumentumokkal:

   * Arg [0]: A bemeneti WAV hangfájl adja meg.
   * [1]. arg: Adja meg a hang nyelvterületi beállításokat.
   * Arg [2]: Adja meg a felismerési módok: *rövid* a a `ShortPhrase` mód és *hosszú* a a `LongDictation` mód.
   * Arg [3]: Az Előfizetés kulcs a beszédfelismerés felismerési szolgáltatás eléréséhez adja meg.

## <a name="samples-explained"></a>Minták alapján

### <a name="recognition-modes"></a>Felismerés módok

* `ShortPhrase` mód: egy utterance legfeljebb 15 másodperc hosszú. Mivel adatokat küld a kiszolgáló, az ügyfél megkapja a több részleges eredményt és egy végső legjobb eredményt.
* `LongDictation` mód: egy utterance akár 10 perc hosszú. Mivel adatokat küld a kiszolgáló, az ügyfél megkapja a több részleges eredményt, és több végső eredmény azt jelzi, ha a kiszolgáló mondat szünet alapján.

### <a name="supported-audio-formats"></a>Támogatott hang formátumok

A hang transzformációs API hang/WAV támogatja a következő kodekek segítségével:

* PCM egyetlen csatorna
* Siren
* SirenSR

### <a name="preferences"></a>Beállítások

Egy SpeechClient, meg kell először létre kell hoznia egy beállítások objektum. A beállítások objektum értéke a paraméterek készletét, amely beállítja a beszédfelismerés szolgáltatás működését. A következő mezők áll:

* `SpeechLanguage`: A beszédfelismerés szolgáltatásnak küldött hang a területi beállítás.
* `ServiceUri`: A beszédfelismerés szolgáltatás hívásához használt a végpontnak.
* `AuthorizationProvider`: IAuthorizationProvider megvalósítása jogkivonatok beolvasása a beszédfelismerés szolgáltatás eléréséhez használt. Bár a minta egy kognitív szolgáltatások engedélyezési szolgáltató, erősen ajánlott, hogy hozzon létre a saját megvalósítási token-gyorsítótárazási kezelésére.
* `EnableAudioBuffering`: Egy speciális beállítás. Lásd: [kapcsolat kezelése](#connection-management).

### <a name="speech-input"></a>Bemeneti

A SpeechInput objektum két mezőből áll:

* **Hang**: az Ön által választott, ahol az SDK hang lekéri A adatfolyam megvalósításának. Ez lehet bármely [adatfolyam](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , amely támogatja az olvasást.
   > [!NOTE]
   > Az SDK az adatfolyam végét észleli, ha az adatfolyamot visszaadja **0** olvasási.

* **RequestMetadata**: a beszédfelismerés kérelmekre vonatkozó metaadatok. További információkért lásd: a [hivatkozás](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Beszéd kérelem

SpeechClient és SpeechInput objektumokat kell létrehozni, miután a RecognizeAsync használatával indítson egy lekérdezést beszéd szolgáltatás.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

A kérelem befejezése után a RecognizeAsync által visszaadott feladat befejezése után. Az utolsó RecognitionResult a felismerési végén. A feladat sikertelen lehet, ha a szolgáltatás vagy az SDK váratlanul meghibásodik.

### <a name="speech-recognition-events"></a>Beszéd felismerés események

#### <a name="partial-results-event"></a>Részleges eredmények esemény

Ez az esemény minden alkalommal, amikor a beszédfelismerés szolgáltatás előrejelzi mi, előfordulhat, hogy kell arról, még mielőtt beszéd menüelemnek (használatakor `MicrophoneRecognitionClient`) vagy a Befejezés adatküldés (használatakor `DataRecognitionClient`). Előfizethet az esemény használatával `SpeechClient.SubscribeToPartialResult()`. Az általános események előfizetés metódus használatával kérheti le vagy `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Térjen vissza a formátum** | Leírás |
------|------
**LexicalForm** | Ezt az űrlapot az optimális feldolgozatlan, nyers beszédfelismerés felismerési eredmények igénylő alkalmazások általi használatra.
**Megjelenő szöveg** | Más néven Inverz szöveg normalizálási, nagybetűk, absztrakt és Profanitás maszkolása felismert kifejezés. Profanitás csillag karakterekkel van maszkolva kezdeti vonal utáni, például "d x." Ezt az űrlapot az optimális alkalmazásokat, amelyek a beszédfelismerés felismerési eredmények megjelenítéséhez a felhasználó általi használatra.
**Megbízhatóság** | Megbízhatósági szintjét a felismert kifejezés jelenti. a társított Audio a beszédfelismerés felismerés kiszolgáló által definiált konfigurációjának kialakításához.
**MediaTime** | Az aktuális idő (a 100 nanoszekundumos időegységekkel) a hangadatfolyam kezdetét viszonyítva.
**MediaDuration** | Az aktuális kifejezés időtartama vagy hosszának a hang szegmenst (100 nanoszekundumos időegységekkel) viszonyítva.

#### <a name="result-event"></a>Eredmény esemény
Amikor befejezi a beszéd (a `ShortPhrase` üzemmód), ez a függvény. Az eredmény a n-ajánlott beállításokkal által megadott. A `LongDictation` módot, az esemény hívása többször, azt jelzi, ha a kiszolgáló mondat szünet alapján. Előfizethet az esemény használatával `SpeechClient.SubscribeToRecognitionResult()`. Az általános események előfizetés metódus használatával kérheti le vagy `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Térjen vissza a formátum** | Leírás |
------|------|
**RecognitionStatus** | A hogyan állította elő a felismerési állapotát. Például azt miatt sikeres felismerés vagy állították miatt megszakítani a kapcsolat, stb.
**Kifejezések** | N-legjobb felismert kifejezések a felismerési megbízhatósággal készlete.

A felismerési eredmények további információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Beszéd felismerés válasz

Például a beszéd választ:
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

Az API-t használja a kérelmenként egyetlen WebSocket-kapcsolat. Optimális felhasználói élmény az SDK megkísérli kapcsolódjon Beszéd szolgáltatáshoz, és indítsa el a felismerési az utolsó RecognitionResult kapott. Például, hogy a hang kérelem hosszú két perc, az SDK-t a egy perces be van jelölve, egy RecognitionEvent kapott, és egy hálózati hiba történt a öt másodpercen belül, az SDK-val kezdődik, amely elindítja a egy perces jelre az új kapcsolatot.

>[!NOTE]
>Az SDK nem kereshető vissza a egy perces jelre mert előfordulhat, hogy az adatfolyam nem támogatja a keresést. Ehelyett az SDK tartja, hogy a hang meghajtóin használ, és törli a puffer RecognitionResult események kap, egy belső puffer.

## <a name="buffering-behavior"></a>Pufferelés viselkedése

Alapértelmezés szerint az SDK-t, hogy egy hálózati megszakítási akkor fordul elő, akkor helyreállíthatja puffereli hang. Ha célszerű a hang, a hálózati kapcsolat bontása során elveszett elvetéséhez, és indítsa újra a kapcsolat esetén, célszerű úgy, hogy hang pufferelés letiltásához `EnableAudioBuffering` a beállítások objektumot a `false`.

## <a name="related-topics"></a>Kapcsolódó témakörök

[Microsoft beszéd C# szolgáltatás kódtár referenciája](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
