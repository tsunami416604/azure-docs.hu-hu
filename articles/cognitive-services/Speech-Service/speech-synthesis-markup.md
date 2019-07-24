---
title: Speech szintézis Markup Language (SSML) – Speech Services
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
ms.openlocfilehash: dd535f96c60a3f9259a108f3e8aff643eed1870d
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414715"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Beszédszintézis-jelölőnyelv (SSML)

A Speech szintézis Markup Language (SSML) egy XML-alapú Markup Language, amely lehetővé teszi a fejlesztők számára, hogy a szöveg-beszéd szolgáltatás segítségével hogyan konvertálja a bemeneti szöveget a szintetizált beszédbe. Az egyszerű szöveghez képest a SSML lehetővé teszi a fejlesztők számára a hangfelvétel, a kiejtés, a beszéd arány, a kötet és még több szöveg-beszéd típusú kimenet finomhangolását. Normál írásjelek, például egy időszak utáni felfüggesztés vagy a megfelelő hanglejtés használata, ha a mondatot kérdőjeltel végződik, automatikusan kezeli a rendszer.

A SSML Speech Services-implementációja World Wide Web Consortium [Speech szintézis Markup Language 1,0](https://www.w3.org/TR/speech-synthesis)-es verzióján alapul.

> [!IMPORTANT]
> A kínai, Japán és koreai karakterek két karakterből állnak a számlázáshoz. További információ: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurális és egyéni hangok

Válasszon a standard és a neurális hangok közül, vagy hozzon létre saját egyéni hangját a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang 4 nyelven és területi beállításban érhető el. A támogatott nyelvek, területi beállítások és hangok (neurális és standard) teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

A standard, a neurális és az egyéni hangok megismeréséhez lásd a [szöveg – beszéd áttekintést](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Támogatott SSML elemek

Minden SSML-dokumentum SSML elemekkel (vagy címkékkel) jön létre. Ezek az elemek a Pitch, a prosody, a Volume és más beállítások módosítására szolgálnak. A következő szakaszokban részletesen ismertetjük az egyes elemek használatát, és ha egy elem megadása kötelező vagy nem kötelező.  

> [!IMPORTANT]
> Ne feledje, hogy idézőjelek között idézőjeleket kell használni. A jól formázott, érvényes XML-fájlokra vonatkozó szabványokhoz idézőjelek közé kell foglalni az attribútumot. Például egy jól formázott, érvényes elem, de `<prosody volume=90>` nem. `<prosody volume="90">` Előfordulhat, hogy a SSML nem ismeri fel az idézőjelek között lévő attribútum-értékeket.

## <a name="create-an-ssml-document"></a>SSML-dokumentum létrehozása

`speak`a gyökérelem, és minden SSML-dokumentum esetében **kötelező** . Az `speak` elem fontos információkat tartalmaz, például a verziót, a nyelvet és a Markup szókincs definícióját.

**Syntax**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| version | Megadja a dokumentum korrektúrájának értelmezéséhez használt SSML-specifikáció verzióját. Az aktuális verzió 1,0. | Kötelező |
| XML: lang | Megadja a legfelső szintű dokumentum nyelvét. Az érték tartalmazhat kisbetűs, kétbetűs nyelvi kódot (például **en**), vagy a nyelvi kódot és a nagybetűs országot/régiót (például **en-us**). | Kötelező |
| xmlns | Megadja a dokumentumhoz tartozó URI-t, amely meghatározza a SSML dokumentum Markup szókincsét (az elemek típusát és az attribútum nevét). Az aktuális URI https://www.w3.org/2001/10/synthesis:. | Kötelező |

## <a name="choose-a-voice-for-text-to-speech"></a>Hang kiválasztása szöveg és beszéd között

Az `voice` elem megadása kötelező. A szövegről beszédre használt hang megadására szolgál.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| name | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Kötelező |

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

## <a name="use-multiple-voices"></a>Több beszédhangot használata

A `speak` elemen belül több hang is megadható a szöveg – beszéd kimenethez. Ezeket a hangokat különböző nyelveken lehet megtekinteni. Minden hang esetében a szöveget egy `voice` elembe kell becsomagolni.

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| name | A szöveg-beszéd kimenethez használt hang azonosítása. A támogatott hangok teljes listáját a [nyelvi támogatás](language-support.md#text-to-speech)című témakörben tekintheti meg. | Kötelező |

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

Alapértelmezés szerint a Text-to-speech szolgáltatás a standard és a neurális hangok semleges beszéd stílusa alapján szintetizálja a szöveget. A neurális hangokkal a beszélő stílust úgy állíthatja be, hogy kifejezze az elemet a `<mstts:express-as>` vidámság, az empátia vagy a hangulat alapján. Ez egy opcionális elem az Azure Speech Services szolgáltatásban.

A jelen neurális hangok esetében jelenleg a beszélő stílusának módosításait támogatja a rendszer:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

A módosításokat a rendszer a mondat szintjén alkalmazza, a stílus pedig hangon változik. Ha a stílus nem támogatott, a szolgáltatás az alapértelmezett semleges beszéd stílusát fogja visszaadni.

**Syntax**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| type | Megadja a beszéd stílusát. A beszélő stílusok jelenleg hangspecifikusak. | Akkor szükséges, ha a beszélő stílust módosítja egy neurális hanghoz. A használatakor `mstts:express-as`meg kell adni a típust. Ha a megadott érték érvénytelen, akkor a rendszer figyelmen kívül hagyja ezt az elemet. |

Ebből a táblázatból megállapíthatja, hogy az egyes neurális hangfelismerések milyen beszélő stílusokat támogatnak.

| Hang | Type | Leírás |
|-------|------|-------------|
| `en-US-JessaNeural` | típus =`cheerful` | Pozitív és boldog érzelemet ad ki |
| | típus =`empathy` | A gondozás és a megértés értelme |
| `zh-CN-XiaoxiaoNeural` | típus =`newscast` | Egy formális hangjelzést ad a hírekhez hasonló módon |
| | típus =`sentiment` | Egy megható üzenetet vagy egy történetet közvetít |

**Példa**

Ez a SSML-kódrészlet azt szemlélteti `<mstts:express-as>` , hogy az elem hogyan változtatja meg a `cheerful`beszéd stílusát a alkalmazásban.

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

Az `break` elem használatával szüneteltetheti a szavak közötti szüneteltetéseket (vagy megszakításokat), vagy megakadályozhatja, hogy a szöveg-beszéd szolgáltatás automatikusan hozzáadja a szüneteltetéseket.

> [!NOTE]
> Ezzel az elemmel felülbírálhatja egy szó vagy kifejezés szöveg-beszédre (TTS) vonatkozó alapértelmezett viselkedését, ha az adott szó vagy kifejezés szintetizált beszéde természetellenesen hangzik. Állítsa `strength`a értékre, hogymegakadályozzaaprosodic-töréspontot,amelyetarendszerautomatikusanbeszúraszöveg-beszédszolgáltatásba.`none`

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| erősségét | Meghatározza a Szüneteltetés relatív időtartamát az alábbi értékek egyikének használatával:<ul><li>nincs</li><li>x – gyenge</li><li>gyenge</li><li>közepes (alapértelmezett)</li><li>erős</li><li>x – erős</li></ul> | Választható |
| time | Megadja a szünet időtartamát másodpercben vagy ezredmásodpercben. Érvényes értékek például 2s és 500 | Választható |

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
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Bekezdések és mondatok meghatározása

`p`és `s` az elemek a bekezdések és a mondatok jelölésére használatosak. Ezen elemek hiányában a szöveg-beszéd szolgáltatás automatikusan meghatározza a SSML-dokumentum szerkezetét.

Az `p` elem tartalmazhat szöveget és a következő elemeket: `phoneme` `prosody` `break` `audio` `say-as` `s`, ,`mstts:express-as`,,,, és. `sub`

Az `s` elem tartalmazhat szöveget és a következő elemeket `sub`: `prosody` `break` `phoneme` `audio` ,`say-as`,,,, és. `mstts:express-as`

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

## <a name="use-phonemes-to-improve-pronunciation"></a>A fonémák használata a kiejtés javítására

Az `ph` elem a fonetikus kiejtés SSML-dokumentumokban való használata. Az `ph` elem csak szöveget és más elemeket tartalmazhat. Az emberi olvasásra alkalmas beszédet mindig tartalékként adja meg.

A fonetikus ábécék olyan telefonokból állnak, amelyek betűkből, számokból vagy karakterből állnak, esetenként kombinációban. Minden telefon egyedi hangfelismerést ír elő. Ez ellentétben áll a latin ábécével, ahol bármely betű több beszélt hangot is jelenthet. Vegye figyelembe a "c" betű különböző kiejtéseit a "candy" és a "megszüntetés" kifejezésben, vagy a "th" betű kombinációjának különböző kiejtéseit a "Thing" és a "The" kifejezésben.

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| ábécé | Megadja az `ph` attribútumban szereplő karakterlánc kiejtésének szintetizálása során használandó fonetikus ábécét. Az ábécét megadó karakterláncot kisbetűs betűkkel kell megadni. Az alábbiakban megadhatja a lehetséges ábécéket.<ul><li>IPA &ndash; nemzetközi fonetikus ábécé</li><li>SAPI &ndash; Speech API telefonos készlet</li><li>UPS &ndash; univerzális telefonos készlet</li></ul>Az ábécé csak az elem fonéma vonatkozik. További információ: [fonetikus ábécé referenciája](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Választható |
| pH | Olyan telefonszámot tartalmazó karakterlánc, amely a szó kiejtését határozza meg `phoneme` a elemben. Ha a megadott karakterlánc nem felismerhető telefonokat tartalmaz, a szöveg-beszéd (TTS) szolgáltatás elutasítja a teljes SSML-dokumentumot, és a dokumentumban megadott egyik beszédfelismerési kimenetet sem hozza létre. | Fonémák használata esetén kötelező. |

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

Ez `prosody` a elem a szurok, a countour, a tartomány, a sebesség, az időtartam és a kötet változásainak megadására szolgál a szöveg-beszéd kimenethez. Az `prosody` elem tartalmazhat szöveget és a következő elemeket: `p` `phoneme` `break` `audio` `prosody` `s`, ,`sub`,,,, és. `say-as`

Mivel a prosodic-attribútumok értékei a széles skálán változhatnak, a beszédfelismerő felismeri a hozzárendelt értékeket arra a javaslatra, hogy a kiválasztott hang tényleges prosodic értékei legyenek. A szöveg-beszéd szolgáltatás korlátozza vagy helyettesíti a nem támogatott értékeket. Példa a nem támogatott értékekre: 1 MHz vagy 120-es kötet.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| betűközű | Megadja a szöveg alappályáját. A szurok a következőképpen fejezhető ki:<ul><li>Abszolút érték, amelyet a szám a "Hz" (Hertz) kifejezéssel jelez. Például: 600 Hz.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal, majd a "Hz" vagy "St" előtaggal jelöl, amely a szurok módosításához szükséges összeget határozza meg. Például: + 80Hz vagy-2st. A "Szent" érték azt jelzi, hogy a változási egység semitone-e, amely a normál diatonikus-skálán lévő hang (fél lépés) fele.</li><li>Állandó érték:<ul><li>x – alacsony</li><li>alacsony</li><li>közepes</li><li>Magas</li><li>x – magas</li><li>alapértelmezett</li></ul></li></ul>. | Választható |
| kontúr | A kontúr nem támogatott a neurális hangok esetében. A kontúr a hangfelvétel tartalmának változásait jelöli a célok tömbje a beszédfelismerési kimenetben megadott időpontokban. Az egyes célkitűzéseket paraméter párok halmaza határozza meg. Példa: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paraméterekben az első érték határozza meg a szurok változásának helyét a szöveg időtartamának százalékában. A második érték határozza meg a szurok növelésének vagy csökkentésének mértékét, egy relatív érték vagy a szurok enumerálási értéke alapján ( `pitch`lásd:). | Választható |
| tartomány  | Egy érték, amely a szövegben lévő szurok tartományát jelöli. A leíráshoz `range` `pitch`megadhatja az azonos abszolút értékeket, relatív értékeket vagy enumerálási értékeket. | Választható |
| sebessége  | Megadja a szöveg beszédi arányát. A következőképpen lehet `rate` kifejezni:<ul><li>Egy relatív érték, amely az alapértelmezett érték szorzóként funkcionál. Az *1* érték például nem változik a díjszabásban. A *.5* értéke a ráta felére csökkentése. A *3* érték a ráta beutazását eredményezi.</li><li>Állandó érték:<ul><li>x – lassú</li><li>lassú</li><li>közepes</li><li>gyors</li><li>x – gyors</li><li>alapértelmezett</li></ul></li></ul> | Választható |
| duration  | Az az időtartam, amelynek el kell telnie, amíg a Speech szintézis (TTS) szolgáltatás beolvassa a szöveget, másodpercben vagy ezredmásodpercben. Például: *2s* vagy *1800ms*. | Választható |
| kötet  | Megadja a beszélő hangjának hangerejét. A kötet a következőképpen fejezhető ki:<ul><li>Abszolút érték 0,0 és 100,0 közötti tartományban, a legcsendestől a leghangosabbig. Például 75. Az alapértelmezett érték a 100,0.</li><li>Egy relatív érték, amelyet a rendszer a "+" vagy "-" előtaggal jelöl meg, amely a kötet módosításának mértékét határozza meg. Például: + 10 vagy-5,5.</li><li>Állandó érték:<ul><li>csendes</li><li>x – Soft</li><li>puha</li><li>közepes</li><li>hangos</li><li>x – hangos</li><li>alapértelmezett</li></ul></li></ul> | Választható |

### <a name="change-speaking-rate"></a>Nyelvű sebességének módosítása

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

### <a name="change-pitch"></a>Térköz módosítása

A szurok módosítása a Word vagy a mondat szintjén is alkalmazható a standard hangokra. Míg a szurok módosításait csak a mondat szintjén lehet a neurális hangokra alkalmazni.

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

## <a name="next-steps"></a>További lépések

* [Nyelvi támogatás: hangok, területi beállítások, nyelvek](language-support.md)
