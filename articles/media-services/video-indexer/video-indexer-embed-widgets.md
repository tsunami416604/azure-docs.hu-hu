---
title: Videoindexelő widgetek beágyazása az alkalmazásokba
titleSuffix: Azure Media Services
description: További információ a Video Indexer widgetek alkalmazásokba való beágyazásáról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 4d92bd3709c5f56db0095ca1be2caa0e9c78b42f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336818"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Videoindexelő widgetek beágyazása az alkalmazásokba

Ez a cikk bemutatja, hogyan ágyazhat be videoindexelő widgeteket az alkalmazásokba. A Video Indexer háromféle widget beágyazását támogatja az alkalmazásokba: *Cognitive Insights*, *Player*és *Editor*.

A 2-es verziótól kezdve a widget alap URL-címe tartalmazza a megadott fiók régióját. Az USA nyugati régiójában például egy `https://wus2.videoindexer.ai/embed/insights/...`fiók a következőket hozza létre: .

## <a name="widget-types"></a>Vezérlőtípusok

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

A Kognitív elemzési vezérlő az összes vizuális elemzést tartalmazza, amely a videóindexelési folyamat során lett kinyerve. A Cognitive Insights widget a következő választható URL-paramétereket támogatja:

|Név|Meghatározás|Leírás|
|---|---|---|
|`widgets` | Vesszővel elválasztott sztringek | Lehetővé teszi a megjeleníteni kívánt elemzések szabályozását.<br/>Példa: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` csak az emberek és a kulcsszavak felhasználói felületének elemzési adatait jeleníti meg.<br/>Elérhető lehetőségek: emberek, animáltKarakterek, kulcsszavak, címkék, érzelmek, érzelmek, témák, kulcsképek, átirat, ocr, hangszórók, jelenetek, és namedEntities.|
|`controls`|Vesszővel elválasztott sztringek|Lehetővé teszi a megjeleníteni kívánt vezérlők vezérlését.<br/>Példa: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` csak a keresési és letöltési gombot jeleníti meg.<br/>Elérhető lehetőségek: keresés, letöltés, készletek, nyelv.|
|`language`|Rövid nyelvkód (nyelvnév)|Az elemzési nyelvet szabályozza.<br/>Például: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Vagy`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Rövid nyelvkód | A felhasználói felület nyelvét szabályozza. Az alapértelmezett érték `en`. <br/>Példa: `locale=de`.|
|`tab` | A kijelölt alapértelmezett lap | Az **Elemzések** lapot szabályozza, amely alapértelmezés szerint megjelenik. <br/>Példa: `tab=timeline` az elemzéseket az **Idővonal** lap kijelölésével jeleníti meg.|

### <a name="player-widget"></a>Lejátszó vezérlő

A Lejátszó widget segítségével adaptív átviteli sebesség használatával streamelhet videókat. A Lejátszó widget a következő választható URL-paramétereket támogatja.

|Név|Meghatározás|Leírás|
|---|---|---|
|`t` | Másodpercekkel az elejétől | A játékos a megadott időponttól kezdel el játszani.<br/> Példa: `t=60`. |
|`captions` | Nyelvkód | A felirat letöltése a megadott nyelven történik a widget betöltése során, hogy elérhető legyen a **Feliratok** menüben.<br/> Példa: `captions=en-US`. |
|`showCaptions` | Logikai érték | Beállítja, hogy a lejátszó betöltésekor a feliratok megjelenítése már engedélyezve legyen.<br/> Példa: `showCaptions=true`. |
|`type`| | Aktiválja az audiolejátszó bőrét (a videorészt eltávolítjuk).<br/> Példa: `type=audio`. |
|`autoplay` | Logikai érték | Azt jelzi, hogy a lejátszónak betöltéskor el kell-e kezdenie a videó lejátszását. Az alapértelmezett érték `true`.<br/> Példa: `autoplay=false`. |
|`language` | Nyelvkód | A lejátszó nyelvét szabályozza. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`.|

### <a name="editor-widget"></a>Szerkesztő widget

A Szerkesztő widget segítségével új projekteket hozhat létre, és kezelheti a videó elemzési adatait. A Szerkesztő widget a következő választható URL-paramétereket támogatja.

|Név|Meghatározás|Leírás|
|---|---|---|
|`accessToken`<sup>*</sup> | Sztring | Hozzáférést biztosít azokhoz a videókhoz, amelyek csak a widget beágyazására használt fiókban találhatók.<br> A szerkesztő widget `accessToken` hez szükség van a paraméterre. |
|`language` | Nyelvkód | A lejátszó nyelvét szabályozza. Az alapértelmezett érték `en-US`.<br/>Példa: `language=de-DE`. |
|`locale` | Rövid nyelvkód | Az elemzési nyelvet szabályozza. Az alapértelmezett érték `en`.<br/>Példa: `language=de`. |

<sup>*</sup>A tulajdonosnak `accessToken` óvatosan kell eljárnia.

## <a name="embedding-public-content"></a>Nyilvános tartalom beágyazása

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyére.
2. Jelölje ki azt a videót, amelyet dolgozni szeretne.
3. Válassza a videó alatt megjelenő **Beágyazás** gombot.

    ![Beágyazás gomb a Video Indexelőben](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Miután kiválasztotta a **Beágyazás** gombot, kiválaszthatja azt a widgetet, amelyet be szeretne ágyazni az alkalmazásba.
4. Válassza ki a kívánt típusú widgetet **(Cognitive Insights,** **Player**vagy **Editor**).
 
5. Másolja a beágyazási kódot, majd adja hozzá az alkalmazáshoz.

    ![Alkalmazás kódjának beágyazása – Videoindexelő](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Ha problémái vannak a videoURL-ek `location` megosztásával, adja hozzá a paramétert a hivatkozáshoz. A paramétert [azAzure-régiókra](regions.md)kell beállítani, amelyekben a Video Indexelő létezik. Például: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Személyes tartalom beágyazása

Privát videó beágyazásához egy hozzáférési jogkivonatot kell átadnia az `src` iframe attribútumában:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
A Cognitive Insights widget tartalmának levételéhez használja az alábbi módszerek egyikét:

- Az Insights Widget API [beszereznie.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- A [Get Video Access Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Adja hozzá lekérdezési paraméterként az URL-címhez. Adja meg ezt `src` az URL-címet az iframe értékeként, ahogy azt korábban is látható.

Ahhoz, hogy a beágyazott widgetben szerkesztési elemzési lehetőségeket biztosítson, át kell adnia egy hozzáférési tokent, amely szerkesztési engedélyeket tartalmaz. Használja [a Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) `&allowEdit=true`et vagy a Video Access Token [beszereznie](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) a segítségével.

## <a name="widgets-interaction"></a>Vezérlőinterakciók

A Cognitive Insights widget interakcióba léphet egy videóval az alkalmazásban. Ez a szakasz bemutatja, hogy ez a kommunikáció hogyan valósítható meg.

![Cognitive Insights widget videoindexelő](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Ahhoz, hogy a Video Indexer widgetek kommunikálhassanak más összetevőkkel, a Video Indexer szolgáltatás:

- A kereszt-eredetű kommunikáció HTML5 metódusát használja. `postMessage`
- ellenőrzi az üzenetet a VideoIndexer.ai forráson.

Ha megvalósítja a saját játékoskódját, és integrálható a Cognitive Insights widgetekkel, az Ön felelőssége, hogy ellenőrizze az VideoIndexer.ai származó üzenet eredetét.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgetek beágyazása az alkalmazásba vagy a blogba (ajánlott)

Ez a szakasz bemutatja, hogyan érhető el a két Video Indexer widget közötti interakció, így amikor a felhasználó kiválasztja az alkalmazás insight vezérlőjét, a játékos a megfelelő pillanatra ugrik.

1. Másolja a Lejátszó vezérlő beágyazási kódját.
2. Másolja a Kognitív elemzési vezérlő beágyazási kódját.
3. Adja hozzá a [közvetítőfájlt](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js), amely a két vezérlő közötti kommunikációt kezeli:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Most, amikor a felhasználó kiválasztja az alkalmazás éleslátás-vezérlését, a játékos a megfelelő pillanatra ugrik.

További információt a [Video Indexer - Beágyazás mindkét Widgets demo című témakörben talál.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A Kognitív elemzési vezérlő beágyazása és az Azure Media Player használata a tartalmak lejátszásához

Ez a szakasz bemutatja, hogyan érhető el a Cognitive Insights-widget és az Azure Media Player-példány közötti interakció az [AMP beépülő modul](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)használatával.

1. Videoindexelő beépülő modul hozzáadása az AMP-lejátszóhoz:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Az Azure Media Player létrehozása a Video Indexer beépülő modullal.

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
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Másolja a Kognitív elemzési vezérlő beágyazási kódját.

Most már kommunikálhat az Azure Media Playerrel.

További információt az [Azure Media Player + VI Insights bemutatóban](https://codepen.io/videoindexer/pen/rYONrO)talál.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>A Video Indexer Cognitive Insights widget beágyazása és egy másik videolejátszó használata

Ha az Azure Media Player-en kívül más videolejátszót használ, manuálisan kell manipulálnia a videolejátszót a kommunikáció eléréséhez.

1. Szúrja be a videólejátszót.

    Például egy szabványos HTML5-lejátszó:

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

További információt az [Azure Media Player + VI Insights bemutatóban](https://codepen.io/videoindexer/pen/YEyPLd)talál.

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha a saját [Azure Media Player-alkalmazásával](https://aka.ms/azuremediaplayer)videoindexelő-elemzéseket ágyaz be, a módszer segítségével beszerezheti a `GetVttUrl` feliratokat (feliratokat). A JavaScript-metódust a Video Indexer AMP `getSubtitlesUrl` beépülő modulból is meghívhatja (ahogy azt korábban is látható).

## <a name="customizing-embeddable-widgets"></a>Beágyazható vezérlők testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív elemzési vezérlő

Kiválaszthatja a kívánt elemzési típusokat. Ehhez adja meg őket a következő URL-paraméter értékeként, amely hozzáadódik a beágyazási kódhoz `&widgets=<list of wanted widgets>`(az API-ból vagy a webalkalmazásból): .

A lehetséges értékek a következők: **emberek**, **kulcsszavak,** **érzelmek**, **átirat**, és **a keresés.**

Ha például olyan widgetet szeretne beágyazni, amely csak személyeket és keresési elemzéseket tartalmaz, az iframe beágyazási URL-címe a következőképpen fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Az iframe ablak címe az iframe URL-cím megadásával `&title=<YourTitle>` is testreszabható. (Ez testre \<szabja a HTML-cím> érték).

Ha például azt szeretné, hogy az iframe-ablak "MyInsights" címet adjon, az URL így fog kinézni:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Fontos tudni, hogy ez a beállítás csak olyan esetekben releváns, ha azt szeretné, hogy az elemzések új ablakban nyíljanak meg.

### <a name="player-widget"></a>Lejátszó vezérlő

A Video Indexer-lejátszó beágyazásakor megadhatja a lejátszó méretét az iframe méretének meghatározásával.

Példa:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Alapértelmezés szerint a Video Indexer player automatikusan létrehozott feliratokat, amelyek alapján a videó átirata. Az átirat a videóból a videó feltöltésekén kiválasztott forrásnyelvvel kerül kinyerésre.

Ha más nyelvvel szeretne beágyazni, `&captions=< Language | "all" | "false" >` hozzáadhatja a beágyazólejátszó URL-címét. Ha az összes elérhető nyelven szeretne feliratokat kapni, használja az értéket. `all` Ha azt szeretné, hogy a feliratok alapértelmezés `&showCaptions=true`szerint megjelenjenek, átadhatja a programot.

A beágyazási URL így fog kinézni:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Ha le szeretné tiltani a feliratokat, `captions` a `false`paraméter értékét a .

#### <a name="autoplay"></a>Robotpilota
Alapértelmezés szerint a lejátszó elkezdi lejátszani a videót. úgy is dönthet, `&autoplay=false` hogy nem, ha átadja az előző beágyazási URL-címet.

## <a name="code-samples"></a>Kódminták

Tekintse meg a [kódminták](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) tártárát, amely a Video Indexer API és a Widgets mintáit tartalmazza:

| Fájl/mappa                       | Leírás                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Videóindexelő videó betöltése egyéni Azure Media Playerbe.                        |
| `azure-media-player-vi-insights`  | A VI Insights beágyazása egyéni Azure Media Player alkalmazásba.                             |
| `control-vi-embedded-player`      | Beágyazni VI Player és ellenőrzik azt kívülről.                                    |
| `custom-index-location`           | VI Insights beágyazása egyéni külső helyről (lehet ügyfél egy blob).     |
| `embed-both-insights`             | A VI Insights alapszintű használata a játékosok és az elemzések között egyaránt.                            |
| `embed-insights-with-AMP`         | Vi Insights-widget beágyazása egyéni Azure Media Player alkalmazásba.                      |
| `customize-the-widgets`           | Vi widgetek beágyazása testreszabott beállításokkal.                                     |
| `embed-both-widgets`              | Beágyazni VI Player és Insights és kommunikálni közöttük.                      |
| `url-generator`                   | A felhasználók által megadott beállítások alapján hoz létre egyéni beágyazási URL-t.             |
| `html5-player`                    | A VI Insights beágyazása alapértelmezett HTML5-videólejátszóval.                           |

## <a name="next-steps"></a>További lépések

A Video Indexer-elemzések megtekintéséről és szerkesztéséről a [VideoIndexer-elemzések megtekintése és szerkesztése című témakörben](video-indexer-view-edit.md)talál további információt.

Is, nézd meg [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
