---
title: Video Indexer widgetek beágyazása az alkalmazásokba
titlesuffix: Azure Media Services
description: Megtudhatja, hogyan ágyazhat be Video Indexer widgeteket az alkalmazásba.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: fc0b447630b5e1ac360b1d84869cea02186672fc
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "71036627"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Video Indexer widgetek beágyazása az alkalmazásokba

Ebből a cikkből megtudhatja, hogyan ágyazhat be Video Indexer widgeteket az alkalmazásaiba. Video Indexer támogatja a három típusú widget beágyazását az alkalmazásba: *kognitív betekintést*, *lejátszót*és *szerkesztőt*. 

A 2. verziótól kezdődően a widget alap URL-címe tartalmazza a megadott fiók régióját. Például az USA nyugati régiójában lévő fiók a következőt hozza létre: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Vezérlőtípusok

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

A kognitív betekintő widget a videó-indexelési folyamatból kinyert összes vizuális bepillantást tartalmazza. A kognitív bepillantások widget a következő opcionális URL-paramétereket támogatja.

|Név|Meghatározás|Leírás|
|---|---|---|
|`widgets`|Vesszővel elválasztott sztringek|Lehetővé teszi a megjeleníteni kívánt adatfelismerések szabályozását. <br/> Példa: a `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` csak a felhasználók és a márkák felhasználói felületi bepillantást jeleníti meg.<br/>Elérhető lehetőségek: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Vegye figyelembe, hogy a `widgets` URL-cím paraméter nem támogatott a 2. verzióban.<br/>|
|`locale`|Egy rövid nyelvi kód|Az adatfelismerés nyelvét vezérli. Az alapértelmezett érték `en`. <br/> Példa: `locale=de`.|
|`tab`|Az alapértelmezett kijelölt lap|Az alapértelmezés szerint megjelenített adatvizsgálatok lapot vezérli. <br/> Példa: a `tab=timeline` megjeleníti az eredményeket az **Idősor** lapon kiválasztva.|

### <a name="player-widget"></a>Lejátszó vezérlő

A Player widget Adaptív átviteli sebesség használatával is továbbíthatja a videót. A Player widget a következő opcionális URL-paramétereket támogatja.

|Név|Meghatározás|Leírás|
|---|---|---|
|`t`|Másodperc az elejétől|A lejátszó a megadott időpontból kezdi a lejátszást.<br/> Példa: `t=60`.|
|`captions`|Nyelvi kód|A megadott nyelven beolvassa a feliratot a widget betöltésével, hogy elérhető legyen a **feliratok** menüben.<br/> Példa: `captions=en-US`.|
|`showCaptions`|Logikai érték|Beállítja, hogy a lejátszó betöltésekor a feliratok megjelenítése már engedélyezve legyen.<br/> Példa: `showCaptions=true`.|
|`type`||Aktiválja a hanglejátszó bőrét (a videó rész törlődik).<br/> Példa: `type=audio`.|
|`autoplay`|Logikai érték|Azt jelzi, hogy a játékosnak be kell-e játszania a videót a betöltéskor. Az alapértelmezett érték `true`.<br/> Példa: `autoplay=false`.|
|`language`|Nyelvi kód|A lejátszó nyelvének szabályozása. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`.|

### <a name="editor-widget"></a>Szerkesztői widget

A szerkesztői widgettel új projekteket hozhat létre, és kezelheti a videókban észlelt eredményeket. A szerkesztői widget a következő opcionális URL-paramétereket támogatja.

|Név|Meghatározás|Leírás|
|---|---|---|
|`accessToken`<sup>*</sup>|Sztring|Hozzáférést biztosít azokhoz a videókhoz, amelyek csak a widget beágyazásához használt fiókban találhatók.<br> A szerkesztő widgethez a `accessToken` paraméter szükséges.|
|`language`|Nyelvi kód|A lejátszó nyelvének szabályozása. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`.|
|`locale`|Egy rövid nyelvi kód|Az adatfelismerés nyelvét vezérli. Az alapértelmezett érték `en`.<br/>Példa: `language=de`.|

<sup>*</sup> A tulajdonosnak körültekintően kell megadnia `accessToken`.

## <a name="embedding-public-content"></a>Nyilvános tartalom beágyazása

1. Jelentkezzen be a [video Indexer](https://www.videoindexer.ai/) webhelyére.
2. Válassza ki a videót, amelyet használni szeretne.
3. Kattintson a videó alatt megjelenő **beágyazás** gombra.

    ![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    A **beágyazás** gomb kiválasztását követően kiválaszthatja az alkalmazásba beágyazni kívánt widgetet. 
4. Válassza ki a kívánt widget típusát (**kognitív ismeretek**, **lejátszó**vagy **szerkesztő**).
 
5. Másolja a beágyazási kódot, majd adja hozzá az alkalmazáshoz. 

    ![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Ha problémák merülnek fel a videó URL-címeinek megosztásakor, adja hozzá a `location` paramétert a hivatkozáshoz. A paramétert azon [Azure-régiókra kell beállítani, amelyekben video Indexer létezik](regions.md). Például: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Személyes tartalom beágyazása

Privát videó beágyazásához át kell adnia egy hozzáférési jogkivonatot az IFRAME **src** attribútumában:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
A kognitív ismereteket ismertető widget-tartalmak beszerzéséhez használja a következők egyikét:<br/>
- Az eredmények [lekérése widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API.<br/>
- A [videó-hozzáférési jogkivonat beolvasása](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). Adja hozzá lekérdezési paraméterként az URL-címhez. Ezt az URL-címet az IFRAME **src** értékének kell megadnia, ahogy az a korábban látható.

A beágyazott widget szerkesztési információinak megadásához olyan hozzáférési jogkivonatot kell átadnia, amely szerkesztési engedélyeket tartalmaz. Használja a [Get bepillantások widgetet](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) vagy a [videó hozzáférési token beszerzése](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Vezérlőinterakciók

A kognitív betekintő widget interakcióba léphet az alkalmazás egy videóján. Ez a szakasz bemutatja, hogy ez a kommunikáció hogyan valósítható meg.

![Vezérlő](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Video Indexer widgetek más összetevőkkel való kommunikációjának lekéréséhez a Video Indexer szolgáltatás:

- A a kereszthivatkozások közötti kommunikáció HTML5-módszerének **postMessage**használja. 
- ellenőrzi az üzenetet a VideoIndexer.ai forráson. 

Ha saját lejátszó kódot valósít meg, és integrálja a kognitív betekintő widgeteket, az Ön felelőssége, hogy ellenőrizze a VideoIndexer.ai származó üzenet eredetét.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Widgetek beágyazása az alkalmazásba vagy a blogjába (ajánlott) 

Ez a szakasz azt mutatja be, hogyan lehet interakciót elérni két Video Indexer widget között, hogy amikor egy felhasználó kiválasztja az alkalmazás Insight Control vezérlőjét, a lejátszó a megfelelő pillanatra ugrik.

1. Másolja a lejátszó widget beágyazási kódját.
2. Másolja ki a kognitív ismeretek beágyazási kódját.
3. Adja hozzá a [közvetítő fájlt](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) a két widget közötti kommunikáció kezeléséhez:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Most, amikor egy felhasználó kiválasztja a betekintési vezérlőt az alkalmazásban, a lejátszó a megfelelő pillanatra ugrik.

További tudnivalókért tekintse meg a [widgetek video Indexer beágyazását bemutató](https://codepen.io/videoindexer/pen/NzJeOb)témakört.

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A Kognitív elemzési vezérlő beágyazása és az Azure Media Player használata a tartalmak lejátszásához

Ez a szakasz bemutatja, hogyan lehet interakciót elérni egy kognitív bepillantási widget és egy Azure Media Player-példány között az [amp beépülő](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)modullal.
 
1. Video Indexer beépülő modul hozzáadása az AMP-lejátszóhoz:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Azure Media Player példánya a Video Indexer beépülő modullal.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
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

        // Init your AMP instance.
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
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Másolja ki a kognitív ismeretek beágyazási kódját.

Most már képesnek kell lennie kommunikálni Azure Media Player.

További információkért tekintse meg a [Azure Media Player + VI bepillantást ismertető bemutatót](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>A Video Indexer kognitív betekintési widget beágyazása és más videolejátszó használata

Ha nem a Azure Media Playert használja, akkor manuálisan kell módosítania a videolejátszót a kommunikáció eléréséhez. 

1. Szúrja be a videólejátszót.

    Szabványos HTML5-lejátszó például:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Ágyazza be a Kognitív elemzési vezérlőt.
3. Implementálja a lejátszóval való kommunikációt az „üzenet” eseményre való figyeléssel. Példa:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

További információkért tekintse meg a [Azure Media Player + VI bepillantást ismertető bemutatót](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha saját [Azure Media Playerekkel](https://aka.ms/azuremediaplayer)ágyaz be video Indexer-betekintést, akkor a **GetVttUrl** metódussal lezárt feliratokat (feliratokat) kaphat. JavaScript-metódust is meghívhat a Video Indexer AMP beépülő modul **getSubtitlesUrl** (a korábban látható módon). 

## <a name="customizing-embeddable-widgets"></a>Beágyazható vezérlők testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

Kiválaszthatja, hogy milyen típusú bepillantást szeretne. Ehhez adja meg a következő URL-paraméter értékét a beolvasott beágyazási kódhoz (az API-ból vagy a webalkalmazásból) hozzáadott értékként: `&widgets=<list of wanted widgets>`.

A lehetséges értékek a következők: **személyek**, **kulcsszavak**, **érzelmek**, **átiratok**és **keresések**.

Ha például olyan widgetet szeretne beágyazni, amely csak személyeket és keresési eredményeket tartalmaz, az iframe Beágyazási URL-címe a következőképpen fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Az IFRAME-ablak címe testreszabható úgy is, hogy `&title=<YourTitle>` biztosít az IFRAME URL-címéhez. (Testreszabja a HTML \<title > értéket).
    
Ha például azt szeretné, hogy az IFRAME-ablak a "MyInsights" címet adja meg, az URL-cím a következőhöz hasonló lesz:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Fontos tudni, hogy ez a beállítás csak olyan esetekben releváns, ha azt szeretné, hogy az elemzések új ablakban nyíljanak meg.

### <a name="player-widget"></a>Lejátszó vezérlő

A Video Indexer-lejátszó beágyazásakor megadhatja a lejátszó méretét az iframe méretének meghatározásával.

Példa:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Alapértelmezés szerint a Video Indexer-lejátszó automatikusan létrehozta a videó átiratán alapuló, automatikusan létrehozott feliratozást. A videóból kinyert átirat a videó feltöltésekor kiválasztott forrás nyelvét jelöli.

Ha más nyelvű beágyazást szeretne használni, `&captions=< Language | ”all” | “false” >` adhat hozzá a beágyazási lejátszó URL-címéhez. Ha a feliratokat az összes elérhető nyelv feliratában szeretné használni, használja a `all` értéket. Ha azt szeretné, hogy a feliratok alapértelmezés szerint megjelenjenek, `&showCaptions=true` adhat át.

A beágyazási URL-cím ezután a következőképpen fog kinézni: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Ha le szeretné tiltani a feliratokat, `false`ként átadhatja a `captions` paraméter értékét.

#### <a name="autoplay"></a>Robotpilota
Alapértelmezés szerint a lejátszó elkezdi lejátszani a videót. úgy is dönthet, hogy nem továbbítja `&autoplay=false`t az előző beágyazási URL-címre.

## <a name="next-steps"></a>Következő lépések

Video Indexer információk megtekintésével és szerkesztésével kapcsolatos információkért lásd: [video Indexer-információk megtekintése és szerkesztése](video-indexer-view-edit.md).

Tekintse meg a [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ)is.
