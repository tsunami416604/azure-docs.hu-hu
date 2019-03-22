---
title: Szűrők és dinamikus jegyzékek |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek archiválása a szelektív streamelési hoz létre.
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
ms.author: cenkd;juliako
ms.openlocfilehash: 229f89a8803f089c24981f56e00e36efe96de3aa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316593"
---
# <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [2-es verzió](media-services-dynamic-manifest-overview.md)
> * [3-as verzió](../latest/filters-dynamic-manifest-overview.md)

2.17 kiadástól kezdve, a Media Services lehetővé teszi az eszközök szűrőket határozhat meg. Ezeket a szűrőket, amelyek lehetővé teszik az ügyfelek úgy dönteni, hogy többek között a kiszolgálóoldali szabályok: lejátszani egy videót (a teljes videó lejátszása) helyett csak egy részét, vagy adja meg, hogy az ügyfél eszköz képes-e kezelni (hang- és beállításkészletben csak egy részhalmazát összes verzió helyett, amelyek az eszköz társítva). Ez a szűrés az eszközök a gazdafájlon keresztül **dinamikus Manifest**, amelyek létrejönnek a videó továbbításához a felhasználói kérésre megadott szűrő(k) alapján.

Ez a témakör ismerteti a gyakori forgatókönyvek, amelyben szűrők használatával lehet előnyös, ha az ügyfelek, mutató hivatkozásokat talál, amelyek bemutatják, hogyan hozhat létre programozott módon a szűrőket.

## <a name="overview"></a>Áttekintés
Az ügyfelek számára (élő eseményeket vagy igény szerinti videó streaming) tartalomtovábbításkor a cél, egy jó minőségű videót biztosításához a különböző eszközökre, különböző hálózati körülmények között. A cél tegye a következők eléréséhez:

* a stream többszörös átviteli sebességű kódolás ([adaptív sávszélességű](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) video-adatfolyamot (Ez gondoskodik a minőségi és hálózati feltételek), és 
* használja a Media Services [dinamikus csomagolási](media-services-dynamic-packaging-overview.md) dinamikusan csomagolni a stream a különböző protokollok (Ez gondoskodik a különböző eszközökön streaming). Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH. 

### <a name="manifest-files"></a>Jegyzékfájlok
Ha Ön kódolása az adaptív bitsebességű folyamatos átvitel, egy **manifest** (lista) fájl jön létre (a fájl a szöveg- vagy XML-alapú). A **manifest** fájl tartalmaz metaadatokat például streamelési: nyomon követheti a típusa (hang, videó vagy szöveg), nyomon követheti a neve, kezdési és befejezési idő, sávszélességű (Tulajdonságok), nyomon követése nyelvek, bemutató ablak (csúszóablakban rögzített időtartama), videó kodek () FourCC). A arra utasítja a Windows Media player beolvasni a következő részlet azáltal, hogy a következő lejátszható videó szilánkok érhető el, és azok helyétől kapcsolatos információkat is. Szilánk (vagy szegmensek) olyan a tényleges "" a videó tartalmát.

Íme egy példa a jegyzékfájl: 

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
Nincsenek [forgatókönyvek](media-services-dynamic-manifest-overview.md#scenarios) Ha később az ügyfél az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot igények. Példa:

* Adott eszköz: csak a megadott beállításkészletben és/vagy a megadott nyelv nyomon követi az eszköz, amellyel a tartalmat ("megjelenítésszűrés") által támogatott kézbesítéséhez. 
* Csökkentse a jegyzékfájlt ("aljelentés klip szűrés") élő esemény alárendelt klip megjelenítéséhez.
* Trim ("vágás egy videó") egy videó kezdete.
* Módosítsa a megjelenítési ablakot (DVR) annak érdekében, hogy a Windows Media player ("módosításának bemutató időszak") megadott DVR időszak korlátozott hosszát.

Ezt a rugalmasságot érhet el, a Media Services által **dinamikus jegyzékfájlok** alapú az előre meghatározott [szűrők](media-services-dynamic-manifest-overview.md#filters).  A szűrők határozza meg, miután a az ügyfelek használhatja őket egy adott megjelenítés a klipek alárendelt a videó továbbításához. A streamelési URL-CÍMBEN szereplő azokat megadni szűrő(k) alapján. Sikerült alkalmazni az adaptív sávszélességű streamelési protokollok által támogatott szűrők [dinamikus csomagolási](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH és Smooth Streaming. Példa:

MPEG DASH URL-cím szűrővel

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming URL-cím szűrővel

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


A tartalom és a streamelési URL-címek build kapcsolatos további információkért lásd: [tartalomtovábbítás áttekintése](media-services-deliver-content-overview.md).

> [!NOTE]
> Vegye figyelembe, hogy a dinamikus jegyzékfájlok ne módosítsa az eszköz és az eszköz alapértelmezett jegyzék. Az ügyfél kérése egy streamet, vagy a szűrők nélkül választhatja. 
> 
> 

### <a id="filters"></a>Szűrők
Az eszközintelligencia szűrők két típusa van: 

* Globális szűrők (az Azure Media Services-fiókban lévő bármely eszközre is alkalmazható, a fiók élettartama) és a 
* Helyi szűrők (csak egy eszköz, amellyel a szűrő lett társítva létrehozásakor kell alkalmazni, az eszköz élettartama). 

A globális és helyi szűrőtípusok pontosan azonos tulajdonságokkal rendelkezik. A fő különbség a kettő között, mely forgatókönyvek milyen típusú egy filer megfelelő. Globális szűrők alkalmasak általában eszközprofilok (megjelenítésszűrés), a helyi szűrők és a záró szóközöket egy adott eszköz használható.

## <a id="scenarios"></a>Gyakori forgatókönyvek
Mint említette, mielőtt az ügyfelek számára (élő eseményeket vagy igény szerinti videó streaming) tartalomtovábbításkor célja az, hogy egy kiváló minőségű videó továbbítása különböző eszközökre, különböző hálózati körülmények között. Emellett előfordulhat, hogy rendelkezik egyéb követelmények szűrése az eszközök és a használatával **dinamikus Manifest**s. A következő szakaszok lehetővé teszik a különböző szűrési forgatókönyv rövid áttekintése.

* Adja meg az audio- és verzió, amely bizonyos eszközöket (és nem az összes a beállításkészletben az eszközhöz társított) képes kezelni egy részén alapulhatnak. 
* Csak egy részét (a teljes videó lejátszása) helyett egy videó lejátszásának.
* Módosítsa a DVR megjelenítési ablakot.

## <a name="rendition-filtering"></a>Megjelenítés szűrése
Előfordulhat, hogy az eszköz több kódolási profilt (H.264 alapterv, H.264 magas, AACL, AACH, Dolby digitális Plus) és több minőségi bitsebességre való átkódolása kódolása választja. Azonban nem minden ügyfél eszköz lesz a támogatja, az objektumhoz tartozó összes profilok és bitsebességre való átkódolása. Régebbi Android-eszközök például csak H.264 alapkonfiguráció + AACL támogatják. Sávszélesség- és eszköz számítási magasabb bitsebességre való átkódolása küld egy eszköz, amely nem olvasható be az előnyöket, hulladékok. Az ilyen eszközt kell dekódolja az összes megadott információt, csak a vertikális leskálázás való megjelenítéshez.

A dinamikus Manifest eszközprofilok hozhat létre például mobil-, HD/SD, stb-konzol és a nyomon követi és minőségű, amit érdemes figyelembe venni az egyes profilok.

![Megjelenítés szűrési példát][renditions2]

A következő példában egy kódoló egy mezzanine eszköz kódolandó hét ISO MP4-fájlnak videó beállításkészletben (a 180p a 1080p) lett megadva. A kódolt objektumhoz dinamikusan csomagolható be a következő adatfolyam-továbbítási protokollok bármelyikét: HLS, Smooth, és MPEG DASH.  A diagram tetején jelenik meg a HLS-jegyzékfájl a szűrők az eszköz (tartalmaz minden hét beállításkészletben).  A bal alsó "ott" nevű szűrőt alkalmaztak, amelyhez a HLS-jegyzékfájl jelenik meg. A "ott" szűrő meghatározza, hogy távolítsa el az összes bitsebességre való átkódolása alább 1 MB/s, amely eredményezett az alsó két minőségi szint alatt levágja, a válaszban. Az alul a jobb oldalon a HLS-jegyzékfájlt, amelyhez "mobileszköz" nevű szűrőt alkalmaztak jelenik meg. A "mobileszköz" szűrő Megadja, hogy távolítsa el a beállításkészletben, ahol a megoldás nem haladja meg a két eredményezett 720p 1080p beállításkészletben levágja, folyamatban van.

![Megjelenítés szűrése][renditions1]

## <a name="removing-language-tracks"></a>Nyelv eltávolítása sávok
Az eszközök lehetnek több hang nyelvet, például az angol, spanyol, francia, stb. Általában a lejátszó SDK kezelők alapértelmezett hangsávra kiválasztása, és elérhető hang követi nyomon egy felhasználó által választott. Az ilyen lejátszó SDK-k fejlesztéséhez kihívást jelenthet, implementálható különböző eszközspecifikus player-keretrendszerek van szükség. Is az egyes platformokon Player API-k korlátozva, és nem tartalmaznak arról, hogy a felhasználók hol nem válassza ki és módosítsa az alapértelmezett hangsávra hang kijelölés funkció. Eszköz szűrőket szabályozhatja a viselkedés hang kívánt nyelveket tartalmazó szűrők létrehozásával.

![Nyelvi nyomon követi a szűréshez][language_filter]

## <a name="trimming-start-of-an-asset"></a>Egy eszköz levágási kezdete
A legtöbb élő események streamelése operátorok néhány tesztet, a tényleges esemény előtt futtassa. Ezek lehetnek például egy befutó hasonlóan ez az esemény kezdete előtt: "Program megkezdődik rövid ideig". Ha a program az archiválás, a tesztelés és állóképek adatokat is archivált és benne a bemutatót. Azonban ezt az információt nem kell feltüntetni az ügyfelek számára. A dinamikus jegyzékfájl létrehozásához egy kezdési idejének szűrője, és a nemkívánatos adatok eltávolítása a jegyzékfájlban.

![Tisztítás kezdő][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Az élő archívumot subclips (nézetek) létrehozása
Sokáig futó számos élő események és élő archívumot tartalmazhat több esemény. Az élő esemény befejezése után műsorszolgáltatók érdemes részekre az élő archívumot logikai programot, és állítsa le a feladatütemezések. Ezután a virtuális programok külön-külön nem közzé bejegyzés az élő archívumot feldolgozását, és nem hoz létre a különböző eszközöket (amely nem kap a CDN az az előnye, hogy a meglévő gyorsítótárazott szilánkok). Ilyen virtuális programok a következők: a negyedévek egy labdarúgó vagy Kosárlabda játékot, a baseball innings vagy egyéni események minden sport-program.

A dinamikus Manifest hozhat létre szűrők használatával kezdési/befejezési idején és virtuális nézeteket hozhat létre az élő archívumot tetején keresztül. 

![Részklip szűrő][subclip_filter]

Szűrt eszköz:

![Síelés][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Bemutató ablak (DVR) beállítása
Jelenleg az Azure Media Services. kör alakú archív, ahol konfigurálható az időtartam, 5 perc között – 25 óra kínál. Jegyzékfájl szűrés működés közbeni DVR ablakának létrehozása az archívum felső keresztül media törlése nélkül is használható. Ha sikeresen szeretne biztosítani egy korlátozott DVR-ablak áthelyezése élő oldala, és a egy időben megtartandó egy nagyobb méretű archiválási ablak számos forgatókönyv közül választhat. A szórást küldő számítógép lehet, hogy szeretné használni az adatokat, a DVR ablakban jelölje ki a legjobb pillanatokból összeválogatott kívül esik, vagy he\she érdemes másik DVR windows biztosít különböző eszközökön. Például a mobileszközök a legtöbb kezelik a nagy DVR windows (is rendelkezik egy 2 perces DVR ablak mobileszközökhöz és a egy órás asztali ügyfelek esetén).

![DVR ablak][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (élő pozíciótól) beállítása
Jegyzékfájl szűrés használható néhány másodpercig eltávolítása egy élő programot élő szélétől. Szűrés lehetővé teszi, hogy sikeresen, tekintse meg a bemutatót az előzetes verzió kiadvány ponton és a hirdetmény beszúrási pontok létrehozása előtt a megtekintők az adatfolyamhoz (biztonsági-ki által 30 másodperc). Ezután leküldése a hirdetmények az ügyfél-keretrendszereket időt a fogadott és feldolgozni az információkat a hirdetés lehetőség előtt sikeresen.

A hirdetés támogatás mellett a LiveBackoff beállítás segítségével a megtekintők pozíció beállítása, hogy amikor az ügyfelek konfigurációsodródás, és nyomja le az élő edge továbbra is hozzáférhetnek a töredékek helyett HTTP 404-es vagy 412 hiba-kiszolgálóról.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Egyetlen szűrő szabályokban több kombinálása
Egyetlen szűrő több szűrési szabály kombinálhatók. Például megadhat egy "tartomány szabály" befutók eltávolítása egy élő archívumot, és elérhető bitsebességre való átkódolása ki is szűrheti. Több szűrési szabályok alkalmazásakor a végeredmény az összes szabály metszet.

![több-szabályok][multiple-rules]

## <a name="create-filters-programmatically"></a>Szűrők létrehozása programozott módon
A következő cikkből szűrők kapcsolódó entitások Media Services. A cikk azt is bemutatja, hogyan hozhat létre programozott szűrőket.  

[Szűrők létrehozása REST API-k](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinált szűrők (szűrő összeállítás)
Több szűrő, egy egyetlen URL-címben is kombinálhatók. 

Az alábbi forgatókönyvet mutat be, hogy miért érdemes a szűrők kombinálásával:

1. A videó tulajdonságait, például Android- vagy iPAD eszközökhöz szűrése (ahhoz, hogy korlátozza a videó minőségű) kell. Szeretné eltávolítani a nem kívánt tulajdonságok, akkor kell létrehoznia egy globális szűrő az eszközprofilok alkalmas. A cikkben korábban említett globális szűrők a társítást minden további nélkül azonos media services fiók alatt az összes eszköz használható. 
2. Szeretné is, és a egy eszköz a kezdő és záró idő záró szóközöket. Ennek érdekében lenne helyi szűrő létrehozása, és a kezdő és záró idő beállítása. 
3. Mindkét ezeket a szűrőket összevonására (nélkül kombináció, hozzá kell minőségi szűrése az levágási szűrőt, amely megnehezíti szűrő használati).

A szűrők kombinálásával, állítsa be a szűrő nevét a jegyzékfájl listára kell pontosvesszővel elválasztott URL-CÍMÉT. Tegyük fel, hogy egy szűrő nevű *MyMobileDevice* , amely szűri tulajdonságait, és van egy másik nevű *MyStartTime* beállítása egy adott kezdési idő. Ehhez hasonló kombinálhatja őket:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Legfeljebb három szűrők kombinálásával. 

További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Ismert problémák és korlátozások
* Dinamikus jegyzékfájl működik, Képcsoporttal határok (kulcs keretek), ezért vágást rendelkezik Képcsoporttal pontosságát. 
* Használhatja ugyanazt a helyi és globális szűrők szűrő nevet. Helyi szűrők rendelkezik nagyobb prioritással, és felülírják a globális szűrőket.
* Frissít egy szűrőt, ha a streamvégpont frissítéséhez a szabályok akár 2 percet vesz igénybe. Ha a tartalom kiszolgálása egyes szűrők használatával (és a proxyk és a CDN gyorsítótárazza a gyorsítótárak), player hibák frissítése ezeket a szűrőket eredményezhet. Javasoljuk, hogy a gyorsítótár ürítése a szűrő frissítése után. Ha ezt a beállítást nem lehetséges, fontolja meg egy másik szűrővel.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Tartalom továbbítása az ügyfelek – áttekintés](media-services-deliver-content-overview.md)

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
