---
title: "A nyílt forráskódú Media keretrendszer zökkenőmentes adatfolyam beépülő modul"
description: "Ismerje meg, hogyan használható az Azure Media Services Smooth Streaming beépülő modul az Adobe nyílt forrás Media keretrendszert."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 9c764f176ae75085320882de3fb26d8e7d52daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>A Microsoft Smooth Streaming beépülő modul az Adobe nyílt forráskódú Media keretrendszer használata
## <a name="overview"></a>Áttekintés
A Microsoft Smooth Streaming beépülő modul megnyitása forrás Media keretrendszer 2.0-s (SS a OSMF) OSMF alapértelmezett bővíti ki, és hozzáadja a tartalom lejátszását Microsoft Smooth Streaming új és meglévő OSMF játékosok. A beépülő modul villogó Media lejátszás (SMP) is hozzáadja Smooth Streaming lejátszás képességeit.

SS OSMF a beépülő modul két verziója tartalmazza:

* Statikus Smooth Streaming beépülő modul OSMF (.swc)
* Dinamikus Smooth Streaming beépülő modul OSMF (.swf)

Jelen dokumentum céljából feltételezzük, hogy az olvasó rendelkezik OSMF és OSMF általános ismeretét beépülő modulok. OSMF kapcsolatos további információkért tekintse meg a dokumentáció a [hivatalos OSMF webhely](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>OSMF 2.0 Smooth Streaming beépülő modul
A beépülő modul betöltése és igény szerinti, Smooth Streaming a következő funkciókkal tartalom lejátszását támogatja:

* Igény szerinti Smooth Streaming lejátszás (Play, szüneteltetése, Seek, Stop)
* Élő Smooth Streaming lejátszás (lejátszás)
* Élő DVR funkciók (szünet, Seek, DVR lejátszás, nyissa meg a működés közbeni)
* Videó kodekek - H.264 támogatása
* Hang kodekek - AAC támogatása
* Több hang nyelvi váltás OSMF beépített API-khoz
* Maximális lejátszás minőségi kijelölés OSMF beépített API-khoz
* Oldalkocsi OSMF feliratok beépülő modul a feliratok bezárása
* Az Adobe&reg; Flash&reg; Player 11.4 vagy újabb verzióját.
* Ebben a verzióban csak a OSMF 2.0 támogatja.

## <a name="supported-features-and-known-issues"></a>Támogatott szolgáltatások és ismert problémák
Támogatott szolgáltatások, a nem támogatott funkciókat és az ismert problémák teljes listáját lásd [Ez a dokumentum](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>A beépülő modul betöltése
OSMF beépülő modulok tölthetők be statikusan (fordítás során) vagy dinamikusan (futásidőben). A Smooth Streaming beépülő modul OSMF le a dinamikus és statikus verziójával együtt.

* Statikus betöltése: statikusan betöltése, a statikus könyvtárat (SWC) fájl megadása kötelező. A projektek és a végső kimenetet fájl egyesítése mutató hivatkozás statikus beépülő modulok hozzá szeretné adni a fordítás során.
* Dinamikus betöltése: dinamikusan betöltéséhez a lefordított (SWF) fájl megadása kötelező. Dinamikus beépülő modulok betöltése a futásidejű, és nem szerepel a projekt kimenet. (Lefordított kimeneti) Dinamikus beépülő modulok tölthetők be HTTP- és protokollok használatával.

A statikus és dinamikus betöltése további információkért lásd: a hivatalos [OSMF beépülő modul oldal](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS OSMF statikus be
Az alábbi kódrészlet a SS beépülő modul betöltése a OSMF statikusan és OSMF MediaFactory osztály alapvető videó lejátszása ismerteti. Többek között a következő OSMF: SS, előtt győződjön meg arról, hogy a projektbe egy hivatkozást tartalmaz-e a "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" statikus beépülő modul.

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


### <a name="ss-for-osmf-dynamic-loading"></a>SS OSMF dinamikus be
Az alábbi kódrészlet szemlélteti a SS beépülő modul betöltése a OSMF dinamikusan és alapvető lejátszása videót a OSMF MediaFactory osztály használatával. Többek között a következő OSMF: SS, mielőtt a "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" dinamikus beépülő modul másolása a projekt mappába, ha betölti a fájl protokoll használatával, vagy a webkiszolgálóra HTTP terhelést másolja. Nincs szükség ahhoz, hogy a projekt referenciáihoz "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" szerepeljen.

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Az SS ODMF dinamikus beépülő modul villogó Media lejátszás
OSMF dinamikus beépülő modul Smooth Streaming összeegyeztethető [villogó Media lejátszás (SMP)](http://osmf.org/strobe_mediaplayback.html). A SS a OSMF beépülő modul segítségével adja hozzá a tartalom lejátszását Smooth Streaming SMP. Ehhez másolja "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" alatt webkiszolgálón a HTTP-terhelést, az alábbi lépéseket követve:

1. Keresse meg a [villogó Media lejátszás telepítőből](http://osmf.org/dev/2.0gm/setup.html). 
2. Állítsa be az src Smooth Streaming forráshoz, (pl. http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. A kívánt konfigurációs módosításokat, majd kattintson a kép és a frissítés.
   
   **Megjegyzés:** a tartalom webkiszolgáló kell egy érvényes crossdomain.xml. 
4. Másolja és illessze be a kódot egy egyszerű HTML-weblap kedvenc szövegszerkesztőjével, többek között az alábbi példa használatával:

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



1. Smooth Streaming OSMF beépülő modul hozzáadása a beágyazási kódot, és mentse.
   
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
2. Mentse a HTML-lapot, és tegye közzé a webkiszolgálóhoz. Keresse meg a közzétett weblapot a kedvenc Flash&reg; Player internetböngésző (az Internet Explorer, Chrome, Firefox, így tovább) engedélyezve van.
3. Adobe belüli Smooth Streaming tartalom élvez&reg; Flash&reg; Player.

Az általános OSMF fejlesztési további információkért lásd: a hivatalos [OSMF fejlesztési lap](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Adaptív adatfolyam-beépülő modul OSMF frissítéshez Microsoft](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

