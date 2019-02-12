---
title: Hirdetések ügyféloldali beillesztése |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hirdetések ügyféloldali beillesztése.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: cc5f3f729acca1f7aa23a7714300c1b581c6f7f8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993894"
---
# <a name="inserting-ads-on-the-client-side"></a>Hirdetések ügyféloldali beillesztése
Ez a cikk különböző típusú hirdetések ügyféloldali beillesztése információt tartalmaz.

Élő adatfolyam-továbbítási videók lezárt feliratozás és az Active Directory támogatásával kapcsolatos információkat lásd: [támogatott kódolt feliratok és Ad beszúrási szabványok](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Az Azure Media Player jelenleg nem támogatja a hirdetéseket.
> 
> 

## <a id="insert_ads_into_media"></a>A Media Ads beszúrása
Az Azure Media Services reklámjelölőket keresztül a Windows Media Platform támogatja: Player keretrendszerekkel. Ad-támogatás a Player keretrendszerek érhetők el a Windows 8, a Silverlight, a Windows Phone 8 és az iOS-eszközökhöz. Minden egyes player keretrendszer, amely bemutatja, hogyan valósíthat meg egy médialejátszó alkalmazásba mintakód tartalmazza. Nincsenek az adathordozó: a lista beilleszthet ads három különböző típusú.

* **Lineáris** – teljes keret hirdetések fő videó megállítása.
* **Lineáris** – területre hirdetések vannak feltüntetve, a fő videó lejátszása, általában egy emblémáját vagy más statikus kép umístit v a Windows Media player.
* **Kiegészítő** – kívül a Windows Media player megjelenített hirdetéseket.

Hirdetések helyezhető a fő videó idősorán bármely pontján. A Windows Media player kell mondja el, ha lejátszása az ad és melyik lejátszásához hirdetéseket. Ebben az esetben a szabványos XML-alapú fájlokat használatával: Videó Ad szolgáltatási sablon (VAST), digitális videót több Ad lista (VMAP), a Media absztrakt alkalmazás-előkészítés sablont (OSZLOPOS), és a digitális videolejátszó Ad illesztőjének definícióját (VPAID). HATALMAS fájlokat adja meg, milyen hirdetések megjelenítéséhez. VMAP fájlokat adja meg, mikor lejátszása különböző hirdetések és HATALMAS XML-kódot tartalmaz. OSZLOPOS fájlokhoz, amely is tartalmazhat HATALMAS XML feladatütemezési ads egy másik módja. VPAID fájlokat adja meg a videó lejátszási és az ad vagy ad-kiszolgáló közötti illesztőfelületet.

Minden egyes player keretrendszer eltérően működik, és minden egyes tárgyalja a saját cikkben. Ez a cikk ismerteti az alapszintű mechanizmusok ads szúrható. Videolejátszó alkalmazások ads kérése egy ad-kiszolgálóról. Az ad kiszolgáló válaszolhat a különféle módokon:

* Egy HATALMAS fájlt küld vissza
* Egy VMAP fájlt (az embedded VAST) adja vissza
* Egy OSZLOPOS fájlt (az embedded VAST) adja vissza
* VPAID ads HATALMAS fájlt vissza

### <a name="using-a-video-ad-service-template-vast-file"></a>Videó Ad szolgáltatás sablon (VAST) fájl használatával
Egy HATALMAS fájl adja meg, milyen ad vagy hirdetések megjelenítéséhez. A következő XML formátumú egy HATALMAS fájlt egy lineáris ad példát:

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

A lineáris ad írja le a <**lineáris**> elemet. Azt adja meg az ajánlat időtartamát az ad, a nyomon követés, kattintson a kampányban, és számos keresztül **MediaFile** elemeket. Követési események belül vannak megadva a <**TrackingEvents**> elem és a egy ad-kiszolgáló az ad megtekintése közben előforduló különféle események nyomon követése. Ebben az esetben a kezdő, középpont befejeződött, és bontsa ki a eseményeket a rendszer nyomon követi. Az indítási esemény akkor fordul elő, amikor az megjelenik az ad. A középpont esemény következik be legalább 50 %-a az ad-idősor már megtekintett. Az ad a teljes körű futott a teljes esemény következik be. A kibontás esemény következik be, amikor a felhasználó a videólejátszó nyit a teljes képernyős. Clickthroughs vannak megadva, a <**Átkattintós**> elemet egy <**VideoClicks**> elem és a egy jelenítsen meg, ha a felhasználó kattint a ad erőforrás URI-t adja meg. ClickTracking van megadva egy <**ClickTracking**> elemen belül is a <**VideoClicks**> elem határozza meg azt a Windows Media Player kérése, amikor a felhasználó kattint az ad követési erőforrás . A <**MediaFile**> elemeket adja meg az ad egy adott kódolási adatait. Ha egynél több <**MediaFile**> elemben, a videó lejátszási választhat a platform a legjobb kódolást.

Lineáris ads is megjeleníthetők a megadott sorrendben. Ehhez hozzá további <Ad> elemeit, amelyek a VAST fájlt, és adja meg a feladatütemezés attribútum használatával sorrendjét. A következő példa ezt mutatja be:

```xml
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
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

Lineáris ads megadott egy <Creative> elemet is. A következő példa bemutatja egy <Creative> elem, amely nem lineáris ad ismerteti.

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

A <**NonLinearAds**> elem tartalmazhat egy vagy több <**NonLinear**> elemek, amelyek mindegyike egy lineáris ad is ismertetik. A <**NonLinear**> elem azt határozza meg az erőforrás a nemlineáris ad-hez. Az erőforrás lehet egy <**StaticResource**>, <**IFrameResource**>, vagy egy <**HTMLResource**>. <**StaticResource**>-HTML erőforrás ismerteti, és határozza meg, amely meghatározza, hogyan jelenjen meg az erőforrás egy creativeType attribútum:

Kép/gif, a kép/jpeg, a lemezkép/png – az erőforrás megjelenik egy HTML <**img**> címke.

Application/x-javascript – az erőforrás megjelenik egy HTML <**parancsfájl**> címke.

Flash-lejátszó Application/x-shockwave-flash – az erőforrás megjelenik.

**IFrameResource** ismerteti egy HTML-erőforrást egy IFRAME elembe jelenhet meg. **HTMLResource** HTML-kódot, amely szúrhatók be egy weblap, egy darab ismerteti. **TrackingEvents** adja meg a követési események és az esemény bekövetkeztekor kérés URI Azonosítóját. Ebben a példában a acceptInvitation és összecsukása események nyomon követi. További információ a **NonLinearAds** elemet és annak gyermekeihez IAB.NET/VAST talál. Vegye figyelembe, hogy a **TrackingEvents** elem található a **NonLinearAds** elem helyett a **NonLinear** elemet.

Kiegészítő ads belül definiált egy <CompanionAds> elemet. A <CompanionAds> elemet tartalmazhat egy vagy több <Companion> elemeket. Minden egyes <Companion> elem egy kiegészítő ad ismerteti, és tartalmazhat egy <StaticResource>, <IFrameResource>, vagy <HTMLResource> amely lineáris ad alkalmazott módon vannak megadva. Egy HATALMAS fájlt tartalmazhat több kiegészítő hirdetések és a lejátszóalkalmazás kiválaszthatja a legmegfelelőbb ad megjelenítéséhez. VAST kapcsolatos további információkért lásd: [HATALMAS 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Egy digitális videót több Ad lista (VMAP) fájl használatával
VMAP fájl lehetővé teszi, hogy adja meg, ha ad oldaltörések történik, minden egyes break mennyi ideig tart, hány ads szünet is meg és hirdetések típusú lehet megszakítás alatt jelenik meg. A következő egy példa, amely meghatározza egy egyetlen ad break VMAP fájlban:

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

VMAP fájl kezdődik a <VMAP> elem, amely tartalmaz egy vagy több <AdBreak> elemeket, egy ad break meghatározása. Minden ad break egy/csere típusú, eseti azonosítója és idő eltolása szolgál. A breakType attribútum határozza meg, amely során a szünet lejátszhatók ad: lineáris, nem lineáris, vagy megjelenítése. Hirdetések térkép megjelenítése HATALMAS kiegészítő hirdetéseket. Egynél több ad típusa (szóközök nélkül) vesszővel tagolt listában adható meg. A breakID az ad-azonosítója egy nem kötelező megadni. A timeOffset adja meg, ha az ad üzenetnek kell megjelennie. A következő módokon adhatók meg:

1. Idő ezredmásodpercben .mmm esetén óó vagy hh:mm:ss.mmm formátumú. Ez az attribútum értékét idejét határozza meg a videó ütemterv kezdettől fogva az ad/csere típusú elejére.
2. Százalékos – n % formátumban ahol n értéke a videó ütemterv százalékos az ad lejátszás előtt lejátszása
3. Kezdő és záró – Itt adhatja meg, hogy egy ad üzenetnek kell megjelennie, előtt vagy után megjelenik a videót
4. Pozíció – ad oldaltörések sorrendjét határozza meg, ha az ad oldaltörések időzítése ismeretlen, például élő adatfolyam-továbbítás. Minden ad break sorrendje a következő #n formátumban, ahol n az 1 vagy nagyobb egész szám van megadva. 1 azt jelzi, hogy az ad lejátszani az első adandó 2 azt jelzi, hogy az ad lejátszani a második lehetőséghez, és így tovább.

Belül a <AdBreak> elemben is lehet az egyik <**AdSource**> elemet. A <**AdSource**> elem tartalmazza a következő attribútumokat:

1. Azonosítója – az ad-forrása azonosítóját határozza meg.
2. allowMultipleAds – logikai érték, amely meghatározza, hogy több ads jeleníthető meg az ad/csere során
3. belül választ ad átirányítja egy választható logikai érték, amely meghatározza, hogy ha a videolejátszó tiszteletben kell followRedirects –

A <**AdSource**> elem biztosít a Windows Media player beágyazott ad választ, vagy egy hivatkozást ad választ. Ez tartalmazza a következő elemek közül:

* <VASTAdData> azt jelzi, hogy a HATALMAS ad választ a VMAP fájl van beágyazva.
* <AdTagURI> egy URI-t ad választ hivatkozik egy másik rendszerből
* <CustomAdData> – tetszés szerinti karakterlánc, amely nem HATALMAS választ jelöli

Ebben a példában egy egysoros ad választ meg van adva egy <VASTAdData> elem, amely tartalmazza a HATALMAS ad választ. Az egyéb elemek kapcsolatos további információkért lásd: [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

A <**AdBreak**> elem is tartalmazhat egy <**TrackingEvents**> elemet. A <**TrackingEvents**> elem lehetővé teszi, hogy nyomon követheti az elején vagy végén egy ad break, vagy hogy hiba történt az ad/csere során. A <**TrackingEvents**> elem tartalmaz egy vagy több <**követési**> elemek, amelyek mindegyike egy követési eseményének és a egy nyomkövetési URI-t adja meg. A lehetséges követési események a következők:

1. breakStart – nyomon követi az ad/csere típusú kezdete
2. breakEnd – egy ad break megvalósításának nyomon követése
3. Hiba – az ad/csere során fellépő hiba követi nyomon.

Az alábbi példa bemutatja egy VMAP fájlt, amely meghatározza a nyomkövetési események

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

További információ a <**TrackingEvents**> elemet és annak gyermekeihez: http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Egy Media absztrakt alkalmazás-előkészítés a sablonfájlt (OSZLOPOS) használatával
Egy OSZLOPOS fájlt adja meg, amelyek meghatározzák, hogy mikor jelenik meg az ad eseményindítók teszi lehetővé. Az alábbiakban látható egy példa, amely tartalmaz egy előre összegző ad, egy közepes összegző ad és a egy utáni bevezetése ad eseményindítók OSZLOPOS fájlt.

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


OSZLOPOS fájl kezdődik a **OSZLOPOS** elem, amely tartalmaz egy **eseményindítók** elemet. A <triggers> egy vagy több elemet tartalmaz **eseményindító** elemek, amelyek meghatározzák, hogy mikor lehet lejátszani egy ad.

A **eseményindító** elem tartalmaz egy **startConditions** elemet, adja meg, ha az ad lejátszása kell kezdődnie. A **startConditions** egy vagy több elemet tartalmaz <condition> elemeket. Ha minden egyes <condition> egy eseményindító kezdeményezi, vagy visszavonja, attól függően, hogy igaz értéket ad vissza a <condition> belül található egy **startConditions** vagy **endConditions** elem jelölik. Ha több <condition> elemeket is, mint egy implicit vagy lesznek kezelve, bármely feltétel kiértékelése igaz kezdeményezni az eseményindító hatására. <condition> elemek ágyazhatók be. Ha gyermek <condition> elemek előre be van állítva, akkor számít egy implicit és, minden feltétel igaz kezdeményezni az eseményindító kell visszaadnia. A <condition> elem tartalmazza a következő attribútumokat, amelyek meghatározzák a feltétel:

1. **típus** – határozza meg az állapot, esemény vagy tulajdonság
2. **név** – a tulajdonságot, vagy a kiértékelés során használandó esemény neve
3. **érték** – fog értékelni egy tulajdonság értékét
4. **operátor** – a kiértékelés során használandó műveletet: EQ (egyenlő), a NEQ (nem egyenlő), a GTR (nagyobb), a GEQ (nagyobb vagy egyenlő), az LT (kisebb), LEQ (kisebb vagy egyenlő), a maradék (modulus)

**endConditions** is tartalmaznak <condition> elemeket. Ha a feltétel igaz értékre, a trigger alaphelyzetbe áll. A <trigger> elem emellett tartalmaz egy <sources> elem, amely tartalmaz egy vagy több <source> elemeket. A <source> elemek a ad választ és ad válasz típusának definiálása az URI-t. Ebben a példában egy URI-t egy HATALMAS választ kapja.

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>A videó Player-Ad Interface Definition (VPAID)
VPAID API-k olyan videolejátszót kommunikálni végrehajtható ad egységek engedélyezése. Ez lehetővé teszi interaktív ad élményt. A felhasználók beavatkozhatnak-az ad és az ad válaszolhat a megjelenítő által végzett műveleteket. Például egy ad jeleníthet meg gombok, amelyek lehetővé teszik a felhasználó további információt, vagy az ad hosszabb verziójának megtekintéséhez. A videó lejátszási támogatnia kell a VPAID API-t, és a végrehajtható ad meg kell valósítania az API-t. Amikor egy kér egy ad-kiszolgálóról a kiszolgálót az ad HATALMAS választ, amely tartalmaz egy VPAID ad jelenhetnek meg.

Egy végrehajtható ad jön létre a kódot, amely például Adobe Flash™ vagy egy webböngészőben végrehajtható JavaScript futtatókörnyezet környezetben kell végrehajtani. Egy ad-kiszolgáló egy VPAID ad tartalmazó HATALMAS választ ad vissza, ha a apiFramework értékét az attribútum a <MediaFile> elemnek kell lennie a "VPAID". Ez az attribútum meghatározza, hogy az abban található ad egy VPAID végrehajtható ad. A type attribútumot állítsa a végrehajtható fájlt, például az "application/x-shockwave-flash" vagy "application/x-javascript" MIME-típusát. A következő XML-kódrészlet bemutatja a <MediaFile> egy HATALMAS választ, amely tartalmazza a VPAID végrehajtható ad elemet.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Egy végrehajtható ad használatával lehet inicializálni a <AdParameters> elemen belül a <Linear> vagy <NonLinear> elemek HATALMAS választ. További információ a <AdParameters> elem, lásd: [HATALMAS 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). A VPAID API-val kapcsolatos további információkért lásd: [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>A Windows- vagy Ad-támogatás a Windows Phone 8-lejátszó megvalósítása
A Microsoft Media Platform: Player keretrendszer a Windows 8 és Windows Phone 8-alkalmazásokra, amelyek bemutatják, hogyan valósíthat meg egy videólejátszó alkalmazások a keretrendszer használatával gyűjteményét tartalmazza. Letöltheti a Player keretrendszer és a minták a [Player keretrendszer a Windows 8 és Windows Phone 8](https://playerframework.codeplex.com).

A Microsoft.PlayerFramework.Xaml.Samples megoldás megnyitásakor látni fogja a mappák a projekten belül számos. A hirdetési mappa tartalmazza a videolejátszót létrehozásához az ad-támogatás a mintakódot. A hirdetési belüli mappa a következő egy XAML és cs fájlok száma, amelyek bemutatják, hogyan helyezze be a hirdetések eltérő módon. Az alábbi lista ismerteti:

* AdPodPage.xaml mutatja be egy ad-pod megjelenítéséhez.
* AdSchedulingPage.xaml ads ütemezése mutatja be.
* FreeWheelPage.xaml ads ütemezése a FreeWheel beépülő modul használatával mutatja be.
* MastPage.xaml bemutatja, hogyan ads ütemezni egy OSZLOPOS fájllal.
* ProgrammaticAdPage.xaml programozott módon ütemezhet egy videóban ads mutatja be.
* ScheduleClipPage.xaml bemutatja, hogyan ütemezhet egy ad egy HATALMAS fájl nélkül.
* VastLinearCompanionPage.xaml bemutatja, hogyan szúrható be egy lineáris és kiegészítő ad.
* VastNonLinearPage.xaml Beszúrás nem lineáris ad mutatja be.
* VmapPage.xaml bemutatja, hogyan adja meg a hirdetések VMAP-fájllal.

Ezek a minták minden egyes használja a Media Player osztály a player keretrendszer által meghatározott. A legtöbb minták használata, amely számos ad választ formátumú támogatása beépülő modulokat. A ProgrammaticAdPage minta programozott módon használja, ha a Media Player példánya.

### <a name="adpodpage-sample"></a>AdPodPage Sample
Ebben a példában a AdSchedulerPlugin mikor jelenjenek meg az ad meg. Ebben a példában egy közepes összegző hirdetmény öt másodperc után lejátszandó van ütemezve. Az ad-pod (csoportja hirdetések megjelenítéséhez sorrendben) egy-egy ad-kiszolgáló által visszaadott HATALMAS fájlban van megadva. Az URI-t a HATALMAS fájl van megadva a <RemoteAdSource> elemet.

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

A AdSchedulerPlugin kapcsolatos további információkért lásd: [hirdetési a Player keretrendszer, a Windows 8 és Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Ez a példa a AdSchedulerPlugin is használ. Ütemezi a három hirdetések, egy üzem előtti összegző ad, a közepes összegző ad és egy utáni bevezetése ad. Az URI-t az egyes hirdetések esetében az VAST van megadva egy <RemoteAdSource> elemet.

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
Ez a minta a FreeWheelPlugin, amely meghatározza a forrásattribútumot, amely meghatározza egy SmartXML fájlra mutat, amely meghatározza a tartalom ad, valamint az ütemezési információkat ad URI-t használja.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Ez a minta a MastSchedulerPlugin, amely lehetővé teszi, hogy egy OSZLOPOS fájlt használja. Az adatforrás-attribútum meghatározza az OSZLOPOS fájl helyét.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Ez a minta programozott módon kommunikál a Media Player. A ProgrammaticAdPage.xaml fájl példányosítják a Media Player:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

A ProgrammaticAdPage.xaml.cs fájlt hoz létre egy AdHandlerPlugin hozzáad egy TimelineMarker megadásához egy ad üzenetnek kell megjelennie, és ezt követően hozzáadja a kezelő, amely betölti egy RemoteAdSource HATALMAS fájlba URI megadása, és az ad majd játszik MarkerReached eseményhez.

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
Ebben a példában a AdSchedulerPlugin ütemezése egy közepes összegző ad egy .wmv-fájlt, amely tartalmazza az ad megadásával.

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
Ez a példa bemutatja, hogyan használható a AdSchedulerPlugin ütemezni egy közepes összegző lineáris ad egy kiegészítő ad-vel. A <RemoteAdSource> elem azt határozza meg a HATALMAS fájl helyét.

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
Ebben a példában a AdSchedulerPlugin ütemezni egy lineáris és a egy nem lineáris ad. A HATALMAS helye meg van adva a <RemoteAdSource> elemet.

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
Ebben a példában a VmapSchedulerPlugin ütemezése ads VMAP fájl használatával. Az URI-t a VMAP fájl forrásattribútumának van megadva a <VmapSchedulerPlugin> elemet.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>IOS rendszerű Ad-támogatás a videolejátszó megvalósítása
A Microsoft Media Platform: Player keretrendszere az iOS-alkalmazásokra, amelyek bemutatják, hogyan valósíthat meg egy videólejátszó alkalmazások a keretrendszer használatával gyűjteményét tartalmazza. Letöltheti a Player keretrendszer és a minták a [Azure Media Player keretrendszerének](https://github.com/Azure/azure-media-player-framework). A GitHub-oldalon rendelkezik, amely tartalmaz további információt a player keretrendszer Wiki mutató hivatkozást és a játékosok minta bemutatása: [Az Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Hirdetések VMAP ütemezése
Az alábbi példa bemutatja, hogyan ütemezése ads VMAP fájl használatával.

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

### <a name="scheduling-ads-with-vast"></a>Hirdetések VAST ütemezése
A következő minta bemutatja, hogyan ütemezhet egy frissítéskibocsátási kötés HATALMAS ad.


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

   A következő minta bemutatja, hogyan ütemezhet egy korai kötés HATALMAS ad.

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

A következő minta bemutatja, hogyan szúrható be egy ad hozzávetőleges Kivágás szerkesztése (Rrásszámítógép) használatával

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

Az alábbi példa bemutatja, hogyan ütemezhet egy ad-pod.

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

Az alábbi példa bemutatja, hogyan ütemezhet egy nem kiemelt közepes összegző ad. Egy nem kiemelt ad csak játsszák után függetlenül bármely kérné a megjelenítő hajt végre.

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

Az alábbi példa bemutatja, hogyan ütemezhet egy Beragadó közepes összegző ad. Minden alkalommal, amikor eléri az adott pont a videó eseményeket egy idővonalon megjelenítve egy Beragadó ad jelenik meg.

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

A következő minta bemutatja, hogyan ütemezhet egy utáni bevezetése ad.

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

A következő minta bemutatja, hogyan ütemezhet egy üzem előtti összegző ad.

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

A következő minta bemutatja, hogyan ütemezhet egy átfedő vásárolok összegző ad.

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

