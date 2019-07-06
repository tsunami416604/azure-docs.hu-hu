---
title: Beszéd összefoglaló Markup Language (SSML) – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Használja a Speech összefoglaló Markup Language írásmódja és a szöveg-hang transzformációs prosody szabályozásához.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604819"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Beszédszintézis-jelölőnyelv (SSML)

Beszéd összefoglaló Markup Language (SSML), egy XML-alapú jelölőnyelv, amely lehetővé teszi a fejlesztők hogyan bemeneti szöveg megadása a szöveg-hang transzformációs szolgáltatásával szintetizált lesz konvertálva. Egyszerű szöveges képest, SSML lehetővé teszi a fejlesztőknek finomhangolása a terv lényegét, írásmódja, beszéd arány, a kötet és más, a szöveg-hang transzformációs kimenet. Normál írásjelek, például egy idő után felfüggesztés vagy a megfelelő intonation használatát, amikor egy mondatnál végződik a kérdőjel automatikusan kezeli.

SSML beszédszolgáltatások végrehajtása az webes Consortium alapul [Speech összefoglaló Markup Language 1.0-s verziója](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kínai, japán és koreai karaktereket is számlázási két karakternek számítanak. További információkért lásd: [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard szintű, Neurális és egyéni beszédhangot

Standard és a Neurális beszédhangot közül választhat, vagy létrehozhat saját egyéni beszédfelismerési a termék vagy márka egyedi. 75 + standard beszédhangot legfeljebb 45 nyelv és területi beállítás érhető el, és 5 Neurális beszédhangot 4 nyelv és területi beállítás érhető el. Beszédhangot (Neurális és standard), támogatott nyelvek és területi beállítások teljes listáját lásd: [nyelvi támogatás](language-support.md).

Neurális, standard és egyéni beszédhangot kapcsolatos további információkért lásd: [szöveg-hang transzformációs áttekintése](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Támogatott SSML elemei

Minden egyes SSML dokumentum SSML elemet (vagy címkék) jön létre. Ezek az elemek lényegét, prosody, kötet és egyéb beállítására használt. A következő szakaszok bemutatják, hogyan minden eleme szolgál, és ha egy elem-e a kötelező vagy nem kötelező.  

> [!IMPORTANT]
> Ne felejtse el használni az attribútumértékek körüli idézőjeleket. Helytelen formátumú, érvényes XML-szabványok attribútum értékei dupla idézőjelek közé kell a szükséges. Ha például `<prosody volume="90">` helytelen formátumú, érvényes elem, de `<prosody volume=90>` nem. SSML nem ismeri fel, amelyek nem találhatók ajánlatok attribútumértékek.

## <a name="create-an-ssml-document"></a>SSML dokumentum létrehozása

`speak` a gyökérelem, továbbá **szükséges** SSML dokumentumok. A `speak` elem fontos információkat, például a verziója, nyelv és a jelölőnyelvi szókincsből eredőket definícióját tartalmazza.

**Syntax**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| version | Azt jelzi, hogy a dokumentum jelölőnyelvi értelmezésére szolgálnak SSML specifikáció verziója. A jelenlegi verziója 1.0-t. | Kötelező |
| xml:lang | Meghatározza a legfelső szintű dokumentum nyelvét. Az érték egy kisbetűs, kétbetűs nyelvi kódot tartalmazhatnak (például **en**), vagy a nyelvi kódját, és a nagybetűk ország/régió (például **en-US**). | Kötelező |
| xmlns | Adja meg az URI-t a dokumentumot, amely meghatározza a SSML dokumentum jelölőnyelvi szószedet (az elem típusát és az attribútumok nevében). Az aktuális URI https://www.w3.org/2001/10/synthesis. | Kötelező |

## <a name="choose-a-voice-for-text-to-speech"></a>Válassza egy hang szöveg-beszéd átalakítás

A `voice` elem megadása kötelező. Adja meg a használt szöveg-hang transzformációs hang szolgál.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| name | A hang-szöveg-hang transzformációs kimenethez használt azonosítja. Támogatott beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech). | Kötelező |

**Példa**

> [!NOTE]
> Ez a példa a `en-US-Jessa24kRUS` hangalapú. Támogatott beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Több beszédhangot használata

Belül a `speak` elem, szöveg-hang transzformációs kimeneti több beszédhangot is megadhat. Ezek adott beszédhangot különböző nyelvekhez is lehet. Az egyes hang, a szöveget kell csomagolni, az egy `voice` elemet.

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| name | A hang-szöveg-hang transzformációs kimenethez használt azonosítja. Támogatott beszédhangot teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech). | Kötelező |

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Nyelvű stílus módosítása

> [!IMPORTANT]
> Ez a funkció csak a Neurális beszédhangot fog működni.

Alapértelmezés szerint a szöveg-hang transzformációs szolgáltatás szintetizál egy semleges beszédmódjához használja mind a standard szintű, mind a Neurális beszédhangot szöveget. A Neurális beszédhangot cheerfulness, beépíteni az együttérzést és a róluk szóló véleményeket Express beszédmódjához módosíthatja a `<mstts:express-as>` elemet. Ez az egyedi Azure Speech Services választható elem.

Nyelvű stílus korrekciók jelenleg ezek Neurális beszédhangot támogatottak:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Változások a mondat szintre vonatkoznak, és stílus hangalapú változhat. Ha a stílus nem támogatott, a szolgáltatás visszaadja a speech az alapértelmezett semleges stílusú beszélni.

**Syntax**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| type | Itt adható meg beszédmódjához. Nyelvű stílusok jelenleg az adott hang. | Ha Neurális hangot beszédmódjához beállítása szükséges. Ha használ `mstts:express-as`, akkor meg kell adni a típusát. Érvénytelen érték van megadva, ezt az elemet figyelmen kívül. |

Ez a táblázat segítségével meghatározhatja, melyik nyelvű stílusok egyes Neurális hanghasználathoz támogatottak.

| Hang | Típus | Leírás |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Egy pozitív és boldog érzelemfelismerési kifejezze |
| | type=`empathy` | Kifejezze megismerje a ápolásáért és ismertetése |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Fejezi ki a formális hangfrekvenciás, hasonló hírek szórás |
| | type=`sentiment` | Ruház érintkező üzenet vagy egy történetet |

**Példa**

Ez a kódrészlet SSML mutatja be a `<mstts:express-as>` elem szolgál a beszédmódjához módosítása `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Hozzáadni vagy eltávolítani egy break vagy szünetelteti a

Használja a `break` elem szüneteltetése (vagy oldaltörések) beszúrása a szavak közötti, vagy akadályozható meg automatikusan hozzáadja a szöveg-hang transzformációs szolgáltatás szünetel.

> [!NOTE]
> Az elem segítségével felülírhatja az alapértelmezett viselkedést, a szövegfelolvasás egy szót vagy kifejezést, ha az adott szó vagy kifejezés a szintetizált természetes úgy érzi. Állítsa be `strength` való `none` prosodic szünet, amely automatikusan szúrja be a tex – speech service elkerülése érdekében.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| erőssége | Adja meg a következő értékek egyikével szünetet relatív időtartama:<ul><li>Egyik sem</li><li>x-weak</li><li>gyenge</li><li>Közepes (alapértelmezett)</li><li>Erős</li><li>x erős</li></ul> | Optional |
| time | Adja meg a másodperc vagy milliszekundum szünetet abszolút időtartama. Példák az érvényes értékek a következők 2s és 500 | Optional |

| erőssége | Leírás |
|----------|-------------|
| Egyik sem, vagy ha nincs megadva érték | 0 ms |
| x-weak | 250 ms |
| gyenge | 500 ms |
| Közepes | 750 ms |
| Erős | 1000 ms |
| x erős | 1250 ms |


**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Adja meg a mondatokat és bekezdés

`p` és `s` elemek bekezdések és mondatokat, illetve jelölésére használják. Ezek az elemek nincs a szöveg-hang transzformációs szolgáltatás automatikusan a SSML dokumentumok struktúráját határozza meg.

A `p` elem is tartalmazhat, szöveg és a következő elemeket: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, és `s`.

A `s` elem is tartalmazhat, szöveg és a következő elemeket: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, és `sub`.

**Syntax**

```XML
<p></p>
<s></s>
```

**Példa**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>Fonémából használata írásmódja javítása érdekében

A `ph` elem nevének fonetikus írásmódja SSML dokumentumok a szolgál. A `ph` elem csak tartalmazhat szöveg, nincs más elemeket. Mindig adja meg az emberek számára olvasható speech tartalékként.

Fonetická betűk telefonokon, melyek a betűk, számok és karakterek, néha kombináció állnak. Minden telefon beszéd egyedi hang ismerteti. Ez a szakembereket a Latin ábécére, ahol minden betűjének több használja a beszélt hangot jelöl. Fontolja meg a különböző kiejtés a levél "c" szavak "csoki" és "cease", vagy a különböző kiejtés a levél kombináció "th" szavak "dolognak" és "a".

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| ábécé | Itt adhatja meg, akkor használja, ha a karakterlánc a írásmódja szintetizáló fonetická ábécé a `ph` attribútum. A karakterlánc az ábécé kisbetűsnek kell adni. A lehetséges betűk, megadhatja azt az alábbiakban.<ul><li>IPA &ndash; nemzetközi fonetikus ábécét</li><li>sapi &ndash; Speech API Phone Set</li><li>UPS &ndash; univerzális Phone beállítása</li></ul>Az ábécé csak a phoneme elemében vonatkozik. További információkért lásd: [Fonetická referencia](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Optional |
| pH | Adja meg a kiejtés szavának besorolt tartalmazó karakterláncot a `phoneme` elemet. Ha a megadott karakterlánc ismeretlen telefonok tartalmaz, a szöveg-hang transzformációs (Szövegfelolvasás) szolgáltatás a teljes SSML dokumentum elutasítja, és hoz létre, a beszéd kimenetet a dokumentumban szereplő egyik. | Ha fonémából használata szükséges. |

**Példák**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Prosody beállítása

A `prosody` elem azt adhatja meg a terv lényegét, countour, tartomány, arány, időtartama és a szöveg-hang transzformációs kimeneti kötet módosítása. A `prosody` elem is tartalmazhat, szöveg és a következő elemeket: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, és `s`.

Prosodic attribútum értékei eltérőek lehetnek, számos különböző keresztül, mert a beszédfelismerő értelmezi a hozzárendelt értékek egy javaslatot, mi a tényleges prosodic értékek a kiválasztott hang kell lennie. A szöveg-hang transzformációs szolgáltatás, vagy ha behelyettesít értékeket, amelyek nem támogatottak. Néhány lehetséges érték nem támogatott olyan, a terv lényegét 1 MHz vagy egy 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| térköz | Azt jelzi, hogy a szöveg a referenciakonfiguráció felébresztve. A terv lényegét, előfordulhat, hogy express:<ul><li>Rögzített érték, egy számot "Hz" (Hz) követ. Ha például a 600Hz.</li><li>A relatív érték, egy szám, amelyet kifejezett "+" vagy "-" és "Hz" vagy "st" követ, amely meghatározza egy adott mennyiség a térköz módosítása. Például: + 80 Hz vagy - 2st. A "st" jelzi a módosítás egység semitone, amely fele a standard szintű diatonic a képviselő hangvételét (félig lépés).</li><li>Állandó érték:<ul><li>x – alacsony</li><li>Alacsony</li><li>Közepes</li><li>Magas</li><li>x magas</li><li>alapértelmezett</li></ul></li></ul>. | Optional |
| ELOSZLÁS | Körvonal Neurális beszédhangot nem támogatott. Körvonal beszédtartalom felébresztve változásai, a beszéd-kimenetben megadott pozíciók célok tömbjeként jelöli. Minden cél párok paraméter beállítása határozza meg. Példa: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paramétereket az első értéket az időtartam, a szöveg százalékában térköz módosítása helyét adja meg. A második érték meghatározza a növelje vagy csökkentse a terv lényegét, egy relatív érték vagy egy enumerálás használatával a terv lényegét (lásd: `pitch`). | Optional |
| Címtartomány  | A szöveg felébresztve tartományán jelölő érték. Előfordulhat, hogy express `range` az azonos abszolút értékeket, képest relatív értékeket vagy enumerálási leírására szolgáló `pitch`. | Optional |
| Arány  | Azt jelzi, hogy a szöveg nyelvű arányát. Előfordulhat, hogy express `rate` mint:<ul><li>Relatív érték, egy számot, amely az alapértelmezett Mindösszesen funkcionál. Érték például *1* eredményeként az arány nem változik. Érték *.5* egy sebessége halving eredményez. Érték *3* egy sebessége tripling eredményez.</li><li>Állandó érték:<ul><li>x-lassú</li><li>lassú</li><li>Közepes</li><li>gyors</li><li>x-gyors</li><li>alapértelmezett</li></ul></li></ul> | Optional |
| duration  | Az az időtartam, amely kell eltelnie a beszéd közben összefoglaló (Szövegfelolvasás) szolgáltatás olvassa be a szöveget, másodperc vagy milliszekundum. Ha például *2s* vagy *1800ms*. | Optional |
| kötet  | Azt jelzi, hogy a nyelvű hang a kötetek szintjén. A kötetet, előfordulhat, hogy express:<ul><li>Rögzített érték, a tartomány számos 0,0-100.0 a kifejezett *legcsendesebb* való *leghangosabb*. Ha például a 75. Az alapértelmezett érték 100.0.</li><li>A relatív érték, egy szám, amelyet kifejezett "+" vagy "-", amely meghatározza egy módosításához a kötetet. Például: + 10 vagy-5.5.</li><li>Állandó érték:<ul><li>Beavatkozás nélküli</li><li>x-soft</li><li>helyreállítható</li><li>Közepes</li><li>Hangos</li><li>x-loud</li><li>alapértelmezett</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>Nyelvű sebességének módosítása

A word vagy mondatszintű standard beszédhangot sebességét, és beszéljen is alkalmazható. Mivel a sebességét, és beszéljen csak alkalmazható a Neurális beszédhangot a mondat szintjén.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Kötet módosítása

A word vagy mondatszintű standard beszédhangot hangerőmódosítások is alkalmazható. Mivel a hangerőmódosítások csak a mondat szintjén Neurális beszédhangot is alkalmazható.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Térköz módosítása

A word vagy mondatszintű standard beszédhangot felébresztve módosítások is alkalmazható. Mivel a terv lényegét módosítások csak a mondat szintjén Neurális beszédhangot alkalmazható.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Lényegét kontúrt módosítása

> [!IMPORTANT]
> Lényegét Munkaeloszlás módosítások Neurális beszédhangot nem támogatott.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>További lépések

* [Nyelvi támogatás: beszédhangot, a területi beállításokat, a nyelvek](language-support.md)
