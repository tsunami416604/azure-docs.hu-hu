---
title: A jegyzékek szűrése a Dinamikus csomagoló használatával
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan hozhat létre szűrőket a Dinamikus packager használatával a jegyzékek szűréséhez és szelektív streameléséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186212"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>A jegyzékek szűrése a Dinamikus csomagoló használatával

Amikor adaptív sávszélességű streamelési tartalmat jelenít meg az eszközökre, néha közzé kell tennie a jegyzékfájl több verzióját, hogy meghatározott eszközképességeket vagy rendelkezésre álló hálózati sávszélességet célozzon meg. A [Dinamikus csomagoló](dynamic-packaging-overview.md) lehetővé teszi olyan szűrők megadását, amelyek kiszűrhetik az egyes kodekeket, felbontásokat, bitrátákat és hangsáv-kombinációkat menet közben. Ez a szűrés szükségtelennek teszi több példány létrehozását. Egyszerűen közzé kell tennie egy új URL-t a céleszközökre (iOS, Android, SmartTV vagy böngészők) konfigurált szűrők egy adott készletével, valamint a hálózati funkciókkal (nagy sávszélességű, mobilvagy alacsony sávszélességű forgatókönyvekkel). Ebben az esetben az ügyfelek módosíthatják a tartalom streamelését a lekérdezési karakterláncon keresztül (az elérhető [eszközszűrők vagy fiókszűrők](filters-concept.md)megadásával), és szűrőkhasználatával streamelhetik az adatfolyam adott szakaszait.

Egyes kézbesítési forgatókönyvek megkövetelik, hogy győződjön meg arról, hogy az ügyfél nem fér hozzá bizonyos pályák. Előfordulhat például, hogy nem szeretne közzétenni egy olyan jegyzékfájlt, amely HD-sávokat tartalmaz egy adott előfizetői rétegben. Vagy előfordulhat, hogy el szeretne távolítani egy adott adaptív sávszélességű (ABR) sávot, hogy csökkentse egy adott eszközre történő kézbesítés költségét, amely nem profitálna a további pályákból. Ebben az esetben az előre létrehozott szűrők listáját társíthatja a [streamelési lokátorhoz](streaming-locators-concept.md) a létrehozáskor. Az ügyfelek ezután nem tudják manipulálni a tartalom streamelésének módját, mert azt a **streamelési lokátor**határozza meg.

A szűrés kombinálásával szűrőket adhat meg [a streamelési lokátoron](filters-concept.md#associating-filters-with-streaming-locator) + az ügyfél által az URL-ben megadott további eszközspecifikus szűrőket. Ez a kombináció további zeneszámok, például metaadatok vagy eseményfolyamok, hangnyelvek vagy leíró hangsávok korlátozására használható.

Ez a képesség, hogy különböző szűrőket adjon meg a streamen, hatékony **dinamikus jegyzékkezelő** megoldást biztosít a céleszközök több használati esetének célzásához. Ez a témakör ismerteti a **dinamikus jegyzékfájlokkal kapcsolatos fogalmakat,** és példákat ad olyan forgatókönyvekre, amelyekben használhatja ezt a funkciót.

> [!NOTE]
> A dinamikus jegyzékek nem módosítják az eszközt és az eszköz alapértelmezett jegyzékfájlját.

## <a name="overview-of-manifests"></a>A jegyzékek áttekintése

Az Azure Media Services támogatja a HLS, Az MPEG DASH és a Smooth Streaming protokollokat. A [dinamikus csomagolás](dynamic-packaging-overview.md)részeként a streamelési ügyfél jegyzékfájljai (HLS-fő lista, DASH-médiabemutató leírása [MPD] és sima streamelés) dinamikusan jönnek létre az URL-cím formátumválasztója alapján. További információt a Közös igény szerinti munkafolyamat kézbesítési protokolljai című témakörben [talál.](dynamic-packaging-overview.md#delivery-protocols)

### <a name="get-and-examine-manifest-files"></a>Jegyzékfájl beésése és vizsgálata

Megadhatja a szűrőpálya tulajdonságfeltételeinek listáját, amely alapján a stream (élő vagy videó on-demand [VOD]) sávjait kell szerepeltetni egy dinamikusan létrehozott jegyzékfájlban. A számok tulajdonságainak leéséhez és vizsgálatához először be kell töltenie a smooth streaming jegyzéket.

A [.NET oktatóanyaggal rendelkező fájlok feltöltése, kódolása és streamelése megmutatja,](stream-files-tutorial-with-api.md#get-streaming-urls) hogyan hozhat létre a streamelési URL-címeket a .NET segítségével. Ha futtatja az alkalmazást, az egyik URL-cím a `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`Sima streamelési jegyzékre mutat: .<br/> Másolja és illessze be az URL-címet a böngésző címsorába. A fájl letöltődik. Bármelyik szövegszerkesztőben megnyithatja.

A REST példa: [Fájlok feltöltése, kódolása és streamelése REST-tel című témakörben.](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Videoadatfolyam bitsebességének figyelése

Az Azure [Media Player bemutatólapjával](https://aka.ms/azuremediaplayer) figyelheti a videóstream bitrátáját. A bemutató oldal diagnosztikai információkat jelenít meg a **Diagnosztika** lapon:

![Az Azure Media Player diagnosztikája][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Példák: Szűrőkkel rendelkező URL-címek a lekérdezési karakterláncban

Szűrőket alkalmazhat az ABR streamelési protokollokra: HLS, MPEG-DASH és Smooth Streaming. Az alábbi táblázat néhány példát mutat be a szűrőkkel ellátott URL-címekre:

|Protocol (Protokoll)|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Interpretáció szűrése

Választhat, hogy az eszközt több kódolási profilba (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) és több minőségi bitrátába kódolja. Azonban nem minden ügyféleszköz támogatja az eszköz összes profilját és bitrátát. A régebbi Android-eszközök például csak a H.264 Baseline+AACL-t támogatják. Magasabb bitsebességek küldése olyan eszközre, amely nem tudja megszerezni az előnyöket, pazarolja a sávszélességet és az eszközszámítást. Egy ilyen eszköznek meg kell fejtenie az összes adott információt, csak a megjelenítéshez.

A dinamikus jegyzékfájl segítségével eszközprofilokat (például mobil, konzol vagy HD/SD) hozhat létre, és felveheti azokat a zeneszámokat és tulajdonságokat, amelyeket az egyes profilok részének szeretne használni. Ezt hívják kiadatásszűrésnek. Az alábbi ábra egy példát mutat be.

![Példa az interpretáció szűrésére dinamikus jegyzékfájllal][renditions2]

A következő példában egy kódoló segítségével kódolt a mezzanine eszköz hét ISO MP4s videó kiadatás (180p-től 1080p- ig). A kódolt eszköz [dinamikusan csomagolható](dynamic-packaging-overview.md) a következő streamelési protokollok bármelyikébe: HLS, MPEG DASH és Smooth.

Az alábbi ábra tetején látható a HLS-jegyzékfájl az eszköz szűrők nélkül. (Mind a hét interpretációt tartalmazza.)  A bal alsó sarokban az ábrán egy HLS-jegyzékfájl látható, amelyre egy "ott" nevű szűrőt alkalmaztak. Az "ott" szűrő az 1 Mbps alatti összes bitráta eltávolítását határozza meg, így az alsó két minőségi szint lekerült a válaszból. A jobb alsó sarokban az ábra azt a HLS-jegyzékfájlt mutatja, amelyre a "mobil" nevű szűrőt alkalmazták. A "mobil" szűrő az olyan interpretációk eltávolítását határozza meg, ahol a felbontás nagyobb, mint 720p, így a két 1080p-s interpretációt eltávolították.

![Interpretáció szűrése dinamikus jegyzékfájllal][renditions1]

## <a name="removing-language-tracks"></a>Nyelvi sávok eltávolítása
A kellékek több hangnyelvet is tartalmazhatnak, például az angolt, a spanyolt, a franciát és így tovább. A Player SDK általában kezeli az alapértelmezett hangsáv-kiválasztást és a felhasználónkénti rendelkezésre álló hangsávokat.

Az ilyen játékos SDK-k fejlesztése kihívást jelent, mivel különböző implementációkat igényel az eszközspecifikus lejátszókeretek között. Emellett egyes platformokon a Lejátszó API-k korlátozottak, és nem tartalmazzák azt a hangkijelölési funkciót, amelyben a felhasználók nem választhatják ki vagy módosíthatják az alapértelmezett hangsávot. Az eszközszűrőkkel szabályozhatja a viselkedést, ha olyan szűrőket hoz létre, amelyek csak a kívánt hangnyelveket tartalmazzák.

![Nyelvi sávok szűrése dinamikus jegyzékfájllal][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Eszköz kezdetének vágása

A legtöbb élő streamelési eseményben az operátorok néhány tesztet futtatnak a tényleges esemény előtt. Például, lehet, hogy egy pala, mint ez kezdete előtt az esemény: "Program indul egy pillanatra."

Ha a program archiválás, a teszt és pala adatok is archivált, és tartalmazza a bemutatót. Ez az információ azonban nem jelenik meg az ügyfelek számára. A dinamikus jegyzékfájl segítségével létrehozhat egy indítási időszűrőt, és eltávolíthatja a nem kívánt adatokat a jegyzékfájlból.

![Eszköz kezdőeszközének vágása dinamikus jegyzékfájllal][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Alklipek (nézetek) létrehozása élő archívumból

Sok élő esemény hosszú ideig fut, és az élő archívum több eseményt is tartalmazhat. Az élő esemény befejezése után a műsorszolgáltatók az élő archívumot logikai programindítási és -leállítási szekvenciákra szeretnék felbontani.

Ezeket a virtuális programokat külön közzéteheti az élő archívum utófeldolgozása nélkül, és nem hozhat létre külön eszközöket (amelyek nem kapják meg a cdn-ek meglévő gyorsítótárazott töredékeinek előnyeit). Ilyen virtuális programok például egy futball- vagy kosárlabdajáték negyede, a baseball ban való inningek vagy bármely sportprogram egyedi eseményei.

A dinamikus jegyzékfájl segítségével szűrőket hozhat létre a kezdési/befejezési időpontok használatával, és virtuális nézeteket hozhat létre az élő archívum tetején.

![A dinamikus jegyzékfájllal rendelkező részklip szűrő][subclip_filter]

Itt van a szűrt eszköz:

![Szűrt eszköz dinamikus jegyzékfájllal][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>A bemutatóablak beállítása (DVR)

Jelenleg az Azure Media Services körkörös archívumot kínál, ahol az időtartam 1 perc és 25 óra között konfigurálható. A jegyzékfájl-szűrés segítségével az archívum tetején lévő görgetésre kijelölt DVR-ablakot hozhat létre adathordozó törlése nélkül. Sok olyan forgatókönyv van, ahol a műsorszolgáltatók korlátozott DVR ablakot szeretnének biztosítani az élő éllel való mozgáshoz, és ugyanakkor nagyobb archiválási ablakot tartanak. Előfordulhat, hogy a műsorszolgáltató a DVR ablakán kívül lévő adatokat szeretné használni a klipek kiemelésére, vagy különböző DVR-ablakokat szeretne biztosítani a különböző eszközökhöz. Például a legtöbb mobileszköz nem kezeli a nagy DVR-ablakokat (a mobileszközökhöz 2 perces DVR-ablak, asztali ügyfelek esetében pedig egy óra áll elő).

![DVR ablak dinamikus jegyzékfájllal][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>A LiveBackoff beállítása (élő pozíció)

A jegyzékfájl-szűréssel néhány másodpercet távolíthat el az élő program élő éléről. A szűrés lehetővé teszi a műsorszolgáltatók számára, hogy az előnézeti közzétételi ponton nézzék meg a bemutatót, és hirdetésbeszúrási pontokat hozzanak létre, mielőtt a nézők megkapják az adatfolyamot (30 másodperccel kihátrálva). A műsorszolgáltatók ezután ezeket a hirdetéseket az ügyfélkeretükbe tolhatják időben, hogy a hirdetési lehetőség előtt megkapják és feldolgozzák az információkat.

A mellett, hogy a reklám támogatás, az élő back-off beállítás lehet használni, hogy állítsa be a nézők helyzetét, hogy amikor az ügyfelek drift, és nyomja meg az élő széle, akkor is kap töredékek a szerverről. Így az ügyfelek nem kaphttp 404-es vagy 412-es hibaüzenetet.

![Szűrő élő vissza-off dinamikus jegyzékfájl][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Több szabály egyesítése egyetlen szűrőben

Több szűrési szabályt is kombinálhat egyetlen szűrőben. Például megadhat egy "tartományszabályt", hogy eltávolítsa a palákat egy élő archívumból, és kiszűrje a rendelkezésre álló bitsebességeket. Ha több szűrési szabályt alkalmaz, a végeredmény az összes szabály metszéspontja.

![Több szűrési szabály dinamikus jegyzékfájllal][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Több szűrő kombinálása (szűrőösszetétel)

Egy URL-címben több szűrőt is kombinálhat. A következő forgatókönyv bemutatja, hogy miért érdemes kombinálni a szűrőket:

1. Ki kell szűrned a videó tulajdonságait a mobileszközökön, például Androidon vagy iPaden (a videó minőségének korlátozása érdekében). A nem kívánt tulajdonságok eltávolításához hozzon létre egy, az eszközprofiloknak megfelelő fiókszűrőt. A fiókszűrőket az összes eszközhöz használhatja ugyanazon Media Services-fiókban, további társítás nélkül.
1. Azt is szeretné, hogy vágja le a kezdő és záró idő egy eszköz. A vágáshoz hozzon létre egy eszközszűrőt, és állítsa be a kezdési/befejezési időpontot.
1. Mindkét szűrőt kombinálni szeretné. Kombináció nélkül minőségi szűrést kell hozzáadnia a vágási szűrőhöz, ami megnehezítené a szűrőhasználatot.

A szűrők kombinálásához pontosvesszővel tagolt formátumban állítsa be a szűrőneveket a jegyzék-/lista URL-címére. Tegyük fel, hogy van egy *MyMobileDevice nevű szűrője,* amely szűri a tulajdonságokat, és van egy másik nevű *MyStartTime* egy adott kezdési időpont beállításához. Legfeljebb három szűrőt egyesíthet.

További információt [ebben a blogbejegyzésben talál.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)

## <a name="considerations-and-limitations"></a>Szempontok és korlátozások

- A **forceEndTimestamp**, **presentationWindowDuration**és **liveBackoffDuration** értékeit nem kell VOD-szűrőhöz beállítani. Csak élő szűrőforgatókönyvekhez használatosak.
- A dinamikus jegyzékfájl gop-határokban (kulcskeretekben) működik, így a vágás gop pontosságú.
- Ugyanazt a szűrőnevet használhatja a számla- és eszközszűrőkhöz. Az eszközszűrők elsőbbsége télveződnek, és felülírják a számlaszűrőket.
- Ha frissíti a szűrőt, akár 2 percet is igénybe vehet, amíg a streamelési végpont frissíti a szabályokat. Ha szűrőket használt a tartalom kiszolgálására (és a tartalmat proxykban és CDN-gyorsítótárakban gyorsítótárazta), ezeknek a szűrőknek a frissítése lejátszóhibákat okozhat. Javasoljuk, hogy törölje a gyorsítótárat a szűrő frissítése után. Ha ez a beállítás nem lehetséges, fontolja meg egy másik szűrő használatát.
- Az ügyfeleknek manuálisan kell letölteniük a jegyzékfájlt, és elemezniük kell a pontos kezdési időbélyegzőt és időskálát.

    - Az eszköz ben lévő sávok tulajdonságainak meghatározásához [kése és vizsgálja meg a jegyzékfájlt.](#get-and-examine-manifest-files)
    - Az eszközszűrő időbélyegző tulajdonságainak beállítására a következő képlet áll: <br/>startTimestamp &lt;= kezdési&gt; +  &lt;idő a jegyzékben&gt; a szűrő várható kezdési időpontja másodpercben * időskála

## <a name="next-steps"></a>További lépések

Az alábbi cikkek bemutatják, hogyan hozhat létre szűrőket programozott módon:  

- [Szűrők létrehozása REST API-kkal](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása CLI-vel](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
