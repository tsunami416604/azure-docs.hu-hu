---
title: Az Open Source Media Framework a Smooth Streaming beépülő modul
description: Ismerje meg, hogyan használható az Azure Media Services Smooth Streaming beépülő modul az Adobe Open Source Media Framework.
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
ms.openlocfilehash: c40d8b93a7487619cc94586c7e6b4cdc550435cd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314059"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>A Microsoft Smooth Streaming beépülő modul az Adobe Open Source Media Framework használata  
## <a name="overview"></a>Áttekintés
Open Source Media keretrendszer 2.0-s (SS OSMF-hez) a Microsoft Smooth Streaming beépülő modul OSMF alapértelmezett bővíti ki, és új és meglévő OSMF lejátszók tartalmak lejátszás Microsoft Smooth Streaming hozzáadja. A beépülő modul a Strobe Media Playbackben (SMP) is hozzáadja a lejátszás Smooth Streaming lehetőségeit.

SS OSMF-hez a beépülő modul két verzióját tartalmazza:

* Statikus Smooth Streaming beépülő modul OSMF (.swc)
* A dinamikus Smooth Streaming beépülő modul OSMF (.swf)

Jelen dokumentum céljából feltételezzük, hogy az olvasó rendelkezik olyan általános ismeretek OSMF és OSMF beépülő modulokat. OSMF kapcsolatos további információkért lásd: a dokumentáció a a [hivatalos OSMF-webhely](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming beépülő modul OSMF 2.0
A beépülő modul betöltése és igény szerinti Smooth Streaming-tartalmat a következő funkciókkal lejátszása támogatja:

* Igény szerinti Smooth Streaming lejátszási (lejátszás, szüneteltetése, keresési, Leállítás)
* Az élő Smooth Streaming lejátszási (lejátszás)
* Élő DVR-funkciók (szüneteltetése, keresési, DVR-lejátszás – Go-to-Live)
* Videokodekek - H.264 támogatása
* Hang kodekek - AAC támogatása
* Több többnyelvű hang közötti váltás az OSMF beépített API-kkal
* Maximális lejátszási minőségi kijelölés OSMF beépített API-kkal
* Az oldalkocsi feliratokkal OSMF feliratok beépülő modul használatával
* Az Adobe&reg; Flash&reg; Player 11.4 vagy újabb verziója.
* Ez a verzió csak az OSMF 2.0 támogatja.

## <a name="supported-features-and-known-issues"></a>Támogatott szolgáltatások és ismert problémák
Támogatott szolgáltatások, a nem támogatott szolgáltatások és az ismert problémák teljes listájáért tekintse meg [ebben a dokumentumban](https://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>A beépülő modul betöltése
OSMF beépülő modulok tölthetők be statikusan (a fordítás során) vagy dinamikusan (történő futtatása). A Smooth Streaming beépülő modul OSMF letölthető a dinamikus és statikus verzió magában foglalja.

* Statikus betöltése: A betöltés statikusan, egy statikus kódtárat (SWC) fájlt kötelező megadni. Statikus beépülő modulokat a fordítási időben kerülnek a projektek és egyesítése a végeredmény fájl referenciaként.
* A dinamikus betöltése: A betöltés dinamikusan, előre lefordított (CHF) fájl szükség. Dinamikus beépülő modulok betöltése a futtatókörnyezetben, és nem szerepel a projekt kimenet. (A lefordított kimenet) A dinamikus beépülő modulok tölthetők be HTTP- és protokollok használatával.

A statikus és dinamikus betöltése további információkért tekintse meg a hivatalos [OSMF beépülő modul oldal](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS az OSMF statikus betöltése
Az alábbi kódrészlet bemutatja, hogyan statikusan betölteni a a SS beépülő modul OSMF-hez és OSMF MediaFactory osztály használatával egy alapszintű videó lejátszásához. Többek között a következők az OSMF kód SS előtt győződjön meg arról, hogy a projekt leírása tartalmazza a "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" statikus beépülő modult.

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


### <a name="ss-for-osmf-dynamic-loading"></a>SS az OSMF dinamikus betöltése
Az alábbi kódrészlet bemutatja, hogyan dinamikusan betölteni a a SS beépülő modul OSMF-hez, és a egy alapszintű lejátszása videó az OSMF MediaFactory osztály használatával. Többek között a SS OSMF-kódot, mielőtt a "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" dinamikus beépülő modul átmásolása a projektmappa fájllistájának, ha azt szeretné betölteni a fájlt protokoll használatával, vagy másolja a webkiszolgálón a HTTP-terheléselosztást. Hiba esetén nem kell "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" szerepeljenek a projekt referenciáihoz.

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Az SS ODMF dinamikus beépülő modullal a strobe Media lejátszás
A Smooth Streaming OSMF dinamikus beépülő modul összeegyeztethető [Strobe Media Playbackben (SMP)](http://osmf.org/strobe_mediaplayback.html). Az SS OSMF beépülő modul használatával adja hozzá a tartalom lejátszását Smooth Streaming az állapotáttelepítési pont. Ehhez másolja a "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" alatt egy webkiszolgálón a HTTP-terheléselosztást, az alábbi lépéseket követve:

1. Keresse meg a [Strobe Media lejátszás beállítása lapon](http://osmf.org/dev/2.0gm/setup.html). 
2. Az src (pl. egy Smooth Streaming forrás beállítása http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Végezze el a kívánt konfigurációs módosításokat, majd kattintson a Preview és a frissítés.
   
   **Note** Your content web server needs a valid crossdomain.xml. 
4. Másolja és illessze be a kódot egy tetszőleges szövegszerkesztőben, például az alábbi példa használatával egyszerű HTML-oldalt:

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
2. Mentse a HTML-oldalt, és egy webkiszolgálón tegye közzé. Tallózással keresse meg a közzétett webes lapot a kedvenc Flash&reg; Player internetböngészőben (az Internet Explorert, Chrome, Firefox, így tovább) engedélyezve van.
3. Élvezze a Smooth Streaming-tartalmat belül az Adobe&reg; Flash&reg; Player.

Általános OSMF fejlesztési további információkért tekintse meg a hivatalos [OSMF fejlesztési lap](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A Microsoft adaptív Streamelés beépülő modul OSMF-frissítés](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

