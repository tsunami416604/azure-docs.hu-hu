---
title: Az alkalmazások Azure Video Indexer widgetek beágyazása |} A Microsoft Docs
description: ''
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 6ee87b6d7c89745472bf6000de66f682cbf2cca9
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544115"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>A Video Indexer widgetek beágyazása az alkalmazásokba

Ez a cikk bemutatja, hogyan beágyazása a Video Indexer widgetek az alkalmazásokba. A video Indexer widgetek beágyazási két típusát támogatja az alkalmazásba: **Cognitive Insights** és **Player**. 
## <a name="widget-types"></a>Widget típusok

### <a name="cognitive-insights-widget"></a>A cognitive Insights widget

A **Cognitive Insights** widgetet tartalmaz minden visual elemzéseket kínálnak, amelyek az indexelési folyamat a videó könyvtárban találhatók. Az insights widget támogatja az URL-cím a következő opcionális paraméterei:

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|widgetek|Karakterláncok vesszővel elválasztva|Lehetővé teszi, hogy a megjeleníteni kívánt insights vezérlését. <br/>Példa: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` jelenik meg egyetlen személyek és márkákat felhasználói felület insights<br/>Az elérhető lehetőségek: azok, akik, kulcsszavakat, jegyzetek, márkákat, hangulati, átirat, keresés.<br/>nem támogatott URL-CÍMEN keresztül, verzió = 2<br/><br/>**Megjegyzés:** a **widgetek** param URL-cím nem támogatott, ha **verzió = 2** szolgál. |
|verzió:|Verziói a **Cognitive Insights** widget|A legújabb elemzéseit widget frissítések lekéréséhez adja hozzá a `?version=2` param a beágyazási URL-cím lekérdezése. Például: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?version=2` <br/> A régebbi verzió beszerzéséhez, csak távolítsa el a `version=2` az URL-címből.

### <a name="player-widget"></a>Player widget

A **Player** widget lehetővé teszi, hogy a videó használatával adaptív átviteli sebesség. A player widget támogatja az URL-cím a következő opcionális paraméterei:

|Name (Név)|Meghatározás|Leírás|
|---|---|---|
|T|A kezdő másodperc|Lehetővé teszi a pont lejátszása a megadott idő a lejátszó elindítása.<br/>Példa: t = 60|
|akadálymentes feliratok|Nyelvkód|Lekérdezi az adott nyelven a felirat a widget elérhető lesz az a feliratok menü betöltése során.<br/>Példa: a feliratok = en-US|
|showCaptions|Logikai érték|Lehetővé teszi a Betöltés a feliratok már engedélyezve van a Windows Media player.<br/>Példa: showCaptions = true|
|type||Egy hanglejátszó felszín (video rész törli) aktiválódik.<br/>Példa: az = audio|
|Automatikus lejátszás|Logikai érték|Azt jelzi, ha a Windows Media player kell videó lejátszásának indítása betöltésekor (az alapértelmezett érték true).<br/>Példa: automatikus lejátszás = false|
|Nyelv|Nyelvkód|Azt szabályozza, a lejátszó nyelv (alapértelmezés: en-US)<br/>Példa: nyelv de-DE =|

## <a name="embedding-public-content"></a>Nyilvános tartalmak beágyazása

1. Keresse meg a [Video Indexer](https://www.videoindexer.ai/) webhelyet, és jelentkezzen be.
2. Kattintson a "beágyazási" gombra a videó alatt látható.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Miután rákattint a gombra, egy beágyazási modális fog megjelenni a képernyőn, ahol kiválaszthatja, melyik widget szeretné beágyazási az alkalmazásban.
    Válassza a widget (**Player** vagy **Cognitive Insights**), hogy az alkalmazás illessze be a beágyazott kódot állít elő.
 
3. Válassza ki a kívánt widget (**Cognitive Insights** vagy **Player**).
4. Másolja a beágyazási kódot, és adja hozzá az alkalmazáshoz. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Titkos tartalmak beágyazása

Első beágyazása a kódok beágyazni ablakok (ahogyan az előző szakaszban látható) **nyilvános** csak videókra. 

Ha a beágyazni kívánt egy **privát** videó, végre kell hajtania a hozzáférési jogkivonatot a **iframe**a **src** attribútum:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>
    
Használja a [ **Get Insights Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) API, a Cognitive Insights widget tartalmát szerkesztheti, vagy használja [ **videó hozzáférési jogkivonat lekérése** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) , és adja hozzá, mint egy lekérdezési param az URL-címre, jelennek meg. Adja meg az URL-címet, mint a **iframe**a **src** értéket.

Ha azt szeretné, szerkesztési insights képességeket biztosít (ilyen például a webalkalmazás már) a beágyazott widget található, akkor át egy hozzáférési jogkivonatot szerkesztési engedélyekkel. Használat [ **Get Insights Widget** ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) vagy [ **videó hozzáférési jogkivonat lekérése** ](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) a **& szerkeszthető = true**. 

## <a name="widgets-interaction"></a>Widgetek-interakció

A **Cognitive Insights** widget módon léphetnek interakcióba az alkalmazás egy videót. Ez a szakasz bemutatja, hogyan érhet el a kommunikációt.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Eltérő eredetű kommunikáció

A Video Indexer widgetek kommunikálni más összetevők lekéréséhez a Video Indexer szolgáltatás a következőket teszi:

- Eltérő eredetű kommunikációval HTML5-alapú módszer **postMessage** és 
- Az üzenet érvényesíti VideoIndexer.ai forrás között. 

Ha úgy dönt, hogy a saját player kód megvalósítását, és végezze el az integrációt **Cognitive Insights** widgets, feladata a ellenőrzése a forrás, az üzenet, amely VideoIndexer.ai származik.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Mindkét típusú widgetek beágyazása az alkalmazásba vagy blog (ajánlott) 

Ez a szakasz bemutatja, hogyan érhet el két Video Indexer widgetek közötti interakció így amikor egy felhasználó rákattint a insight vezérlő az alkalmazás, a Windows Media player a megfelelő pillanatban ugrik.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Másolás a **Player** widget beágyazási kódot.
2. Másolás a **Cognitive Insights** beágyazási kódot.
3. Adja hozzá a [ **közvetítő fájl** ](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) kezelni a két widgeteket közötti kommunikáció:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Mostantól Ha egy felhasználó az alkalmazás a insight vezérlő kattint, a Windows Media player ugrik a megfelelő pillanatban.

További információkért lásd: [bemutató](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>A Cognitive Insights widgetek beágyazása, és használja az Azure Media Player a tartalom lejátszása

Ez a szakasz bemutatja, hogyan érhet el belőle egy **Cognitive Insights** widget és a egy Azure Media Player példánya használja a [AMP beépülő modul](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. A Video Indexer beépülő modul hozzáadása a és lejátszó.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Hozza létre az Azure Media Player a Video Indexer beépülő modullal.

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

3. Másolás a **Cognitive Insights** beágyazási kódot.

Most már képes kommunikálni az Azure Media Player kell lennie.

További információkért lásd: [bemutató](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Beágyazása a Video Indexer Cognitive Insights widget, és használjon saját lejátszót (Ez lehet egyetlen lejátszó)

A saját player használatakor gondoskodik a lejátszó kezelésére szolgáló kell saját kezűleg a kommunikáció megvalósítása érdekében. 

1. Helyezze be a videó lejátszási.

    Ha például a szabványos HTML5-lejátszó

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. A Cognitive Insights widget ágyazhat be.
3. A "message" esemény úgy hajtja végre a kommunikációhoz a Player. Példa:

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


További információkért lásd: [bemutató](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Feliratok hozzáadása

Ha Videóindexelő-elemzések beágyazása saját AMP Playerrel, használhatja a **GetVttUrl** metódus zárt feliratozást (felirat) lekéréséhez. A Video Indexer AMP beépülő modul a is meghívhat egy javascript metódus **getSubtitlesUrl** (ahogyan korábban látható). 

## <a name="customizing-embeddable-widgets"></a>Beágyazható widgetek testreszabása

### <a name="cognitive-insights-widget"></a>A cognitive insights widget
Kiválaszthatja a kívánt érték megadásával insights típusú, a következő URL paraméter hozzáadva a beágyazási kód lekérése (API-ból vagy a webes alkalmazás):

**& widgetek =** \<állásának widgetek listája >

A lehetséges értékek a következők: azok, akik, kulcsszavakat, hangulati, átirat, keresés.

Például, ha azt szeretné, beágyazása az IFRAME-keret beágyazási URL-cím csak személyek és a keresési elemzéseket tartalmazó widget fog kinézni: https://www.videoindexer.ai/embed/insights/ <accountId> / <videoId>/? widgetek =, személyek keresése

Az IFRAME-keret ablak címe is testre azáltal, hogy **& cím =** <YourTitle> az IFRAME-keret URL-címre. (Azt a html, testre szabom \<title > érték).
Például, ha azt szeretné, hogy az IFRAME-keret ablak a cím "MyInsights", az URL-címet fog kinézni: https://www.videoindexer.ai/embed/insights/ <accountId> / <videoId>/? cím = MyInsights. Figyelje meg, hogy ez a beállítás akkor jelentősége, csak abban az esetben, amikor szüksége van az insights megnyitása új ablakban.

### <a name="player-widget"></a>Player widget
Beágyazása a Video Indexer lejátszó, ha kiválaszthatja a Windows Media player méretét adja meg az IFRAME-keret méretét.

Példa:

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />

Video Indexer player alapértelmezés szerint fog rendelkezik automatikus feliratkészítés az átiratok a videó, amely a videóban látottakat a Forrásnyelv a videó feltöltésekor kiválasztott kivonták alapján.

Ha eltérő nyelvű beágyazni kívánt, hozzáadhat **& feliratok = < nyelv |} "all" |} "false" >** a beágyazási lejátszó URL-címet, vagy a put "all" értéket, ha azt szeretné, hogy az összes elérhető nyelveket a feliratok.
Ha azt szeretné, hogy a feliratok alapértelmezés szerint megjelenő, amelyet továbbíthat **& showCaptions = true**

A beágyazási URL-címet ezután fog kinézni: https://www.videoindexer.ai/embed/player/ <accountId> / <videoId>/? feliratok olasz =. Ha szeretné letiltani a feliratok, "false" feliratok paraméter értéke adhat át.

Automatikus lejátszás – a Windows Media player elindul a videó lejátszásának alapértelmezés szerint. nem választhat passing & Automatikus lejátszás = hamis értékre a fenti beágyazási URL-címet.

## <a name="next-steps"></a>További lépések

Megtekintheti és szerkesztheti a Videóindexelő-elemzések kapcsolatos információkért lásd: [ez](video-indexer-view-edit.md) cikk.

Emellett tekintse meg [Video indexer codepen](https://codepen.io/videoindexer/pen/eGxebZ).
