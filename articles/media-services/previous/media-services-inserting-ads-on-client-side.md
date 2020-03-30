---
title: Hirdetések beszúrása az ügyféloldalra | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan szúrhat be hirdetéseket a médiaaz ügyféloldalon.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 274ee09ae98dd229b255e58261f462e322be9f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565740"
---
# <a name="inserting-ads-on-the-client-side"></a>Hirdetések beszúrása az ügyféloldalra
Ez a cikk arról tartalmaz információt, hogyan lehet különböző típusú hirdetéseket beilleszteni az ügyféloldalra.

Az élő közvetítésű videók feliratozásáról és hirdetéstámogatásáról a [Támogatott feliratozási és hirdetésbeillesztési szabványok című](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads)témakörben talál.

> [!NOTE]
> Az Azure Media Player jelenleg nem támogatja a hirdetéseket.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Hirdetések beszúrása a médiába
Az Azure Media Services támogatja a hirdetésbeillesztést a Windows Media Platformon keresztül: Player frameworks. A hirdetéstámogatással rendelkező játékoskeretrendszerek Windows 8, Silverlight, Windows Phone 8 és iOS rendszerű eszközökön érhetők el. Minden játékos keretrendszer tartalmaz minta kódot, amely megmutatja, hogyan kell végrehajtani egy játékos alkalmazás. Három különböző típusú hirdetéseket lehet beszúrni a média:list.

* **Lineáris** – full frame hirdetések, amelyek szüneteltetik a fő videót.
* **Nemlineáris** – a fő videó lejátszása közben megjelenő átfedő hirdetések, általában embléma vagy más statikus kép a lejátszón belül.
* **Társ** – a lejátszón kívül megjelenő hirdetések.

A hirdetések a fő videó idővonalának bármely pontján elhelyezhetők. Meg kell mondania a játékosnak, hogy mikor kell lejátszania a hirdetést, és milyen hirdetéseket kell játszania. Ez szabványos XML-alapú fájlok használatával történik: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) és Digital Video Player Ad Interface Definition (VPAID). A VAST-fájlok határozzák meg, hogy milyen hirdetéseket jelenítsen meg. A VMAP-fájlok határozzák meg, hogy mikor kell különböző hirdetéseket lejátszani, és tartalmazzák a VAST XML-t. Mast fájlok egy másik módja annak, hogy szekvencia hirdetéseket is tartalmazhatvast XML. A VPAID fájlok meghatározzák a videolejátszó és a hirdetés- vagy hirdetésszerver közötti felületet.

Minden játékos keret másképp működik, és minden fedezi a saját cikket. Ez a cikk a hirdetések beszúrásához használt alapvető mechanizmusokat ismerteti. A videólejátszó-alkalmazások hirdetéseket kérnek egy hirdetésszerverről. A hirdetésszerver többféleképpen tud válaszolni:

* VAST-fájl visszaadása
* VMAP-fájl visszaadása (beágyazott VAST fájllal)
* MAST-fájl visszaadása (beágyazott VAST-fájllal)
* Vast-fájl visszaküldése VPAID-hirdetésekkel

### <a name="using-a-video-ad-service-template-vast-file"></a>VIDEOhirdetés-szolgáltatássablon (VAST) fájl használata
A VAST-fájl határozza meg, hogy milyen hirdetéseket vagy hirdetéseket jelenítsen meg. A következő XML egy lista egy vast fájl lineáris hirdetés:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

A lineáris hirdetésleírása a <**lineáris**> elem. Itt adható meg a hirdetés időtartama, az események nyomon követése, az átkattintás, a kattintáskövetés és számos **MediaFile-elem.** A nyomon követési események a **<TrackingEvents**> elemben vannak megadva, és lehetővé teszik a hirdetésszerver számára a hirdetés megtekintése során bekövetkező különböző események nyomon követését. Ebben az esetben a kezdési, középponti, teljes és kibontási események nyomon követik. A kezdő esemény akkor következik be, amikor a hirdetés megjelenik. A felezőpont esemény akkor következik be, amikor a hirdetés idővonalának legalább 50%-át megtekintették. A teljes esemény akkor következik be, amikor a hirdetés a végére fut. A kibontási esemény akkor következik be, amikor a felhasználó teljes képernyősre bontja a videolejátszót. Az átkattintások egy <**VideoClicks**> elemen belüli <**ClickThrough**> elemével vannak megadva, és egy olyan erőforrás URI-ját adják meg, amely akkor jelenik meg, amikor a felhasználó a hirdetésre kattint. ClickTracking van megadva egy <**ClickTracking**> elem, szintén a <**VideoClicks**> elem, és meghatározza a nyomkövető erőforrás a játékos kérésére, amikor a felhasználó rákattint a hirdetésre. A **MediaFile** <> elemek a hirdetés adott kódolásával kapcsolatos információkat tartalmaznak. Ha egynél több <**MediaFile**> elem, a videólejátszó kiválaszthatja a platform legjobb kódolását.

A lineáris hirdetések meghatározott sorrendben jeleníthetők meg. Ehhez adjon hozzá `<Ad>` további elemeket a VAST-fájlhoz, és adja meg a sorrendet a szekvencia attribútum használatával. A következő példa ezt illusztrálja:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

A nemlineáris hirdetések egy `<Creative>` elemben is meg vannak adva. A következő példa `<Creative>` egy nemlineáris hirdetésleírását leíró elemet mutat be.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

A **NonLinearAds** <> elem egy vagy több <**nonlineáris**> elemet tartalmazhat, amelyek mindegyike nemlineáris hirdetést tud leírni. A **nonlineáris**> elem <a nemlineáris hirdetés erőforrását adja meg. Az erőforrás lehet egy <**StaticResource**>, egy <**IFrameResource**> vagy egy <**HTMLResource**>. \<**A StaticResource**> egy nem HTML-alapú erőforrást ír le, és egy creativeType attribútumot határoz meg, amely meghatározza az erőforrás megjelenítésének módját:

Kép / gif, kép / jpeg, kép / png - az erőforrás jelenik meg egy HTML <**img**> tag.

Application/x-javascript – az erőforrás html <**parancsfájlban** jelenik meg> címkével.

Alkalmazás/x-shockwave-flash – az erőforrás flash lejátszóban jelenik meg.

**Az IFrameResource** egy IFrame-erőforrásban megjeleníthető HTML-erőforrást ír le. **A HTMLResource** egy weblapba beszúrható HTML-kóddarabot ír le. **TrackingEvents** határozza meg a követési események és az URI kérni, ha az esemény bekövetkezik. Ebben a példában az acceptInvitation és az összecsukási események nyomon követik. A **NonLinearAds** elemről és annak gyermekeiről a IAB.NET/VAST című témakörben talál további információt. Vegye figyelembe, hogy a **TrackingEvents** elem a **NonLinearAds** elemen belül található, nem pedig a **NonLinear** elemben.

A kísérőhirdetések egy `<CompanionAds>` elemen belül vannak definiálva. Az `<CompanionAds>` elem egy vagy `<Companion>` több elemet tartalmazhat. Minden `<Companion>` elem egy kísérőhirdetést ír `<StaticResource>`le, és tartalmazhat egy , `<IFrameResource>`vagy `<HTMLResource>` a nemlineáris hirdetéshez hasonló módon megadott elemet. A VAST-fájlok több kísérőhirdetést is tartalmazhatnak, és a lejátszóalkalmazás kiválaszthatja a leginkább megjelenítendő hirdetést. A VAST-ról további információt a [VAST 3.0 című](https://www.iab.net/media/file/VASTv3.0.pdf)témakörben talál.

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Több virtuális videó lejátszási listájának (VMAP) használata
A VMAP-fájl segítségével megadhatja, hogy mikor következnek be a hirdetéstörések, mennyi ideig jelenjenek meg az egyes szünetek, hány hirdetés jeleníthető meg egy szünetben, és hogy milyen típusú hirdetések jelenhetnek meg a szünetben. A következő egy példa VMAP-fájlban, amely egyetlen hirdetéstörést határoz meg:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

A VMAP-fájl `<VMAP>` egy olyan elemmel `<AdBreak>` kezdődik, amely egy vagy több elemet tartalmaz, és mindegyik egy-egy hirdetéstörést határoz meg. Minden hirdetési szünet ben megad egy töréstípust, egy törésazonosítót és egy időeltolást. A breakType attribútum a szünetben lejátszható hirdetés típusát adja meg: lineáris, nem lineáris vagy megjelenítés. A vizuális hirdetések vast-társhirdetésekre vannak leképezve. Egy vesszővel elválasztott listában több hirdetéstípus is megadható. A töredazonosító a hirdetés nem kötelező azonosítója. Az időeltolás határozza meg, hogy mikor jelenjen meg a hirdetés. A következő módokon adható meg:

1. Idő – hh:mm:ss vagy hh:mm:ss.mmm formátumban, ahol .mmm ezredmásodperc. Ennek az attribútumnak az értéke határozza meg a videó idővonalának kezdetétől a hirdetésszünet elejéig töltött időt.
2. Százalékos – n%-os formátumban, ahol n a videó idővonalának lejátszása a hirdetés lejátszása előtt
3. Start/End – megadja, hogy a hirdetés a videó megjelenítése előtt vagy után jelenjen meg
4. Pozíció – a hirdetésszünetek sorrendjét adja meg, ha a reklámszünetek időzítése ismeretlen, például élő közvetítés esetén. Az egyes hirdetéstörések sorrendjét a #n formátumban adja meg, ahol n 1- es vagy nagyobb egész szám. 1 azt jelenti, hogy a hirdetés nek az első adandó alkalommal kell lejátszania, 2 azt jelenti, hogy a hirdetésnek a második lehetőségnél kell lejátszania, és így tovább.

Az `<AdBreak>` elemen belül lehet egy <**AdSource**> elem. Az **AdSource**> elem <a következő attribútumokat tartalmazza:

1. Azonosító – a hirdetésforrás azonosítóját adja meg
2. allowMultipleAds – logikai érték, amely meghatározza, hogy több hirdetés is megjeleníthető-e a hirdetésszünet ben
3. followRedirects – egy opcionális logikai érték, amely meghatározza, hogy a videólejátszónak tiszteletben kell-e tartania a hirdetésválaszon belüli átirányításokat

Az **adSource**> elem <inline hirdetésválaszt vagy egy hirdetésre való hivatkozást biztosít a játékosszámára. A következő elemek egyikét tartalmazhatja:

* `<VASTAdData>`azt jelzi, hogy egy VAST-hirdetési válasz be van ágyazva a VMAP-fájlba
* `<AdTagURI>`egy URI, amely egy másik rendszer hirdetésválaszára hivatkozik
* `<CustomAdData>`-egy tetszőleges karakterlánc, amely nem VAST-választ jelöl

Ebben a példában egy in-line hirdetési válasz `<VASTAdData>` van megadva egy olyan elemmel, amely vast-hirdetési választ tartalmaz. A többi elemről további információt a [VMAP című témakörben talál.](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap)

Az **AdBreak**> <elem egy <**TrackingEvents**> elemet is tartalmazhat. A <**TrackingEvents**> elem lehetővé teszi a hirdetésszünet kezdetének vagy végének, illetve azt, hogy hiba történt-e a hirdetésszünet során. A **<TrackingEvents**> elem egy vagy több <**követés**> elemet tartalmaz, amelyek mindegyike egy követési eseményt és egy követési URI-t határoz meg. A lehetséges nyomon követési események a következők:

1. breakStart – a hirdetésszünet elejét követi nyomon
2. breakEnd – a hirdetési szünet befejezésének nyomon követése
3. hiba – nyomon követi a hirdetésszünet során történt hibát

A következő példa egy VMAP-fájlt mutat be, amely meghatározza a nyomon követési eseményeket

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

A **TrackingEvents**> elem és gyermekei <további információt ahttp://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Adathordozó-absztrakt szekvenálási sablon (MAST) fájl használata
A MAST-fájl lehetővé teszi a hirdetés megjelenítésének időpontjára meghatározó eseményindítók megadását. Az alábbi példa mast fájl, amely tartalmazza az eseményindítók egy pre roll hirdetés, egy mid-roll hirdetés, és egy roll utáni hirdetés.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


A MAST-fájl **MAST** egy mast-elemmel kezdődik, amely egy **eseményindító elemet** tartalmaz. Az `<triggers>` elem egy vagy több **eseményindító** elemet tartalmaz, amelyek meghatározzák, hogy mikor kell egy hirdetésnek megjátszania.

Az **eseményindító** elem tartalmaz egy **startConditions** elemet, amely meghatározza, hogy a hirdetés mikor kezdje meg a lejátszást. A **startConditions** elem egy `<condition>` vagy több elemet tartalmaz. Ha `<condition>` minden kiértékelésigaz egy eseményindító van kezdeményezve `<condition>` vagy visszavont attól függően, hogy a tartalmaz egy **startConditions** vagy **endConditions** elem, illetve. Ha `<condition>` több elem van jelen, akkor implicit VAGY ként kezeli őket, a true-ra kiértékelődő állapotok az eseményindító indítását eredményezik. `<condition>`elemek egymásba ágyazhatók. Ha `<condition>` a gyermekelemek előre vannak beállítva, a rendszer implicit ÉS-ként kezeli őket, minden feltételnek igaz értéket kell kiértékelnie ahhoz, hogy az eseményindító elinduljon. Az `<condition>` elem a következő attribútumokat tartalmazza, amelyek meghatározzák a feltételt:

1. **típus** – a feltétel, esemény vagy tulajdonság típusát adja meg
2. **név** – az értékelés során használandó tulajdonság vagy esemény neve
3. **érték** – az az érték, amely alapján egy tulajdonságot értékelni fognak
4. **operátor** – az értékelés során használandó művelet: EQ (egyenlő), NEQ (nem egyenlő), GTR (nagyobb), GEQ (nagyobb vagy egyenlő), LT (Kisebb, mint), LEQ (kisebb vagy egyenlő), MOD (modulo)

**endConditions** is `<condition>` tartalmaz nak elemeket. Amikor egy feltétel kiértékelt igaz az eseményindító alaphelyzetbe áll. Az `<trigger>` elem egy `<sources>` vagy több `<source>` elemet tartalmazó elemet is tartalmaz. Az `<source>` elemek határozzák meg az URI-t a hirdetési válaszhoz és a hirdetésválasz típusához. Ebben a példában egy URI kap egy VAST-választ.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>A Video Player-Ad Interface Definition (VPAID) használata
A VPAID egy API, amely lehetővé teszi a végrehajtható hirdetési egységek számára a videólejátszóval való kommunikációt. Ez lehetővé teszi a rendkívül interaktív hirdetési élményt. A felhasználó interakcióba léphet a hirdetéssel, és a hirdetés reagálhat a megtekintő által végrehajtott műveletekre. Előfordulhat például, hogy egy hirdetés olyan gombokat jelenít meg, amelyek lehetővé teszik a felhasználó számára, hogy több információt vagy a hirdetés hosszabb verzióját jelenítse meg. A videólejátszónak támogatnia kell a VPAID API-t, és a végrehajtható hirdetésnek végre kell hajtania az API-t. Amikor egy játékos egy hirdetésszervertől kér hirdetést, a kiszolgáló egy VPAID-hirdetést tartalmazó VAST-válaszra válaszolhat.

A végrehajtható hirdetés olyan kódban jön létre, amelyet olyan futásidejű környezetben kell végrehajtani, mint például az Adobe Flash™ vagy a Webböngészőben végrehajtható JavaScript. Ha egy hirdetéskiszolgáló VPAID-ad-t tartalmazó VAST-választ ad vissza, `<MediaFile>` az elemben lévő apiFramework attribútum értékének "VPAID" értékűnek kell lennie. Ez az attribútum azt adja meg, hogy a tartalmazott hirdetés VPAID végrehajtható fájl. A típusattribútumot a végrehajtható fájl MIME-típusára kell állítani, például "application/x-shockwave-flash" vagy "application/x-javascript". A következő XML-kódrészlet `<MediaFile>` egy VPAID végrehajtható fájlt tartalmazó VAST-válasz elemét jeleníti meg.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

A végrehajtható hirdetés inicializálható `<AdParameters>` a `<Linear>` `<NonLinear>` VAST-válasz ban vagy elemekben lévő elemek használatával. Az elemről `<AdParameters>` további információt a [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf)című témakörben talál. A VPAID API-ról további információt a [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf)című témakörben talál.

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Windows vagy Windows Phone 8 Player implementálása hirdetéstámogatással
A Microsoft Media Platform: Player Framework for Windows 8 és Windows Phone 8 mintaalkalmazások gyűjteményét tartalmazza, amelyek bemutatják, hogyan valósíthatja meg a videolejátszó alkalmazásokat a keretrendszer használatával. Letöltheti a Player Framework és a mintákat [Player Framework for Windows 8 és Windows Phone 8](https://playerframework.codeplex.com).

Amikor megnyitja a Microsoft.PlayerFramework.Xaml.Samples megoldást, a projekten belül számos mappa jelenik meg. A Hirdetés mappa tartalmazza a hirdetéstámogatással rendelkező videólejátszó létrehozásához szükséges mintakódot. A Hirdetési mappában számos XAML/cs fájl található, amelyek mindegyike megmutatja, hogyan kell más módon beilleszteni a hirdetéseket. Az alábbi lista az alábbiakat ismerteti:

* AdPodPage.xaml: A hirdetéspod megjelenítése.
* AdSchedulingPage.xaml A hirdetések ütemezésének módját mutatja be.
* FreeWheelPage.xaml Megmutatja, hogyan kell használni a FreeWheel plugin ütemezése hirdetéseket.
* MastPage.xaml: Bemutatja, hogyan ütemezheti a hirdetéseket mast fájllal.
* ProgrammaticAdPage.xaml Megmutatja, hogyan lehet programozott módon ütemezni a hirdetéseket egy videóba.
* ScheduleClipPage.xaml Bemutatja, hogyan ütemezheti a hirdetés tvastfájl nélkül.
* VastLinearCompanionPage.xaml: Lineáris és kísérőhirdetés beszúrásának módját mutatja be.
* VastNonLinearPage.xaml: Megmutatja, hogyan szúrhat be nem lineáris hirdetéseket.
* VmapPage.xaml: Megmutatja, hogyan adhatók meg vmap-fájllal rendelkező hirdetések.

Ezek a minták mindegyike a lejátszó keretrendszer által meghatározott MediaPlayer osztályt használja. A legtöbb minta olyan bővítményeket használ, amelyek támogatják a különböző hirdetésválasz-formátumokat. A ProgrammaticAdPage minta programozott módon kommunikál egy MediaPlayer-példányokkal.

### <a name="adpodpage-sample"></a>AdPodPage minta
Ez a minta az AdSchedulerPlugin segítségével határozza meg, hogy mikor jelenjen meg egy hirdetés. Ebben a példában a program a név közbeni hirdetést öt másodperc után lejátssza. A hirdetésegység (a hirdetések sorrendjében megjelenítendő csoportja) egy hirdetésszerverről visszaadott VAST-fájlban van megadva. A VAST-fájl URI-ja meg `<RemoteAdSource>` van adva az elemben.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Az AdSchedulerPlugin szolgáltatásról további információt a Windows 8 és Windows Phone 8 rendszeren [futó Hirdetés a Lejátszó keretrendszerében című témakörben talál.](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Ez a minta az AdSchedulerPlugin-t is használja. Három hirdetést, egy előgörgetés előtti hirdetést, egy közép-roll hirdetést és egy roll utáni hirdetést ütemez. Az egyes hirdetések VAST-értékének URI-ja egy `<RemoteAdSource>` elemben van megadva.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>Szabadkeréklap
Ez a minta a FreeWheelPlugin-t használja, amely egy forrás attribútumot ad meg, amely egy SmartXML-fájlra mutat, amely a hirdetés tartalmát és a hirdetésütemezési adatokat is meghatározza.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage (Árboclap)
Ez a minta a MastSchedulerPlugin-t használja, amely lehetővé teszi a MAST-fájl használatát. A Forrás attribútum adja meg a MAST-fájl helyét.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Ez a minta programozott módon kommunikál a MediaPlayer programmal. Az ProgrammaticAdPage.xaml fájl a MediaPlayer t imasizálja:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

A ProgrammaticAdPage.xaml.cs fájl létrehoz egy AdHandlerPlugin- t, hozzáad egy TimelineMarker-t, amely meghatározza, hogy mikor jelenjen meg egy hirdetés, majd hozzáad egy kezelőt a MarkerReached eseményhez, amely betölt egy REMOTEAdSource-ot, amely URI-t ad meg egy VAST-fájlhoz, majd lejátssza a hirdetést.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage (ScheduleClipPage)
Ez a minta az AdSchedulerPlugin segítségével ütemezi a roll közepén megjelenő hirdetést a hirdetést tartalmazó .wmv fájl megadásával.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Ez a minta bemutatja, hogyan lehet az AdSchedulerPlugin segítségével egy kísérőhirdetéssel együtt sorakozó, folyamatos idejű lineáris hirdetésütemezését. Az `<RemoteAdSource>` elem adja meg a VAST fájl helyét.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Ez a minta az AdSchedulerPlugin segítségével ütemez lineáris és nem lineáris hirdetést. A VAST-fájl helye az `<RemoteAdSource>` elemmel együtt van megadva.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAP lap
Ez a minta a VmapSchedulerPlugin segítségével ütemezi a hirdetéseket VMAP-fájl használatával. A VMAP-fájl URI-ja az `<VmapSchedulerPlugin>` elem Forrás attribútumában van megadva.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>IOS-videolejátszó megvalósítása hirdetéstámogatással
A Microsoft Media Platform: Player Framework for iOS mintaalkalmazások gyűjteményét tartalmazza, amelyek bemutatják, hogyan valósíthatja meg a videolejátszó alkalmazásokat a keretrendszer használatával. A Lejátszó keretrendszer és a minták letölthetők az [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework)rendszeréből. A GitHub-lap egy wikire mutató hivatkozást tartalmaz, amely további információkat tartalmaz a lejátszó keretrendszerről, és bemutatja a lejátszómintáját: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Hirdetések ütemezése A VMAP segítségével
A következő példa bemutatja, hogyan ütemezheti a hirdetéseket VMAP-fájl használatával.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Hirdetések ütemezése vast-mal
A következő minta bemutatja, hogyan ütemezheti a késői kötési VAST-hirdetést.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   A következő minta bemutatja, hogyan ütemezheti a korai kötési VAST-hirdetést.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

A következő minta bemutatja, hogyan szúrhat be egy hirdetést a Durva vágásszerkesztés (RCE) használatával

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

A következő példa bemutatja, hogyan ütemezheti a hirdetéspodot.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

A következő példa bemutatja, hogyan ütemezheti a nem ragadós görgetős hirdetéseket. A nem ragadós hirdetés lejátszása csak egyszer történik meg, függetlenül attól, hogy a megtekintőt keresik-e.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

A következő példa bemutatja, hogyan ütemezheti a ragadós roll közepén álló hirdetéseket. A videó idővonalának megadott pontjának elérésekor a ragadós hirdetés minden alkalommal megjelenik.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

A következő minta bemutatja, hogyan ütemezheti a roll utáni hirdetéseket.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

A következő minta bemutatja, hogyan ütemezheti a görgetés előtti hirdetéseket.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

A következő minta bemutatja, hogyan ütemezhet egy görgetés közbeni fedvényt.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
