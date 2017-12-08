---
title: "Szűrők és dinamikus jegyzékfájlokban |} Microsoft Docs"
description: "Ez a témakör ismerteti, az ügyfél használhassa őket adott szakaszaival adatfolyam adatfolyam-szűrők létrehozásához. A Media Services dinamikus jegyzékfájlokat archiválására a szelektív streaming hoz létre."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 4034fd0aa64627c107a43208dcca766f7f44d5d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékfájlokban
2.11 kiadástól kezdve a Media Services lehetővé teszi az eszközök szűrőit. Ezek a szűrők, amelyek lehetővé teszik az ügyfelek akkor megteheti, többek között a kiszolgáló oldalán szabályok: lejátszás videó (és nem a teljes videó lejátszása) részt vagy a hang- és interpretációk, amelyet a felhasználói eszköz kezelni tud (és nem minden a interpretációk társított adategységet) csak egy részét. Ez a szűrés a eszközök archivált keresztül **dinamikus Manifest**khoz, az ügyfél kérésre videó adatfolyam jönnek létre a megadott szűrő alapján.

A témakörök ismerteti a gyakori forgatókönyvek, amelyben szűrők segítségével nagyon hasznos a felhasználók és a hivatkozások témakörökre mutatnak, amelyek bemutatják, hogyan lehet programozott módon-szűrők létrehozásához lenne (jelenleg hozhatja létre szűrők REST API-kat csak).

## <a name="overview"></a>Áttekintés
Ha az ügyfelek (streaming élő eseményeket vagy video-on-demand) történő célja, hogy a videók kiváló minőségben biztosításához a különböző hálózati körülmények különböző eszközökre. Eléréséhez a cél tegye a következőket:

* a többszörös sávszélességű adatfolyamot kódolása ([adaptív sávszélességű](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) video-adatfolyamot (ez kezeli minőségi és hálózati körülményekhez) és 
* használja a Media Services [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md) dinamikusan őket csomagolni az adatfolyam különböző protokollok (ez kezeli a különböző eszközökön streaming). A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming vagy MPEG DASH. 

### <a name="manifest-files"></a>Fájlok
Ha Ön kódolása egy adaptív sávszélességű streamelés esetén az eszköz egy **manifest** (lista) fájl jön létre (a fájl szöveges vagy XML-alapú). A **manifest** fájl tartalmazza, például a streaming metaadatok: típusa (hang, videó vagy), akkor a nevét, a kezdő és záró idő, a sávszélességű (Tulajdonságok), a nyomon követése nyelven, nyomon bemutató ablakban (csúszóablak rögzített időtartama), a videó kodek (FourCC). A arra utasítja a Windows Media player beolvasása a következő töredéke a következő lejátszható videó töredék elérhető és a helyére vonatkozó információk is. Töredék (vagy szegmensek) a tényleges "adattömbök" videó tartalom.

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

### <a name="dynamic-manifests"></a>Dinamikus jegyzékfájlokban
Nincsenek [forgatókönyvek](media-services-dynamic-manifest-overview.md#scenarios) mikor az ügyfél kell rugalmasabb, mint az alapértelmezett eszköz jegyzékfájl leírtakhoz. Példa:

* Adott eszköz: csak a megadott interpretációk és/vagy rendszerkötet, amely az eszköz által támogatott nyelvi számok megadott fájlmegosztásba lejátszásához használt a tartalom ("verzióinak szűrése"). 
* Csökkentse a jegyzékfájl ("altípusa klip szűrése") egy élő esemény alárendelt klip megjelenítéséhez.
* Trim ("díszítésre videó") videó elindítása.
* Módosítsa a megjelenítési ablakot (DVR) ahhoz, hogy a Windows Media Player ("beállító bemutató ablak") a DVR ablak korlátozott hosszát adja meg.

Erre a rugalmasságra eléréséhez a Media Services kínál **dinamikus jelentkezik** alapú az előre meghatározott [szűrők](media-services-dynamic-manifest-overview.md#filters).  Szűrőinek megadása után az ügyfelek használhatják őket egy adott verzióinak vagy alárendelt videóklipeket a videó. Az adatfolyam-továbbítási URL-cím közül néhányat kizárjanak volna adnia. Szűrők alkalmazható az adaptív sávszélességű streamelési által támogatott protokollok [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH vagy Smooth Streaming. Példa:

A szűrővel MPEG DASH URL-címe

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Smooth Streaming URL-cím elé szűrő

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


A tartalom és a streamelési URL-címek build kapcsolatos további információkért lásd: [tartalom áttekintése kézbesítéséhez](media-services-deliver-content-overview.md).

> [!NOTE]
> Vegye figyelembe, hogy dinamikus jelentkezik, ne változtassa meg az eszköz és az alapértelmezett jegyzéket az adott eszköz számára. Az ügyfél vagy a szűrők nélkül adatfolyam kérelem választhat. 
> 
> 

### <a id="filters"></a>Szűrők
Az eszköz szűrők két típusa van: 

* Globális szűrők (az Azure Media Services-fiók bármely eszközre alkalmazhatók, élettartama pedig a fiók) és 
* Helyi szűrők (csak egy eszköz, amellyel a szűrő lett rendelve a létrehozása után kell alkalmazni, az eszköz élettartamáról). 

A globális és helyi szűrőtípusok pontosan azonos tulajdonságokkal rendelkezik. A fő különbség a kettő között mely forgatókönyvek milyen típusú egy fájlkiszolgáló megfelelő. Globális szűrők alkalmasak általában (verzióinak szűrés) eszközprofilok ahol helyi szűrők lehet levágni a egy adott eszköz használható.

## <a id="scenarios"></a>Gyakori helyzetek
Ahogyan volt előtt, amikor az ügyfelek (streaming élő eseményeket vagy video-on-demand) történő célja, hogy a videók kiváló minőségben biztosításához a különböző hálózati körülmények különböző eszközökre. Ezenkívül előfordulhat, hogy a szűrés az objektumok és a használatával további követelményekkel rendelkezik **dinamikus Manifest**s. A következő szakaszok biztosítják a különböző szűrési forgatókönyvek rövid áttekintést.

* Adja meg a hang- és interpretációk bizonyos eszközök kezelhető (és nem minden a interpretációk társított adategységet) csak egy részét. 
* Lejátszás videó (és nem a teljes videó lejátszása) részt.
* Állítsa be úgy a DVR megjelenítési ablakot.

## <a name="rendition-filtering"></a>Verzióinak szűrése
Választhatja az eszköz több kódolási profilok (H.264 alapterv, H.264 magas, AACL, AACH, Dolby digitális Plus), és több minőségi bitrates kódolása. Azonban nem minden ügyfél eszközök támogatják az eszköz profilok és bitrates. Például a régebbi Android-eszközök csak H.264 alapterv + AACL támogatja. Magasabb bitrates küld egy eszköz, amely nem olvasható be a következő előnyöket, sávszélesség és az eszköz számítási hulladékok. Ilyen eszköz kell dekódolása összes megadott információ csak a megjelenítésre csökkentheti.

A dinamikus Manifest eszközprofilok hozhat létre mobile, például a konzolon, HD/SD stb., és nyomon követi és tulajdonságait, amelybe az egyes profilok egy részét.

![Szűrési verzióinak – példa][renditions2]

A következő példában egy kódoló használt kódolja a mezzanine eszköz a hét ISO MP4 videó interpretációk (a 1080p 180p). A kódolt objektumhoz is dinamikusan csomagolja a rendszer a következő adatfolyam-továbbítási protokollok: MPEG DASH, HLS és zökkenőmentes.  A diagram tetején jelenik meg az eszköz nincs szűrőkkel HLS jegyzékfájljának (tartalmaz minden hét interpretációk).  A bal alsó jelenik meg, amelyhez "ott" nevű szűrő lett alkalmazva a HLS-jegyzékfájlt. A "ott" szűrő meghatározza, hogy távolítsa el az összes bitrates 1 MB/s, az alsó két szolgáltatásminőségi szinteket alatt levágja, a válaszban szereplő eredményező alatt.  A jobb alsó jelenik meg a HLS-jegyzékfájlt, amelyhez nevű, "mobilalkalmazás" szűrő lett alkalmazva. A "mobileszköz" szűrő határozza meg, ahol. a megoldás lehet nagyobb, mint ami a két 720p interpretációk eltávolítása folyamatban levágja 1080p interpretációk.

![Verzióinak szűrése][renditions1]

## <a name="removing-language-tracks"></a>Nyelv eltávolítása számok
Az eszközök közé tartozik a több hang nyelv, például az angol, spanyol, francia, stb. Általában a Player SDK kezelők alapértelmezett hang statisztikák nyomon követésének kiválasztása, és elérhető hang nyomon követi az egyes felhasználó kiválasztása. Ilyen Player SDK-k fejlesztését kihívást, az eszközspecifikus player-keretrendszerek között különböző megvalósítások igényel. Is az egyes platformokon Player API-k korlátozva, és nem tartalmazzák a hang kijelölés szolgáltatás arról, hogy a felhasználók hol nem válassza ki és módosítsa az alapértelmezett hang követése. Eszköz szűrőkkel, amelyek csak tartalmazzák a kívánt hang nyelvek szűrők létrehozásával befolyásolhatja a viselkedését.

![Szűrés nyelvi számok][language_filter]

## <a name="trimming-start-of-an-asset"></a>Egy eszköz tisztítás kezdő
A legtöbb események élő adatfolyamainak továbbítása operátorok néhány teszt a tényleges esemény előtt futtassa. Előfordulhat, hogy például tartalmaznak egy lappal például ez az esemény a megkezdése előtt: "Program akkor kezdődik, rövid ideig gombra. Ha a program archiválás van, a vizsgálati és a lappal adatokat is archivált, és fog szerepelni a bemutató. Azonban ezeket az információkat kell nem jeleníthető meg az ügyfelek számára. A dinamikus Manifest kezdési idő szűrő létrehozása, és a nemkívánatos adatok eltávolítása a jegyzékfájlban.

![Tisztítás kezdő][trim_filter]

## <a name="creating-sub-clips-views-from-a-live-archive"></a>Élő archívumból alárendelt videóklipeket (nézetek) létrehozása
Számos élő esemény hosszú ideig futniuk és élő archív állhatnak több esemény. Az élő esemény után végpontok műsorszolgáltatók érdemes szakítsa meg az élő archívum be logikai programot, és állítsa le a feladatütemezések. Ezt követően a virtuális programok külön-külön nem közzé post az élő archívum feldolgozásához, és nem hozza létre külön az eszközöket, (amelyek nem kap a tartalomtovábbító a előnye, hogy a meglévő gyorsítótárazott töredék). Ilyen virtuális programok (alárendelt videóklipeket) a következők: a negyedévek egy bemutatjuk vagy Kosárlabda játék, a baseball innings vagy egy délután olimpia program az egyes események.

A dinamikus Manifest szűrőkkel kezdő és záró időpont használatával, és virtuális nézeteket hozhat létre az élő archívum felső keresztül. 

![Szűrő subclip][subclip_filter]

Szűrt eszköz:

![Terveztek][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Megjelenítési ablakot (DVR) beállítása
Azure Media Services jelenleg, ahol konfigurálható az időtartam, 5 perc között - 25 óra körkörös archív kínál. Manifest szűrés működés közbeni DVR ablakának létrehozása az archívum felső keresztül media törlése nélkül használható. Nincsenek számos forgatókönyv, ahol műsorszolgáltatók szeretne biztosítani egy korlátozott DVR-ablakot, mely helyezi át, az élő oldala, és egyszerre tartsa egy nagyobb archiválási ablakot. A szórást küldő számítógép érdemes használnia az adatokat, a DVR ablakban jelölje ki a videóklipeket kívül esik, vagy he\she is szeretne biztosítani a különböző DVR windows különböző eszközökhöz. Például a mobil eszközeinek (rendelkezhet egy 2 perces DVR ablakot a mobileszközökhöz és 1 óra asztali ügyfelek) nagy DVR windows kezelik.

![DVR ablak][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (élő pozíciótól) beállítása
Manifest szűrés segítségével néhány másodpercig eltávolítása egy élő program élő szélétől. Ez lehetővé teszi a műsorszolgáltatók tekintse meg a bemutató a preview kiadvány ponton és a hirdetmény beszúrási pontok létrehozása előtt a felhasználóknak az adatfolyamhoz (általában a biztonsági indító által 30 másodperc). Műsorszolgáltatók ezután leküldése a hirdetmények az ügyfél keretrendszerek, hogy időben fogadott és feldolgozni az információkat a hirdetés lehetőség előtt.

A hirdetés támogatás mellett LiveBackoff ügyfél élő letöltési pozíció hangolását, hogy amikor az ügyfelek eltolódás, majd nyomja le az élő él továbbra is kaphatnak töredék kiszolgálóról 412 vagy 404-es HTTP-hibák helyett használható.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinálásával egyetlen szűrőben több szabály
Kombinálásával egyetlen szűrőben több szűrési szabályok. Példa egy tartomány szabály lappal eltávolítása egy élő archiválás és szűréssel is elérhető bitrates adhat meg. Több szűrési szabály end eredménye (csak metszetének) összetételének ezeket a szabályokat.

![több-szabályok][multiple-rules]

## <a name="create-filters-programmatically"></a>Hozzon létre szoftveres szűrők
A következő témakör ismerteti a Media Services entitások szűrők kapcsolódó. A témakör azt is bemutatja, hogyan szűrők programozott módon létrehozásához.  

[Hozzon létre szűrők REST API-k](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinált szűrők (szűrő összeállítás)
Az egy URL-cím több szűrő kombinálhatja is. 

Az alábbi forgatókönyvet mutatja be, hogy miért érdemes kombinálását szűrők:

1. Meg kell a mobil eszközök, Android vagy iPAD például video minőségű (videó minőségű korlátozásához). A nem kívánt minőségű eltávolításához egy globális szűrőt, amely megfelelő eszközprofilok hozna létre. Fent említett globális szűrők a azonos media services-fiók nélkül további társítás az összes eszköz használható. 
2. Érdemes lehet levágni a kezdő és záró idő az eszköz. Ennek érdekében ehhez hozzon létre egy helyi szűrőt, és a kezdő és záró idő beállítása. 
3. Ezek a szűrők (nélkül minőségi szűrés hozzáadása a tisztítás szűrő, amely szűrő használati nehéz kell kombinációja) kombinálni szeretné.

A szűrő nevét a jegyzék lista be kell szűrők egyesítéséhez URL-cím elé pontosvesszővel tagolva. Tegyük fel nevű szűrést *MyMobileDevice* minőségű szűrők, és van egy másik nevű *MyStartTime* beállítani egy adott kezdési időpontja. Ehhez hasonló egyesítheti őket:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

3 szűrők kombinálhatja. 

További információ: [ez](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Tudja, problémák és korlátozások
* Dinamikus jegyzékfájl működik GOP határok (kulcs keretek), ezért díszítésre rendelkezik GOP pontosságát. 
* Használhatja a helyi és globális szűrők azonos szűrő nevét. Vegye figyelembe, hogy helyi szűrő magasabb prioritással rendelkezik, és felülírja a globális szűrők.
* Ha frissíti a szűrőt, legfeljebb 2 percet, hogy frissíti a szabályok streamvégpont is igénybe vehet. Ha a tartalom állítása és kiszolgálása között egyes szűrők használatával (és proxyk és a CDN a gyorsítótárba helyezett gyorsítótárak), ezek a szűrők frissítése okozhat player sikertelen. A gyorsítótár kiürítése után a szűrő frissítése érdekében ajánlott. Ha ezt a beállítást nem lehetséges, érdemes lehet egy másik szűrőt.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Kézbesíteni a tartalmakat a felhasználók áttekintése](media-services-deliver-content-overview.md)

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
