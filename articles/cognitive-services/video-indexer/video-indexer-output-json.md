---
title: Vizsgálja meg az Azure videó indexelő kimenetét |} Microsoft Docs
description: Ez a témakör a videó indexelő kimeneti megvizsgálja.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349878"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Vizsgálja meg a V1-es API által létrehozott videó indexelő kimenet

> [!Note]
> A videó indexelő V1-es API-k mára elavult, és eltávolítja a 2018 augusztus 1-jétől. El kell kezdenie a videó indexelő v2 API-k használatával zavarokat elkerülése érdekében.
>
> Fejlesztéséhez videó indexelő v2 API-khoz, tekintse meg a útmutatását [Itt](https://api-portal.videoindexer.ai/). 

A hívás esetén a **beolvasása bontása** API és a response állapot rendben, a válasz tartalmának részletes JSON-kimenetét le. A JSON-tartalmak a megadott videó elemzései, beleértve a (Beszélgetés szövegének felismerése, személyek) részleteit tartalmazza. Kulcsszavak (témakörök), a lapok, a blokkok tartalmazza. Minden egyes blokk tartalmaz időtartományhoz, Beszélgetés szövegének sorok, OCR sorok, hangulati elemek, arc, és blokkolja a miniatűrökön.

Használhatja a **beolvasása bontása** API-t a teljes körű információkat a videó lekérni egy JSON-ként tartalmát.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
A videó röviden bemutatják azok insights billentyűkombináció lenyomásával is vizuálisan ellenőrizheti a **lejátszása** a videó indexelő portálon videó gombjára. További információkért lásd: [megtekintés és Szerkesztés videó insights](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk a JSON-tartalmak által visszaadott megvizsgálja a **beolvasása bontása** API. Előfordulhat, hogy hasznos lehet, hogy tekintse át a [fogalmak](video-indexer-concepts.md) cikk.

> [!NOTE]
> Videó indexelő a hozzáférési jogkivonatok lejárati egy óra.

## <a name="root-elements"></a>Gyökérelemet tartalmaz

Attribútum | Leírás
---|---
id|Ez a videó azonosítóját. Például 63c6d532ff.
partíció|Ahhoz, hogy később keresse meg azt a felhasználó megadhatja a feltöltési logikai partícióval.
név|A videó neve. Például az Azure figyelő.
leírás|A videó leírása. Például "John Kemnetz illesztése Scott Hanselman bemutatja az Azure megfigyelővel hatványra emelésének Azure figyelési adatok feloldásához."
Felhasználónév|A videó hozta létre. Például Channel9 videók.
CreateTime |Létrehozásakor. Például 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|A videót a következő módok egyike lehet: **titkos**, **nyilvános**. **Nyilvános** -a videó fiókja és bárki, aki egy hivatkozást a video mindenki számára látható. **Személyes** -a videó fiókja a mindenki számára látható.
isOwned|IGAZ, ha az aktuális felhasználó rendelkezik a videót. Ellenkező esetben hamis.  
isBase|IGAZ, ha bontásban tartalmazza a videó forrás alapul. Hamis, ha bontásban tartalmazza a lista, amely egy másik lebontása származik.
durationInSeconds|A videó időtartama.
summarizedInsights|Tartalmaz egy [summarizedInsights](#summarizedInsights).
Részletezés|Tartalmazhat egy vagy több [bontása](#breakdowns)
Közösségi|Tartalmaz egy **közösségi** elem az száma kedveli és a nézetek a videó.

## <a name="summarizedinsights"></a>summarizedInsights

Ez a szakasz a insights összegzését jeleníti meg.

Attribútum | Leírás
---|---
név|A videó neve. Például az Azure figyelő.
shortId|A videó Azonosítóját. Például 63c6d532ff.
privacyMode|A részletes információkat a következő módok egyike lehet: **titkos**, **nyilvános**. **Nyilvános** -a videó fiókja és bárki, aki egy hivatkozást a video mindenki számára látható. **Személyes** -a videó fiókja a mindenki számára látható.
időtartam|Egy időtartam, amely leírja a egyet történt időt tartalmazza. DURATION érték, másodpercben.
thumbnailUrl|A videó miniatűr teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Figyelje meg, hogy ha a videó titkos, az URL-cím egy egy órán keresztül hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és azt lekérése újra egy új URL-címmel rendelkező bontásban tartalmazza, vagy hívja az beszerzése egy új jogkivonatot, és a teljes URL-címet manuálisan GetAccessToken kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ accessToken] ").
felületei|Tartalmazhat egy vagy több [felületei](#faces)
Kapcsolatos témakörök|Tartalmazhat egy vagy több [kapcsolatos témakörök](#topics)
hangulati elemek|Tartalmazhat egy vagy több [hangulati elemek](#sentiments)
audioEffects| Tartalmazhat egy vagy több [audioEffects](#audioEffects)
márka| Nulla vagy több tartalmazhat [márka](#brands)
Statisztika|További információkért lásd: [statisztikák](#Statistics)
.
### <a name="statistics"></a>Statisztika

|Name (Név)|Leírás|
|---|---|
|CorrespondenceCount|A videó megfelelések száma.|
|WordCount|Beszélőfelismerési szót száma.|
|SpeakerNumberOfFragments|Videó rendelkezik-e a beszélőre töredék mennyisége.|
|SpeakerLongestMonolog|A beszélőre leghosszabb monolog. Ha a beszélőre belül a monolog silences szerepel. A rendszer eltávolítja a csend elején és végén a monolog.| 
|SpeakerTalkToListenRatio|A számítás a beszélőre monolog (nélkül a csend a között) a teljes időnek a videó osztva a fordított idő alapul. Az idő a harmadik tizedesjel lesz kerekítve.|

## <a name="breakdowns"></a>Részletezés

Ez a szakasz a insights részleteit jeleníti meg.

Attribútum | Leírás
---|---
id|A részletes információkat azonosítóját. Például 63c6d532ff.
state|A megadott lebontása azonosító feldolgozási állapotát. A következők egyike lehet: feltöltött, feldolgozásra, feldolgozott, sikertelen.
processingProgress|A folyamatban van. Ha például 10 %.
externalId| Beállíthat externalId feltöltés közben. Például "4f9c3500-eca7-4ab3-987e-a745017af698." Később kereshet a videók által a külső azonosítóját.
Alkalmazásazonosítójának|Beállíthat Alkalmazásazonosítójának feltöltés közben. 
metaadatok|Beállíthatja a metaadatok feltöltése során. 
megállapítások|Tartalmazhat egy vagy több [insights](#insights)
thumbnailUrl|A videó miniatűr teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Figyelje meg, hogy ha a videó titkos, az URL-cím egy egy órán keresztül hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és azt lekérése újra egy új URL-címmel rendelkező bontásban tartalmazza, vagy hívja az beszerzése egy új jogkivonatot, és a teljes URL-címet manuálisan GetAccessToken kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ accessToken] ").
publishedUrl|A közzétett URL-címe. Például "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest."
viewToken|A tulajdonosi jogkivonattal
sourceLanguage|Az adatforrás nyelvi. A következők támogatottak: Kínai angol, francia, német, olasz, japán, portugál, orosz, spanyol.
nyelv|A Beszélgetés szövegének nyelvét.

## <a name="insights"></a>megállapítások

Attribútum | Leírás 
---|---
transcriptBlocks|Tartalmazhat egy vagy több [transcriptBlocks](#transcriptBlocks)
Kapcsolatos témakörök|Tartalmazhat egy vagy több [kapcsolatos témakörök](#topics)
felületei|Tartalmazhat egy vagy több [felületei](#faces)
a résztvevők|Tartalmazhat egy vagy több [résztvevők](#participants)
contentModeration|Tartalmazhat egy [contentModeration](#contentModeration)
audioEffectsCategories|Tartalmazhat egy vagy több [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>felületei

### <a name="summarizedinsights"></a>summarizedInsights

**lapok** alatt megjelenő **summarizedInsights**, megjelenítése minden lapot a videó megtalálható összegzését.

Attribútum | Leírás 
---|---
id|Egy személy azonosítója. Például 11775.
shortId|A rövid azonosítóját. Mivel a lista több bontása származhat, ezt az Azonosítót szükséges tudni, amelyek e részletes adatok forrása minden lapot.  
név|Ha a oldallal, kerül annak a személynek a nevét. Például "Scott Hanselman." Ha a tapasztalt ismeretlen, "Ismeretlen #" kerül. 
leírás|Ha a felületen, a leírás a Bing API keresés alapján van feltöltve. Ellenkező esetben a leírás megadása **null**.
cím|Ha a felületen, a leírás a Bing API keresés alapján van feltöltve. Ellenkező esetben a cím nem **null**.
thumbnailFullUrl|A tapasztalt miniatűr teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Figyelje meg, hogy ha a videó titkos, az URL-cím egy egy órán keresztül hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és azt lekérése újra egy új URL-címmel rendelkező bontásban tartalmazza, vagy hívja az beszerzése egy új jogkivonatot, és a teljes URL-címet manuálisan GetAccessToken kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ accessToken] ").
megjelenésének|Tartalmazhat egy vagy több [megjelenésének](#appearances)
seenDuration|Mennyi ideig felülethez fordult elő (másodpercben).
seenDurationRatio|A videó időtartam (0-1) képest jelenlétét.

### <a name="breakdown-insights"></a>részletes információkat insights

**lapok** alatt megjelenő **bontása**, minden lapot a videó megtalálható kapcsolatos részleteket ismertetik.

Attribútum | Leírás 
---|---
id|Egy személy azonosítója. Például 11775.
bingId|
név|Ha a oldallal, kerül annak a személynek a nevét. Például "Scott Hanselman". Ha a tapasztalt ismeretlen, "Ismeretlen #" kerül. 
thumbnailId|Például 616468f0-1636-4efa-94e7-262f2e575059.
leírás|Ha a felületen, a leírás a Bing API keresés alapján van feltöltve. Ellenkező esetben a leírás megadása **null**.
cím|Ha a felületen, a leírás a Bing API keresés alapján van feltöltve. Ellenkező esetben a cím nem **null**.
imageUrl|Az URL-cím egy olyanra, amely használatban van a forrás videó mutat.  
megbízhatóság|Az vetett bizalmat pontszám (magasabb jobb,).
knownPersonId|Egy ismert személy (például celebrity) azonosítója. Ha egy személy nem ismert, akkor a azonosító nullákat tartalmaz. Például e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>Kapcsolatos témakörök

### <a name="summarizedinsights"></a>summarizedInsights

**témakörök** alatt megjelenő **summarizedInsights**, megjelenítése az egyes témakörök a videó megtalálható összegzését.

Attribútum | Leírás 
---|---
név|A témakör neve (például "Azure"). 
megjelenésének|Tartalmazhat egy vagy több [megjelenésének](#appearances).
isTranscript|IGAZ, ha a Beszélgetés szövegének található. Hamis, ha egy OCR található.

### <a name="breakdown-insights"></a>részletes információkat insights

**témakörök** alatt megjelenő **bontása**, a videó megtalálható minden témakör részleteket ismertetik.

|Attribútum | Leírás |
|---|---|
|id|A témakör egyedi azonosítója.|
|név|A témakör neve.|
|Típusazonosító|Jelenleg ez az attribútum nem használt.|
|szavakat|Jelenleg ez az attribútum nem használt.|
|Dimenziószáma|Relevanciájának pontszám (magasabb jobb,).|

## <a name="sentiments"></a>hangulati elemek

Attribútum | Leírás
---|---
sentimentKey| Jelenleg a következő hangulati elemek támogatottak: pozitív, semleges, negatív. 
megjelenésének|Tartalmazhat egy vagy több [megjelenésének](#appearances)|.
seenDurationRatio|A videó időtartam (0-1) képest jelenlétét.

## <a name="audioeffects"></a>audioEffects

Attribútum | Leírás 
---|---
audioEffectKey| Érvényes értékek a következők: beszéd átalakítás, csend, HandClaps.
megjelenésének|Tartalmazhat egy vagy több [megjelenésének](#appearances)
seenDurationRatio|A videó időtartam (0-1) képest jelenlétét.
seenDuration|Mennyi ideig hang effektus volt észlelhető (másodpercben).

## <a name="appearances"></a>megjelenésének

Attribútum | Leírás 
---|---
startTime| Idő értékét.
endTime|Idő értékét.
startSeconds| Idő értékét.
endSeconds| Idő értékét.

## <a name="participants"></a>a résztvevők

Attribútum | Leírás 
---|---
id|A résztvevő azonosítója.
név|A résztvevő neve. Például hangalapú #1.
pictureUrl|A **pictureUrl** attribútum későbbi használatra van fenntartva.

## <a name="contentmoderation"></a>contentModeration

Attribútum | Leírás 
---|---
adultClassifierValue|A megbízhatósági szint, hogy rendelkezik-e a videó felnőtt tartalom.
racyClassifierValue|A megbízhatósági szint, hogy a videó ellopható tartalma.
bannedWordsCount|Profanitás szavak száma. 
bannedWordsRatio|Profanitás szavak szavak teljes száma a aránya.
reviewRecommended|Logikai érték, amely azt jelzi, ha a videó manuálisan kell vizsgálni.
isAdult|Jelzi, ha a videó tekinthető felnőtt videó után manuális tekintse át a logikai értékek.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Attribútum | Leírás 
---|---
type|A kategória azonosítója.
kulcs|A következők egyikét: beszéd átalakítás, csend, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Attribútum | Leírás
---|---
id|A blokk Szegmensazonosítóját.
sorok|Tartalmazhat egy vagy több [sorok](#lines)
sentimentIds|A **sentimentIds** attribútum későbbi használatra van fenntartva.
thumbnailIds|A **thumbnailIds** attribútum későbbi használatra van fenntartva.
Véleményeket|A blokk egyik változóját (0-1, az pozitív negatív) véleményeket.
felületei|Tartalmazhat egy vagy több [lapok](#faces).
felismerése|Tartalmazhat egy vagy több [felismerése](#ocrs).
audioEffectInstances|Tartalmazhat egy vagy több [audioEffectInstances](#audioEffectInstances).
háttérben|Tartalmazhat egy vagy több [színfalak](#scenes).
Jegyzetek|Nulla vagy több tartalmazhat [jegyzetek](#annotations).

## <a name="ocrs"></a>felismerése

Ismerteti, hogy mi a videót, a szöveges tartalom található. 

Attribútum | Leírás 
---|---
timeRange|Az eredeti videó az időtartományt.
adjustedTimeRange|AdjustedTimeRange az időt, amely az aktuális lista viszonyítva. Különböző sornyi különböző videók létrehozhat egy listát, mert egy egy órán keresztül videó igénybe vehet, és csak 1 használja azt, például 10:00 – 10:15. Ebben az esetben, hogy a lista 1 sorral, ha az időtartományt 10:00 – 10:15, de a adjustedTimeRange érték 00:00 – 00:15.
sorok|Tartalmazhat egy vagy több [sorok](#lines).

## <a name="lines"></a>sorok

### <a name="transcriptblocks"></a>transcriptBlocks

**sorok** alatt megjelenő **transcriptBlocks**, a videó megtalálható ki sornyi ismertetik.

Attribútum | Leírás 
---|---
id| A sor azonosítója.
timeRange|Az eredeti videó az időtartományt.
adjustedTimeRange|AdjustedTimeRange az időt, amely az aktuális lista viszonyítva. Különböző sornyi különböző videók létrehozhat egy listát, mert egy egy órán keresztül videó igénybe vehet, és csak 1 használja azt, például 10:00 – 10:15. Ebben az esetben, hogy a lista 1 sorral, ha az időtartományt 10:00 – 10:15, de a adjustedTimeRange érték 00:00 – 00:15.
participantID| A sor hangalapú azonosítója.
szöveg| A Beszélgetés szövegének.
isIncluded| Az alap bontása mindig igaz. A származtatott listákat, a video-adatforrásban szereplő sorok értékre isIncluded = true. A sorok, amelyek hamis.

### <a name="ocrs"></a>felismerése

**sorok** alatt megjelenő **felismerése**, a videó megtalálható felismerése sornyi ismertetik.

Attribútum | Leírás 
---|---
id|A OCR azonosítóját.
Szélessége|Jelenleg ez az attribútum nem használt.
Magassága|Jelenleg ez az attribútum nem használt.
nyelv|A OCR nyelv.
textData|A Felismert szöveg.
megbízhatóság|Az vetett bizalmat pontszám (magasabb jobb,).

## <a name="scenes"></a>háttérben

Attribútum | Leírás 
---|---
id|A leképezni kívánt jelenetben azonosítóját.
timeRange|Tartalmaz egy **timeRange**.
keyFrame|A kulcs keret idején.
pillanatfelvételek|Tartalmazhat egy vagy több [pillanatfelvételek](#shots).

## <a name="shots"></a>pillanatfelvételek

Attribútum | Leírás 
---|---
id||A hibaüzenetet azonosítóját.
timeRange|Tartalmaz egy **timeRange**.
keyFrame|A kulcs keret idején.

## <a name="audioeffectinstances"></a>audioEffectInstances

Attribútum | Leírás 
---|---
type|Az index a hang esemény: vidámság = 1, HandClaps = 2, zene = 3, beszéd = 4, csend = 5
tartományok|Tartalmazhat egy vagy több [tartományok](#ranges).

## <a name="ranges"></a>tartományok

**tartományok** alatt megjelenő **audioEffectInstances**, ezeket a tartományokat a hang hatások ismertetik.

Attribútum | Leírás 
---|---
timeRange|Az eredeti videó az időtartományt.
adjustedTimeRange|AdjustedTimeRange az időt, amely az aktuális lista viszonyítva. Különböző sornyi különböző videók létrehozhat egy listát, mert egy egy órán keresztül videó igénybe vehet, és abból, például 10:00 – 10:15 csak egy vonalat használja. Ebben az esetben, hogy a lista 1 sorral, ha az időtartományt 10:00 – 10:15, de a adjustedTimeRange érték 00:00 – 00:15.

## <a name="annotations"></a>Jegyzetek

A címkék felismerhető objektumok, élő szervezet, díszletek, műveletek és vizuális minták alapján adja vissza.

|Attribútum|Leírás|
|---|---|
|id|A jegyzet azonosítója.|
|Name (Név)|A jegyzet (például személy, Athletic játék, fekete keretek) neve.|
|Megjelenésének|Egy vagy több megjelenésének tartalmazhat.|

## <a name="brands"></a>márka

Üzleti és a termék márkáját nevek szöveg Beszélgetés szövegének és/vagy videó OCR beszéd észlelhető. Visual felismerését a márka vagy embléma észlelése nem tartalmaz.

Attribútum | Leírás
---|---
id | A márka azonosítója.
név | A márka, a Bing szolgáltatástól, vagy egy testreszabott márka neve.  
wikiId | A márka wikipedia URL-utótagot. Például a "Target_Corporation" pedig a utótag [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | A márka tartozó Wikipedia URL-címe, ha létezik-e. Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
megbízhatóság | A abban, hogy a videó indexelő márka jelentő (0-1) értéke.
leírás | A márka Wikipedia kinyert leírása. 
megjelenésének | Egy vagy több megjelenésének tartalmazhat.
seenDuration | A videó időtartam (0-1) képest jelenlétét.

## <a name="next-steps"></a>További lépések

A saját lebontása létrehozásával kapcsolatos további információkért lásd: [megtekintése és szerkesztése a videó indexelő insights](video-indexer-view-edit.md).

További widgetek beágyazása az alkalmazás információ: [beágyazása videó indexelő widgeteket saját alkalmazásaiba](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Lásd még

[Videó indexelő API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Az indexelő áttekintő videó](video-indexer-overview.md)

