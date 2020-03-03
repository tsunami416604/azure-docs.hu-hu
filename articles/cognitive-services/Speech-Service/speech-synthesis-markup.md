---
title: Speech szintézis Markup Language (SSML) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Használja a Speech összefoglaló Markup Language írásmódja és a szöveg-hang transzformációs prosody szabályozásához.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: dapine
ms.openlocfilehash: 499770b664757ec0f3a0bd3b26e0de36007741b6
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228076"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>A szintézis fejlesztése a Speech szintézis Markup Language (SSML) nyelvvel

A Speech szintézis Markup Language (SSML) egy XML-alapú Markup Language, amely lehetővé teszi a fejlesztők számára, hogy a szöveg-beszéd szolgáltatás segítségével hogyan konvertálja a bemeneti szöveget a szintetizált beszédbe. Az egyszerű szöveghez képest a SSML lehetővé teszi a fejlesztők számára a hangfelvétel, a kiejtés, a beszéd arány, a kötet és még több szöveg-beszéd típusú kimenet finomhangolását. Normál írásjelek, például egy időszak utáni felfüggesztés vagy a megfelelő hanglejtés használata, ha a mondatot kérdőjeltel végződik, automatikusan kezeli a rendszer.

A SSML beszédfelismerési szolgáltatásának megvalósítása a World Wide Web Consortium [Speech szintézis Markup Language 1,0-es verzióján](https://www.w3.org/TR/speech-synthesis)alapul.

> [!IMPORTANT]
> A kínai, Japán és koreai karakterek két karakterből állnak a számlázáshoz. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurális és egyéni hangok

Válasszon a standard és a neurális hangok közül, vagy hozzon létre saját egyéni hangját a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang négy nyelven és területi beállításban érhető el. A támogatott nyelvek, területi beállítások és hangok (neurális és standard) teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

A standard, a neurális és az egyéni hangok megismeréséhez lásd a [szöveg – beszéd áttekintést](text-to-speech.md).

## <a name="special-characters"></a>Speciális karakterek

A SSML használata során vegye figyelembe, hogy a speciális karaktereket, például az idézőjeleket, az aposztrófokat és a zárójeleket el kell kerülni. További információ [: Extensible Markup Language (XML) 1,0: D függelék](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Támogatott SSML elemek

Minden SSML-dokumentum SSML elemekkel (vagy címkékkel) jön létre. Ezek az elemek a Pitch, a prosody, a Volume és más beállítások módosítására szolgálnak. A következő szakaszokban részletesen ismertetjük az egyes elemek használatát, és ha egy elem megadása kötelező vagy nem kötelező.  

> [!IMPORTANT]
> Ne feledje, hogy idézőjelek között idézőjeleket kell használni. A jól formázott, érvényes XML-fájlokra vonatkozó szabványokhoz idézőjelek közé kell foglalni az attribútumot. Például `<prosody volume="90">` egy jól formázott, érvényes elem, de a `<prosody volume=90>` nem. Előfordulhat, hogy a SSML nem ismeri fel az idézőjelek között lévő attribútum-értékeket.

## <a name="create-an-ssml-document"></a>SSML-dokumentum létrehozása

`speak` a gyökérelem, és minden SSML-dokumentum esetében **kötelező** . A `speak` elem fontos információkat tartalmaz, például a verziószámot, a nyelvet és a Markup szókincs definícióját.

**Szintaxis**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `version` | Megadja a dokumentum korrektúrájának értelmezéséhez használt SSML-specifikáció verzióját. Az aktuális verzió 1,0. | Kötelező |
| `xml:lang` | Megadja a legfelső szintű dokumentum nyelvét. Az érték tartalmazhat kisbetűs, kétbetűs nyelvi kódot (például `en`), illetve a nyelvi kódot és a nagybetűs országot/régiót (például `en-US`). | Kötelező |
| `xmlns` | Megadja a dokumentumhoz tartozó URI-t, amely meghatározza a SSML dokumentum Markup szókincsét (az elemek típusát és az attribútum nevét). Az aktuális URI https://www.w3.org/2001/10/synthesis. | Kötelező |

## <a name="choose-a-voice-for-text-to-speech"></a>Hang kiválasztása szöveg és beszéd között

A `voice` elem megadása kötelező. A szövegről beszédre használt hang megadására szolgál.

**Szintaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `name` | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Kötelező |

**Példa**

> [!NOTE]
> Ez a példa a `en-US-Jessa24kRUS` hangot használja. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg.

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Több beszédhangot használata

A `speak` elemen belül több hang is megadható a szöveg-beszéd kimenethez. Ezeket a hangokat különböző nyelveken lehet megtekinteni. Minden hang esetében a szöveget `voice` elembe kell becsomagolni. 

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `name` | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Kötelező |

> [!IMPORTANT]
> Több hang nem kompatibilis a szó határával. Több hang használata érdekében le kell tiltani a szó határát.

### <a name="disable-word-boundary"></a>Szó határának letiltása

A beszédfelismerési SDK nyelvétől függően a `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` tulajdonságot úgy állítja be, hogy `false` az `SpeechConfig` objektum egy példányán.

# <a name="c"></a>[C#](#tab/csharp)

További információ: <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank">`SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank">`SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank">`setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank">`set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

További információ: <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank">`setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">`setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">`setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Beszéd stílusainak módosítása

> [!IMPORTANT]
> A beszélő stílusok beállítása csak neurális hangokat fog működni.

Alapértelmezés szerint a Text-to-speech szolgáltatás a standard és a neurális hangok semleges beszéd stílusa alapján szintetizálja a szöveget. A neurális hangokat beállíthatja a beszélő stílusát, hogy kifejezze a vidámságot, az empátiát vagy a hangulatát a `<mstts:express-as>` elemmel. Ez egy opcionális elem, amely egyedi a beszédfelismerési szolgáltatáshoz.

A jelen neurális hangok esetében jelenleg a beszélő stílusának módosításait támogatja a rendszer:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

A módosításokat a rendszer a mondat szintjén alkalmazza, a stílus pedig hangon változik. Ha a stílus nem támogatott, a szolgáltatás az alapértelmezett semleges beszéd stílusát fogja visszaadni.

**Szintaxis**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `type` | Megadja a beszéd stílusát. A beszélő stílusok jelenleg hangspecifikusak. | Akkor szükséges, ha a beszélő stílust módosítja egy neurális hanghoz. `mstts:express-as`használata esetén a típust meg kell adni. Ha a megadott érték érvénytelen, akkor a rendszer figyelmen kívül hagyja ezt az elemet. |

Ebből a táblázatból megállapíthatja, hogy az egyes neurális hangfelismerések milyen beszélő stílusokat támogatnak.

| Hang | Típus | Leírás |
|-------|------|-------------|
| `en-US-JessaNeural` | `type="cheerful"` | Pozitív és boldog érzelemet ad ki |
| | `type="empathy"` | A gondozás és a megértés értelme |
| | `type="chat"` | Beszéljen egy hétköznapi, nyugodt hangnemben |
| | `type="newscast"` | Egy formális hangjelzést ad a hírekhez hasonló módon |
| | `type="customerservice"` | Beszéljen barátságos és türelmes módon az ügyfélszolgálatnál |
| `zh-CN-XiaoxiaoNeural` | `type="newscast"` | Egy formális hangjelzést ad a hírekhez hasonló módon |
| | `type="sentiment"` | Egy megható üzenetet vagy egy történetet közvetít |

**Példa**

Ez a SSML-kódrészlet azt szemlélteti, hogy a `<mstts:express-as>` elem hogyan módosítható a beszéd stílusa `cheerful`re.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Szünet/szünet hozzáadása vagy eltávolítása

A `break` elem használatával szüneteltetheti a szavak közötti szüneteltetéseket (vagy megszakításokat), vagy megakadályozhatja, hogy a szöveg-beszéd szolgáltatás automatikusan hozzáadja a szüneteltetéseket.

> [!NOTE]
> Ezzel az elemmel felülbírálhatja egy szó vagy kifejezés szöveg-beszédre (TTS) vonatkozó alapértelmezett viselkedését, ha az adott szó vagy kifejezés szintetizált beszéde természetellenesen hangzik. Állítsa be úgy a `strength`t, hogy `none`, hogy megakadályozza a prosodic megszakítást, amelyet a rendszer automatikusan beszúr a szöveg-beszéd szolgáltatásba.

**Szintaxis**

```xml
<break strength="string" />
<break time="string" />
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `strength` | Meghatározza a Szüneteltetés relatív időtartamát az alábbi értékek egyikének használatával:<ul><li>nincs</li><li>x – gyenge</li><li>gyenge</li><li>közepes (alapértelmezett)</li><li>erős</li><li>x – erős</li></ul> | Optional |
| `time` | Megadja a szünet időtartamát másodpercben vagy ezredmásodpercben. Érvényes értékek például `2s` és `500` | Optional |

| Erősségét | Leírás |
|----------|-------------|
| Nincs, vagy ha nincs megadva érték | 0 MS |
| x – gyenge | 250 MS |
| gyenge | 500 ms |
| közepes | 750 ms |
| erős | 1000 MS |
| x – erős | 1250 MS |


**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Bekezdések és mondatok meghatározása

a `p` és `s` elemek a bekezdések és a mondatok jelölésére használatosak. Ezen elemek hiányában a szöveg-beszéd szolgáltatás automatikusan meghatározza a SSML-dokumentum szerkezetét.

A `p` elem tartalmazhat szöveget és a következő elemeket: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`és `s`.

A `s` elem tartalmazhat szöveget és a következő elemeket: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`és `sub`.

**Szintaxis**

```XML
<p></p>
<s></s>
```

**Példa**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>A fonémák használata a kiejtés javítására

A `ph` elem a SSML-dokumentumok fonetikus kiejtéséhez használatos. A `ph` elem csak szöveget tartalmazhat, más elemeket nem. Az emberi olvasásra alkalmas beszédet mindig tartalékként adja meg.

A fonetikus ábécék olyan telefonokból állnak, amelyek betűkből, számokból vagy karakterből állnak, esetenként kombinációban. Minden telefon egyedi hangfelismerést ír elő. Ez ellentétben áll a latin ábécével, ahol bármely betű több beszélt hangot is jelenthet. Vegye figyelembe a "c" betű különböző kiejtéseit a "candy" és a "megszüntetés" kifejezésben, vagy a "th" betű kombinációjának különböző kiejtéseit a "Thing" és a "The" kifejezésben.

**Szintaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `alphabet` | Meghatározza a karakterlánc kiejtésének szintetizálása során használandó fonetikus ábécét a `ph` attribútumban. Az ábécét megadó karakterláncot kisbetűs betűkkel kell megadni. Az alábbiakban megadhatja a lehetséges ábécéket.<ul><li>IPA &ndash; nemzetközi fonetikus ábécé</li><li>SAPI &ndash; Speech API telefonos készlet</li><li>UPS &ndash; univerzális telefonvonal</li></ul>Az ábécé csak az elem fonéma vonatkozik. További információ: [fonetikus ábécé referenciája](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Optional |
| `ph` | Olyan telefonszámot tartalmazó karakterlánc, amely a szó kiejtését határozza meg a `phoneme` elemben. Ha a megadott karakterlánc nem felismerhető telefonokat tartalmaz, a szöveg-beszéd (TTS) szolgáltatás elutasítja a teljes SSML-dokumentumot, és a dokumentumban megadott egyik beszédfelismerési kimenetet sem hozza létre. | Fonémák használata esetén kötelező. |

**Példák**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>A kiejtés javítása egyéni lexikon használatával

Néha a TTS nem tudja pontosan kiejteni a szót, például egy vállalat vagy egy idegen név. A fejlesztők a SSML `phoneme` és `sub` címkével határozhatják meg az entitások olvasását, vagy megadhatják több entitás olvasását úgy, hogy egy egyéni lexikon-fájlra hivatkoznak `lexicon` címke használatával.

**Szintaxis**

```XML
<lexicon uri="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `uri` | A külső PLS-dokumentum címe. | Kötelező. |

**Használat**

1\. lépés: egyéni lexikon definiálása 

Az entitások olvasását megadhatja egy. XML vagy. pls fájlban tárolt egyéni lexikon elemek listája alapján.

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

Minden `lexeme` elem egy lexikon elem. a `grapheme` `lexeme`OrthoGraph leíró szöveget tartalmaz. A kiolvasási űrlap `alias`ként is megadható. A telefon karakterláncát `phoneme` elemben lehet megadni.

A `lexicon` elem legalább egy `lexeme` elemet tartalmaz. Minden `lexeme` elem legalább egy `grapheme` elemet tartalmaz, valamint egy vagy több `grapheme`, `alais`és `phoneme` elemet. A `grapheme` elem a <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">helyesírást <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>leíró szöveget tartalmaz. A `alias` elemek a betűszó vagy egy rövidített kifejezés kiejtésének jelzésére szolgálnak. A `phoneme` elem szöveget tartalmaz, amely leírja, hogy a `lexeme` hogyan lett kimondva.

További információ az egyéni lexikon-fájlról: a [kiejtési lexikon specifikációjának (pls) 1,0-es verziója](https://www.w3.org/TR/pronunciation-lexicon/) a W3C webhelyén.

2\. lépés: töltse fel az 1. lépésben létrehozott egyéni lexikont, amely bárhol tárolható, és javasoljuk, hogy Microsoft Azure, például az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)tárolja.

3\. lépés: a SSML-beli egyéni lexikon-fájlra vonatkozó hivatkozás

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
A "BTW" a következőképpen lesz beolvasva: "by the way". A "Benigni" a megadott "bɛ tɛsɨfɒn ni ː nji" IPA-vel lesz beolvasva.  

**Korlátozás**
- Fájlméret: az egyéni lexikon fájlméretének maximális korlátja 100 kb, ha ez meghaladja a méretet, a szintézisi kérelem sikertelen lesz.
- Lexikon gyorsítótárának frissítése: az egyéni lexikont a rendszer az első betöltéskor kulcsként fogja gyorsítótárazni a TTS szolgáltatásban. Az azonos URI-val rendelkező lexikon 15 percen belül nem lesz újratöltve, ezért az egyéni lexikon-módosításnak 15 percnél hosszabb ideig kell megvárnia, hogy érvénybe lépjen.

**SAPI-telefon készlete**

A fenti példában a nemzetközi fonetikus társítás (IPA) telefonvonalat használjuk. Javasoljuk, hogy a fejlesztők az IPA-t használják, mivel az IPA a nemzetközi szabvány. 

Figyelembe véve, hogy az IPA nem könnyű megjegyezni, a Microsoft definiálja a SAPI-telefont hét nyelvre (`en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN`és `zh-TW`). További ABC-információk: [fonetikus ábécé-hivatkozás](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx).

Az alábbi ábrán látható módon használhatja a SAPI-telefont egyéni lexikonokkal. Állítsa az ABC értéket a **SAPI**értékre.

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

A részletes SAPI-ábécével kapcsolatos további információkért tekintse meg a [SAPI ábécé-referenciát](sapi-phoneset-usage.md).

## <a name="adjust-prosody"></a>Prosody módosítása

A `prosody` elem a szöveg-beszéd kimenethez tartozó szurok, countour, tartomány, sebesség, időtartam és kötet változásainak megadására szolgál. A `prosody` elem tartalmazhat szöveget és a következő elemeket: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`és `s`.

Mivel a prosodic-attribútumok értékei a széles skálán változhatnak, a beszédfelismerő felismeri a hozzárendelt értékeket arra a javaslatra, hogy a kiválasztott hang tényleges prosodic értékei legyenek. A szöveg-beszéd szolgáltatás korlátozza vagy helyettesíti a nem támogatott értékeket. Példa a nem támogatott értékekre: 1 MHz vagy 120-es kötet.

**Szintaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `pitch` | Megadja a szöveg alappályáját. A szurok a következőképpen fejezhető ki:<ul><li>Abszolút érték, amelyet a szám a "Hz" (Hertz) kifejezéssel jelez. Például 600 Hz.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal, majd a "Hz" vagy "St" előtaggal jelöl, amely a szurok módosításához szükséges összeget határozza meg. Például: + 80 Hz vagy-2st. A "Szent" érték azt jelzi, hogy a változási egység semitone-e, amely a normál diatonikus-skálán lévő hang (fél lépés) fele.</li><li>Állandó érték:<ul><li>x – alacsony</li><li>alacsony</li><li>közepes</li><li>magas</li><li>x – magas</li><li>alapértelmezett</li></ul></li></ul>. | Optional |
| `contour` | A kontúr nem támogatott a neurális hangok esetében. A kontúr a szurok változásait jelöli. Ezek a változások a beszédfelismerési kimenetben megadott időpontokban a célok tömbje jelennek meg. Az egyes célkitűzéseket paraméter párok halmaza határozza meg. Például: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paraméterekben az első érték határozza meg a szurok változásának helyét a szöveg időtartamának százalékában. A második érték megadja a szurok növelésének vagy csökkentésének mértékét, egy relatív érték vagy a szurok enumerálási értéke alapján (lásd: `pitch`). | Optional |
| `range` | Egy érték, amely a szövegben lévő szurok tartományát jelöli. `range` a `pitch`leírásához használt értékek, relatív értékek vagy enumerálási értékek használatával is kifejezhető. | Optional |
| `rate` | Megadja a szöveg beszédi arányát. `rate` a következőképpen lehet kifejezni:<ul><li>Egy relatív érték, amely az alapértelmezett érték szorzóként funkcionál. Az *1* érték például nem változik a díjszabásban. Az *0,5* -es érték a sebesség felére csökkentéset eredményez. A *3* érték a ráta beutazását eredményezi.</li><li>Állandó érték:<ul><li>x – lassú</li><li>lassú</li><li>közepes</li><li>gyors</li><li>x – gyors</li><li>alapértelmezett</li></ul></li></ul> | Optional |
| `duration` | Az az időtartam, amelynek el kell telnie, amíg a Speech szintézis (TTS) szolgáltatás beolvassa a szöveget, másodpercben vagy ezredmásodpercben. Például: *2s* vagy *1800ms*. | Optional |
| `volume` | Megadja a beszélő hangjának hangerejét. A kötet a következőképpen fejezhető ki:<ul><li>Abszolút érték 0,0 és 100,0 közötti tartományban, a *legcsendestől* a *leghangosabbig*. Például 75. Az alapértelmezett érték a 100,0.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal jelöl meg, amely a kötet módosításának mértékét határozza meg. Például: + 10 vagy-5,5.</li><li>Állandó érték:<ul><li>csendes</li><li>x – Soft</li><li>puha</li><li>közepes</li><li>hangos</li><li>x – hangos</li><li>alapértelmezett</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>Nyelvű sebességének módosítása

A felszólalási arány a szokásos hangokra alkalmazható a Word vagy a mondat szintjén. Míg a felszólalási arány csak a következő mondat szintjén alkalmazható neurális hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Kötet módosítása

A kötetek módosításai a Word vagy a mondat szintjén is alkalmazhatók a standard hangokra. Míg a kötetek módosításai csak a mondat szintjén alkalmazhatók a neurális hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Térköz módosítása

A szurok módosítása a Word vagy a mondat szintjén is alkalmazható a standard hangokra. Míg a szurok módosításait csak a mondat szintjén lehet a neurális hangokra alkalmazni.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Lényegét kontúrt módosítása

> [!IMPORTANT]
> A szurok-kontúr változásai nem támogatottak a neurális hangokkal.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>Say-as elem

`say-as` egy opcionális elem, amely megadja az elem szövegének típusát (például szám vagy dátum). Ez útmutatást nyújt a beszédfelismerési motornak a szöveg kiejtéséről.

**Szintaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `interpret-as` | Megadja az elem szövegének tartalomtípusát. A típusok listáját az alábbi táblázat tartalmazza. | Kötelező |
| `format` | További információkat nyújt az elem szövegének pontos formázásáról olyan tartalomtípusok esetében, amelyek kétértelmű formátummal rendelkezhetnek. A SSML az azokat használó tartalomtípusok formátumait határozzák meg (lásd az alábbi táblázatot). | Optional |
| `detail` | Megadja a megbeszélni kívánt részletességi szintet. Ez az attribútum például kérheti, hogy a Speech szintézis motor "központozás" jelölést küldjön. Nincsenek definiálva szabványos értékek a `detail`hoz. | Optional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A `interpret-as` és `format` attribútumok támogatott tartalomtípusai a következők. A `format` attribútumot csak akkor adja meg, ha a `interpret-as` dátum és idő értékre van állítva.

| értelmezés – as | format | Értelmezés |
|--------------|--------|----------------|
| `address` | | A szöveget címként kell kimondani. A Speech szintézis motorja:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Mint "én vagyok a 150th Court North East Redmond Washington." |
| `cardinal`, `number` | | A szöveg kardinális számként van kimondva. A Speech szintézis motorja:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Ahogy a "három alternatíva van." |
| `characters`, `spell-out` | | A szöveg külön betűként van kiírva (helyesírás). A Speech szintézis motorja:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T"-ként. |
| `date` | DMY, MDY, YMD, énh, ym, My, MD, DM, d, m, y | A szöveget dátumként kell kimondani. A `format` attribútum a dátum formátumát (*d = nap, m = hónap és y = év*) adja meg. A Speech szintézis motorja:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Ahogy a mai év október tizenkilencedik 2016. |
| `digits`, `number_digit` | | A szöveget külön számjegyek sorozata beszéljük. A Speech szintézis motorja:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9". |
| `fraction` | | A szöveg tört számként van kimondva. A Speech szintézis motorja:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"Három nyolcadik egy hüvelyk". |
| `ordinal` | | A szöveg sorszámként van kimondva. A Speech szintézis motorja:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"Válassza a harmadik lehetőséget". |
| `telephone` | | A szöveg telefonszámként van kimondva. A `format` attribútum az országkód jelölő számjegyeket tartalmazhat. Például: "1" az Egyesült Államok vagy "39" esetében Olaszország esetében. A beszédfelismerési motor felhasználhatja ezeket az információkat, hogy a kiejtését egy telefonszámra irányítsa. A telefonszám magában foglalhatja az országkódot is, és ha igen, elsőbbséget élvez a `format`található országkód felett. A Speech szintézis motorja:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />A "saját szám a 8 8 8 5 5 5 1 2 1 2-es körzetszám". |
| `time` | hms12, hms24 | A szöveget a rendszer időpontként beszéli. A `format` attribútum azt adja meg, hogy az idő 12 órás (hms12) vagy 24 órás (hms24) óra használatával van-e megadva. Használjon kettőspontot az órákat, perceket és másodperceket jelölő számok elválasztásához. Az alábbi példák érvényesek: 12:35, 1:14:32, 08:15 és 02:50:45. A Speech szintézis motorja:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Ahogy "a vonat négyen M." |

**Használat**

A `say-as` elem csak szöveget tartalmazhat.

**Példa**

A Speech szintézis motor a következő példát szemlélteti: "az első kérés egy, a tizenkilencedik 20 10-es, a korai érkezési idő pedig a 12 35 ÓRAKOR."
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Rögzített hang hozzáadása

a `audio` egy opcionális elem, amely lehetővé teszi MP3 hang beszúrását egy SSML-dokumentumba. A hangelem törzse tartalmazhat egyszerű szöveges vagy SSML jelölést, amely akkor beszél, ha a hangfájl nem érhető el, vagy nem játszható le. Emellett a `audio` elem szöveget és a következő elemeket is tartalmazhatja: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`és `sub`.

A SSML-dokumentumban szereplő összes hangnak meg kell felelnie a következő követelményeknek:

* Az MP3-t egy internetről elérhető HTTPS-végponton kell üzemeltetni. HTTPS szükséges, és az MP3-fájlt üzemeltető tartománynak érvényes, megbízható SSL-tanúsítványt kell tartalmaznia.
* Az MP3-fájlnak érvényes MP3-fájlként (MPEG v2) kell lennie.
* A bitsűrűségnek 48 kbps-nak kell lennie.
* A mintavételi sebességnek 16 000 Hz-nek kell lennie.
* Az egyetlen válaszban lévő összes szöveges és hangfájl együttes teljes ideje nem lehet nagyobb, mint 90 (90) másodperc.
* Az MP3 nem tartalmazhat ügyfél-specifikus vagy más bizalmas információt.

**Szintaxis**

```xml
<audio src="string"/></audio>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `src` | Megadja a hangfájl helyét/URL-címét. | Kötelező, ha a hangelemet használja a SSML-dokumentumban. |

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
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

## <a name="add-background-audio"></a>Háttérbeli hang hozzáadása

A `mstts:backgroundaudio` elem lehetővé teszi a háttérbeli hang hozzáadását a SSML-dokumentumokhoz (vagy szöveg-beszéd típusú hangfájlt keverve). `mstts:backgroundaudio` a háttérben egy hangfájlt lehet hurokba felvenni, a szöveg és a beszéd elején elhalványul, és a szöveg és a beszéd végén elhalványul.

Ha a megadott háttérbeli hang rövidebb, mint a szöveg-beszéd vagy a Halványítás, a rendszer hurokba kerül. Ha a szöveg-beszédnél hosszabb, akkor leáll, amikor befejeződött a Halványítás.

SSML-dokumentumok esetében csak egy háttér-hangfájl engedélyezett. Azonban `audio` címkéket is intersperse a `voice` elemen belül, ha további hangfelvételt szeretne hozzáadni a SSML-dokumentumhoz.

**Szintaxis**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `src` | Megadja a háttér hangfájljának helyét/URL-címét. | Kötelező, ha a SSML-dokumentumban háttér hang van használatban. |
| `volume` | Meghatározza a háttér-hangfájl kötetét. **Elfogadott értékek**: `0` `100` bezárólag. Az alapértelmezett érték `1`. | Optional |
| `fadein` | Meghatározza a háttérbeli hang "elhalványulás" időtartamát ezredmásodpercben. Az alapértelmezett érték `0`, ami egyenértékű a nem áttűnéssel. **Elfogadott értékek**: `0` `10000` bezárólag.  | Optional |
| `fadeout` | Meghatározza, hogy a háttérbeli hang mennyi ideig elhalványul ezredmásodpercben. Az alapértelmezett érték `0`, ami egyenértékű a nem elhalványulás beállításával. **Elfogadott értékek**: `0` `10000` bezárólag.  | Optional |

**Példa**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Következő lépések

* [Nyelvi támogatás: hangok, területi beállítások, nyelvek](language-support.md)
