---
title: A Video Indexer widgetek beágyazása az alkalmazásokba
titlesuffix: Azure Media Services
description: Megtudhatja, hogyan ágyazhat be Video Indexer-vezérlőket az alkalmazásába.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 73ceb0a92b97e90b1fdb0c5562d623505e86b870
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784892"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>A Video Indexer widgetek beágyazása az alkalmazásokba

Ez a cikk azt ismerteti, hogyan lehet beágyazni Video Indexer-vezérlőket az alkalmazásokba. Video Indexer az alkalmazásba való beágyazásával kétféle típusú widgetek támogatja: **A cognitive Insights** és **Player**. 

2-es verzióval kezdődően a widget alap URL-cím tartalmazza a fiókrégió. Például hoz létre egy fiókot az USA nyugati régiójában: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Vezérlőtípusok

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

A **Kognitív elemzési vezérlő** az összes vizuális elemzést tartalmazza, amely a videóindexelési folyamat során lett kinyerve. Az elemzési vezérlő a következő nem kötelező URL-paramétereket támogatja:

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|widgets|Vesszővel elválasztott sztringek|Lehetővé teszi annak szabályozását, mely elemzéseket szeretné megjeleníteni. <br/>Példa: a `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` csak a személyekre és márkákra vonatkozó felhasználói felületi elemzéseket jeleníti meg.<br/>Elérhető lehetőségek: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Nem támogatott a version=2 paraméterű URL-címek esetében<br/><br/>**Megjegyzés:** A widgetek URL paraméter nem támogatott a 2. verzióban. |

### <a name="player-widget"></a>Lejátszó vezérlő

A **Lejátszó** vezérlő lehetővé teszi a videó streamelését adaptív átviteli sebességgel. A lejátszó vezérlő a következő nem kötelező URL-paramétereket támogatja:

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|t|Kezdés utáni másodpercek|Beállítja, hogy a lejátszó az adott időponttól induljon el.<br/>Például: t=60|
|captions|Nyelvkód|Lekéri a vezérlő betöltése közben a megadott nyelvű feliratot, amely elérhető lesz a feliratmenüben.<br/>Például: captions=en-US|
|showCaptions|Logikai érték|Beállítja, hogy a lejátszó betöltésekor a feliratok megjelenítése már engedélyezve legyen.<br/>Például: showCaptions=true|
|type||Aktivál egy audiólejátszó felületet (a videólejátszó rész el lesz távolítva).<br/>Például: type=audio|
|autoplay|Logikai érték|Jelzi, hogy a lejátszó a betöltést követően automatikusan lejátssza-e a videót (az alapértelmezett érték a true).<br/>Például: autoplay=false|
|language|Nyelvkód|A lejátszó nyelvét szabályozza (az alapértelmezett érték: en-US)<br/>Például: language=de-DE|

## <a name="embedding-public-content"></a>Nyilvános tartalom beágyazása

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Kattintson a videóra, amellyel dolgozni kíván.
3. Kattintson a videó alatt megjelenő „embed” (Beágyazás) gombra.

    ![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    A gombra kattintva megjelenik egy beágyazási ablak, ahol kiválaszthatja, melyik vezérlőt szeretné beágyazni az alkalmazásba.
    Ha kiválaszt egy vezérlőt (**Lejátszó** vagy **Kognitív elemzési**), a rendszer létrehozza a hozzá tartozó beágyazási kódot, amelyet beilleszthet az alkalmazásba.
 
4. Válassza ki a kívánt vezérlőtípust (**Kognitív elemzési** vagy **Lejátszó**).
5. Másolja a beágyazási kódot, és adja hozzá az alkalmazáshoz. 

    ![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Személyes tartalom beágyazása

A felugró beágyazási ablakokba (lásd az előző szakaszt) csak **nyilvános** videók kódjait lehet beágyazni. 

**Privát** videó beágyazásához meg kell adnia egy hozzáférési jogkivonatot az **iframe** **src** attribútumában:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Használhatja az [**Elemzések lekérése vezérlő**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API-t a Kognitív elemzési vezérlő tartalmainak lekéréséhez, vagy használhatja a [**Videó lekérése hozzáférési jogkivonatot**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?), amelyet az URL-címhez adhat lekérdezési paraméterként a fentebb látható módon. Adja meg ezt az URL-címet az **iframe** **src** attribútumának értékeként.

Ha elemzésszerkesztési lehetőségeket is hozzá kíván adni a beágyazott vezérlőhöz (ahogyan az a webalkalmazásunk esetében is van), meg kell adnia egy hozzáférési jogkivonatot szerkesztési engedélyekkel. Használja az [**Elemzések lekérése vezérlőt**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) vagy a [**Videó lekérése hozzáférési jogkivonatot**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) az **&allowEdit=true** paraméterrel. 

## <a name="widgets-interaction"></a>Vezérlőinterakciók

A **Kognitív elemzési** vezérlő képes kommunikálni az alkalmazásban található videókkal. Ez a szakasz bemutatja, hogy ez a kommunikáció hogyan valósítható meg.

![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Ahhoz, hogy a Video Indexer-vezérlők kommunikáljanak az egyéb összetevőkkel, a Video Indexer szolgáltatás a következőket teszi:

- Az eltérő eredetű kommunikációra szolgáló **postMessage** HTML5-módszert használja, és 
- ellenőrzi az üzenetet a VideoIndexer.ai forráson. 

Ha Ön saját lejátszókódot implementál, és ezt integrálja a **Kognitív elemzési** vezérlőkkel, akkor az Ön feladata, hogy ellenőrizze a VideoIndexer.ai-tól érkező üzenet eredetét.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Mindkét vezérlőtípus beágyazása az alkalmazásban/blogban (ajánlott) 

Ez a szakasz bemutatja, hogyan teremthető meg a kommunikáció két Video Indexer-vezérlő között, hogy ha egy felhasználó az alkalmazásban az elemzésvezérlőre kattint, a lejátszó a releváns részhez ugorjon.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. Másolja a **Lejátszó** vezérlő beágyazási kódját.
2. Másolja a **Kognitív elemzési** vezérlő beágyazási kódját.
3. Adja hozzá a [**közvetítőfájlt**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js), amely a két vezérlő közötti kommunikációt kezeli:

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Ezentúl ha egy felhasználó az alkalmazásban az elemzésvezérlőre kattint, a lejátszó a releváns részhez ugrik.

További információkért tekintse meg [ezt a bemutatót](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A Kognitív elemzési vezérlő beágyazása és az Azure Media Player használata a tartalmak lejátszásához

Ez a szakasz bemutatja, hogyan valósítható meg a kommunikáció a **Kognitív elemzési** vezérlő és egy Azure Media Player-példány között az [AMP beépülő modulja](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js) segítségével.
 
1. Adjon hozzá egy Video Indexer beépülő modult az AMP lejátszóhoz.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Példányosítsa az Azure Media Playert a Video Indexer beépülő modullal.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Másolja a **Kognitív elemzési** vezérlő beágyazási kódját.

Mostantól működnie kell az Azure Media Playerrel való kommunikációnak.

További információkért tekintse meg [ezt a bemutatót](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>A Video Indexer Kognitív elemzési vezérlőjének beágyazása és saját lejátszó használata (bármilyen lejátszó lehet)

Ha saját lejátszót használ, manuálisan kell módosítania a lejátszót a kommunikáció megvalósítása érdekében. 

1. Szúrja be a videólejátszót.

    Ez lehet például egy szabványos HTML5-lejátszó.

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Ágyazza be a Kognitív elemzési vezérlőt.
3. Implementálja a lejátszóval való kommunikációt az „üzenet” eseményre való figyeléssel. Példa:

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

További információkért tekintse meg [ezt a bemutatót](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha a saját AMP lejátszójába ágyazza be a Video Indexer-elemzéseket, akkor a **GetVttUrl** metódussal feliratokat jeleníthet meg. Ehhez a **getSubtitlesUrl** JavaScript-metódust is meghívhatja a Video Indexer AMP beépülő moduljából. 

## <a name="customizing-embeddable-widgets"></a>Beágyazható vezérlők testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

Kiválaszthatja a kívánt érték megadásával insights típusú, a következő URL paraméter hozzáadva a beágyazási kód lekérése (API-ból vagy a webes alkalmazás): `&widgets=<list of wanted widgets>`.

Lehetséges értékek: people, keywords, sentiments, transcript, search.

Például ha azt szeretné, beágyazása az IFRAME-keret beágyazási URL-cím csak személyek és a keresési elemzéseket tartalmazó widget fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Az IFRAME-keret ablak címe is testre azáltal, hogy `&title=<YourTitle>` az IFRAME-keret URL-címre. (Ez a html \<title> értékét módosítja.)
    
Például ha azt szeretné, hogy az IFRAME-keret ablak a cím "MyInsights", az URL-címet fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Fontos tudni, hogy ez a beállítás csak olyan esetekben releváns, ha azt szeretné, hogy az elemzések új ablakban nyíljanak meg.

### <a name="player-widget"></a>Lejátszó vezérlő

A Video Indexer-lejátszó beágyazásakor megadhatja a lejátszó méretét az iframe méretének meghatározásával.

Példa:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Alapértelmezés szerint a Video Indexer-lejátszóban automatikusan létrehozott feliratok is találhatók, amelyeket a szolgáltatás a videóból nyer ki, és a videó feltöltésekor kiválasztott forrásnyelv alapján ír át.

Ha szeretne egy másik nyelvű beágyazási, hozzáadhat `&captions=< Language | ”all” | “false” >` a beágyazási lejátszó URL-címet, vagy a put "all" értéket, ha azt szeretné, hogy az összes elérhető nyelveket a feliratok.
Ha azt szeretné, hogy a feliratok alapértelmezés szerint megjelenő, amelyet továbbíthat `&showCaptions=true`.

A beágyazási URL-címet ezután fog kinézni: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

A feliratok letiltásához adja meg a „false” értéket a captions paraméterhez.

Automatikus lejátszás – alapértelmezés szerint a lejátszó elindítja a videó lejátszását. Ha ezt nem szeretné, adja meg az &autoplay=false paramétert a fenti beágyazási URL-címben.

## <a name="next-steps"></a>További lépések

A Video Indexer-elemzések megtekintésével és szerkesztésével kapcsolatos további információkat [ebben a cikkben](video-indexer-view-edit.md) olvashat.

Emellett tekintse meg [a Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
