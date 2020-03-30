---
title: Szűrők és dinamikus jegyzékek | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogyan hozhat létre szűrőket, hogy az ügyfél használhassa őket az adatfolyam adott szakaszainak streameléséhez. A Media Services dinamikus jegyzékeket hoz létre a szelektív adatfolyam archiválására.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015846"
---
# <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek

> [!div class="op_single_selector" title1="Válassza ki a Media Services használt verzióját:"]
> * [2-es verzió](media-services-dynamic-manifest-overview.md)
> * [3-as verzió](../latest/filters-dynamic-manifest-overview.md)

A 2.17-es kiadástól kezdve a Media Services lehetővé teszi, hogy szűrőket határozzon meg az eszközökhöz. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára, hogy olyan műveleteket válasszanak, mint például: a videónak csak egy részét játssza le (a teljes videó lejátszása helyett), vagy csak a hang- és videointerpretációk egy részhalmazát adja meg, amelyet az ügyfél eszköze kezelni tud ( az eszközhöz társított összes interpretáció helyett). Az eszközök szűrése dinamikus **jegyzékeken**keresztül érhető el, amelyek az ügyfél kérésére jönnek létre a videó adott szűrő(k) alapján történő streamelésére.

Ez a témakör azokat a gyakori eseteket ismerteti, amelyekben a szűrők használata előnyös lehet az ügyfelek számára, és olyan témakörökre mutató hivatkozásokat, amelyek bemutatják a szűrők programozott létrehozását.

## <a name="overview"></a>Áttekintés
Amikor a tartalmat az ügyfeleknek kézbesíti (élő események vagy igény szerinti videó streamelése), a cél az, hogy kiváló minőségű videót juttasson el különböző eszközökre különböző hálózati feltételek mellett. E cél elérése érdekében tegye a következőket:

* kódolja az adatfolyamot többsávszélességű[(adaptív bitráta)](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)videostreamre (ez gondoskodik a minőségről és a hálózati feltételekről) és 
* a Media Services [dinamikus csomagolásával](media-services-dynamic-packaging-overview.md) dinamikusan újracsomagolhatja az adatfolyamot különböző protokollokba (ez gondoskodik a különböző eszközökön történő streamelésről). A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH. 

### <a name="manifest-files"></a>Jegyzékfájlok
Amikor adaptív sávszélességű streameléshez kódol egy eszközt, létrejön egy **jegyzékfájl** (lejátszási lista) fájl (a fájl szövegalapú vagy XML-alapú). A **jegyzékfájl** olyan adatfolyam-metaadatokat tartalmaz, mint például: műsorszám típusa (hang, videó vagy szöveg), műsorszám neve, kezdési és befejezési idő, bitráta (tulajdonságok), műsorszavak, bemutatóablak (rögzített időtartamú csúszóablak), videokodek (FourCC). Arra is utasítja a játékost, hogy a következő töredéket szerezze be a következő elérhető lejátszható videótöredékekről és azok helyéről. A töredékek (vagy szegmensek) a videotartalom tényleges "részei".

Íme egy példa egy jegyzékfájlra: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dinamikus jegyzékek
Vannak olyan [esetek,](media-services-dynamic-manifest-overview.md#scenarios) amikor az ügyfél nek nagyobb rugalmasságra van szüksége, mint az alapértelmezett eszköz jegyzékfájljában leírtak. Példa:

* Eszközspecifikus: csak a megadott interpretációkat és/vagy meghatározott nyelvi sávokat adja meg, amelyeket a tartalom lejátszására használt eszköz támogat ("interpretációs szűrés"). 
* Csökkentse a jegyzékfájlt, hogy egy élő esemény ("alklipszűrés") részklipjét jelenítse meg.
* A videó elejének levágása ("videó vágása").
* Állítsa be a bemutatóablakot (DVR) annak érdekében, hogy a lejátszóban a DVR ablak ának korlátozott hossza biztosított ("bemutatóablak beállítása").

E rugalmasság elérése érdekében a Media Services előre definiált [szűrőkön](media-services-dynamic-manifest-overview.md#filters)alapuló **dinamikus jegyzékeket** kínál.  Miután definiálta a szűrőket, az ügyfelek felhasználhatják őket a videó adott interpretációjának vagy alklipjeinek streamelésére. Ezek a szűrő(k) a streamelési URL-cím. A szűrők alkalmazhatók a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)által támogatott adaptív sávszélességű streamelési protokollokra: HLS, MPEG-DASH és Smooth Streaming. Példa:

MPEG DASH URL-cím szűrővel

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Sima streamelési URL-cím szűrővel

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


A tartalom kézbesítéséről és a folyamatos átvitelű URL-ek létrehozásáról a Tartalom áttekintése című témakörben olvashat [bővebben.](media-services-deliver-content-overview.md)

> [!NOTE]
> Vegye figyelembe, hogy a dinamikus jegyzékek nem módosítja az eszközt és az eszköz alapértelmezett jegyzékfájlját. Az ügyfél dönthet úgy, hogy szűrővel vagy anélkül kér adatfolyamot. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filters (Szűrők)
Az eszközszűrőknek két típusa van: 

* Globális szűrők (az Azure Media Services-fiók bármely eszközére alkalmazhatók, a fiók élettartama) és 
* Helyi szűrők (csak olyan eszközre alkalmazható, amelyhez a szűrő t a létrehozáskor társítva volt, az eszköz élettartama). 

A globális és a helyi szűrőtípusok tulajdonságai pontosan megegyeznek. A fő különbség a kettő között az, hogy milyen típusú filer alkalmasabb. A globális szűrők általában alkalmasak olyan eszközprofilokhoz (interpretációs szűréshez), ahol helyi szűrők használhatók egy adott eszköz vágására.

## <a name="common-scenarios"></a><a id="scenarios"></a>Gyakori forgatókönyvek
Ahogy már említettük, amikor a tartalmat az ügyfeleknek kézbesítjük (élő események vagy igény szerinti videó közvetítése), a cél az, hogy kiváló minőségű videót juttasson el különböző eszközökre különböző hálózati körülmények között. Emellett előfordulhat, hogy más követelmények, amelyek magukban foglalják az eszközök szűrése és a **dinamikus jegyzékfájl**s használatával. A következő szakaszok rövid áttekintést nyújtanak a különböző szűrési forgatókönyvekről.

* Adja meg csak a hang- és videointerpretációk egy részét, amelyet bizonyos eszközök kezelni tudnak (az eszközhöz társított összes interpretáció helyett). 
* A videónak csak egy részét játssza le (ahelyett, hogy az egész videót lejátszana).
* Állítsa be a DVR prezentációs ablakát.

## <a name="rendition-filtering"></a>Interpretáció szűrése
Dönthet úgy, hogy eszközét több kódolási profilba (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) és több féle bitrátába kódolja. Azonban nem minden ügyféleszköz támogatja az eszköz összes profilját és bitrátát. A régebbi Android-eszközök például csak a H.264 Baseline+AACL-t támogatják. Magasabb bitráták küldése olyan eszközre, amely nem tudja megszerezni az előnyöket, pazarolja a sávszélességet és az eszközszámítást. Az ilyen eszköznek meg kell fejtenie az összes megadott információt, csak a megjelenítéshez lekicsinyítette.

A Dynamic Manifest segítségével olyan eszközprofilokat hozhat létre, mint a mobil, a konzol, a HD/SD stb., és felveheti azokat a számokat és tulajdonságokat, amelyeket az egyes profilok részének szeretne használni.

![Példa interpretáció szűrésére][renditions2]

A következő példában egy kódoló segítségével kódolt a mezzanine eszköz hét ISO MP4s videó kiadatás (180p-től 1080p- ig). A kódolt eszköz dinamikusan csomagolható a következő streamelési protokollok bármelyikébe: HLS, Smooth és MPEG DASH.  A diagram tetején a HLS-jegyzékfájl a szűrők nélküli eszköz höz látható (mind a hét interpretációt tartalmazza).  A bal alsó sarokban megjelenik a HLS-jegyzékfájl, amelyre az "ott" nevű szűrőt alkalmazták. Az "ott" szűrő az 1Mbps alatti összes bitráta eltávolítását határozza meg, ami azt eredményezte, hogy az alsó két minőségi szint lelett távolítva a válaszban. A jobb alsó sarokban megjelenik a HLS jegyzékfájl, amelyre a "mobil" nevű szűrőt alkalmazták. A "mobil" szűrő azt határozza meg, hogy az okat eltávolítsuk azokat az interpretációkat, amelyek felbontása nagyobb, mint 720p, ami a két 1080p-s interpretáció eltávolítását eredményezte.

![Interpretáció szűrése][renditions1]

## <a name="removing-language-tracks"></a>Nyelvi sávok eltávolítása
A vagyontárgyak között több hangnyelv is lehet, például angol, spanyol, francia stb. Általában a Player SDK-kezelők alapértelmezett hangsáv-kiválasztása és a felhasználónként elérhető hangsávok. Ez kihívást jelent, hogy dolgozzon ki ilyen Játékos SDK-k, igényel különböző megvalósítások között eszköz-specifikus játékos-keretrendszerek. Emellett egyes platformokon a Lejátszó API-k korlátozottak, és nem tartalmazzák a hangválasztó funkciót, ahol a felhasználók nem választhatják ki vagy módosíthatják az alapértelmezett hangsávot. Az eszközszűrőkkel szabályozhatja a viselkedést, ha olyan szűrőket hoz létre, amelyek csak a kívánt hangnyelveket tartalmazzák.

![Nyelvi sávok szűrése][language_filter]

## <a name="trimming-start-of-an-asset"></a>Eszköz vágási kezdete
A legtöbb élő streamelési eseményben az operátorok néhány tesztet futtatnak a tényleges esemény előtt. Például, lehet, hogy egy pala, mint ez kezdete előtt az esemény: "Program indul egy pillanatra". Ha a program archiválás, a teszt és pala adatok is archivált, és tartalmazza a bemutatót. Ez az információ azonban nem jeleníthető meg az ügyfelek számára. A dinamikus jegyzékfájl segítségével létrehozhat egy indítási időszűrőt, és eltávolíthatja a nem kívánt adatokat a jegyzékfájlból.

![Vágás kezdete][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Alklipek (nézetek) létrehozása élő archívumból
Sok élő esemény hosszú ideig fut, és az élő archívum több eseményt is tartalmazhat. Az élő esemény befejezése után a műsorszolgáltatók az élő archívumot logikai programindítási és -leállítási szekvenciákra szeretnék felbontani. Ezután tegye közzé ezeket a virtuális programokat külön-külön az élő archívum utófeldolgozása nélkül, és ne hozzon létre külön eszközöket (amelyek nem kapják meg a cdn-ek meglévő gyorsítótárazott töredékeinek előnyeit). Ilyen virtuális programok például egy futball- vagy kosárlabdajáték negyede, a baseball ban való inningek vagy bármely sportprogram egyedi eseményei.

A dinamikus jegyzékfájl segítségével szűrőket hozhat létre a kezdési/befejezési időpontok használatával, és virtuális nézeteket hozhat létre az élő archívum tetején. 

![Az alklip szűrője][subclip_filter]

Szűrt eszköz:

![Síelés][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Bemutatóablak beállítása (DVR)
Jelenleg az Azure Media Services körkörös archívumot kínál, ahol az időtartam 5 perc és 25 óra között konfigurálható. A jegyzékfájl-szűrés segítségével az archívum tetején lévő görgetésre kijelölt DVR-ablakot hozhat létre adathordozó törlése nélkül. Sok olyan forgatókönyv van, ahol a műsorszolgáltatók korlátozott DVR ablakot szeretnének biztosítani az élő éllel való mozgáshoz, és ugyanakkor nagyobb archiválási ablakot tartanak. Előfordulhat, hogy a műsorszolgáltató a DVR ablakon kívüli adatokat szeretné használni a klipek kiemelésére, vagy különböző DVR-ablakokat szeretne biztosítani a különböző eszközökhöz. Például a legtöbb mobileszköz nem kezeli a nagy DVR-ablakokat (a mobileszközökhöz 2 perces DVR-ablak, asztali ügyfelek esetében pedig egy óra áll elő).

![DVR ablak][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>A LiveBackoff beállítása (élő pozíció)
A jegyzékfájl-szűréssel néhány másodpercet távolíthat el az élő program élő éléről. A szűrés lehetővé teszi a műsorszolgáltatók számára, hogy az előnézeti közzétételi ponton megtekintsék a bemutatót, és hirdetésbeszúrási pontokat hozzanak létre, mielőtt a nézők megkapják az adatfolyamot (30 másodperccel visszakapcsolva). A műsorszolgáltatók ezután ezeket a hirdetéseket az ügyfélkeretükbe tolhatják, hogy még a hirdetési lehetőség előtt megkaphassák és feldolgozhassák az információkat.

A hirdetéstámogatás mellett a LiveBackoff beállítás sal a megtekintők pozíciójának beállítására is használható, így amikor az ügyfelek elsodródnak, és elérik az élő éleket, továbbra is kaphatnak töredékeket a kiszolgálóról, ahelyett, hogy HTTP 404-es vagy 412-es hibát kapnának.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Több szabály egyesítése egyetlen szűrőben
Több szűrési szabályt is kombinálhat egyetlen szűrőben. Példaként definiálhat egy "tartományszabályt", amely eltávolítja a palákat az élő archívumból, és kiszűri a rendelkezésre álló bitrátákat. Több szűrési szabály alkalmazásakor a végeredmény az összes szabály metszéspontja.

![több szabály][multiple-rules]

## <a name="create-filters-programmatically"></a>Szűrők létrehozása programozott módon
A következő cikk a Media Services szűrőkhöz kapcsolódó entitásait ismerteti. A cikk azt is bemutatja, hogyan hozhat létre programozott szűrőket.  

[Hozzon létre szűrőket REST API-kkal.](media-services-rest-dynamic-manifest.md)

## <a name="combining-multiple-filters-filter-composition"></a>Több szűrő kombinálása (szűrőösszetétel)
Egy URL-címben több szűrőt is kombinálhat. 

A következő forgatókönyv bemutatja, hogy miért érdemes kombinálni a szűrőket:

1. Meg kell szűrni a videó tulajdonságait a mobil eszközök, mint például az Android vagy iPAD (annak érdekében, hogy korlátozza a videó tulajdonságait). A nem kívánt tulajdonságok eltávolításához hozzon létre egy globális szűrőt, amely megfelel az eszközprofiloknak. Ahogy azt a cikk korábbi része is említette, a globális szűrők minden eszközéhez használhatók ugyanazon médiaszolgáltatási fiókban, további társítás nélkül. 
2. Azt is szeretné, hogy vágja le a kezdő és záró idő egy eszköz. Ennek elérése érdekében hozzon létre egy helyi szűrőt, és állítsa be a kezdési/befejezési időt. 
3. Mindkét szűrőt kombinálni szeretné (kombináció nélkül minőségi szűrést kell hozzáadnia a vágási szűrőhöz, ami megnehezíti a szűrőhasználatot).

A szűrők kombinálásához pontosvesszővel tagolt jegyzék-/lista URL-címére kell beállítania a szűrőneveket. Tegyük fel, hogy van egy *MyMobileDevice nevű szűrője,* amely szűri a tulajdonságokat, és van egy másik nevű *MyStartTime* egy adott kezdési időpont beállításához. Kombinálhatja őket, mint ez:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Legfeljebb három szűrőt egyesíthet. 

További információ: [ebbe a](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogba.

## <a name="know-issues-and-limitations"></a>Ismerje meg a problémákat és korlátozásokat
* A dinamikus jegyzékfájl gop-határokban (kulcskeretekben) működik, így a vágás gop pontossággal rendelkezik. 
* A helyi és globális szűrőkhöz ugyanazt a szűrőnevet használhatja. A helyi szűrők elsőbbséget élveznek, és felülírják a globális szűrőket.
* Ha frissíti a szűrőt, akár 2 percet is igénybe vehet, amíg a streamelési végpont frissíti a szabályokat. Ha a tartalmat bizonyos szűrőkkel szolgálták ki (proxykban és CDN-gyorsítótárakban gyorsítótárazva), a szűrők frissítése a lejátszó meghibásodását eredményezheti. Javasoljuk, hogy törölje a gyorsítótárat a szűrő frissítése után. Ha ez a beállítás nem lehetséges, fontolja meg egy másik szűrő használatát.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Tartalom kézbesítése az ügyfeleknek – áttekintés](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
