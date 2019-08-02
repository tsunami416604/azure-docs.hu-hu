---
title: Video Indexer widgetek beágyazása az alkalmazásokba
titlesuffix: Azure Media Services
description: Megtudhatja, hogyan ágyazhat be Video Indexer-vezérlőket az alkalmazásába.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ec3c7379c8c7f28765fbc4396d3e9804a6c127f6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663748"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Video Indexer widgetek beágyazása az alkalmazásokba

Ez a cikk azt ismerteti, hogyan lehet beágyazni Video Indexer-vezérlőket az alkalmazásokba. Video Indexer támogatja a három típusú widget beágyazását az alkalmazásba: **Kognitív ismeretek**, **játékosok**és **szerkesztők**. 

A 2. verziótól kezdődően a widget alap URL-címe tartalmazza a megadott fiók régióját. Például az USA nyugati régiójában lévő fiók a következőt hozza `https://wus2.videoindexer.ai/embed/insights/...`létre:.

## <a name="widget-types"></a>Vezérlőtípusok

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

A **Kognitív elemzési vezérlő** az összes vizuális elemzést tartalmazza, amely a videóindexelési folyamat során lett kinyerve. Az elemzési vezérlő a következő nem kötelező URL-paramétereket támogatja:

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|`widgets`|Vesszővel elválasztott sztringek|Lehetővé teszi annak szabályozását, mely elemzéseket szeretné megjeleníteni. <br/>Példa: a `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` csak a személyekre és márkákra vonatkozó felhasználói felületi elemzéseket jeleníti meg.<br/>Elérhető lehetőségek: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Nem támogatott a version=2 paraméterű URL-címek esetében<br/><br/>**Megjegyzés:** A widgetek URL-címe paraméter nem támogatott a 2. verzióban. |
|`locale`|Egy rövid nyelvi kód|Az adatfelismerés nyelvét vezérli. Az alapértelmezett szint a `en`. Például: `language=de`.|
|`tab`|Az alapértelmezett kijelölt lap|Az alapértelmezés szerint megjelenített adatvizsgálatok lapot vezérli. `tab=timeline`kijelöli az adatmegjelenítést az ütemterv lapon.|

### <a name="player-widget"></a>Lejátszó vezérlő

A **Lejátszó** vezérlő lehetővé teszi a videó streamelését adaptív átviteli sebességgel. A lejátszó vezérlő a következő nem kötelező URL-paramétereket támogatja:

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|`t`|Másodperc az elejétől|Beállítja, hogy a lejátszó az adott időponttól induljon el.<br/>Példa: `t=60`.|
|`captions`|Nyelvi kód|Lekéri a vezérlő betöltése közben a megadott nyelvű feliratot, amely elérhető lesz a feliratmenüben.<br/>Példa: `captions=en-US`.|
|`showCaptions`|Logikai érték|Beállítja, hogy a lejátszó betöltésekor a feliratok megjelenítése már engedélyezve legyen.<br/>Példa: `showCaptions=true`.|
|`type`||Aktivál egy audiólejátszó felületet (a videólejátszó rész el lesz távolítva).<br/>Példa: `type=audio`.|
|`autoplay`|Logikai érték|Jelzi, hogy a lejátszó a betöltést követően automatikusan lejátssza-e a videót (az alapértelmezett érték a true).<br/>Példa: `autoplay=false`.|
|`language`|Nyelvi kód|A lejátszó nyelvét szabályozza (az alapértelmezett érték: en-US)<br/>Példa: `language=de-DE`.|

### <a name="editor-widget"></a>Szerkesztői widget 

A **szerkesztői** widgettel új projekteket hozhat létre, és kezelheti a videókban bekövetkező eredményeket.

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|`accessToken`<sup>*</sup>|Sztring|A `accessToken` paramétert a szerkesztő widget használatakor kell megadni.<br/>A hozzáférési jogkivonat olyan videókhoz biztosít hozzáférést, amelyek csak a widget beágyazásához használt fiókon belül találhatók. |
|`language`|Nyelvi kód|A lejátszó nyelvét szabályozza (az alapértelmezett érték: en-US)<br/>Példa: `language=de-DE`.|
|`locale`|Egy rövid nyelvi kód|Az adatfelismerés nyelvét vezérli. Az alapértelmezett szint a `en`. Például: `language=de`.|

<sup>*</sup>A tulajdonosnak körültekintően `accessToken` kell gondoskodnia. 

## <a name="embedding-public-content"></a>Nyilvános tartalom beágyazása

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Kattintson a videóra, amellyel dolgozni kíván.
3. Kattintson a videó alatt megjelenő „embed” (Beágyazás) gombra.

    ![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    A gombra kattintva megjelenik egy beágyazási ablak, ahol kiválaszthatja, melyik vezérlőt szeretné beágyazni az alkalmazásba.
    Ha kijelöl egy widgetet (**kognitív bepillantást**, **lejátszót**vagy **szerkesztőt**), a létrehoz egy beágyazott kódot az alkalmazásba való beillesztéshez.
 
4. Válassza ki a kívánt widget típusát (**kognitív ismeretek**, **lejátszó**vagy **szerkesztő**).
5. Másolja a beágyazási kódot, és adja hozzá az alkalmazáshoz. 

    ![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Ha problémája van a videó URL-címeinek megosztásával, próbálja meg hozzáadni a "location" paramétert a hivatkozáshoz. A paramétert azon [Azure-régiókra kell beállítani, amelyekben video Indexer létezik](regions.md). Például: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Személyes tartalom beágyazása

A felugró beágyazási ablakokba (lásd az előző szakaszt) csak **nyilvános** videók kódjait lehet beágyazni. 

**Privát** videó beágyazásához meg kell adnia egy hozzáférési jogkivonatot az **iframe** **src** attribútumában:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Használhatja az [**Elemzések lekérése vezérlő**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API-t a Kognitív elemzési vezérlő tartalmainak lekéréséhez, vagy használhatja a [**Videó lekérése hozzáférési jogkivonatot**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?), amelyet az URL-címhez adhat lekérdezési paraméterként a fentebb látható módon. Adja meg ezt az URL-címet az **iframe** **src** attribútumának értékeként.

Ha elemzésszerkesztési lehetőségeket is hozzá kíván adni a beágyazott vezérlőhöz (ahogyan az a webalkalmazásunk esetében is van), meg kell adnia egy hozzáférési jogkivonatot szerkesztési engedélyekkel. Használja az [**Elemzések lekérése vezérlőt**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) vagy a [**Videó lekérése hozzáférési jogkivonatot**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) az **&allowEdit=true** paraméterrel. 

## <a name="widgets-interaction"></a>Vezérlőinterakciók

A **Kognitív elemzési** vezérlő képes kommunikálni az alkalmazásban található videókkal. Ez a szakasz bemutatja, hogy ez a kommunikáció hogyan valósítható meg.

![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Ahhoz, hogy a Video Indexer-vezérlők kommunikáljanak az egyéb összetevőkkel, a Video Indexer szolgáltatás a következőket teszi:

- Az eltérő eredetű kommunikációra szolgáló **postMessage** HTML5-módszert használja, és 
- ellenőrzi az üzenetet a VideoIndexer.ai forráson. 

Ha Ön saját lejátszókódot implementál, és ezt integrálja a **Kognitív elemzési** vezérlőkkel, akkor az Ön feladata, hogy ellenőrizze a VideoIndexer.ai-tól érkező üzenet eredetét.

### <a name="embed-widgets-in-your-application--blog-recommended"></a>Widgetek beágyazása az alkalmazásba/blogba (ajánlott) 

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

Ha saját [Azure Media Playerekkel](https://aka.ms/azuremediaplayer)ágyaz be video Indexer-betekintést, akkor a **GetVttUrl** metódussal lezárt feliratokat (feliratokat) kaphat. Ehhez a **getSubtitlesUrl** JavaScript-metódust is meghívhatja a Video Indexer AMP beépülő moduljából. 

## <a name="customizing-embeddable-widgets"></a>Beágyazható vezérlők testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

Kiválaszthatja, hogy milyen típusú eredményeket szeretne megadni, ha értéket ad meg a következő URL-paraméternek, amelyet a rendszer a beolvasott beágyazási kódhoz (API-ból vagy a webalkalmazásból) ad hozzá: `&widgets=<list of wanted widgets>`.

Lehetséges értékek: people, keywords, sentiments, transcript, search.

Ha például olyan widgetet szeretne beágyazni, amely csak személyeket tartalmaz, és keresési eredményeket keres, az iframe Beágyazási URL-címe a következőképpen fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Az IFRAME-ablak címe testreszabható az IFRAME URL-cím megadásával `&title=<YourTitle>` is. (Ez a html \<title> értékét módosítja.)
    
Ha például azt szeretné, hogy az IFRAME-ablak a "MyInsights" címet adja meg, az URL-cím a következőhöz hasonló lesz:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Fontos tudni, hogy ez a beállítás csak olyan esetekben releváns, ha azt szeretné, hogy az elemzések új ablakban nyíljanak meg.

### <a name="player-widget"></a>Lejátszó vezérlő

A Video Indexer-lejátszó beágyazásakor megadhatja a lejátszó méretét az iframe méretének meghatározásával.

Példa:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Alapértelmezés szerint a Video Indexer-lejátszóban automatikusan létrehozott feliratok is találhatók, amelyeket a szolgáltatás a videóból nyer ki, és a videó feltöltésekor kiválasztott forrásnyelv alapján ír át.

Ha más nyelvű beágyazást szeretne használni, hozzáadhat `&captions=< Language | ”all” | “false” >` a beágyazott lejátszó URL-címéhez, vagy az összes elérhető nyelvi feliratot megadhatja értékként.
Ha azt szeretné, hogy a feliratok alapértelmezés szerint megjelenjenek, továbbíthatja `&showCaptions=true`a következőt:.

A beágyazási URL-cím ezután a következőképpen fog kinézni: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

A feliratok letiltásához adja meg a „false” értéket a captions paraméterhez.

Automatikus lejátszás – alapértelmezés szerint a lejátszó elindítja a videó lejátszását. Ha ezt nem szeretné, adja meg az &autoplay=false paramétert a fenti beágyazási URL-címben.

## <a name="next-steps"></a>További lépések

A Video Indexer-elemzések megtekintésével és szerkesztésével kapcsolatos további információkat [ebben a cikkben](video-indexer-view-edit.md) olvashat.

Tekintse meg a [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ)is.
