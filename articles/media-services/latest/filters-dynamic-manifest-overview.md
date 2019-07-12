---
title: Szűrők és az Azure Media Services dinamikus jegyzékek |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek érdekében, ami a szelektív hoz létre.
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
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835823"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Előre szűrése a jegyzékek dinamikus Packager használatával

Ha, adaptív sávszélességű streamelés eszközökre tartalom jusson, gyakran egy jegyzéket több verziójának közzététele a célként megadott eszközképességek vagy a rendelkezésre álló hálózati sávszélességet kell. A [dinamikus Packager](dynamic-packaging-overview.md) lehetővé teszi, hogy adja meg a szűrőket, amely képes szűrni az adott kodekek ki megoldások bitsebességre való átkódolása és hang nyomon kombinációk a működés közbeni szükségtelenné több példány létrehozásához. Egyszerűen szeretne közzétenni egy meghatározott készletének a Céleszközök (iOS, Android, Okostelevízió vagy böngészők) és a hálózati képességekkel (nagy sávszélességű, mobil és alacsony sávszélességű forgatókönyv) a beállított szűrőknek egy új URL-CÍMÉT. Ebben az esetben az ügyfelek is módosíthatja a lekérdezési karakterlánc a tartalom adatfolyamként (elérhető megadásával [eszköz szűrők vagy a fiók szűrők](filters-concept.md)) és a szűrők segítségével konkrét szakaszokra stream datového proudu.

Néhány kézbesítési forgatókönyvek igényelnek, győződjön meg arról, hogy az ügyfél nem tudja elérni az adott nyomon követi. Például előfordulhat, hogy nem szeretné közzétenni a jegyzékfájl, amely tartalmazza a HD nyomon követi egy adott előfizetői szintre. Vagy előfordulhat, hogy el kívánja távolítani adott adaptív sávszélességű (ABR) nyomon követi a szállítási egy adott eszközhöz, amely nem kiaknázhatják a további nyomon követi a költségek csökkentése érdekében. Ebben az esetben az előre létrehozott szűrők listájának társíthatott a [Streamelési lokátor](streaming-locators-concept.md) létrehozáskor. Ebben az esetben az ügyfelek a tartalom adatfolyamként hogyan nem módosíthatja, azt határozza meg a **Streamelési lokátor**.

Kombinálhatja megadása szűrésével [szűrők a Streamelési lokátor](filters-concept.md#associating-filters-with-streaming-locator) + konkrét szűrők további eszköz, amely az ügyfél az URL-címet adja meg. Ez lehet hasznos, ha további nyomon követi, mint a metaadatok vagy esemény streamet, hang nyelvek vagy leíró hangsáv korlátozni szeretné. 

A különböző szűrők megadása a streamben biztosít egy hatékony **dinamikus Manifest** adatkezelési megoldás, amelyekre több használatieset-forgatókönyvek a célként megadott eszközökhöz. Ez a témakör ismerteti a kapcsolódó fogalmak **dinamikus jegyzékfájlok** és a példákat mutat be, amely használja ezt a szolgáltatást érdemes forgatókönyvek.

> [!NOTE]
> A dinamikus jegyzékek nem változtatja az eszköz és az eszköz alapértelmezett jegyzék. 
> 

##  <a name="overview-of-manifests"></a>Jegyzékek áttekintése

Az Azure Media Services támogatja a HLS, MPEG DASH és Smooth Streaming protokollokat. Részeként [dinamikus csomagolási](dynamic-packaging-overview.md), a streaming (HLS fő lista, DASH Media bemutató ismertetése [MPD] és Smooth Streaming) ügyfél jegyzékek dinamikus jönnek létre a formátum választó az URL-cím alapján. Tekintse meg a kézbesítési protokollok [gyakori igény szerinti munkafolyamat](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>GET, és vizsgálja meg a jegyzékfájlok

Azt adja meg a szűrő nyomon követése tulajdonság feltétel alapján dinamikusan létrehozott jegyzék szerepeltetni kívánt melyik nyomon követi a Stream (élő vagy igény szerinti [VOD] videó) listáját. És vizsgálja meg a tulajdonságait, a nyomon követi, akkor először betölteni a Smooth Streaming jegyzékfájlt.

A [feltöltése, kódolása és streamelése a .NET-tel fájlok](stream-files-tutorial-with-api.md#get-streaming-urls) oktatóanyag bemutatja, hogyan hozhat létre a streamelési URL-címeket a .NET használatával. Ha futtatja az alkalmazást, az URL-címek közül mutat a Smooth Streaming jegyzékfájl: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Másolja és illessze be az URL-címet a böngésző címsorába. A rendszer letölti a fájlt. Megnyithatja egy tetszőleges szövegszerkesztőben.

A REST-példa: [feltöltése, kódolása és streamelése a fájlokat a REST segítségével](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Az átviteli sebesség, video-adatfolyamot figyelése

Használhatja a [Azure Media Player demó lap](http://aka.ms/azuremediaplayer) figyelése az átviteli sebesség, video-adatfolyamot. A bemutató lap diagnosztikai adatokat jelenít meg a **diagnosztikai** lapon:

![Az Azure Media Player diagnostics][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Példák: URL-címet a lekérdezési karakterlánc-szűrők

Streamelési protokollok ABR szűrőket alkalmazhat: HLS, MPEG-DASH és Smooth Streaming. Az alábbi táblázatban néhány példa az URL-címek szűrőket jeleníti meg:

|Protocol|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Megjelenítés szűrése

Kiválaszthatja, hogy az eszköz több kódolási profilt (H.264 alapterv, H.264 magas, AACL, AACH, Dolby digitális Plus) és több minőségi bitsebességre való átkódolása kódolása. Azonban nem minden ügyfél eszköz lesz a támogatja, az objektumhoz tartozó összes profilok és bitsebességre való átkódolása. Régebbi Android-eszközök például csak H.264 alapkonfiguráció + AACL támogatja. Sávszélesség- és eszköz számítási magasabb bitsebességre való átkódolása küld egy eszköz, amely nem olvasható be a következő előnyöket hulladékok. Ilyen eszközt kell dekódolja az összes megadott információt, csak a vertikális leskálázás való megjelenítéshez.

Dinamikus Manifest eszközprofilok hozhat létre (mobil, például a konzolon, illetve a HD/SD), és a nyomon követi és a egy részét az egyes profilok kívánt tulajdonságait. Nevű megjelenítésszűrés. Az alábbi ábrán egy példa látható.

![Megjelenítésszűrés – példa][renditions2]

A következő példában egy kódoló egy mezzanine eszköz kódolandó hét ISO MP4-fájlnak videó beállításkészletben (a 180p a 1080p) lett megadva. A kódolt objektumhoz lehet [dinamikusan csomagolt](dynamic-packaging-overview.md) be a következő adatfolyam-továbbítási protokollok bármelyikét: HLS, MPEG DASH és Smooth. 

A következő ábra tetején látható, a HLS manifest az eszköz nincs szűrőket. (Tartalmazza az összes hét beállításkészletben.)  A bal alsó az ábrán látható, amelyhez "ott" nevű szűrőt alkalmaztak egy HLS-jegyzékfájlt. A "ott" szűrő arra utasítja az összes bitsebességre való átkódolása alább 1 MB/s, így az alsó két minőségi szintek úgy lettek levágja, a válaszban. A jobb alsó sarokban az ábrán látható a HLS-jegyzékfájlt, amelyhez "mobileszköz" nevű szűrőt alkalmaztak. A "mobileszköz" szűrő arra utasítja, ahol a felbontást, nagyobb 720p, a két 1080 p beállításkészletben is levágja, így beállításkészletben.

![Megjelenítés szűrése][renditions1]

## <a name="removing-language-tracks"></a>Nyelv eltávolítása sávok
Az eszközök lehetnek több hang nyelvet, például az angol, spanyol, francia, stb. Általában a lejátszó SDK kezelők alapértelmezett hangsávra kiválasztása, és elérhető hang követi nyomon egy felhasználó által választott.

Az ilyen lejátszó SDK-k fejlesztése is kihívást jelent, mivel implementálható különböző eszközspecifikus player keretrendszerekkel igényel. Is az egyes platformokon Player API-k korlátozva, és nem tartalmazzák a hang kijelölés funkció arról, hogy a felhasználók hol nem válassza ki és módosítsa az alapértelmezett hangsávra. Eszköz szűrőket szabályozhatja a viselkedés hang kívánt nyelveket tartalmazó szűrők létrehozásával.

![Nyelvek szűrése][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Az eszköz start-csonkolás

A legtöbb élő események streamelése operátorok néhány tesztet, a tényleges esemény előtt futtassa. Ezek lehetnek például egy befutó hasonlóan ez az esemény kezdete előtt: "Program megkezdődik rövid ideig." 

Ha a program az archiválás, a tesztelés és állóképek adatokat is archivált és benne a bemutatót. Azonban ezt az információt nem kell feltüntetni az ügyfelek számára. A dinamikus jegyzékfájl létrehozásához egy kezdési idejének szűrője, és a nemkívánatos adatok eltávolítása a jegyzékfájlban.

![Tisztítás kezdő][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Az élő archívumot subclips (nézetek) létrehozása

Sokáig futó számos élő események és élő archívumot tartalmazhat több esemény. Az élő esemény befejezése után műsorszolgáltatók érdemes részekre az élő archívumot logikai programot, és állítsa le a feladatütemezések. 

A virtuális programok külön-külön teheti közzé bejegyzés az élő archívumot feldolgozását, és nem hoz létre a különböző eszközöket (amely nem kap a CDN az az előnye, hogy a meglévő gyorsítótárazott szilánkok) nélkül. Ilyen virtuális programok a következők: a negyedévek egy labdarúgó vagy Kosárlabda játékot, a baseball innings vagy egyéni események minden sport-program.

Dinamikus Manifest is szűrők létrehozása a kezdési/befejezési idején és az élő archívumot tetején keresztül virtuális nézeteket hozhat létre. 

![Részklip szűrő][subclip_filter]

Íme a szűrt objektumhoz:

![Síelés][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>A bemutató ablak (DVR) beállítása

Jelenleg az Azure Media Services idejére hol konfigurálható 5 perc és 25 óra között körkörös archív kínál. Jegyzékfájl szűrés működés közbeni DVR ablakának létrehozása az archívum felső keresztül media törlése nélkül is használható. Ha sikeresen szeretne biztosítani egy korlátozott DVR-ablak áthelyezése élő oldala, és a egy időben megtartandó egy nagyobb méretű archiválási ablak számos forgatókönyv közül választhat. A szórást küldő számítógép lehet, hogy szeretné használni az adatokat, a DVR ablakban jelölje ki a legjobb pillanatokból összeválogatott kívül esik, vagy adjon meg másik DVR windows a különböző eszközökön szeretnék. Például a mobileszközök a legtöbb kezelik a nagy DVR windows (is rendelkezik egy 2 perces DVR ablak mobileszközökhöz és 1 óra asztali ügyfelek esetén).

![DVR ablak][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (élő pozíciótól) beállítása

Jegyzékfájl szűrés használható néhány másodpercig eltávolítása egy élő programot élő szélétől. Szűrés lehetővé teszi, hogy sikeresen, tekintse meg a bemutatót az előzetes verzió kiadvány ponton és a hirdetmény beszúrási pontok létrehozása előtt a megtekintők az adatfolyamhoz (alapját 30 másodperc). Műsorszolgáltatók ezután elküldheti ezeket a hirdetmények, az ügyfél-keretrendszereket időben, számukra, hogy fogadják és feldolgozzák az adatokat a hirdetés lehetőség előtt.

A hirdetés támogatás mellett az élő visszatartási beállítás segítségével a nézők helyének beállítása, így az ügyfelek konfigurációsodródás, és nyomja le az élő Edge-ben, ha továbbra is hozzáférhetnek a töredékek a kiszolgálóról. Ezzel a módszerrel az ügyfelek nem jelenik meg a HTTP 404-es vagy 412 hiba.

![Élő visszalépéssel szűrője][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Egyetlen szűrő szabályokban több kombinálása

Egyetlen szűrő több szűrési szabály kombinálhatók. Például megadhat egy "tartomány szabály" befutók eltávolítása egy élő archívumot, és elérhető bitsebességre való átkódolása ki is szűrheti. Több szűrési szabályok alkalmazásakor a végeredmény esetén az összes szabály metszet.

![Több szűrési szabályok][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinált szűrők (szűrő összeállítás)

Több szűrő, egy egyetlen URL-címben is kombinálhatók. Az alábbi forgatókönyvet mutat be, hogy miért érdemes a szűrők kombinálásával:

1. A videó tulajdonságait, például Android- vagy iPad eszközökhöz szűrése (ahhoz, hogy korlátozza a videó minőségű) kell. Távolítsa el a nemkívánatos minőségű, hozzon létre egy szűrő az eszközprofilok alkalmas. Fiókszűrők használhatja ugyanazt a Media Services-fiók társítás minden további nélkül alatt az összes eszköz.
1. Szeretné is, és a egy eszköz a kezdő és záró idő záró szóközöket. Ennek érdekében fog létrehozni egy adategység-szűrőt, és a kezdő és záró idő beállítása. 
1. Érdemes úgy, hogy mindkét ezeket a szűrőket. Kombináció, nélkül kell minőségi szűrés a tisztítás szűréséhez, amely biztosítja, szűrő használati nehezebb hozzáadása.


A szűrők kombinálásával, állítsa be a szűrő nevét a jegyzékfájl listára kell URL-címet pontosvesszővel tagolt formátumú. Tegyük fel, hogy egy szűrő nevű *MyMobileDevice* , amely szűri tulajdonságait, és van egy másik nevű *MyStartTime* beállítása egy adott kezdési idő. Legfeljebb három szűrők kombinálásával. 

További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

- A tartozó értékeket **forceEndTimestamp**, **presentationWindowDuration**, és **liveBackoffDuration** VOD szűrő nem kell beállítani. Csak élő szűrő célokra használhatók. 
-  Dinamikus jegyzék Képcsoporttal határain belül (fő keretek) működik, így vágást rendelkezik Képcsoporttal pontossága.
-  Használhatja ugyanazt a fiókot és az eszközintelligencia szűrők szűrő nevet. Eszközintelligencia szűrők rendelkezik nagyobb prioritással, és felülírja a fiók szűrők.
- Ha frissíti a szűrőt, akár 2 percig a streamvégpont frissítéséhez a szabályok is igénybe vehet. Ha használt szűrők a tartalom továbbítása (és, gyorsítótárazza a tartalmat a proxyk és a CDN gyorsítótárazza) player hibák frissítése ezeket a szűrőket eredményezhet. Azt javasoljuk, hogy a szűrő frissítése után törölheti a gyorsítótárat. Ha ezt a beállítást nem lehetséges, fontolja meg egy másik szűrővel.
- Az ügyfeleknek kell manuálisan a jegyzékfájl letöltése és elemzése a pontos kezdési idő bélyeg és méretezési idő.
    
    - A számok az adategység tulajdonságainak meghatározásához [lekérése, és vizsgálja meg a jegyzékfájl](#get-and-examine-manifest-files).
    - Az eszközintelligencia szűrő időbélyegző tulajdonságainak képlete: <br/>startTimestamp = &lt;kezdő időpont a jegyzékfájlban&gt; +  &lt;szűrő kezdési idő másodpercben várt&gt; * időskálára

## <a name="next-steps"></a>További lépések

A következő cikkek bemutatják, hogyan hozhat létre programozott módon a szűrők:  

- [Szűrők létrehozása REST API-k](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása a CLI-vel](filters-dynamic-manifest-cli-howto.md)

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
