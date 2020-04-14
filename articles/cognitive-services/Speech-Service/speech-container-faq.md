---
title: A beszédszolgáltatások tárolói gyakran feltett kérdések (GYIK)
titleSuffix: Azure Cognitive Services
description: Beszédtárolók telepítése és futtatása. a beszéd-szöveg üzenet valós időben átírja a hangadatfolyamokat olyan szöveggé, amelyet az alkalmazások, eszközök vagy eszközök használhatnak vagy jeleníthetnek meg. A szövegfelolvasás a bemeneti szöveget emberszerű szintetizált beszédmé alakítja.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 4d597b872cf31a823f314d9f3c67c9f45201c542
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258473"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>A beszédszolgáltatások tárolói gyakran feltett kérdések (GYIK)

Ha a beszédfelismerési szolgáltatást tárolókkal használja, a gyakran ismételt kérdések gyűjteményére támaszkodhat, mielőtt a támogatáshoz hozzátelne. Ez a cikk rögzíti a kérdéseket különböző mértékben, az általános technikai. A válasz kibontásához kattintson a kérdésre.

## <a name="general-questions"></a>Általános kérdések

<details>
<summary>
<b>Hogyan működnek a beszédtárolók, és hogyan állíthatom be őket?</b>
</summary>

**Válasz:** A termelési fürt beállításakor több dolgot is figyelembe kell venni. Először is, egyetlen nyelv beállítása, több tárolók, ugyanazon a gépen, nem lehet nagy probléma. Ha problémákat tapasztal, akkor lehet, hogy hardverrel kapcsolatos probléma - ezért először megnézzük az erőforrásokat, azaz; CPU és a memória specifikációk.

Fontolja meg egy `ja-JP` pillanatra, a konténer és a legújabb modell. Az akusztikai modell a legigényesebb darab CPU-bölcs, míg a nyelvi modell követeli a legtöbb memóriát. Amikor összehasonlítottuk a használatot, körülbelül 0,6 CPU-mag kell ahhoz, hogy egyetlen beszéd-szöveg kérést dolgozzunk fel, amikor a hang valós időben áramlik be (például a mikrofonból). Ha a hangot gyorsabban táplálja, mint a valós idejű (például egy fájlból), akkor a használat megduplázódhat (1,2x magok). Eközben az alább felsorolt memória a beszéd dekódolásához szükséges memória. *Nem* veszi figyelembe a nyelvi modell tényleges teljes méretét, amely a fájlgyorsítótárban fog elraktáradni. Mert `ja-JP` ez egy további 2 GB; (6-7 `en-US`GB).

Ha olyan gépe van, ahol kevés a memória, és több nyelvet próbál telepíteni rá, lehetséges, hogy a fájlgyorsítótár megtelt, és az operációs rendszer kénytelen a modelleket be- és kiítani. Egy futó átírás, hogy katasztrofális lehet, és vezethet lassulások és egyéb teljesítmény következményei.

Továbbá előre becsomagoljuk a végrehajtható fájlokat a [speciális vektorkiterjesztésű (AVX2)](speech-container-howto.md#advanced-vector-extension-support) utasításkészlettel rendelkező gépekhez. Az AVX512 utasításkészlettel rendelkező gépekkódgenerálást igényelnek az adott célhoz, és 10 10-es tároló 10-es indításához ideiglenesen kimerítheti a CPU-t. Egy ilyen üzenet jelenik meg a docker-naplókban:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Végül beállíthatja, hogy hány dekódert szeretne egyetlen `DECODER MAX_COUNT` tárolóban *egy* változó használatával. Tehát alapvetően a Termékváltozattal (CPU / memória) kell kezdenünk, és javasolhatjuk, hogyan hozhatjuk ki belőle a legjobbat. Egy nagyszerű kiindulási pont az ajánlott gazdagép erőforrás-specifikációira utal.

<br>
</details>

<details>
<summary>
<b>Tudna segíteni a kapacitástervezésés a költségbecslés a prem beszéd tárolók?</b>
</summary>

**Válasz:** A tárolókapacitás kötegelt feldolgozási módban, minden dekóder képes kezelni 2-3x valós időben, két CPU-magok, egyetlen felismerés. Nem javasoljuk, hogy tárolópéldányonként kettőnél több egyidejű felismerést tartson, de javasoljuk, hogy a tárolók további példányait futtassa megbízhatósági/rendelkezésre állási okokból, egy terheléselosztó mögött.

Bár lehet, hogy minden tárolópéldány több dekóderrel fut. Például előfordulhat, hogy 7 dekódert állíthatunk be egy tárolópéldányonként egy nyolcmagos gépen (egyenként több mint 2x sebességgel), ami 15x átviteli hatással jár. Van egy param, `DECODER_MAX_COUNT` hogy tudatában legyünk. A szélsőséges esetben megbízhatósági és késési problémák merülnek fel, az átviteli képesség jelentősen megnőtt. Egy mikrofon, ez lesz a 1x valós időben. Az általános használat nak körülbelül egy magból kell lennie az egyetlen felismeréshez.

A forgatókönyv feldolgozása 1 K óra/nap kötegelt feldolgozási módban, szélsőséges esetben, 3 virtuális gépek is kezelni 24 órán belül, de nem garantált. A csúcsnapok, a feladatátvétel, a frissítés és a minimális biztonsági mentés/BCP biztosításához fürtenként 3 helyett 4–5 gépet ajánlunk, és 2+ fürttel.

A hardverek esetében a `DS13_v2` szabványos Azure Virtuális gép referenciaként szolgál (minden magnak 2,6 GHz-es vagy annál jobbnak kell lennie, ha az AVX2 utasításkészlet engedélyezve van).

| Példány  | vCPU(k) | RAM    | Temp tárolás | Felosztó-ki-you-go az AHB | 1 éves tartalék az AHB-val (% Megtakarítás) | 3 év fenntartva az AHB-val (% Megtakarítás) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/óra            | $0.3528/óra (~41%)                 | $0.2333/óra (~61%)                  |

A tervezési referencia (két 5 virtuális gépből álló fürt 1 K óra/nap audioköteg-feldolgozás kezelésére) alapján az 1 éves hardverköltség a következő lesz:

> 2 (fürtök) * 5 (virtuális gépek fürtenként) * $0.3528/óra * 365 (nap) * 24 (óra) = $31K / év

Fizikai gépre való leképezéskor az általános becslés 1 vCPU = 1 fizikai CPU-mag. A valóságban az 1vCPU erősebb, mint egy mag.

Az on-prem esetében ezek a további tényezők is szerepet játszanak:

- Milyen típusú a fizikai CPU, és hány magok rajta
- Hány processzor fut együtt ugyanazon a dobozon/gépen
- A virtuális gépek beállítása
- Hogyan hiper-threading / többszálas használják
- A memória megosztásának
- Az operációs rendszer, stb.

Általában nincs olyan jól hangolva, mint az Azure a környezetben. Figyelembe véve az egyéb terhelési, azt mondanám, egy biztonságos becslés 10 fizikai CPU magok = 8 Azure vCPU. Bár a népszerű CPU-k nak csak nyolc magja van. A helyszíni üzembe helyezés esetén a költségek magasabbak lesznek, mint az Azure virtuális gépek használata. Vegye figyelembe az értékcsökkenési rátát is.

A szolgáltatás költsége megegyezik az online szolgáltatás: $ 1 /óra a beszéd-szöveg. A beszédszolgáltatás költsége:

> $1 * 1000 * 365 = $365K

A Microsoftnak fizetett karbantartási költség a szolgáltatás szintjétől és tartalmától függ. Ez különböző -ból $29.99/month részére alap szint -hoz száz -ból ezer ha helyszíni szolgáltatás burkolt. A durva szám $300/óra a szolgáltatás/karbantartás esetén. Az emberek ára nem tartozik ide. Az egyéb infrastrukturális költségek (például a tárolás, a hálózatok és a terheléselosztók) nem tartoznak ide.

<br>
</details>

<details>
<summary>
<b>Miért hiányzik az írásjelek az átiratból?</b>
</summary>

**Válasz:** A `speech_recognition_language=<YOUR_LANGUAGE>` explicit módon kell konfigurálni a kérelemben, ha azok a Carbon-ügyfél.

Például:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
A kimenet itt látható:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Használhatok egyéni akusztikai modellt és nyelvi modellt a beszédtárolóval?</b>
</summary>

Jelenleg csak egy modellazonosítót tudunk átadni, akár egyéni nyelvi modellt, akár egyéni akusztikai modellt.

**Válasz:** Az a döntés született, hogy *nem* támogatják egyszerre az akusztikai és nyelvi modelleket. Ez mindaddig érvényben marad, amíg létre nem jön egy egységes azonosító az API-törések csökkentésére. Szóval, sajnos ez nem támogatott most.

<br>
</details>

<details>
<summary>
<b>Meg tudná magyarázni ezeket a hibákat az egyéni beszéd-szöveg tárolóból?</b>
</summary>

**1. hiba:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**1. válasz:** Ha a legújabb egyéni modellel edzel, ezt jelenleg nem támogatjuk. Ha a vonat egy régebbi verzió, lehetővé kell tenni, hogy használja. Még mindig dolgozunk a legújabb verziók támogatásán.

Lényegében az egyéni tárolók nem támogatják a halogén vagy az ONNX-alapú akusztikai modelleket (ami az alapértelmezett az egyéni betanítási portálon). Ez annak köszönhető, hogy az egyéni modellek nem titkosítottak, és nem akarjuk, hogy az ONNX modelleket elérhetővé tegyünk; nyelvi modellek rendben vannak. Az ügyfélnek explicit módon ki kell választania egy régebbi, nem ONNX-modellt az egyéni betanításhoz. A pontosságot ez nem befolyásolja. A modell mérete nagyobb lehet (100 MB-tal).

> Támogatási modell > 20190220 (v4.5 Unified)

**2. hiba:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**2. válasz:** Meg kell adnia a megfelelő hangnevet a kérelemben, amely a kis- és nagybetűk megkülönböztetése. Tekintse meg a teljes szolgáltatásnév-hozzárendelést. A programot `en-US-JessaRUS`a `en-US-JessaNeural` programot kell használnia, mivel a szövegfelolvasás tárolós verziójában jelenleg nem érhető el.

**3. hiba:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**3. válasz:** A feladatfelismerési erőforrás, nem pedig a Cognitive Services-erőforrás létrehozásához hozzon létre.


<br>
</details>

<details>
<summary>
<b>Milyen API-protokollok támogatottak, REST vagy WS?</b>
</summary>

**Válasz:** A beszéd-szöveg és az egyéni beszéd-szöveg tárolók esetében jelenleg csak a websocket alapú protokollt támogatjuk. Az SDK csak a WS-ben támogatja a hívást, de a REST-ben nem. Van egy terv rest támogatás hozzáadására, de jelenleg nem az ETA.There's a plan to add REST support, but not ETA. Mindig olvassa el a hivatalos dokumentációt, [lásd: lekérdezés-előrejelzési végpontok](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Támogatja a CentOS a beszédtárolókat?</b>
</summary>

**Válasz:** A CentOS 7-et még nem támogatja a Python SDK, az Ubuntu 19.04 sem támogatott.

A Python Speech SDK-csomag a következő operációs rendszerekhez érhető el:
- **Windows** - x64 és x86
- **Mac** – macOS X 10.12-es vagy újabb verzió
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 x64-én

A környezet beállításáról további információt a [Python platform beállításáról](quickstarts/setup-platform.md?pivots=programming-language-python)talál. Most az Ubuntu 18.04 az ajánlott verzió.

<br>
</details>

<details>
<summary>
<b>Miért kapok hibákat, amikor megpróbálja meghívni a LUIS előrejelzési végpontjait?</b>
</summary>

A LUIS-tárolót egy IoT Edge-telepítésben használom, és megpróbálom meghívni a LUIS előrejelzési végpontját egy másik tárolóból. A LUIS-tároló figyeli az 5001-es portot, és az általam használt URL a következő:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

A hiba kapok:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

A kérelem megjelenik a LUIS-tároló naplókban, és az üzenet a következőket mondja:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Ez mit jelent? Miről maradtam le? Én követtem a példát a beszéd SDK, [innen](https://github.com/Azure-Samples/cognitive-services-speech-sdk). A forgatókönyv az, hogy észleli a hangot közvetlenül a PC mikrofon, és megpróbálja meghatározni a szándékot, a LUIS alkalmazás általunk betanított. A példa, amit kapcsolódik nem pontosan ezt. És jól működik a LUIS felhőalapú szolgáltatás. A beszédsdk használatával úgy tűnt, hogy mentse nt minket attól, hogy egy külön explicit hívást a beszéd-szöveg API-t, majd egy második hívás a LUIS.

Így mindössze annyit próbálok tenni, hogy váltson a luis a felhőben a LUIS használatával a LUIS-tároló használatával. Nem tudom elképzelni, ha a beszéd SDK működik az egyik, ez nem fog működni a másik.

**Válasz:** A beszédstabk nem használható LUIS-tárolóellen. A LUIS-tároló használata esetén a LUIS SDK vagy LUIS REST API-t kell használni. A beszédstak-tárolóellen kell használni.

A felhő más, mint egy tároló. A felhő több összesített tárolóból (más néven mikroszolgáltatásokból) állhat. Tehát van egy LUIS-tároló, és van egy beszédtároló - két külön tároló. A beszédtároló csak beszédfelismerést végez. A LUIS-tároló csak a LUIS.The LUIS container only does LUIS. A felhőben, mivel mindkét tároló ismert, hogy telepítve van, és ez rossz teljesítmény egy távoli ügyfél megy a felhőbe, nem beszéd, gyere vissza, majd menj a felhőbe, és nem LUIS, biztosítunk egy olyan funkciót, amely lehetővé teszi az ügyfél számára, hogy a beszéd, maradjon a felhőben, menjen a LUIS, majd gyere vissza az ügyfélhez. Így még ebben a forgatókönyvben a speech SDK hanggal a Speech cloud container, majd a Speech cloud container beszél a LUIS felhőtároló szöveggel. A LUIS-tároló fogalma nincs a hang fogadása (nem lenne értelme a LUIS-tároló, hogy fogadja el a folyamatos hang - LUIS egy szöveges szolgáltatás). A prem, nincs bizonyosságunk, hogy az ügyfelünk mindkét tárolót telepítette, nem feltételezzük, hogy az ügyfeleink telephelyén lévő tárolók között vezénylésre kerül, és ha mindkét tároló telepítve van a prem-en, mivel azok helyibbek az ügyfél számára, nem teher az SR-t először visszavinni az ügyfélhez, és az ügyfélnek ezt a szöveget kell vennie, és a LUIS-hoz kell mennie.

<br>
</details>

<details>
<summary>
<b>Miért kapunk hibákat a macOS, a Beszédtároló és a Python SDK használatával?</b>
</summary>

Amikor egy *.wav* fájlt küldünk átírásra, az eredmény a következő:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Tudjuk, hogy a websocket megfelelően van beállítva.

**Válasz:** Ha ez a helyzet, akkor lásd [ezt a GitHub-problémát.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) Van egy work-around, [javasolt itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Carbon rögzített ez a változat 1.8.


<br>
</details>

<details>
<summary>
<b>Miak a különbségek a beszédtároló végpontjaiban?</b>
</summary>

Tudna segíteni töltse ki a következő teszt metrikák, beleértve a milyen funkciókat kell tesztelni, és hogyan kell tesztelni az SDK és a REST API-k? Különösen az "interaktív" és a "beszélgetés" különbségei, amelyeket nem láttam a meglévő doc /mintából.

| Végpont                                                | Funkcionális vizsgálat                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Szöveg szintetizálása (szövegfelolvasás)                                  |     | Igen      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services on-prem diktálás v1 websocket végpont        | Igen | Nem       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | A Cognitive Services on-prem interaktív v1 websocket végpont  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | A kognitív szolgáltatások on-prem beszélgetés v1 websocket végpont |     |          |

**Válasz:** Ez a fúzió:
- Emberek kipróbálás a diktálás végpont részére konténer, ( Im' nem persze hogyan ők kapott amit URL)
- Az<sup>első</sup> fél végpontja a tárolóban lévő.
- Az első<sup>fél</sup> végpontja, amely a speech.fragment üzeneteket adja vissza a `speech.hypothesis` 3<sup>rd</sup> rd part végpontok helyett a diktálási végponthoz.
- A Carbon rövid `RecognizeOnce` útmutatók minden használat (interaktív mód)
- Carbon, amelynek `speech.fragment` azt állítják, hogy az üzenetek igénylő nem tér vissza interaktív módban.
- Szén, amelynek azt állítja, tűz kiadás épít (megölve a folyamatot).

A megoldás vagy a kód folyamatos felismerésének használatára való áttérés, vagy (gyorsabb) csatlakozás a tároló interaktív vagy folyamatos végpontjaihoz.
A kód, állítsa be a végpontot <host:port>/speech/recognition/interactive/cognitiveservices/v1

A különböző módokat lásd: Beszédmódok – lásd alább:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A megfelelő javítás jön sdk 1.8, amely on-prem támogatás (felveszi a megfelelő végpontot, így nem lesz rosszabb, mint az online szolgáltatás). Addig is, van egy minta a folyamatos felismeréshez, miért nem mutatunk rá?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Melyik módot használjam a különböző hangfájlokhoz?</b>
</summary>

**Válasz:** Az [alábbiakban rövid útmutatót olvashat a Python használatával.](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python) A többi hivatkozott nyelvet a dokumentumoldalon találja.

Csak hogy tisztázza az interaktív, beszélgetés, és diktálás; ez egy fejlett módja annak, hogy meghatározza a konkrét módon, ahogy a szolgáltatás kezeli a beszéd kérelmet. Sajnos az on-prem tárolók ban meg kell adni a teljes URI-t (mivel tartalmazza a helyi gépet), így ez az információ kiszivárgott az absztrakcióból. Együttműködünk az SDK csapatával, hogy ezt a jövőben még használhatóbbá tegyük.

<br>
</details>

<details>
<summary>
<b>Hogyan tudjuk viszonyítási alap egy durva intézkedés tranzakciók / második / mag?</b>
</summary>

**Válasz:** Íme néhány durva számok számíthatnak a meglévő modell (meg fog változni a jobb az egyik fogunk hajó GA):

- A fájlok, a szabályozás lesz a beszéd SDK, a 2x. Az első öt másodpercben a hang nem szabályozott. Dekóder képes megtenni körülbelül 3x valós időben. Ehhez a teljes CPU-használat közel 2 mag lesz egyetlen felismeréshez.
- A mikrofon, ez lesz a 1x valós időben. A teljes használat nak körülbelül 1 maggal kell lennie egyetlen felismeréshez.

Ez mind ellenőrizhető a docker-naplókból. Mi valójában dump a sort a munkamenet és a kifejezés / utterance statisztika, és amely magában foglalja az RTF számokat.


<br>
</details>

<details>
<summary>
<b>Gyakori, hogy a hangfájlokat tokmányokra osztja a beszédtároló használata érdekében?</b>
</summary>

A jelenlegi tervem az, hogy egy meglévő hangfájlt szétosztok 10 másodperces darabokra, és elküldöm azokat a konténeren keresztül. Ez elfogadható forgatókönyv?  Van jobb módja a nagyobb hangfájlok feldolgozásának a tárolóval?

**Válasz:** Csak használja a beszéd SDK és adja meg a fájlt, akkor nem a helyes dolgot. Miért kell megdarabolni a fájlt?


<br>
</details>

<details>
<summary>
<b>Hogyan lehet több tárolót futtatni ugyanazon a gazdagépen?</b>
</summary>

A doki azt mondja, hogy tegye ki egy másik port, amit én, de a LUIS konténer még mindig hallgat port 5000?

**Válasz:** Próbálja `-p <outside_unique_port>:5000`ki. Például: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technikai kérdések

<details>
<summary>
<b>Hogyan kaphatok nem kötegelt API-kat a 15 másodperces hang &lt;kezelésére?</b>
</summary>

**Válasz:** Ez interaktív módban van. Ha diktálást vagy beszélgetést használ, az nem jelent problémát.


<br>
</details>

<details>
<summary>
<b>Melyek az ajánlott erőforrások, CPU és RAM; 50 egyidejű kérelemesetén?</b>
</summary>

Hány egyidejű kérés lesz egy 4 magos, 4 GB RAM kezelni? Ha például 50 egyidejű kérést kell kiszolgálnunk, hány Core és RAM ajánlott?

**Válasz:** Valós időben, 8 a `en-US`legújabb , ezért javasoljuk, hogy több docker-tárolók túl 6 egyidejű kérelmek. 16 magnál nagyobb lesz, és nem egységes memória-hozzáférési (NUMA) csomópontérzékenylesz. Az alábbi táblázat az egyes beszédtárolók erőforrásainak minimális és ajánlott lefoglalását ismerteti.

# <a name="speech-to-text"></a>[Diktálás](#tab/stt)

| Tároló      | Minimális             | Ajánlott         |
|----------------|---------------------|---------------------|
| Diktálás | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerési szöveg](#tab/cstt)

| Tároló             | Minimális             | Ajánlott         |
|-----------------------|---------------------|---------------------|
| Egyéni beszédfelismerési szöveg | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

| Tároló      | Minimális             | Ajánlott         |
|----------------|---------------------|---------------------|
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelolvasás](#tab/ctts)

| Tároló             | Minimális             | Ajánlott         |
|-----------------------|---------------------|---------------------|
| Egyéni szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

***

- Minden magnak legalább 2,6 GHz-nek vagy gyorsabbnak kell lennie.
- A fájlok esetében a szabályozás a beszédbeszéd SDK-ban lesz, 2x (a hang első 5 másodperce nincs szabályozva).
- A dekóder képes körülbelül 2-3x valós időben. Ehhez a teljes CPU-használat közel lesz két maghoz egyetlen felismeréshez. Ezért nem javasoljuk, hogy tárolópéldányonként kettőnél több aktív kapcsolatot tartson meg. A szélsőséges oldalon az lenne, hogy körülbelül 10 dekóderek 2x `DS13_V2`valós időben egy nyolc maggép, mint . A konténer 1.3-as és újabb verzió, van egy `DECODER_MAX_COUNT=20`param megpróbálhatja beállítás .
- A mikrofon, ez lesz a 1x valós időben. Az általános használat nak körülbelül egy magból kell lennie az egyetlen felismeréshez.

Vegye figyelembe, hogy hány órányi hanggal rendelkezik. Ha a szám nagy, a megbízhatóság/rendelkezésre állás javítása érdekében azt javasoljuk, hogy több példányt futjon a tárolók, akár egy doboz, vagy több doboz mögött terheléselosztó. Vezénylési lehet tenni kubernetes (K8S) és helm, vagy a Docker komponál.

Például 1000 óra/24 óra kezeléséhez megpróbáltuk 3-4 virtuális gép beállítását, virtuális gépenként 10 példány/dekóderek segítségével.

<br>
</details>

<details>
<summary>
<b>A beszédtároló támogatja az írásjeleket?</b>
</summary>

**Válasz:** A nagybetűs séma (ITN) elérhető az on-prem tárolóban. Az írásjelek nyelvfüggőek, és egyes nyelveken, például a kínai és a japán nyelveken nem támogatottak.

Van *do* implicit és alapvető írásjelek támogatása a meglévő tárolók, de `off` alapértelmezés szerint. Ez azt jelenti, hogy `.` a példádban a `。` karaktert kaphatod meg, de a karaktert nem. Az implicit logika engedélyezéséhez az alábbiakban egy példa arra, hogyan kell ezt megtenni a Pythonban a Speech SDK használatával (más nyelveken is hasonló lenne):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Miért kapok 404-es hibákat, amikor beszéd-szöveg tárolóba próbálok adatokat közzétenni?</b>
</summary>

Íme egy példa HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Válasz:** Egyik beszéd-szöveg tárolóban sem támogatjuk a REST API-t, csak a WebSocketeket támogatjuk a Speech SDK-n keresztül. Mindig olvassa el a hivatalos dokumentációt, [lásd: lekérdezés-előrejelzési végpontok](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>A beszéd-szöveg szolgáltatás használatakor miért jelenik meg ez a hibaüzenet?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Válasz:** Ez általában akkor fordul elő, ha a hangot gyorsabban táplálja, mint ahogy azt a beszédfelismerési tároló el tudja helyezni. Az ügyfélpufferek megtelnek, és a törlés aktiválódik. Ellenőriznie kell az egyidejűséget és az RTF-et, amelyre a hangot elküldi.

<br>
</details>

<details>
<summary>
<b>Meg tudná magyarázni ezeket a szövegfelolvasó tároló hibákat a C++ példákból?</b>
</summary>

**Válasz:** Ha a tároló verzió régebbi, mint 1.3, akkor ezt a kódot kell használni:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

A régebbi tárolók nem rendelkeznek a carbon `FromHost` api-val való kapcsolathoz szükséges végponttal. Ha az 1.3-as verzióhoz használt tárolóedényeket kell használni, akkor ezt a kódot kell használni:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Az alábbiakban egy `FromEndpoint` példa az API használatára:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A `SetSpeechSynthesisVoiceName` függvény neve, mert a frissített szövegfelolvasó motorral rendelkező tárolókhoz szükség van a hangnévre.

<br>
</details>

<details>
<summary>
<b>Hogyan használhatom a beszédsdka 1.7-es részét egy beszédtárolóval?</b>
</summary>

**Válasz:** A beszédfelismerési tárolóban három végpont van a különböző használatokhoz, amelyek beszédmódokként vannak definiálva – lásd alább:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Ezek különböző célokra, és használják másképp.

Python [minták](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Egyéni végponttal (azaz: egyfelismeréses (interaktív mód) `SpeechConfig` végpontparaméterrel), lásd: `speech_recognize_once_from_file_with_custom_endpoint_parameters()`.
- A folyamatos felismeréshez (beszélgetési mód), és csak módosítsa, `speech_recognize_continuous_from_file()`hogy egy egyéni végpontot használjon a fentiek szerint, lásd: .
- Ha engedélyezni szeretné a diktálást a fentihez hasonló mintákban `speech_config`(csak `speech_config.enable_dictation()`akkor, ha valóban szüksége van rá), közvetlenül a létrehozása után adja hozzá a kódot.

A C# a diktálás engedélyezéséhez hívja meg a `SpeechConfig.EnableDictation()` függvényt.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| Nyelv | API-részletek |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Jelenleg nem támogatott, és nem is tervezik. |

<br>
</details>

<details>
<summary>
<b>Hogyan használhatom a beszédsdka 1.8-as részét egy beszédtárolóval?</b>
</summary>

**Válasz:** Van egy új `FromHost` API. Ez nem helyettesíti és nem módosítja a meglévő API-kat. Ez csak összead egy vagylagos út -hoz teremt egy beszéd config használ egy szokás házigazda.

### <a name="fromhost-apis"></a>`FromHost`Api

| Nyelv | API-részletek |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Egyelőre nem támogatott |

> Paraméterek: állomás (kötelező), előfizetési kulcs (nem kötelező, ha használhatja a szolgáltatást nélküle).

Az állomás `protocol://hostname:port` formátuma nem `:port` kötelező (lásd alább):
- Ha a tároló helyileg fut, `localhost`a állomásnév a .
- Ha a tároló távoli kiszolgálón fut, használja a kiszolgáló állomásnevét vagy IPv4-címét.

Állomásparaméter-példák a szöveggé való beszédhez:
- `ws://localhost:5000`- nem biztonságos csatlakozás helyi tárolóhoz az 5000-es port használatával
- `ws://some.host.com:5000`- nem biztonságos kapcsolat egy távoli kiszolgálón futó tárolóval

Python minták felülről, `host` de `endpoint`használja paraméter helyett:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Cognitive Services-tárolók](speech-container-howto.md)
