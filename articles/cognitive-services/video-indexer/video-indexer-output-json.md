---
title: Az Azure-Videóindexelő kimenetének vizsgálata |} A Microsoft Docs
description: Ez a témakör a Video Indexer kimeneti megvizsgálja.
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
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>A v1 API által előállított Videóindexelő kimenetének vizsgálata

> [!Note]
> A Video Indexer V1 API-k elavultak, és 2018. augusztus 1-től a törlődni fog. A fennakadások elkerülése érdekében érdemes a Video Indexer v2 API-k használatához.
>
> A Video Indexer v2 API-k fejlesztéséhez, tekintse meg a útmutatását [Itt](https://api-portal.videoindexer.ai/). 

Meghívásakor a **első lebontások** API és a válasz állapota OK, a részletes JSON-kimenet, a válasz tartalma kapunk. A JSON-tartalmak tartalmazza többek között a megadott videó insights (szöveges, felismerése, személyek) részleteit. A kulcsszavak (témakörök), arcok, blokkok szerepel. Minden egyes blokk típusú beágyazás időtartományok, átirat sorok, optikai Karakterfelismerés sorok, hangulati, arcok, és blokkolja a miniatűrökön.

Használhatja a **első lebontások** API-t egy JSON-fájlként egy videó teljes áttekintését tartalom beolvasása.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Vizuálisan is megvizsgálhatja a videó összefoglaló insights billentyűkombináció lenyomásával a **lejátszása** a videó a Video Indexer portálon gombjára. További információkért lásd: [megtekintés és Szerkesztés feltárását](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk által visszaadott JSON-tartalmak megvizsgálja a **első lebontások** API-t. Tekintse át a hasznos lehet a [fogalmak](video-indexer-concepts.md) cikk.

> [!NOTE]
> A Video Indexer a hozzáférési jogkivonatok lejárati érték egy óra.

## <a name="root-elements"></a>Legfelső szintű elemek

Attribútum | Leírás
---|---
id|Ez a videó azonosítója. Ha például 63c6d532ff.
partíció|Egy logikai partíció, amely a felhasználó megadhatja a feltöltés, annak érdekében, hogy később keressen rá az.
név|A videó neve. Ha például az Azure Monitor.
leírás|A videó leírása. Például "John Kemnetz JOIN Scott Hanselman bemutatja az Azure monitorozási adatok lehetőségeinek kiaknázásához és az Azure Monitor."
Felhasználónév|A videó létrehozója. Ha például a channel9-on videókat.
CreateTime |Időpontja. Ha például a 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|A videó a következő módok egyike lehet: **privát**, **nyilvános**. **Nyilvános** – a videót a fiókját, és bárki, amely rendelkezik a videóra mutató hivatkozást mindenki számára látható-e. **Privát** – a videót a fiókjában mindenki számára látható-e.
isOwned|IGAZ, ha az aktuális felhasználó a videó tulajdonosa. Esetén pedig FALSE érték.  
isBase|IGAZ, ha a táblázat összefoglalja a videó forrás alapján. FALSE (hamis), ha a táblázat összefoglalja egy lista, amely egy másik lebontása származik.
durationInSeconds|A videó időtartama.
summarizedInsights|Legalább egy [summarizedInsights](#summarizedInsights).
lebontások|Tartalmazhat egy vagy több [lebontások](#breakdowns)
Közösségi|Legalább egy **közösségi** elem az száma kedvelések és a nézetek a videó.

## <a name="summarizedinsights"></a>summarizedInsights

Ez a szakasz az insights összegzését jeleníti meg.

Attribútum | Leírás
---|---
név|A videó neve. Ha például az Azure Monitor.
shortId|A videó azonosítója. Ha például 63c6d532ff.
privacyMode|A részletezése a következő módok egyike lehet: **privát**, **nyilvános**. **Nyilvános** – a videót a fiókját, és bárki, amely rendelkezik a videóra mutató hivatkozást mindenki számára látható-e. **Privát** – a videót a fiókjában mindenki számára látható-e.
időtartam|Egy időtartam, amely leírja a időpontja egy elemzést tartalmaz. Időtartam másodpercen belül van.
thumbnailUrl|A videó miniatűrje teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Figyelje meg, hogy a videó nem nyilvános, ha az URL-cím egy egy órás hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és kérje le újra az új url-táblázat összefoglalja, vagy hívja Getaccesstokenhez új hozzáférési jogkivonatot, és manuálisan hozza létre a teljes URL-címet kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ a hozzáférési tokent] ").
arcok|Tartalmazhat egy vagy több [arc](#faces)
kapcsolatos témakörök|Tartalmazhat egy vagy több [kapcsolatos témakörök](#topics)
hangulati|Tartalmazhat egy vagy több [hangulati](#sentiments)
audioEffects| Tartalmazhat egy vagy több [audioEffects](#audioEffects)
márka| Nulla vagy több tartalmazhat [márkái](#brands)
Statisztika|További információkért lásd: [statisztika](#Statistics)
.
### <a name="statistics"></a>Statisztika

|Name (Név)|Leírás|
|---|---|
|CorrespondenceCount|A videóban megfelelések száma.|
|WordCount|A speaker kiszolgálónként szavak számát.|
|SpeakerNumberOfFragments|A videó szerepel a beszélő töredék mennyisége.|
|SpeakerLongestMonolog|A beszélő leghosszabb monolog. Ha a beszélő belül a monolog silences része. Csend elején és végén a monolog törlődik.| 
|SpeakerTalkToListenRatio|A számítás időt a speaker monolog (nélkül a csend köztes) elosztva a videó teljes időtartama alapján. Az idő a harmadik tizedesvesszőtől lesz kerekítve.|

## <a name="breakdowns"></a>lebontások

Ez a szakasz az insights részleteit jeleníti meg.

Attribútum | Leírás
---|---
id|A lebontása azonosítója. Ha például 63c6d532ff.
state|A megadott lebontása azonosító feldolgozási állapotát. A következők valamelyike lehet: feltöltött, feldolgozás, feldolgozás, a sikertelen.
processingProgress|A folyamat állapotát. Ha például 10 %-át.
externalId| ExternalId feltöltésekor állíthatja be. Például "4f9c3500-eca7-4ab3-987e-a745017af698." Később kereshet a videók a külső azonosító alapján
externalUrl|Feltöltés externalUrl állíthatja be. 
metaadatok|Beállíthatja a metaadatok feltöltése során. 
megállapítások|Tartalmazhat egy vagy több [insights](#insights)
thumbnailUrl|A videó miniatűrje teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Figyelje meg, hogy a videó nem nyilvános, ha az URL-cím egy egy órás hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és kérje le újra az új url-táblázat összefoglalja, vagy hívja Getaccesstokenhez új hozzáférési jogkivonatot, és manuálisan hozza létre a teljes URL-címet kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ a hozzáférési tokent] ").
publishedUrl|A közzétett URL-címe. Például "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest."
viewToken|A tulajdonosi jogkivonat
sourceLanguage|A Forrásnyelv. A következők támogatottak: Kínai angol, francia, német, olasz, japán, portugál, orosz, spanyol.
Nyelv|Az átirat nyelvét.

## <a name="insights"></a>megállapítások

Attribútum | Leírás 
---|---
transcriptBlocks|Tartalmazhat egy vagy több [transcriptBlocks](#transcriptBlocks)
kapcsolatos témakörök|Tartalmazhat egy vagy több [kapcsolatos témakörök](#topics)
arcok|Tartalmazhat egy vagy több [arc](#faces)
résztvevők|Tartalmazhat egy vagy több [résztvevők](#participants)
contentModeration|Tartalmazhat egy [contentModeration](#contentModeration)
audioEffectsCategories|Tartalmazhat egy vagy több [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>arcok

### <a name="summarizedinsights"></a>summarizedInsights

**arcok** , amely meg fog jelenni **summarizedInsights**, megjelenítése a videóban található minden egyes face összegzését.

Attribútum | Leírás 
---|---
id|Személy azonosítója. Ha például 11775.
shortId|A rövid azonosítója. Több lebontások származhat egy listát, mert ez az azonosító szükséges derítse ki, hogy mely ezek lebontások minden forrása.  
név|Ha a face felismeri, annak a személynek a nevét egészül ki. Például "házigazdája, Scott Hanselman." Ha a face ismeretlen, "" Ismeretlen "#" kerül. 
leírás|Ha az arcfelismerés, a leírást a Bing API-keresés alapján van feltöltve. Ellenkező esetben a leírás megadása nem **null**.
cím|Ha az arcfelismerés, a leírást a Bing API-keresés alapján van feltöltve. Ellenkező esetben a címe az **null**.
thumbnailFullUrl|A face miniatűr teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Figyelje meg, hogy a videó nem nyilvános, ha az URL-cím egy egy órás hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és kérje le újra az új url-táblázat összefoglalja, vagy hívja Getaccesstokenhez új hozzáférési jogkivonatot, és manuálisan hozza létre a teljes URL-címet kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ a hozzáférési tokent] ").
idővonalnézettel|Tartalmazhat egy vagy több [idővonalnézettel](#appearances)
seenDuration|Mennyi ideig a face a jelent (másodpercben).
seenDurationRatio|A videó időtartama (0-1) viszonyított jelenlétét.

### <a name="breakdown-insights"></a>részletes elemzések

**arcok** , amely meg fog jelenni **lebontások**, adja meg adatait a videóban található minden lapot.

Attribútum | Leírás 
---|---
id|Személy azonosítója. Ha például 11775.
bingId|
név|Ha a face felismeri, annak a személynek a nevét egészül ki. Például "házigazdája, Scott Hanselman". Ha a face ismeretlen, "" Ismeretlen "#" kerül. 
thumbnailId|Ha például 616468f0-1636-4efa-94e7-262f2e575059.
leírás|Ha az arcfelismerés, a leírást a Bing API-keresés alapján van feltöltve. Ellenkező esetben a leírás megadása nem **null**.
cím|Ha az arcfelismerés, a leírást a Bing API-keresés alapján van feltöltve. Ellenkező esetben a címe az **null**.
imageUrl|Az URL-címet, amely átveszi a rendszer a forrás videó kép mutat.  
magabiztosan|A megbízhatósági pontszám (nagyobb érték jobb).
knownPersonId|(Például hírességek) ismert személy azonosítója. Ha egy személy nem ismert, az azonosító a nullát tartalmaz. Ha például e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>kapcsolatos témakörök

### <a name="summarizedinsights"></a>summarizedInsights

**témakörök** , amely meg fog jelenni **summarizedInsights**, egyes témakörök a videóban található egy összegzés megjelenítése.

Attribútum | Leírás 
---|---
név|A témakör neve (például az "Azure"). 
idővonalnézettel|Tartalmazhat egy vagy több [idővonalnézettel](#appearances).
isTranscript|IGAZ, ha a szövegben található. FALSE (hamis), ha egy OCR található.

### <a name="breakdown-insights"></a>részletes elemzések

**témakörök** , amely meg fog jelenni **lebontások**, adja meg adatait a videóban található minden témakörhöz.

|Attribútum | Leírás |
|---|---|
|id|A témakör egyedi azonosítója.|
|név|A témakör nevét.|
|Típusazonosító|Ez az attribútum jelenleg nem használt.|
|szavak|Ez az attribútum jelenleg nem használt.|
|Rang|Relevanciapontszám (nagyobb érték jobb).|

## <a name="sentiments"></a>hangulati

Attribútum | Leírás
---|---
sentimentKey| Jelenleg a következő hangulati támogatottak: pozitív, semleges, negatív. 
idővonalnézettel|Tartalmazhat egy vagy több [idővonalnézettel](#appearances)|.
seenDurationRatio|A videó időtartama (0-1) viszonyított jelenlétét.

## <a name="audioeffects"></a>audioEffects

Attribútum | Leírás 
---|---
audioEffectKey| Érvényes értékek a következők:-beszéd átalakítás, csend, HandClaps.
idővonalnézettel|Tartalmazhat egy vagy több [idővonalnézettel](#appearances)
seenDurationRatio|A videó időtartama (0-1) viszonyított jelenlétét.
seenDuration|Mennyi ideig hang effektus szerepelt (másodpercben).

## <a name="appearances"></a>idővonalnézettel

Attribútum | Leírás 
---|---
startTime| Idő érték.
endTime|Idő érték.
startSeconds| Idő érték.
endSeconds| Idő érték.

## <a name="participants"></a>résztvevők

Attribútum | Leírás 
---|---
id|A résztvevő azonosítója.
név|A résztvevő neve. Ha például Speaker 1.
pictureUrl|A **pictureUrl** attribútum későbbi használatra van fenntartva.

## <a name="contentmoderation"></a>contentModeration

Attribútum | Leírás 
---|---
adultClassifierValue|A megbízhatósági szint, hogy a videó rendelkezik a felnőtt tartalom.
racyClassifierValue|A megbízhatósági szint, hogy a videó fajgyűlölő tartalom rendelkezik-e.
bannedWordsCount|TRÁGÁR szavakat száma. 
bannedWordsRatio|A szavak teljes számát a TRÁGÁR szavakat aránya.
reviewRecommended|Logikai érték, amely azt jelzi, ha a videó manuálisan kell vizsgálni.
isAdult|Logikai értékek, amely azt jelzi, ha a videó számít felnőtt videó után manuális ellenőrzést.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Attribútum | Leírás 
---|---
type|A kategória azonosítója.
kulcs|A következők egyikét:-beszéd átalakítás, csend, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Attribútum | Leírás
---|---
id|A blokk-azonosítója.
sorok|Tartalmazhat egy vagy több [sorok](#lines)
sentimentIds|A **sentimentIds** attribútum későbbi használatra van fenntartva.
thumbnailIds|A **thumbnailIds** attribútum későbbi használatra van fenntartva.
vélemények|A blokk (0 – 1, negatív, pozitív) a róluk szóló véleményeket.
arcok|Tartalmazhat egy vagy több [arcok](#faces).
felismerése|Tartalmazhat egy vagy több [felismerése](#ocrs).
audioEffectInstances|Tartalmazhat egy vagy több [audioEffectInstances](#audioEffectInstances).
Jelenetek|Tartalmazhat egy vagy több [jelenetek](#scenes).
jegyzetek|Nulla vagy több tartalmazhat [jegyzetek](#annotations).

## <a name="ocrs"></a>felismerése

Mi az a szöveges tartalom található videó időponthoz részletesen ismerteti. 

Attribútum | Leírás 
---|---
timeRange|Az eredeti videó az időtartományt.
adjustedTimeRange|AdjustedTimeRange az időtartománya képest az aktuális listába. Különböző sornyi különböző videók lejátszási lista hozhat létre, mert egy egyórás videó igénybe vehet, és használja azt, ha például 10:00-10:15 csak 1 sort. Ebben az esetben kell egy listát az 1 sort, ahol az időtartomány 10:00-10:15 azonban a adjustedTimeRange 00:00 – 00:15.
sorok|Tartalmazhat egy vagy több [sorok](#lines).

## <a name="lines"></a>sorok

### <a name="transcriptblocks"></a>transcriptBlocks

**sorok** , amely meg fog jelenni **transcriptBlocks**, átiratok a videó megtalálható sornyi ismertetik.

Attribútum | Leírás 
---|---
id| A sor azonosítója.
timeRange|Az eredeti videó az időtartományt.
adjustedTimeRange|AdjustedTimeRange az időtartománya képest az aktuális listába. Különböző sornyi különböző videók lejátszási lista hozhat létre, mert egy egyórás videó igénybe vehet, és használja azt, ha például 10:00-10:15 csak 1 sort. Ebben az esetben kell egy listát az 1 sort, ahol az időtartomány 10:00-10:15 azonban a adjustedTimeRange 00:00 – 00:15.
participantID| Ez a sor beszélője azonosítója.
szöveg| A szövegben.
isIncluded| Az alap lebontások mindig igaz. A származtatott listákat, a videót, a forrásban szereplő sorok isIncluded vannak beállítva = true. Minden más sorai false (hamis).

### <a name="ocrs"></a>felismerése

**sorok** , amely meg fog jelenni **felismerése**, felismerése a videóban található sornyi ismertetik.

Attribútum | Leírás 
---|---
id|Az optikai Karakterfelismerés azonosítója.
Szélesség|Ez az attribútum jelenleg nem használt.
Magasság|Ez az attribútum jelenleg nem használt.
Nyelv|Az optikai Karakterfelismerés nyelv.
textData|Az optikai Karakterfelismerés szöveg.
magabiztosan|A megbízhatósági pontszám (nagyobb érték jobb).

## <a name="scenes"></a>Jelenetek

Attribútum | Leírás 
---|---
id|A helyszín azonosítója.
timeRange|Legalább egy **timeRange**.
Kulcsképkocka|A kulcs keret időpontja.
helyességének|Tartalmazhat egy vagy több [helyességének](#shots).

## <a name="shots"></a>helyességének

Attribútum | Leírás 
---|---
id||A képernyőkép-azonosítót.
timeRange|Legalább egy **timeRange**.
Kulcsképkocka|A kulcs keret időpontja.

## <a name="audioeffectinstances"></a>audioEffectInstances

Attribútum | Leírás 
---|---
type|Az index az audio esemény: vidámság = 1, HandClaps = 2, zene = 3, beszéd = 4, csend = 5
tartományok|Tartalmazhat egy vagy több [címtartományok](#ranges).

## <a name="ranges"></a>tartományok

**tartományok** , amely meg fog jelenni **audioEffectInstances**, az adott tartományok hang hatások ismertetik.

Attribútum | Leírás 
---|---
timeRange|Az eredeti videó az időtartományt.
adjustedTimeRange|AdjustedTimeRange az időtartománya képest az aktuális listába. Különböző sornyi különböző videók lejátszási lista hozhat létre, mert egy egyórás videó igénybe vehet, és használja azt, ha például 10:00-10:15 csak egy sort. Ebben az esetben kell egy listát az 1 sort, ahol az időtartomány 10:00-10:15 azonban a adjustedTimeRange 00:00 – 00:15.

## <a name="annotations"></a>jegyzetek

A címkék felismerhető objektumok, tárgy, táj, műveleteket és vizuális minták alapján ad vissza.

|Attribútum|Leírás|
|---|---|
|id|A jegyzet azonosítója.|
|Name (Név)|A jegyzet (például személy, Athletic játékot, fekete keretek) neve.|
|Idővonalnézettel|Egy vagy több idővonalnézettel tartalmazhat.|

## <a name="brands"></a>márka

Üzleti és a termék márkáját nevet észlelt a szöveget szöveges és/vagy videó OCR-hang transzformációs. Ez nem tartalmaz a vizuális elismerését márkái vagy embléma észlelési.

Attribútum | Leírás
---|---
id | Egy adott márkához azonosítója.
név | A márka, a Bing és a egy testre szabott márka neve.  
wikiId | A márka wikipedia URL-címének utótagja. Például a "Target_Corporation" pedig az utótag [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | A márka a Wikipedia URL-címet, ha létezik. Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
magabiztosan | A Video Indexer márka detector használatával (0-1) megbízhatósági értéke.
leírás | A márka Wikipedia kinyert leírása. 
idővonalnézettel | Egy vagy több idővonalnézettel tartalmazhat.
seenDuration | A videó időtartama (0-1) viszonyított jelenlétét.

## <a name="next-steps"></a>További lépések

A saját lebontása létrehozásával kapcsolatos további információkért lásd: [megtekintése és szerkesztése a Videóindexelő-elemzések](video-indexer-view-edit.md).

Widgetek beágyazása az alkalmazásba kapcsolatos információkért lásd: [az alkalmazásokba történő beágyazása a Video Indexer widgetek](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Lásd még

[A video Indexer API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Video Indexer – áttekintés](video-indexer-overview.md)

