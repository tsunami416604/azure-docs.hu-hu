---
title: Beszédszintetizáló nyelv (SSML) – beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédszintetizáló nyelv használata a kiejtés és a proodikia szabályozására a szövegfelolvasásban.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: dc11d26c73c52b5e6c4d8e05cc27dd6ebce0c5d8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399822"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>A szintézis javítása a beszédszintetizáló nyelvi (SSML) segítségével

A beszédszintetizáló nyelv (SSML) egy XML-alapú jelölőnyelv, amely lehetővé teszi a fejlesztők számára, hogy a szövegfelolvasó szolgáltatás segítségével meghatározzák, hogyan konvertálja a program a bemeneti szöveget szintetizált beszédmé. Az egyszerű szöveghez képest az SSML lehetővé teszi a fejlesztők számára, hogy finomhangolják a hangmagasságot, a kiejtést, a beszédarányt, a hangerőt és még sok mást a szövegfelolvasó kimenetből. A normál írásjeleket, például egy időszak után szüneteltetést, vagy a helyes hanglejtést, amikor egy mondat kérdőjellel végződik, a rendszer automatikusan kezeli.

Az SSML beszédszolgáltatás-megvalósítása a World Wide Web Consortium [1.0-s verziójú beszédszintézis-jelölőnyelvi verzióján](https://www.w3.org/TR/speech-synthesis)alapul.

> [!IMPORTANT]
> A kínai, a japán és a koreai karakterek a számlázás két karakteré minősülnek. További információ: [Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Normál, neurális és egyéni hangok

Válasszon a normál és a neurális hangok közül, vagy hozzon létre saját, a termékére vagy márkájára vonatkozó egyéni hangot. Több mint 75 standard hang érhető el több mint 45 nyelven és területi területen, és 5 neurális hang négy nyelven és nyelven. A támogatott nyelvek, területi beállítások és hangok (neurális és szabványos) teljes listáját a [nyelvi támogatás című témakörben tetszés szerint.](language-support.md)

Ha többet szeretne megtudni a normál, neurális és egyéni hangokról, olvassa el [a Szövegfelolvasás – áttekintés című témakört.](text-to-speech.md)

## <a name="special-characters"></a>Speciális karakterek

Az SSML használata közben ne feledje, hogy a speciális karaktereket, például az idézőjeleket, az aposztrófokat és a szögletes zárójeleket meg kell kerülni. További információ: [Extensible Markup Language (XML) 1.0: D függelék](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Támogatott SSML-elemek

Minden SSML-dokumentum SSML-elemekkel (vagy címkékkel) jön létre. Ezek az elemek beállítására használják pályán, prosody, hangerő, és így tovább. A következő szakaszok részletezik, hogyan használják az egyes elemeket, és mikor van szükség egy elemre, vagy mikor nem kötelező.  

> [!IMPORTANT]
> Ne felejtsen el dupla idézőjeleket használni az attribútumértékek körül. A jól formázott, érvényes XML-re vonatkozó szabványok megkövetelik, hogy az attribútumértékeket idézőjelek közé kell tenni. Például `<prosody volume="90">` egy jól formázott, érvényes `<prosody volume=90>` elem, de nem. Előfordulhat, hogy az SSML nem ismeri fel azokat az attribútumértékeket, amelyek nem szerepelnek idézőjelek között.

## <a name="create-an-ssml-document"></a>SSML-dokumentum létrehozása

`speak`a gyökérelem, és minden SSML-dokumentumhoz **szükséges.** Az `speak` elem fontos információkat tartalmaz, például verziót, nyelvet és a jelölő szókincs definícióját.

**Szintaxis**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `version` | A dokumentumjelölés értelmezéséhez használt SSML-specifikáció verzióját jelzi. A jelenlegi verzió 1.0. | Kötelező |
| `xml:lang` | Megadja a gyökérdokumentum nyelvét. Az érték tartalmazhat kisbetűs, kétbetűs nyelvkódot `en`(például ), vagy a nyelvkódot és `en-US`a nagybetűs országot/régiót (például ). | Kötelező |
| `xmlns` | Megadja az SSML-dokumentum jelölőszókincsét (az elemtípusokat és attribútumneveket) meghatározó dokumentum URI-ját. A jelenlegi http://www.w3.org/2001/10/synthesisURI a . | Kötelező |

## <a name="choose-a-voice-for-text-to-speech"></a>Hang kiválasztása a szövegfelolvasáshoz

Az `voice` elem szükséges. A szövegfelolvasáshoz használt hang megadására szolgál.

**Szintaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `name` | A szövegfelolvasáshoz használt hangot azonosítja. A támogatott hangok teljes listáját a Nyelvi támogatás című témakörben [található.](language-support.md#text-to-speech) | Kötelező |

**Példa**

> [!NOTE]
> Ez a `en-US-AriaRUS` példa a hangot használja. A támogatott hangok teljes listáját a Nyelvi támogatás című témakörben [található.](language-support.md#text-to-speech)

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Több hang használata

Az `speak` elemen belül több hangot is megadhat a szövegfelolvasáshoz. Ezek a hangok lehetnek különböző nyelveken. A szöveget minden hanghoz egy `voice` elembe kell tördelni. 

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `name` | A szövegfelolvasáshoz használt hangot azonosítja. A támogatott hangok teljes listáját a Nyelvi támogatás című témakörben [található.](language-support.md#text-to-speech) | Kötelező |

> [!IMPORTANT]
> Több hang nem kompatibilis a szóhatár funkcióval. A szóhatár funkciót le kell tiltani ahhoz, hogy több hangot használhasson.

### <a name="disable-word-boundary"></a>Szóhatár letiltása

A beszédsdk a nyelvtől függően `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` a `false` tulajdonságot az `SpeechConfig` objektum egy példányára állítja be.

# <a name="c"></a>[C #](#tab/csharp)

További információ: <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

További információ: <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Beszédstílusok beállítása

> [!IMPORTANT]
> A beszédstílusok beállítása csak neurális hangokkal működik.

Alapértelmezés szerint a szövegfelolvasó szolgáltatás semleges beszédstílussal szintetizálja a szöveget mind a normál, mind a neurális hangokhoz. Az idegi hangokkal beállíthatja a beszédstílust, hogy kifejezze `<mstts:express-as>` a vidámságot, az empátiát vagy az érzelmeket az elemmel. Ez egy választható elem, amely a beszédfelismerési szolgáltatásban egyedülálló.

Jelenleg a beszédstílus-beállítások támogatottak az alábbi neurális hangokesetén:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`

A módosítások a mondat szintjén lesznek alkalmazva, és a stílus hangonként változik. Ha egy stílus nem támogatott, a szolgáltatás az alapértelmezett semleges beszédstílusban adja vissza a beszédet.

**Szintaxis**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `style` | Megadja a beszédstílust. Jelenleg a beszédstílusok hangspecifikusak. | Szükséges, ha a beszédstílust neurális hanghoz igazítja. Ha `mstts:express-as`a használatát használja, akkor meg kell adni a stílust. Ha érvénytelen értéket ad meg, a rendszer figyelmen kívül hagyja ezt az elemet. |

Ebben a táblázatban határozhatja meg, hogy mely beszédstílusok támogatottak az egyes neurális hangok esetében.

| Hang                   | Stílus                     | Leírás                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast"`        | Kifejezi a formális és szakmai hangon narrátor hírek |
|                         | `style="customerservice"` | Barátságos és segítőkész hangnemet ad az ügyfélszolgálatnak  |
|                         | `style="chat"`            | Alkalmi és nyugodt hangot ad                         |
|                         | `style="cheerful"`        | Pozitív és boldog hangot ad                         |
|                         | `style="empathetic"`      | Kifejezi a törődés és a megértés érzését               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Kifejezi a formális és szakmai hangon narrátor hírek |
|                         | `style="customerservice"` | Barátságos és segítőkész hangnemet ad az ügyfélszolgálatnak  |
|                         | `style="assistant"`       | Meleg és nyugodt hangot ad a digitális asszisztensek számára    |
|                         | `style="lyrical"`         | Dallamos és szentimentális módon fejezi ki az érzelmeket         |

**Példa**

Ez az SSML-kódrészlet `<mstts:express-as>` azt mutatja be, hogy `cheerful`az elem hogyan módosítja a beszédstílust .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Szünet/szünet hozzáadása vagy eltávolítása

Az `break` elem segítségével szüneteket (vagy töréseket) szúrhat be a szavak közé, vagy megakadályozhatja a szövegfelolvasó szolgáltatás által automatikusan hozzáadott szünetekhasználatát.

> [!NOTE]
> Ezzel az elemmel felülbírálhatja a szövegfelolvasás (TTS) alapértelmezett viselkedését egy szó vagy kifejezés esetében, ha az adott szó vagy kifejezés szintetizált beszéde természetellenesnek hangzik. Úgy `strength` `none` van beállítva, hogy megakadályozza a prozodikus törést, amelyet a szövegfelolvasó szolgáltatás automatikusan beszúr.

**Szintaxis**

```xml
<break strength="string" />
<break time="string" />
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `strength` | A szünet relatív időtartamát az alábbi értékek egyikének használatával adja meg:<ul><li>Nincs</li><li>x-gyenge</li><li>Gyenge</li><li>közepes (alapértelmezett)</li><li>Erős</li><li>x-erős</li></ul> | Optional |
| `time` | A szünet abszolút időtartamát adja meg másodpercben vagy ezredmásodpercben. Az érvényes értékek `2s` repronyai:`500` | Optional |

| Erejét                      | Leírás |
|-------------------------------|-------------|
| Nincs, vagy ha nincs megadva érték | 0 ms        |
| x-gyenge                        | 250 ms      |
| Gyenge                          | 500 ms      |
| közepes                        | 750 ms      |
| Erős                        | 1000 ms     |
| x-erős                      | 1250 ms     |

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Bekezdések és mondatok megadása

`p`bekezdések, `s` illetve mondatok jelölésére szolgálnak. Ezen elemek hiányában a szövegfelolvasó szolgáltatás automatikusan meghatározza az SSML-dokumentum szerkezetét.

Az `p` elem szöveget és a `audio` `break`következő `phoneme` `prosody`elemeket `sub` `mstts:express-as`tartalmazhat: `s`, , , `say-as`, , , , és .

Az `s` elem szöveget és a `audio` `break`következő `phoneme` `prosody`elemeket tartalmazhat: , , , `say-as`, , , `mstts:express-as`és `sub`.

**Szintaxis**

```XML
<p></p>
<s></s>
```

**Példa**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Fonálékok használata a kiejtés javításához

Az `ph` elem az SSML-dokumentumok fonetikus kiejtéséhez használható. Az `ph` elem csak szöveget tartalmazhat, más elemeket nem. Mindig adjon ember által olvasható beszédet tartalékként.

A fonetikus ábécék telefonokból állnak, amelyek betűkből, számokból vagy karakterekből állnak, néha kombinálva. Minden telefon egyedi beszédhangot ír le. Ez ellentétben áll a latin ábécével, ahol bármely betű több kimondott hangot képviselhet. Tekintsük a "c" betű különböző kiejtéseit a "cukorka" és a "megszűnés" szavakban, vagy a "th" betűkombináció különböző kiejtését a "dolog" és az "azok" szavakban.

**Szintaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `alphabet` | Megadja azt a fonetikus ábécét, amelyet az `ph` attribútumban lévő karakterlánc kiejtésének szintetizálásakor kell használni. Az ábécét megmeghatározó karakterláncot kisbetűkkel kell megadni. A következő lehetséges ábécékadhatók meg.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Nemzetközi fonetikus ábécé <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [Beszédszolgáltatás fonetikus ábécé](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Univerzális telefonkészlet</li></ul><br>Az ábécé csak az `phoneme` elemben lévőre vonatkozik. | Optional |
| `ph` | Telefonokat tartalmazó karakterlánc, amely meghatározza a szó kiejtését az `phoneme` elemben. Ha a megadott karakterlánc ismeretlen telefonokat tartalmaz, a szövegfelolvasó (TTS) szolgáltatás elutasítja a teljes SSML-dokumentumot, és a dokumentumban megadott beszédkimenetet nem hoz létre. | Fonálékhasználata esetén kötelező. |

**Példák**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Egyéni lexikon használata a kiejtés javításához

Néha a TTS nem tud pontosan kiejteni egy szót, például egy vállalatot vagy egy idegen nevet. A fejlesztők az SSML segítségével `phoneme` és `sub` címkével definiálhatják ezeknek az entitásoknak az olvasását, `lexicon` vagy több entitás olvasását is definiálhatják egy egyéni lexikonfájlra hivatkozva.

**Szintaxis**

```XML
<lexicon uri="string"/>
```

**Attribútumok**

| Attribútum | Leírás                               | Kötelező / Nem kötelező |
|-----------|-------------------------------------------|---------------------|
| `uri`     | A külső PLS-dokumentum címe. | Kötelező.           |

**Használat**

1. lépés: Egyéni lexikon definiálása 

Az entitások olvasását az egyéni lexikonelemek listája határozza meg, amelyet .xml vagy .pls fájlként tárolnak.

**Példa**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Minden `lexeme` elem lexikonelem. `grapheme`a program ortográfát `lexeme`leíró szöveget tartalmaz. A kiolvasott űrlap `alias`a . A telefonhúr `phoneme` elemben is megadható.

Az `lexicon` elem legalább `lexeme` egy elemet tartalmaz. Minden `lexeme` elem legalább `grapheme` egy elemet és `grapheme` `alais`egy `phoneme` vagy több elemet és elemeket tartalmaz. Az `grapheme` elem az <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortográfiát <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>leíró szöveget tartalmaz. Az `alias` elemek egy betűszó vagy rövidített kifejezés kiejtésének jelzésére szolgálnak. Az `phoneme` elem a kiejtés módját `lexeme` leíró szöveget tartalmaz.

Az egyéni lexikonfájlról a [Kiejtéslexikon Specifikáció (PLS) 1.0-s verziójában](https://www.w3.org/TR/pronunciation-lexicon/) a W3C webhelyén talál további információt.

2. lépés: Töltse fel az [1.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

3. lépés: Tekintse meg az egyéni lexikonfájlt az SSML-ben

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" lesz olvasni, mint "By the way". A "Benigni" az IPA "baniンnji" felirattal lesz olvasható.  

**Korlátozás**
- Fájlméret: az egyéni lexikonfájl maximális maximális korlátja 100 KB, ha ezen a méreten túl a szintéziskérelem sikertelen lesz.
- Lexikon-gyorsítótár frissítése: az egyéni lexikon a TTS-szolgáltatás kulcsaként gyorsítótárazva lesz a TTS-szolgáltatás ban, amikor először betölti. Az azonos URI-val rendelkező lexikon nem töltődik be 15 percen belül, ezért az egyéni lexikonmódosításnak legbőleg15 percet kell várnia a hatálybalépéshez.

**Beszédszolgáltatás fonetikus készletei**

A fenti mintában a Nemzetközi Fonetikus Ábécét használjuk, más néven az IPA telefonkészletet. Javasoljuk, hogy a fejlesztők használják az IPA-t, mert ez a nemzetközi szabvány. Figyelembe véve, hogy az IPA nem könnyű megjegyezni, a beszédszolgáltatás`en-US`hét `fr-FR` `de-DE`nyelvre `zh-CN`( `zh-TW`, , `es-ES`, , `ja-JP`, , és .

Használhatja a `sapi` völgy az `alphabet` attribútum egyéni lexikonok az alábbiak szerint:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

A beszédszolgáltatás fonetikus ábécéjéről további információt a [Beszédszolgáltatás fonetikus készletei című témakörben talál.](speech-ssml-phonetic-sets.md)

## <a name="adjust-prosody"></a>Állítsa prosody

Az `prosody` elem a szövegfelolvasás kimenetének hangmagasságának, eloszlásának, tartományának, sebességének, időtartamának és hangerejének változásait adja meg. Az `prosody` elem szöveget és a `audio` `break`következő `p` `phoneme`elemeket `say-as` `sub`tartalmazhat: `s`, , , `prosody`, , , , és .

Mivel a prozodikus attribútumértékek széles tartományban változhatnak, a beszédfelismerő a hozzárendelt értékeket a kijelölt hang tényleges prozodikus értékeinek javaslataként értelmezi. A szövegfelolvasó szolgáltatás korlátozza vagy helyettesíti a nem támogatott értékeket. A nem támogatott értékek például 1 MHz-es hangmagasság vagy 120 térfogat.

**Szintaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `pitch` | A szöveg alapvonal-hangmagasságát jelzi. A hangmagasságot a következőképpen fejezheti ki:<ul><li>Abszolút érték, számban kifejezve, amelyet "Hz" (Hertz) követ. Például 600 Hz.</li><li>Relatív érték, számban kifejezve, amelyet "+" vagy "-" előz meg, majd "Hz" vagy "st", amely a hangmagasság megváltoztatására vonatkozó összeget határoz meg. Például: +80 Hz vagy -2st. Az "st" azt jelzi, hogy a változásegység szemiton, amely a standard diatonikus skálán lévő hang (fél lépés) fele.</li><li>Állandó érték:<ul><li>x-alacsony</li><li>Alacsony</li><li>közepes</li><li>magas</li><li>x-magas</li><li>alapértelmezett</li></ul></li></ul>. | Optional |
| `contour` | A kontúr nem támogatott az idegi hangok esetében. A kontúr a hangmagasság változásait jelöli. Ezek a változások a beszédkimenet megadott időpozícióiban céltömbként jelennek meg. Minden cél paraméterpárok készletei határozzák meg. Például: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paraméterek első értéke a szöveg időtartamának százalékában határozza meg a hangmagasság-változás helyét. A második érték a szurok emeléséhez vagy csökkentéséhez használt összeget adja meg, relatív érték vagy a szurok felsorolási értéke alapján (lásd `pitch`). | Optional |
| `range` | A szöveg hangmagassági tartományát jelölő érték. A leírásához használt abszolút értékeket, relatív értékeket vagy `pitch`felsorolási értékeket használva is kifejezheti. `range` | Optional |
| `rate` | A szöveg beszédi sebességét jelzi. A következőképpen fejezheti ki: `rate`<ul><li>Relatív érték, számban kifejezve, amely az alapértelmezett szorzóként működik. Az *1* érték például nem eredményez irát. A *0,5-ös* érték az arány felére csökken. A *3-as* érték az arány megháromszorozását eredményezi.</li><li>Állandó érték:<ul><li>x-lassú</li><li>Lassú</li><li>közepes</li><li>Gyors</li><li>x-gyors</li><li>alapértelmezett</li></ul></li></ul> | Optional |
| `duration` | Az az időtartam, amelynek el kell telnie, miközben a beszédszintézis (TTS) szolgáltatás másodpercben vagy ezredmásodpercben olvassa be a szöveget. Például *2s* vagy *1800ms*. | Optional |
| `volume` | A beszélő hang hangerőszintjét jelzi. A hangerőt a következőképpen fejezheti ki:<ul><li>Abszolút érték, 0,0 és 100,0 közötti számban kifejezve, a *legcsendesebbtől* a *leghangosabbig.* Például 75. Az alapértelmezett érték 100,0.</li><li>Relatív érték, számban kifejezve, amelyet "+" vagy "-" előz meg, és amely a kötet módosítására vonatkozó összeget ad meg. Például +10 vagy -5,5.</li><li>Állandó érték:<ul><li>Csendes</li><li>x-lágy</li><li>Puha</li><li>közepes</li><li>Hangos</li><li>x-hangos</li><li>alapértelmezett</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>A beszédarány módosítása

A beszédarány a szabványos hangokra alkalmazható a szó- vagy mondatszinten. Míg a beszédarány csak a mondat szintjén alkalmazható neurális hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Hangerő módosítása

A hangerő-módosítások a szabványos hangokra alkalmazhatók a szó- vagy mondatszinten. Míg a kötetváltozások csak a mondat szintjén alkalmazhatók az idegi hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Hangmagasság módosítása

A hangmagasság-módosítások a szabványos hangokra alkalmazhatók a szó- vagy mondatszinten. Míg a hangmagasság-változások csak a mondat szintjén alkalmazhatók az idegi hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>A hangmagasság kontúrjának módosítása

> [!IMPORTANT]
> Neurális hangok nem támogatják a hangmagasság-eloszlás változásait.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as elem

`say-as`választható elem, amely az elem szövegének tartalomtípusát (például számát vagy dátumát) jelzi. Ez útmutatást ad a szöveg kiejtésének módjáról szóló beszédszintetizáló motorhoz.

**Szintaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `interpret-as` | Az elem szövegének tartalomtípusát jelzi. A típusok listáját az alábbi táblázatban talál. | Kötelező |
| `format` | További információt tartalmaz az elem szövegének pontos formázásáról olyan tartalomtípusok esetében, amelyek nem egyértelmű formátumúak lehetnek. Az SSML az azokat használó tartalomtípusok formátumait határozza meg (lásd az alábbi táblázatot). | Optional |
| `detail` | A kimondandó részletességi szintet jelzi. Ez az attribútum például kérheti, hogy a beszédszintetizáló motor kiejtse az írásjeleket. A hoz `detail`nem definiált szabványos értékeket. | Optional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A következő a támogatott tartalomtípusok a `interpret-as` és `format` attribútumok. Csak `format` akkor adja `interpret-as` meg az attribútumot, ha dátum és idő van beállítva.

| interpret-as | Formátum | Értelmezés |
|--------------|--------|----------------|
| `address` | | A szöveg címeként kerül kimondásra. A beszédszintézis motor kimondja:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"A 150. |
| `cardinal`, `number` | | A szöveg et a program kardinális számként mondja ki. A beszédszintézis motor kimondja:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Mint "Három alternatíva van." |
| `characters`, `spell-out` | | A szöveg et egyedi betűkként kell kimondani (beírva). A beszédszintézis motor kimondja:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Mint "T E S T.". |
| `date` | dmy, mdy, ymd, ydm, ym, én, md, dm, d, m, y | A szöveg dátumként van kimondva. Az `format` attribútum a dátum formátumát adja meg (*d=nap, m=hónap és y=év*). A beszédszintézis motor kimondja:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Mivel "Ma október tizenkilencedik etizennyolcadik kétezertizenhat." |
| `digits`, `number_digit` | | A szöveg et az egyes számjegyek sorozataként kell kimondani. A beszédszintézis motor kimondja:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Mint "1 2 3 4 5 6 8 8 9." |
| `fraction` | | A szöveg et törtszámként kell kimondani. A beszédszintézis motor kimondja:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Mint "három nyolcad hüvelyk". |
| `ordinal` | | A szöveg szóorlistaszámként van kimondva. A beszédszintézis motor kimondja:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Mint "Válassza ki a harmadik opciót". |
| `telephone` | | A szöveg et telefonszámként kell kimondani. Az `format` attribútum tartalmazhat országkódot jelképező számjegyeket. Az Egyesült Államokban például az "1", Olaszország esetében "39". A beszédszintetizáló motor ezt az információt használhatja a telefonszám kiejtésének irányítására. A telefonszám az országkódot is tartalmazhatja, és ha igen, elsőbbséget `format`élvez a . A beszédszintézis motor kimondja:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Mint "Az én számom a 8-as körzetszám nyolc nyolc nyolc öt öt egy kettő egy kettő." |
| `time` | hms12, hms24 | A szöveg et idő szerint kell kimondani. Az `format` attribútum azt határozza meg, hogy az idő 12 órás (hms12) vagy 24 órás (hms24) értékkel van-e megadva. Kettőspont használatával elválaszthatja az órákat, perceket és másodperceket jelölő számokat. A következők érvényes időkre vonatkozó példák: 12:35, 1:14:32, 08:15 és 02:50:45. A beszédszintézis motor kimondja:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Mint "A vonat hajnali négykor indul." |

**Használat**

Az `say-as` elem csak szöveget tartalmazhat.

**Példa**

A beszédszintézis motor javunkra a következő példát: "Az első kérés volt egy szoba október tizenkilencedik tizenkilenctíz a korai érkezés tizenkét harmincöt PM."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Rögzített hang hozzáadása

`audio`egy választható elem, amely lehetővé teszi, hogy MP3 hangot szúrjon be egy SSML-dokumentumba. A hangelem törzse egyszerű szöveget vagy SSML-jelölést tartalmazhat, amelyet akkor szólaltat meg, ha a hangfájl nem érhető el vagy nem játszható le. Ezenkívül az `audio` elem szöveget és a `audio` `break`következő `p` `s`elemeket is tartalmazhat: , , , , `phoneme`, `prosody`, , `say-as`, és `sub`.

Az SSML-dokumentumban található hanganyagoknak meg kell felelniük az alábbi követelményeknek:

* Az MP3-nak egy interneten elérhető HTTPS-végponton kell üzemeltetni. HTTPS szükséges, és az MP3-fájlt tároló tartománynak érvényes, megbízható TLS/SSL-tanúsítványt kell bemutatnia.
* Az MP3-nak érvényes MP3-fájlnak (MPEG v2) kell lennie.
* Az átviteli sebességnek 48 kb/s-nak kell lennie.
* A mintavételi sebességnek 16 000 Hz-nek kell lennie.
* Az összes szöveg- és hangfájl összesített teljes ideje egyetlen válaszban nem haladhatja meg a kilencven (90) másodpercet.
* Az MP3 nem tartalmazhat ügyfélspecifikus vagy más érzékeny információt.

**Szintaxis**

```xml
<audio src="string"/></audio>
```

**Attribútumok**

| Attribútum | Leírás                                   | Kötelező / Nem kötelező                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Megadja a hangfájl helyét/URL-címét. | Az SSML-dokumentum hangelemének használata esetén szükséges. |

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Háttérhang hozzáadása

Az `mstts:backgroundaudio` elem lehetővé teszi háttérhang hozzáadását az SSML-dokumentumokhoz (vagy hangfájl szövegfelolvasással való keverését). A `mstts:backgroundaudio` háttérben hurkolhatja a hangfájlokat, elhalványulhat a szövegfelolvasás elején, és elhalványulhat a szövegfelolvasás végén.

Ha a megadott háttérhang rövidebb, mint a szövegfelolvasás vagy a kiürülék, akkor ismétlődik. Ha hosszabb, mint a szövegfelolvasás, akkor leáll, amikor a kiürül.

SSML-dokumentumonként csak egy háttérhangfájl engedélyezett. Az `voice` elemen belüli címkék `audio` etissza, hogy további hangot adjon az SSML-dokumentumhoz.

**Szintaxis**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `src` | A háttérhangfájl helyét/URL-címét adja meg. | Akkor szükséges, ha háttérhangot használ az SSML-dokumentumban. |
| `volume` | Megadja a háttérhangfájl hangerejét. **Elfogadott értékek:** `0` a `100` bezáróérték. Az alapértelmezett érték `1`. | Optional |
| `fadein` | Ezredmásodpercben adja meg a háttérhang "elhalványulása" időtartamát. Az alapértelmezett `0`érték a , amely egyenértékű a nincs fakulásnak. **Elfogadott értékek:** `0` a `10000` bezáróérték.  | Optional |
| `fadeout` | A háttérhang ezredmásodpercben megadott időtartamát adja meg. Az alapértelmezett `0`érték a , amely egyenértékű a nincs elhalványulás számának. **Elfogadott értékek:** `0` a `10000` bezáróérték.  | Optional |

**Példa**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>További lépések

* [Nyelvi támogatás: hangok, területi beállítások, nyelvek](language-support.md)
