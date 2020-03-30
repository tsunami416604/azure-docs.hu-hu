---
title: Sima streaming plugin a nyílt forráskódú média keretrendszerhez
description: Ismerje meg, hogyan használhatja az Azure Media Services Smooth Streaming beépülő modult az Adobe open source media framework számára.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 45a2829411ea4713df898c90be73792718160cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255112"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>A Microsoft Smooth Streaming beépülő modul használata az Adobe open source media framework-hez  
## <a name="overview"></a>Áttekintés
A Microsoft Smooth Streaming beépülő modul a Nyílt forráskódú Media Framework 2.0 (SS for OSMF) számára kiterjeszti az OSMF alapértelmezett képességeit, és hozzáadja a Microsoft Smooth Streaming tartalom lejátszását az új és meglévő OSMF lejátszókhoz. A plugin is hozzáteszi, Smooth Streaming lejátszási képességek Strobe Media Lejátszás (SMP).

SS for OSMF tartalmaz két változata plugin:

* Statikus Smooth Streaming plugin OSMF (.swc)
* Dinamikus Smooth Streaming plugin OSMF (.swf)

Ez a dokumentum feltételezi, hogy az olvasó általános munkaismeretekkel rendelkezik az OSMF és az OSMF beépülő modulokkal kapcsolatban. Az OSMF-ről további információt az [OSMF hivatalos webhelyén](http://osmf.org/)található dokumentációban talál.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming plugin az OSMF 2.0-hoz
A plugin támogatja az igény szerinti Smooth Streaming tartalom betöltését és lejátszását a következő funkciókkal:

* Igény szerinti zökkenőmentes streamelési lejátszás (lejátszás, szünet, keresés, leállítás)
* Élő, sima streamelés (lejátszás)
* Élő DVR funkciók (Szünet, Keresés, DVR lejátszás, Go-to-Live)
* Videokodekek támogatása - H.264
* Audio kodekek támogatása - AAC
* Több hangnyelv váltás osmf beépített API-kkal
* Maximális lejátszási minőség választás osmf beépített API-kkal
* Oldalkocsis feliratok OSMF feliratokkal plugin
* Adobe&reg; &reg; Flash Player 11.4 vagy újabb.
* Ez a verzió csak az OSMF 2.0-s verzióját támogatja.

## <a name="supported-features-and-known-issues"></a>Támogatott funkciók és ismert problémák
A támogatott szolgáltatások, a nem támogatott szolgáltatások és az ismert problémák teljes listáját ebben a [dokumentumban](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)tájékozatja.

## <a name="loading-the-plugin"></a>A beépülő modul betöltése
Az OSMF-bővítmények statikusan (fordításkor) vagy dinamikusan (futásidőben) tölthetők be. A Smooth Streaming plugin OSMF letöltés tartalmazza mind a dinamikus és statikus változatban.

* Statikus betöltés: Statikus betöltéshez statikus könyvtárfájlra (SWC) van szükség. Statikus beépülő modulok kerülnek hivatkozásként a projektek és egyesíteni belül a végső kimeneti fájlt a fordítás kor.
* Dinamikus betöltés: A dinamikus betöltéshez előre lefordított (SWF) fájlszükséges. A dinamikus bővítmények betöltődnek a futásidőben, és nem szerepelnek a projekt kimenetében. (Lefordított kimenet) A dinamikus bővítmények HTTP és FILE protokollok használatával tölthetők be.

További információ a statikus és dinamikus betöltése, lásd a hivatalos [OSMF plugin oldalon](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS osmf statikus betöltéshez
Az alábbi kódrészlet bemutatja, hogyan töltheti be statikusan az OSMF SS beépülő modulját, és hogyan játszhat le egy alapvető videót az OSMF MediaFactory osztály használatával. Az SS for OSMF kód befoglalása előtt győződjön meg arról, hogy a projekthivatkozás tartalmazza az "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" statikus bővítményt.

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS az OSMF dinamikus betöltése esetén
Az alábbi kódrészlet bemutatja, hogyan töltheti be dinamikusan az OSMF SS beépülő modulját, és hogyan játszhat le egy alapvideót az OSMF MediaFactory osztályhasználatával. Mielőtt az SS for OSMF kódot, másolja a "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" dinamikus plugin a projekt mappába, ha azt szeretné, hogy töltse be a FILE protokoll, vagy másolja a webszerver http-terhelés. Nincs szükség az "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" felvételére a projekthivatkozásokban.

csomag {

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media lejátszás az SS ODMF dinamikus plugin
A Smooth Streaming for OSMF dinamikus plugin kompatibilis [a Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html)segítségével. Az SS for OSMF beépülő modul segítségével smooth streaming tartalom lejátszást adhat az SMP-hez. Ehhez másolja az "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" parancsot egy webkiszolgáló alatt HTTP-betöltéshez a következő lépésekkel:

1. Böngésszen a [Strobe Media Lejátszás beállítási lapján.](http://osmf.org/dev/2.0gm/setup.html) 
2. Állítsa az src-t egy sima streamelési forrásra (pl.http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Hajtsa végre a kívánt konfigurációs módosításokat, és kattintson az Előnézet és frissítés gombra.
   
   **Megjegyzés:** A tartalomwebkiszolgálónak érvényes crossdomain.xml fájlra van szüksége. 
4. Másolja és illessze be a kódot egy egyszerű HTML-lapra kedvenc szövegszerkesztőjével, például a következő példában:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Add Smooth Streaming OSMF plugin a beágyazási kódot, és mentse.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Mentse a HTML-lapot, és tegye közzé egy webkiszolgálón. Tallózással keresse meg a&reg; közzétett weboldalt a kedvenc Flash Player-kompatibilis internetböngészőjével (Internet Explorer, Chrome, Firefox, így tovább).
3. Élvezze a Smooth&reg; Streaming&reg; tartalmakat az Adobe Flash Player ben.

Az OSMF általános fejlesztésével kapcsolatos további információkért tekintse meg az [OSMF](http://osmf.org/resources.html)hivatalos fejlesztési oldalát .

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Microsoft Adaptive Streaming beépülő modul az OSMF frissítéséhez](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

