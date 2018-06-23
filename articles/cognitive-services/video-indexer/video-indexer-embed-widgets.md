---
title: Az alkalmazások Azure videó indexelő widgeteket beágyazása |} Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/04/2018
ms.author: juliako
ms.openlocfilehash: 8da06253c58a2de474e879f18013fa1e57f5668d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349710"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Videó indexelő widgeteket beágyazása az alkalmazások

Videó indexelő widgeteket beágyazási kétféle támogatja az alkalmazásba: **kognitív Insights** és **Player**. 

* A **kognitív Insights** widget tartalmaz minden visual insights, hogy a folyamat indexelő videóból könyvtárban találhatók. 
    Az elemzések widget támogatja a következő opcionális URL-cím paraméterei:

    |Name (Név)|Meghatározás|Leírás|
    |---|---|---|
    |widgetek|Vesszővel elválasztott karakterlánc|Lehetővé teszi, hogy a leképezni kívánt elemzéseket vezérlését. <br/>Példa: **widgeteket személyek, márkákat =** , ezzel veszélyezteti csak személyek és márkákat felhasználói felület insights<br/>Az elérhető lehetőségek: személyek, kulcsszavakat, jegyzeteket, márkákat, hangulati elemek, Beszélgetés szövegének, keresés | 
* A **Player** widget lehetővé teszi, hogy adatfolyamként küldje el a videó adaptív átviteli sebesség.

    A player widget támogatja a következő opcionális URL-cím paraméterei:

    |Name (Név)|Meghatározás|Leírás|
    |---|---|---|
    |T|Indítás másodpercben|A pont lejátszása az adott idő alatt a player start teszi.<br/>Példa: t = 60|
    |feliratok|Nyelvkód|Az adott nyelvű feliratot beolvassa a widget a feliratok menü rendelkezésre álló betöltése során.<br/>Példa: feliratai = en-Us|
    |showCaptions|Logikai érték|Lehetővé teszi a Windows Media player betöltése a következő már engedélyezve van a felirat.<br/>Példa: showCaptions = true|
    |type||Egy hang player felszín (videó rész eltávolítása) aktiválja.<br/>Példa: írja be a hang =|
    |automatikus lejátszásának|Logikai érték|Döntse el, hogy a Windows Media player kell kezdődnie, betöltésekor a videó lejátszása (alapértelmezett értéke igaz).<br/>Példa: automatikus lejátszásának = false|
    |nyelv|Nyelvkód|A Windows Media player vezérlő szabályozza honosítási (alapértelmezett érték en-US)<br/>Példa: nyelvi de-DE =|

## <a name="embedding-public-content"></a>A nyilvános tartalmakhoz beágyazás

1. Jelentkezzen be a [videó indexelő](https://api-portal.videoindexer.ai/) fiók. 
2. Kattintson az "embed" gombra, a videó alatt látható.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    A gombra kattintást követően egy beágyazási modális fog megjelenni a képernyőn adja meg a milyen widget kívánt beágyazási az alkalmazásban.
    Widgetet kiválasztása (**Player** vagy **kognitív Insights**), a beágyazott kódot, hogy illessze be az alkalmazás létrehozza.
 
3. Válassza ki a kívánt widget (**kognitív Insights** vagy **Player**).
4. Másolja a beágyazási kódot, és hozzá az alkalmazáshoz. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Bizalmas tartalom beágyazása

Első beágyazása szereplő beágyazandó előugró (ahogy az előző szakaszban) **nyilvános** csak videók. 

Ha szeretné beágyazni egy **titkos** videó, végre kell hajtania a hozzáférési tokent az **iframe**meg **src** attribútum:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<VideoId>/?accessToken=<accessToken>
    
Használja a [ **Get Insights Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API a kognitív Insights widget tartalom eléréséhez, vagy használjon [ **videó hozzáférési jogkivonat beszerzése** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) , és adja hozzá, mint egy a fentiek szerint lekérdezése a param URL-címét. Ez az URL-címet adja meg a **iframe**tartozó **src** érték.

Ha lehetővé szeretné tenni a szerkesztési insights képességei (ilyen például a webes alkalmazás van) a beágyazott widget, akkor adjon át egy hozzáférési jogkivonatot szerkesztési engedélyekkel. Használjon [ **Get Insights Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) vagy [ **videó hozzáférési jogkivonat beszerzése** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) rendelkező **& allowEdit = true**. 

## <a name="widgets-interaction"></a>Widgetek interakció

A **kognitív Insights** widget módon léphetnek interakcióba az alkalmazás videót. Ez a szakasz bemutatja, hogyan kapcsolati eléréséhez.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

Ahhoz, hogy a videó indexelő widgeteket más összetevők kommunikálni, a videó indexelő szolgáltatás a következőket hajtja végre:

- Az eltérő eredetű kommunikál HTML5 metódus **postMessage** és 
- Ellenőrzi az üzenet VideoIndexer.ai származási között. 

Ha úgy dönt, hogy a saját player kód megvalósítását, és hajtsa végre az integráció a **kognitív Insights** widgets, feladata a érvényesíteni az üzenetet, amely VideoIndexer.ai származik forrása.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Mindkét típusú widgeteket beágyazása az alkalmazás / blog (ajánlott) 

Ez a szakasz bemutatja, hogyan két videó indexelő widgeteket közötti interakció eléréséhez, ha a felhasználó kattint az alkalmazás insight levő, a Windows Media player a megfelelő pillanatban ugrik.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Másolás a **Player** widget beágyazási kódot.
2. Másolás a **kognitív Insights** beágyazási kódot.
3. Adja hozzá a [ **közvetítő fájl** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) a két widgeteket közötti kommunikáció kezelésére:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Most, amikor a felhasználó a insight vezérlő az alkalmazásra kattint, a Windows Media player ugrik a megfelelő pillanatban.

További információkért lásd: [ebben a bemutatóban a](https://api-portal.videoindexer.ai/demo-all-widgets).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A kognitív Insights widget beágyazása, és használja az Azure Media Player számára, hogy a tartalom

Ez a szakasz bemutatja, hogyan közötti interakció eléréséhez a **kognitív Insights** widget- és az Azure Media Player példány használatával a [AMP beépülő modul](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. A videó indexelő beépülő modul hozzáadása a AMP player számára.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Azure Media Player példányosítható és a videó indexelő beépülő modul.

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

3. Másolás a **kognitív Insights** beágyazási kódot.

Most már képes kommunikálni az Azure Media Player kell lennie.

További információkért lásd: [ebben a bemutatóban a](https://api-portal.videoindexer.ai/demo-your-amp).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Videó indexelő kognitív Insights widget beágyazása, és a saját lejátszóval (Ez lehet bármely player)

A saját player használatakor rendelkezik-e a player kezelésére gondoskodunk saját kezűleg a kommunikációs elérése érdekében. 

1. Helyezze be a videólejátszó.

    Például egy szabványos HTML5-lejátszó

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. A kognitív Insights widget beágyazása.
3. Kommunikáció a Player megvalósíthatja a "üzenet" esemény figyel. Példa:

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


További információkért lásd: [ebben a bemutatóban a](https://api-portal.videoindexer.ai/demo-your-player).

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha videó indexelő insights beágyazása a saját AMP player, használhatja a **GetVttUrl** metódus használatával kérje le a feliratok (feliratok). Ön is meghívhatja a javascript metódus a videó indexelő AMP beépülő modul a **getSubtitlesUrl** (a korábban bemutatott). 

## <a name="customizing-embeddable-widgets"></a>Beágyazható widgeteket testreszabása

### <a name="cognitive-insights-widget"></a>Kognitív insights widget
Kiválaszthatja a kívánt érték megadásával insights típusú a következő URL paraméter, a beágyazási kódot az beszerzése (API-t vagy a webalkalmazás):

**& widgeteket =** \<kívánt widgeteket listája >

A lehetséges értékek: személyek, kulcsszavakat, a hangulati elemek, a Beszélgetés szövegének, a keresés.

Például, ha szeretné beágyazni az iframe beágyazása URL-cím csak személyek és a keresési insights tartalmazó widgetet fog kinézni: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?widgets=people, keresés

A cím az iframe ablak úgy is beállítható, adja meg a **& cím =** <YourTitle> iframe URL-címét. (Ez fogja szabni a html \<title > érték).
Például, ha szeretne adni a iframe ablakban a cím "MyInsights", az URL-címet fog kinézni: https://www.videoindexer.ai/embed/insights/c4c1ad4c9a/?title=MyInsights. Figyelje meg, hogy ez a beállítás esetén megfelelő csak abban az esetben meg kell nyitnia a insights egy új ablakban.

### <a name="player-widget"></a>Player widget
Videó indexelő player beágyazásakor kiválaszthatja a Windows Media player méretét az IFRAME méret megadásával.

Példa:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/{id}” frameborder="0" allowfullscreen />

Alapértelmezett videó indexelő Windows Media Player fog rendelkezik automatikus jönnek létre, amelyek a videó feltöltése kiválasztott nyelvvel forrás videó kinyert videó szövegének alapján feliratok.

Ha eltérő nyelvű beágyazni kívánt adhat **& feliratok = < nyelv |} "all" |} "false" >** beágyazási player URL-CÍMÉT vagy put "all" értékkel, ha azt szeretné, hogy az összes elérhető nyelvek feliratok.
Ha azt szeretné, hogy a feliratok alapértelmezés szerint megjelenő átadhatók **& showCaptions = true**

A beágyazási URL-cím majd fog kinézni: https://www.videoindexer.ai/embed/player/9a296c6ec3/?captions=italian. Ha le szeretné tiltani a feliratok átadhatók "false" feliratok paraméter értékeként.

Automatikus play – a Windows Media player elindul a videó lejátszása alapértelmezés szerint. dönthet úgy, hogy nem által átadott & automatikus lejátszásának = FALSE értékre a fenti beágyazási URL-címre.

## <a name="next-steps"></a>További lépések

Megtekintése és szerkesztése a videó indexelő insights kapcsolatos információkért lásd: [ez](video-indexer-view-edit.md) cikk.

## <a name="see-also"></a>Lásd még

[Az indexelő áttekintő videó](video-indexer-overview.md)
