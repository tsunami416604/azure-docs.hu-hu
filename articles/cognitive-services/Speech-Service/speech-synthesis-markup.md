---
title: Speech szintézis Markup Language (SSML) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech szintézis Markup Language használatával vezérelheti a kiejtési és a prosody szöveget a beszédben.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d97073666a18a3ffb7a88e1d2350f213ef589e6a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562527"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Beszédszintézis-jelölőnyelv (SSML)

A Speech szintézis Markup Language (SSML) egy XML-alapú Markup Language, amely lehetővé teszi a fejlesztők számára, hogy a szöveg-beszéd szolgáltatás segítségével hogyan konvertálja a bemeneti szöveget a szintetizált beszédbe. Az egyszerű szöveghez képest a SSML lehetővé teszi a fejlesztők számára a hangfelvétel, a kiejtés, a beszéd arány, a kötet és még több szöveg-beszéd típusú kimenet finomhangolását. Normál írásjelek, például egy időszak utáni felfüggesztés vagy a megfelelő hanglejtés használata, ha a mondatot kérdőjeltel végződik, automatikusan kezeli a rendszer.

A SSML beszédfelismerési szolgáltatásának megvalósítása a World Wide Web Consortium [Speech szintézis Markup Language 1,0-es verzióján](https://www.w3.org/TR/speech-synthesis)alapul.

> [!IMPORTANT]
> A kínai, Japán és koreai karakterek két karakterből állnak a számlázáshoz. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurális és egyéni hangok

Válasszon a standard és a neurális hangok közül, vagy hozzon létre saját egyéni hangját a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang 4 nyelven és területi beállításban érhető el. A támogatott nyelvek, területi beállítások és hangok (neurális és standard) teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

A standard, a neurális és az egyéni hangok megismeréséhez lásd a [szöveg – beszéd áttekintést](text-to-speech.md).

## <a name="special-characters"></a>Speciális karakterek

Ha a SSML-t használja a szöveg és a szintetizált beszéd átalakítására, ne feledje, hogy az XML-hez hasonlóan a speciális karakterek, például az idézőjelek, az aposztrófok és a szögletes zárójelek is meg kell menekülniük. További információ [: Extensible Markup Language (XML) 1,0: D függelék](https://www.w3.org/TR/xml/#sec-entexpand).

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

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| version | Megadja a dokumentum korrektúrájának értelmezéséhez használt SSML-specifikáció verzióját. Az aktuális verzió 1,0. | Szükséges |
| XML: lang | Megadja a legfelső szintű dokumentum nyelvét. Az érték tartalmazhat kisbetűs, kétbetűs nyelvi kódot (például **en**), vagy a nyelvi kódot és a nagybetűs országot/régiót (például **en-us**). | Szükséges |
| xmlns | Megadja a dokumentumhoz tartozó URI-t, amely meghatározza a SSML dokumentum Markup szókincsét (az elemek típusát és az attribútum nevét). Az aktuális URI https://www.w3.org/2001/10/synthesis. | Szükséges |

## <a name="choose-a-voice-for-text-to-speech"></a>Hang kiválasztása szöveg és beszéd között

A `voice` elem megadása kötelező. A szövegről beszédre használt hang megadására szolgál.

**Szintaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| név | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Szükséges |

**Példa**

> [!NOTE]
> Ez a példa a `en-US-Jessa24kRUS` hangot használja. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg.

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Több hang használata

A `speak` elemen belül több hang is megadható a szöveg-beszéd kimenethez. Ezeket a hangokat különböző nyelveken lehet megtekinteni. Minden hang esetében a szöveget `voice` elembe kell becsomagolni.

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| név | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Szükséges |

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

## <a name="adjust-speaking-styles"></a>Beszéd stílusainak módosítása

> [!IMPORTANT]
> Ez a funkció csak a neurális hangokat fogja működni.

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

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| type | Megadja a beszéd stílusát. A beszélő stílusok jelenleg hangspecifikusak. | Akkor szükséges, ha a beszélő stílust módosítja egy neurális hanghoz. `mstts:express-as`használata esetén a típust meg kell adni. Ha a megadott érték érvénytelen, akkor a rendszer figyelmen kívül hagyja ezt az elemet. |

Ebből a táblázatból megállapíthatja, hogy az egyes neurális hangfelismerések milyen beszélő stílusokat támogatnak.

| Beszédhang | Type (Típus) | Leírás |
|-------|------|-------------|
| `en-US-JessaNeural` | Type =`cheerful` | Pozitív és boldog érzelemet ad ki |
| | Type =`empathy` | A gondozás és a megértés értelme |
| | Type =`chat` | Beszéljen egy hétköznapi, nyugodt hangnemben |
| | Type =`newscast` | Egy formális hangjelzést ad a hírekhez hasonló módon |
| | Type =`customerservice` | Beszéljen barátságos és türelmes módon az ügyfélszolgálatnál |
| `zh-CN-XiaoxiaoNeural` | Type =`newscast` | Egy formális hangjelzést ad a hírekhez hasonló módon |
| | Type =`sentiment` | Egy megható üzenetet vagy egy történetet közvetít |

**Példa**

Ez a SSML-kódrészlet azt szemlélteti, hogy a `<mstts:express-as>` elem hogyan módosítható a beszéd stílusa `cheerful`re.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
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

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| erősségét | Meghatározza a Szüneteltetés relatív időtartamát az alábbi értékek egyikének használatával:<ul><li>Nincs</li><li>x – gyenge</li><li>gyenge</li><li>közepes (alapértelmezett)</li><li>erős</li><li>x – erős</li></ul> | Választható |
| time | Megadja a szünet időtartamát másodpercben vagy ezredmásodpercben. Érvényes értékek például 2s és 500 | Választható |

| Erősségét | Leírás |
|----------|-------------|
| Nincs, vagy ha nincs megadva érték | 0 ms |
| x – gyenge | 250 MS |
| gyenge | 500 ms |
| közepes | 750 ms |
| erős | 1000 MS |
| x – erős | 1250 MS |


**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>A fonémák használata a kiejtés javítására

A `ph` elem a SSML-dokumentumok fonetikus kiejtéséhez használatos. A `ph` elem csak szöveget tartalmazhat, más elemeket nem. Az emberi olvasásra alkalmas beszédet mindig tartalékként adja meg.

A fonetikus ábécék olyan telefonokból állnak, amelyek betűkből, számokból vagy karakterből állnak, esetenként kombinációban. Minden telefon egyedi hangfelismerést ír elő. Ez ellentétben áll a latin ábécével, ahol bármely betű több beszélt hangot is jelenthet. Vegye figyelembe a "c" betű különböző kiejtéseit a "candy" és a "megszüntetés" kifejezésben, vagy a "th" betű kombinációjának különböző kiejtéseit a "Thing" és a "The" kifejezésben.

**Szintaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| ábécé | Meghatározza a karakterlánc kiejtésének szintetizálása során használandó fonetikus ábécét a `ph` attribútumban. Az ábécét megadó karakterláncot kisbetűs betűkkel kell megadni. Az alábbiakban megadhatja a lehetséges ábécéket.<ul><li>IPA &ndash; nemzetközi fonetikus ábécé</li><li>SAPI &ndash; Speech API telefonos készlet</li><li>UPS &ndash; univerzális telefonvonal</li></ul>Az ábécé csak az elem fonéma vonatkozik. További információ: [fonetikus ábécé referenciája](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Választható |
| pH | Olyan telefonszámot tartalmazó karakterlánc, amely a szó kiejtését határozza meg a `phoneme` elemben. Ha a megadott karakterlánc nem felismerhető telefonokat tartalmaz, a szöveg-beszéd (TTS) szolgáltatás elutasítja a teljes SSML-dokumentumot, és a dokumentumban megadott egyik beszédfelismerési kimenetet sem hozza létre. | Fonémák használata esetén kötelező. |

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

## <a name="adjust-prosody"></a>Prosody módosítása

A `prosody` elem a szöveg-beszéd kimenethez tartozó szurok, countour, tartomány, sebesség, időtartam és kötet változásainak megadására szolgál. A `prosody` elem tartalmazhat szöveget és a következő elemeket: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`és `s`.

Mivel a prosodic-attribútumok értékei a széles skálán változhatnak, a beszédfelismerő felismeri a hozzárendelt értékeket arra a javaslatra, hogy a kiválasztott hang tényleges prosodic értékei legyenek. A szöveg-beszéd szolgáltatás korlátozza vagy helyettesíti a nem támogatott értékeket. Példa a nem támogatott értékekre: 1 MHz vagy 120-es kötet.

**Szintaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| betűközű | Megadja a szöveg alappályáját. A szurok a következőképpen fejezhető ki:<ul><li>Abszolút érték, amelyet a szám a "Hz" (Hertz) kifejezéssel jelez. Például: 600 Hz.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal, majd a "Hz" vagy "St" előtaggal jelöl, amely a szurok módosításához szükséges összeget határozza meg. Például: + 80Hz vagy-2st. A "Szent" érték azt jelzi, hogy a változási egység semitone-e, amely a normál diatonikus-skálán lévő hang (fél lépés) fele.</li><li>Állandó érték:<ul><li>x – alacsony</li><li>alacsony</li><li>közepes</li><li>magas</li><li>x – magas</li><li>alapértelmezett</li></ul></li></ul>. | Választható |
| kontúr | A kontúr nem támogatott a neurális hangok esetében. A kontúr a hangfelvétel tartalmának változásait jelöli a célok tömbje a beszédfelismerési kimenetben megadott időpontokban. Az egyes célkitűzéseket paraméter párok halmaza határozza meg. Példa: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paraméterekben az első érték határozza meg a szurok változásának helyét a szöveg időtartamának százalékában. A második érték megadja a szurok növelésének vagy csökkentésének mértékét, egy relatív érték vagy a szurok enumerálási értéke alapján (lásd: `pitch`). | Választható |
| tartomány  | Egy érték, amely a szövegben lévő szurok tartományát jelöli. `range` a `pitch`leírásához használt értékek, relatív értékek vagy enumerálási értékek használatával is kifejezhető. | Választható |
| rate  | Megadja a szöveg beszédi arányát. `rate` a következőképpen lehet kifejezni:<ul><li>Egy relatív érték, amely az alapértelmezett érték szorzóként funkcionál. Az *1* érték például nem változik a díjszabásban. A *.5* értéke a ráta felére csökkentése. A *3* érték a ráta beutazását eredményezi.</li><li>Állandó érték:<ul><li>x – lassú</li><li>lassú</li><li>közepes</li><li>gyors</li><li>x – gyors</li><li>alapértelmezett</li></ul></li></ul> | Választható |
| duration  | Az az időtartam, amelynek el kell telnie, amíg a Speech szintézis (TTS) szolgáltatás beolvassa a szöveget, másodpercben vagy ezredmásodpercben. Például: *2s* vagy *1800ms*. | Választható |
| hangerő  | Megadja a beszélő hangjának hangerejét. A kötet a következőképpen fejezhető ki:<ul><li>Abszolút érték 0,0 és 100,0 közötti tartományban, a *legcsendestől* a *leghangosabbig*. Például 75. Az alapértelmezett érték a 100,0.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal jelöl meg, amely a kötet módosításának mértékét határozza meg. Például: + 10 vagy-5,5.</li><li>Állandó érték:<ul><li>csendes</li><li>x – Soft</li><li>puha</li><li>közepes</li><li>hangos</li><li>x – hangos</li><li>alapértelmezett</li></ul></li></ul> | Választható |

### <a name="change-speaking-rate"></a>Beszéd arányának módosítása

A felszólalási arány a szokásos hangokra alkalmazható a Word vagy a mondat szintjén. Míg a felszólalási arány csak a következő mondat szintjén alkalmazható neurális hangokra.

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

A kötetek módosításai a Word vagy a mondat szintjén is alkalmazhatók a standard hangokra. Míg a kötetek módosításai csak a mondat szintjén alkalmazhatók a neurális hangokra.

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

### <a name="change-pitch"></a>Szurok módosítása

A szurok módosítása a Word vagy a mondat szintjén is alkalmazható a standard hangokra. Míg a szurok módosításait csak a mondat szintjén lehet a neurális hangokra alkalmazni.

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Szurok körvonalának módosítása

> [!IMPORTANT]
> A szurok-kontúr változásai nem támogatottak a neurális hangokkal.

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
## <a name="say-as-element"></a>Say-as elem  

`say-as` egy opcionális elem, amely megadja az elem szövegének típusát (például szám vagy dátum). Ez útmutatást nyújt a beszédfelismerési motornak a szöveg kiejtéséről. 

**Szintaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| értelmezés – as | Megadja az elem szövegének tartalomtípusát. A típusok listáját az alábbi táblázat tartalmazza. | Szükséges |
| formátum | További információkat nyújt az elem szövegének pontos formázásáról olyan tartalomtípusok esetében, amelyek kétértelmű formátummal rendelkezhetnek. A SSML az azokat használó tartalomtípusok formátumait határozzák meg (lásd az alábbi táblázatot). | Választható |
| részletes | Megadja a megbeszélni kívánt részletességi szintet. Ez az attribútum például kérheti, hogy a Speech szintézis motor "központozás" jelölést küldjön. Nincsenek definiálva szabványos értékek a `detail`hoz. | Választható |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A `interpret-as` és `format` attribútumok támogatott tartalomtípusai a következők. A `format` attribútumot csak akkor adja meg, ha a `interpret-as` dátum és idő értékre van állítva.

| értelmezés – as | formátum | Értelmezés |
|--------------|--------|----------------|
| cím | | A szöveget címként kell kimondani. A Speech szintézis motorja:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Mint "én vagyok a 150th Court North East Redmond Washington." |
| kardinális, szám | | A szöveg kardinális számként van kimondva. A Speech szintézis motorja:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Ahogy a "három alternatíva van." |
| karakterek, helyesírás | | A szöveg külön betűként van kiírva (helyesírás). A Speech szintézis motorja:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T"-ként. |
| dátum  | DMY, MDY, YMD, énh, ym, My, MD, DM, d, m, y | A szöveget dátumként kell kimondani. A `format` attribútum a dátum formátumát (*d = nap, m = hónap és y = év*) adja meg. A Speech szintézis motorja:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Ahogy a mai év október tizenkilencedik 2016. |
| számjegyek, number_digit | | A szöveget külön számjegyek sorozata beszéljük. A Speech szintézis motorja:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9". |
| töredék | | A szöveg tört számként van kimondva. A Speech szintézis motorja:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"Három nyolcadik egy hüvelyk". |
| sorszámok  | | A szöveg sorszámként van kimondva. A Speech szintézis motorja:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"Válassza a harmadik lehetőséget". |
| telefon  | | A szöveg telefonszámként van kimondva. A `format` attribútum az országkód jelölő számjegyeket tartalmazhat. Például: "1" az Egyesült Államok vagy "39" esetében Olaszország esetében. A beszédfelismerési motor felhasználhatja ezeket az információkat, hogy a kiejtését egy telefonszámra irányítsa. A telefonszám magában foglalhatja az országkódot is, és ha igen, elsőbbséget élvez a `format`található országkód felett. A Speech szintézis motorja:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />A "saját szám a 8 8 8 5 5 5 1 2 1 2-es körzetszám". |
| time | hms12, hms24 | A szöveget a rendszer időpontként beszéli. A `format` attribútum azt adja meg, hogy az idő 12 órás (hms12) vagy 24 órás (hms24) óra használatával van-e megadva. Használjon kettőspontot az órákat, perceket és másodperceket jelölő számok elválasztásához. Az alábbi példák érvényesek: 12:35, 1:14:32, 08:15 és 02:50:45. A Speech szintézis motorja:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Ahogy "a vonat négyen M." |

**Használat**

A `say-as` elem csak szöveget tartalmazhat.

**Példa**

A Speech szintézis motor a következő példát szemlélteti: "az első kérés egy, a tizenkilencedik 20 10-es, 12 35-es idő előtti érkezési időponttal rendelkező szobára szól."
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>Rögzített hang hozzáadása

a `audio` egy opcionális elem, amely lehetővé teszi MP3 hang beszúrását egy SSML-dokumentumba. A hangelem törzse tartalmazhat egyszerű szöveges vagy SSML jelölést, amely akkor beszél, ha a hangfájl nem érhető el, vagy nem játszható le. Emellett a `audio` elem szöveget és a következő elemeket is tartalmazhatja: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`és `sub`.

A SSML-dokumentumban szereplő összes hangnak meg kell felelnie a következő követelményeknek:

* Az MP3-t egy internetről elérhető HTTPS-végponton kell üzemeltetni. HTTPS szükséges, és az MP3-fájlt üzemeltető tartománynak érvényes, megbízható SSL-tanúsítványt kell tartalmaznia.
* Az MP3-fájlnak érvényes MP3-fájlként (MPEG v2) kell lennie.
* A bitsűrűségnek 48 kbps-nak kell lennie.
* A mintavételi sebességnek 16000 Hz-nek kell lennie.
* Az egyetlen válaszban lévő összes szöveges és hangfájl együttes teljes ideje nem lehet nagyobb, mint 90 (90) másodperc.
* Az MP3 nem tartalmazhat ügyfél-specifikus vagy más bizalmas információt.

**Szintaxis**

```xml
<audio src="string"/></audio>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| src | Megadja a hangfájl helyét/URL-címét. | Kötelező, ha a hangelemet használja a SSML-dokumentumban. |

**Példa**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
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

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| src | Megadja a háttér hangfájljának helyét/URL-címét. | Kötelező, ha a SSML-dokumentumban háttér hang van használatban. |
| hangerő | Meghatározza a háttér-hangfájl kötetét. **Elfogadott értékek**: `0` `100` bezárólag. Az alapértelmezett érték 0.`1` | Választható |
| fadein | Meghatározza a háttérbeli hang áttűnésének időtartamát ezredmásodpercben. Az alapértelmezett érték `0`, ami egyenértékű a nem áttűnéssel. **Elfogadott értékek**: `0` `10000` bezárólag.  | Választható |
| fadeout | Meghatározza, hogy a háttérbeli hang mennyi ideig elhalványul ezredmásodpercben. Az alapértelmezett érték `0`, ami egyenértékű a nem elhalványulás beállításával. **Elfogadott értékek**: `0` `10000` bezárólag.  | Választható |

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
