---
title: Ügyféloldali ads beszúrása |} Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan ügyféloldali ads beszúrása.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: 57cb06fcee029f72dd1cf81ef086985fdda3f45f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="inserting-ads-on-the-client-side"></a>Ügyféloldali ads beszúrása
Ez a cikk beszúrása ügyféloldali ads különböző típusú információkat tartalmaz.

Az élő adatfolyam-továbbítási videók lezárt feliratok és az ad támogatásával kapcsolatos további információkért lásd: [támogatott kódolt feliratok és Ad beszúrási szabványok](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Az Azure Media Player jelenleg nem támogatja a hirdetések.
> 
> 

## <a id="insert_ads_into_media"></a>A Media Ads beszúrása
Az Azure Media Services támogatást nyújt a Windows Media platformon keresztül ad beszúrási: lejátszó-Keretrendszerekhez. Ad-támogatással rendelkező lejátszó-keretrendszerekhez Windows 8, a Silverlight, a Windows Phone 8 és az iOS-eszközök érhetők el. Minden egyes player keretrendszer mintakód bemutatja, hogy a lejátszóalkalmazás megvalósításához tartalmazza. Nincsenek media: listájába szúrhatók ads három különböző típusú.

* **Lineáris** – teljes keret hirdetések felfüggeszti a fő videó.
* **Nem lineáris** – jelennek meg a fő videó lejátszása hirdetések átmeneti területre, általában embléma vagy egyéb statikus kép kerül a Windows Media player belül.
* **Kiegészítő** – kívül a Windows Media player megjelenített ads.

A fő videó idősorán bármikor ADs helyezhető. A Windows Media player kell arról, mikor számára, hogy az ad és számára, hogy mely hirdetések. Ebben az esetben a szabványos XML alapú fájlok készletből: videó Ad szolgáltatás sablon (VAST), a digitális videót több Ad lista (VMAP), a Media absztrakt alkalmazás-előkészítés sablon (OSZLOPOS) és a digitális videót Player Ad felület Definition (VPAID). NAGY fájlok adja meg, milyen ads megjelenítéséhez. VMAP fájlok idejére különböző ads lejátszásához és HATALMAS XML kódot tartalmaz. A fájlok OSZLOPOS is tartalmazhat túlnyomó XML feladatütemezési hirdetések másik módja van. VPAID fájlok határozza meg azt a videólejátszó és az ad vagy ad-kiszolgáló közötti illesztőfelületet szolgáltasson.

Minden egyes player keretrendszer eltérő módon működik, és minden egyes külön cikk tárgyalja. Ez a cikk ismerteti az alapvető módszerek segítségével szúrják be az ads. Videólejátszó alkalmazások ads kérhet egy ad-kiszolgáló. Az ad-kiszolgáló többféle módon válaszolhat:

* Térjen vissza a túlnyomó fájl
* Térjen vissza a VMAP fájlt (a beágyazott VAST)
* Egy OSZLOPOS fájlt (a beágyazott VAST) visszaadása
* Térjen vissza a VPAID ads túlnyomó fájl

### <a name="using-a-video-ad-service-template-vast-file"></a>Videó Ad szolgáltatás sablon (VAST) fájl használatával
Egy túlnyomó fájlt határozza meg, milyen ad vagy ads megjelenítéséhez. A következő XML-kódja egy lineáris ad túlnyomó fájl például:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
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
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
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

A lineáris ad le a <**lineáris**> elemet. Azt adja meg az ad időtartama, nyomon követés, átkattintással, követési kattintson, és számos **MediaFile** elemek. Nyomkövetési események belül vannak megadva a <**TrackingEvents**> elemet, és teszi lehetővé egy ad-kiszolgáló az ad megtekintése közben előforduló különféle események nyomon követéséhez. Ebben az esetben a kezdő középpont, befejeződött, és bontsa ki a események nyomon követi. Az ad jelenik meg a kezdő esemény következik be. A középpont esemény következik be, legalább 50 %-a az ad idősor már megtekintett. Az ad a befejezési futott a teljes esemény következik be. A kibontott esemény következik be, amikor a felhasználó a videólejátszó bővíti a teljes képernyős. Clickthroughs vannak megadva, a <**Átkattintós**> elemen belül egy <**VideoClicks**> elemet, és adja meg egy erőforrás URI-t jelenítsen meg, ha a felhasználó kattint az ad. ClickTracking van megadva egy <**ClickTracking**> elem, belül is a <**VideoClicks**> elemet, és adja meg a Windows Media Player kérése, amikor a felhasználó kattint az ad követési erőforrás . A <**MediaFile**> elemek adjon meg egy adott kódolását, az ad információt. Ha egynél több <**MediaFile**> elem, a videólejátszó kiválaszthatja a platform legjobb kódolást. 

Lineáris ads megjeleníthető a megadott sorrendben. Ehhez adja hozzá további <Ad> a VAST elemek fájlt, majd adja meg a feladatütemezési attribútum használatával. A következő példa ezt mutatja be:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
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
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
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

Nem lineáris ads megadott egy <Creative> elemet is. Az alábbi példa mutatja egy <Creative> elem az lineáris ad.

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

A <**NonLinearAds**> elem tartalmazhat egy vagy több <**NonLinear**> elemek, amelyek leírhatja lineáris ad. A <**NonLinear**> elem meghatározza a lineáris ad erőforrás. Az erőforrás lehet egy <**StaticResouce**>, <**IFrameResource**>, vagy egy <**HTMLResouce**>. <**StaticResource**> nem HTML erőforrás ismerteti, és arról, hogyan jelenjen meg az erőforrás egy creativeType attribútum határozza meg:

Kép/gif, a kép/jpeg, a lemezkép vagy png – HTML jelenik meg az erőforrás <**img**> címke.

Alkalmazás/x-javascript – HTML jelenik meg az erőforrás <**parancsfájl**> címke.

Alkalmazás/x-shockwave-flash – az erőforrás egy Flash player jelenik meg.

**IFrameResource** egy HTML-erőforrás IFRAME megjeleníthető ismerteti. **HTMLResource** szúrhatók be egy weblap, HTML-kódja egy adat ismerteti. **TrackingEvents** adja meg a nyomkövetési események és az URI-t kérése az esemény akkor következik be. Ez a példa a acceptInvitation és összecsukása események követi. További információ a **NonLinearAds** elem és a gyermekek, lásd: IAB.NET/VAST. Vegye figyelembe, hogy a **TrackingEvents** elem a helyen belüli a **NonLinearAds** elem helyett a **NonLinear** elemet.

Kiegészítő ads meghatározott egy <CompanionAds> elemet. A <CompanionAds> elem tartalmazhat egy vagy több <Companion> elemek. Minden egyes <Companion> elem egy kiegészítő ad ismerteti, és tartalmazhat egy <StaticResource>, <IFrameResource>, vagy <HTMLResource> amely lineáris ad a megszokott módon vannak megadva. A túlnyomó is tartalmazhatnak, több kiegészítő ads, és a lejátszóalkalmazás kiválaszthatja a megjelenítendő legmegfelelőbb ad. VAST kapcsolatos további információkért lásd: [túlnyomó 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Egy digitális videót több Ad-lista (VMAP) fájl használatával
Egy VMAP fájl lehetővé teszi annak megadását, amikor ad oldaltörések fordulhat elő, mennyi ideig egyes szünetek, hány ads belül szünet jeleníthető meg, és ads típusú lehet megszakítás alatt jelenik meg. A következő egy példa VMAP fájl, amely meghatározza egy egyetlen ad break:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
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

Egy VMAP fájl kezdődik egy <VMAP> elem, amely tartalmazza egy vagy több <AdBreak> elemek, egy ad break meghatározása. Minden ad break határozza meg, egy break típusát, a sortörés azonosítója és a idő eltolódását. A breakType attribútum határozza meg a során a szünet lejátszható ad: lineáris, nem lineáris, vagy megjelenítése. Ads térkép megjelenítése túlnyomó kiegészítő ads. Egynél több ad-típus (szóközök nélkül) vesszővel elválasztott listában adható meg. A breakID pedig az ad azonosítója, amelyet a nem kötelező. A timeOffset határozza meg, ha az ad üzenetnek kell megjelennie. Azt is megadhatók a következő módszerek valamelyikével:

1. Idő óó: pp: vagy hh:mm:ss.mmm formátumban, ahol .mmm az ezredmásodperc. Ez az attribútum értékét megadja azt az időtartamot, a videó ütemterv kezdetétől a az ad-break elejére.
2. Százalékos – n % formátumban ahol n az százaléka a videó ütemterv számára, hogy az ad lejátszás előtt
3. Kezdő és záró – Megadja, hogy egy ad üzenetnek kell megjelennie, előtt vagy után a videó meg lett jelenítve.
4. Helyezze – ad oldaltörések sorrendje határozza meg, ha az ad oldaltörések időzítése ismeretlen, például élő Stream továbbítása. Minden ad break sorrendjét a #n formátumban, ahol n az 1 vagy nagyobb egész szám van megadva. 1 azt jelzi, hogy az ad lejátszani az első adandó 2 azt jelzi, hogy az ad lejátszani a második alkalommal és így tovább.

Belül a <AdBreak> elem nem lehet az egyik <**AdSource**> elemet. A <**AdSource**> elem tartalmazza-e a következő attribútumokat:

1. Azonosító – meghatározza az ad-forrás azonosítója
2. allowMultipleAds – egy logikai érték, amely meghatározza, hogy több ads is megjelenjen-e az ad-break során
3. followRedirects – egy választható logikai érték, amely meghatározza, hogy ha a videólejátszó kell tiszteletben átirányítja a felhasználókat egy ad választ belül

A <**AdSource**> elem biztosít a Windows Media player egy beágyazott ad választ vagy egy ad választ mutató hivatkozás. Az alábbi elemek egyikét tartalmazhat:

* <VASTAdData> azt jelzi, hogy a VMAP fájlon belüli beágyazott túlnyomó ad választ
* <AdTagURI> URI, amely egy ad választ hivatkozik másik rendszerről
* <CustomAdData> -a nem túlnyomó választ jelölő tetszőleges karakterláncot.

Ebben a példában egy beágyazott ad választ meg van adva egy <VASTAdData> elem, amely tartalmazza a túlnyomó ad választ. Más elemeivel kapcsolatos további információkért lásd: [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

A <**AdBreak**> elem is tartalmazhat egy <**TrackingEvents**> elemet. A <**TrackingEvents**> elem lehetővé teszi, hogy nyomon követheti a kezdeti vagy egy ad break vagy e hiba történ a ad break végét. A <**TrackingEvents**> elem tartalmaz egy vagy több <**követési**> elemek, amelyek mindegyike egy nyomkövetési esemény és egy követési URI adja meg. A lehetséges nyomkövetési események állnak:

1. breakStart – nyomon követi az ad-break kezdete
2. breakEnd – egy ad break megvalósításának nyomon követése
3. Hiba – nyomon követi a ad break bekövetkezett hiba

A következő példa bemutatja, amely meghatározza a nyomkövetési események VMAP fájl

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

További információt a <**TrackingEvents**> elem és a gyermekek, lásd: http://iab.org/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Egy Media absztrakt alkalmazás-előkészítés sablonfájl (OSZLOPOS) használatával
Egy OSZLOPOS fájlt adja meg, amelyek meghatározzák, hogy mikor jelenik meg az ad eseményindítók teszi lehetővé. Egy példa egy előtti összegző ad, egy közepes összegző ad és a utáni összegző ad eseményindítók tartalmazó OSZLOPOS fájlt a következő:

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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


Egy OSZLOPOS fájl kezdődik egy **OSZLOPOS** elem, amely egy **eseményindítók** elemet. A <triggers> egy vagy több elemet tartalmaz **eseményindító** határozza meg, ha az ad lejátszani. 

A **eseményindító** elem tartalmazza-e egy **startConditions** elemet birtokló adjon meg, ha az ad számára, hogy kezdjen. A **startConditions** egy vagy több elemet tartalmaz <condition> elemek. Ha minden <condition> igaz eseményindító kezdeményezett, vagy visszavonták, attól függően, hogy a <condition> magában foglal egy **startConditions** vagy **endConditions** elem kulcsattribútumokkal. Ha több <condition> elem is szerepel, azokat egy implicit vagy számít, a feltétel kiértékelése igaz, akkor az eseményindító kezdeményezéséhez. <condition> elemek egymásba ágyazható. Ha gyermek <condition> elemek előre be van állítva, akkor számít egy implicit és, minden feltétel ki kell értékelnie az eseményindító kezdeményezése igaz. A <condition> elem tartalmazza-e a következő attribútumok, amelyek meghatározzák a következő feltételt: 

1. **típus** – meghatározza az állapot, esemény vagy tulajdonság típusát
2. **név** – a következő tulajdonság vagy esemény kiértékelés során használandó neve
3. **érték** – az értéket, amelyet a tulajdonság értékelni
4. **operátor** – a kiértékelés során használandó művelet: EQ (egyenlő), a NEQ (nem egyenlő), a GTR (nagyobb), a GEQ (nagyobb vagy egyenlő), a LT (kisebb), LEQ (kisebb vagy egyenlő), MOD ELEMET (Maradékos osztás)

**endConditions** is tartalmazhat, <condition> elemek. Ha a feltétel igaz az eseményindító alaphelyzetbe áll. A <trigger> elem is tartalmaz egy <sources> elem, amely tartalmazza egy vagy több <source> elemek. A <source> elemek a ad választ, és milyen típusú ad választ az URI határozza meg. Ebben a példában egy URI túlnyomó választ kapja. 

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Videó Player Ad felületdefiníció (VPAID) használatával
VPAID az API-k engedélyezésének végrehajtható ad egység egy videólejátszó folytatott kommunikációhoz. Ez lehetővé teszi a magas interaktív ad lép. A felhasználók beavatkozhatnak-e az ad-val, és az ad válaszolhassanak a megjelenítő végrehajtott műveleteket. Például az ad megjelenítésére gombok, amelyek lehetővé teszik a felhasználó hosszabb verzióját az ad vagy további információk megtekintéséhez. A videólejátszó támogatnia kell a VPAID API-t, és a végrehajtható ad meg kell valósítania az API-t. Ha egy player kísérel meg a kiszolgáló ad-kiszolgálóról ad túlnyomó választ, amely tartalmaz egy VPAID ad jelenhetnek meg.

Egy végrehajtható ad Adobe Flash™ vagy a webböngészőben végrehajtható JavaScript futásidejű környezetben kell végrehajtani kód jön létre. Egy ad-kiszolgáló egy olyan VPAID ad tartalmazó túlnyomó választ ad vissza, ha a apiFramework értékének attribútumnak a <MediaFile> elemnek kell lennie a "VPAID". Ez az attribútum Megadja, hogy az abban található ad VPAID végrehajtható ad. Az attribútum a MIME-típusát a végrehajtható fájljához, amilyen például az "application/x-shockwave-flash" vagy "application/x-javascript" értékre kell állítani. A következő XML-részlet mutatja a <MediaFile> VPAID végrehajtható ad tartalmazó túlnyomó választ elemét. 

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Egy végrehajtható ad használatával inicializálhatók a <AdParameters> elemet a <Linear> vagy <NonLinear> elemek túlnyomó választ. További információ a <AdParameters> elem, lásd: [túlnyomó 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). A VPAID API-val kapcsolatos további információkért lásd: [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>A Windows vagy Windows Phone 8 Player Ad-támogatással rendelkező megvalósítása
A Microsoft Media Platform: Player keretrendszer Windows 8 és Windows Phone 8 tartalmaz alkalmazásokra, amelyek bemutatják a keretrendszerrel videólejátszó alkalmazások végrehajtásához. Letöltheti a Player keretrendszer és a minták [Player keretrendszer Windows 8 és Windows Phone 8](https://playerframework.codeplex.com).

A Microsoft.PlayerFramework.Xaml.Samples megoldás megnyitásakor látni fogja a mappák a projekt számát. A hirdetés mappa létrehozása egy videólejátszó ad-támogatással rendelkező kapcsolódik mintakód tartalmazza. A hirdetés belül az mappa az XAML/cs fájlok száma, amelyek bemutatják, hogyan ads beszúrása más módon. Az alábbi lista ismerteti:

* AdPodPage.xaml bemutatja, hogyan egy ad pod megjelenítéséhez.
* AdSchedulingPage.xaml bemutatja, hogyan ads ütemezni.
* FreeWheelPage.xaml ads ütemezni a FreeWheel beépülő modul használatával jeleníti meg.
* MastPage.xaml bemutatja, hogyan ads ütemezése egy OSZLOPOS fájllal.
* ProgrammaticAdPage.xaml programozott módon ütemezhet egy videóban ads mutatja be.
* ScheduleClipPage.xaml bemutatja, hogyan ütemezése egy ad túlnyomó fájl nélkül.
* VastLinearCompanionPage.xaml beszúrása egy lineáris és kiegészítő ad jeleníti meg.
* VastNonLinearPage.xaml bemutatja, hogyan lehet beszúrni egy nem lineáris ad.
* VmapPage.xaml bemutatja, hogyan adhatja meg a hirdetések VMAP fájllal.

Ezeket a mintákat mindegyikének használ a Media Player határozzák meg a player keretrendszer. A legtöbb minták beépülő modulok, amelyek különböző ad választ formátumban támogatásához használja. A ProgrammaticAdPage minta programokon keresztül kommunikál egy MediaPlayer-példányt.

### <a name="adpodpage-sample"></a>AdPodPage minta
Ez a minta a AdSchedulerPlugin megadására az ad megjelenítéséhez használ. Ebben a példában egy közepes összegző hirdetmény öt másodperc után lejátszandó van ütemezve. Ad fogyasztanak (ads sorrendben megjelenítendő csoportja) egy ad-kiszolgáló által visszaadott túlnyomó fájlban van megadva. Az URI-t a túlnyomó fájl van megadva a <RemoteAdSource> elemet.

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

A AdSchedulerPlugin kapcsolatos további információkért lásd: [hirdetési Player keretében a Windows 8 és Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Ez a minta a AdSchedulerPlugin is használ. Három ads, egy előtti összegző ad, egy közepes összegző ad és a utáni összegző ad ütemezés. Az URI-t az egyes hirdetések VAST van megadva egy <RemoteAdSource> elemet.

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

### <a name="freewheelpage"></a>FreeWheelPage
Ezt a mintát használja, amely meghatározza a forrásattribútumot, amely meghatározza az URI, amely egy SmartXML fájlra mutat, amely meghatározza a tartalom ad, valamint az ütemezési információkat ad a FreeWheelPlugin.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Ez a minta a MastSchedulerPlugin, amely lehetővé teszi egy OSZLOPOS fájl használja. Az adatforrás-attribútum meghatározza a OSZLOPOS fájl helyét.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Ez a minta a Media Player programokon keresztül kommunikál. A ProgrammaticAdPage.xaml fájl elindítja a Media Player:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

A ProgrammaticAdPage.xaml.cs fájlt hoz létre egy AdHandlerPlugin ad meg egy TimelineMarker ad üzenetnek kell megjelennie, és hozzáadja a kezelő, amely betölti a RemoteAdSource túlnyomó fájlba URI megadása, és az ad majd játszik MarkerReached eseménynél.

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

### <a name="scheduleclippage"></a>ScheduleClipPage
Ez a minta egy közepes összegző ad ütemezése egy .wmv-fájlt, amely tartalmazza az ad megadásával a AdSchedulerPlugin használja.

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
Ez a minta bemutatja, hogyan használja a AdSchedulerPlugin ütemezése egy közepes összegző lineáris ad egy kiegészítő ad-val. A <RemoteAdSource> elem a túlnyomó fájl helyét adja meg.

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
Ez a minta egy lineáris ütemezése AdSchedulerPlugin és egy nem lineáris ad használja. A nagy fájl helye van megadva a <RemoteAdSource> elemet.

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

### <a name="vmappage"></a>VMAPPage
Ez a minta VMAP fájllal ads ütemezni a VmapSchedulerPlugin használja. Az URI-t a VMAP fájl forrásattribútumának van megadva a <VmapSchedulerPlugin> elemet.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>IOS rendszerű Ad-támogatással rendelkező videó Player megvalósítása
A Microsoft Media Platform: Player keretrendszer IOS-alkalmazásokat, amelyek bemutatják a keretrendszerrel videólejátszó alkalmazások végrehajtásához gyűjteményét tartalmazza. Letöltheti a Player keretrendszer és a minták [Azure Media Player keretrendszer](https://github.com/Azure/azure-media-player-framework). A github-oldalon tartalmaz egy hivatkozást egy Wiki player keretében további adatokat tartalmazó és a player minta bemutatása: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>VMAP hirdetések ütemezése
A következő példa bemutatja, hogyan VMAP fájllal ads ütemezni.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
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

### <a name="scheduling-ads-with-vast"></a>VAST hirdetések ütemezése
A következő példa bemutatja, hogyan ütemezni a késői kötés túlnyomó ad.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
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

   A következő példa bemutatja, hogyan ütemezése egy korai kötés túlnyomó ad.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
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

A következő példa bemutatja, hogyan hirdetések nyers Kivágás szerkesztése (RCE) használatával

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

A következő példa bemutatja, hogyan egy ad pod ütemezni.

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

A következő példa bemutatja, hogyan ütemezése a nem kapcsolódó közepes összegző ad. A nem kapcsolódó ad csak lejátszott, miután függetlenül bármilyen keresést az használatával hajtja végre.

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

A következő példa bemutatja, hogyan egy állandóságát közepes összegző ad ütemezni. Minden alkalommal, amikor az adott pont a videó idősoron éri el a kapcsolódó ad jelenik meg.

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

A következő példa bemutatja, hogyan ütemezése egy utáni összegző ad.

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

A következő példa bemutatja, hogyan ütemezése egy előtti összegző ad.

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

A következő példa bemutatja, hogyan ütemezése egy közepes összegző átfedő ad.

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


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Videolejátszó alkalmazások fejlesztése](media-services-develop-video-players.md)

