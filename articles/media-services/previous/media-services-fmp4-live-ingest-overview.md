---
title: 'Specifikáció: darabolt MP4 élő az Azure Media Services feldolgozása |} A Microsoft Docs'
description: 'Ez az meghatározás protokoll és formátum: darabolt MP4-alapú élő streamelési támogatunk az Azure Media Services ismerteti. Használhatja az Azure Media Services élő események streamelése és a tartalmak valós időben az Azure-t a cloud platform szerint. Ez a dokumentum is ismerteti, ajánlott eljárásokat, amellyel hatékonyan redundáns és robusztus élő betöltési mechanizmusokat.'
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: c6ff386913ed66cf4f74cb577bb8ca58e6932ada
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228878"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Specifikáció: darabolt MP4 élő az Azure Media Services feldolgozása
Ez az meghatározás protokoll és formátum: darabolt MP4-alapú élő streamelési támogatunk az Azure Media Services ismerteti. Media Services élő streamelési szolgáltatás, amellyel az ügyfelek által az Azure-t a felhőplatform az élő események streamelése és valós időben tartalom biztosít. Ez a dokumentum is ismerteti, ajánlott eljárásokat, amellyel hatékonyan redundáns és robusztus élő betöltési mechanizmusokat.

## <a name="1-conformance-notation"></a>1. Megfelelési jelöléssel
A kulcs "," kell nem"lehet" "Szükséges," "SHALL", "kell nem," "SHOULD", "Meg nem," szavak "Ajánlott," "MAY", és a jelen dokumentum "választható" kell értelmezni az RFC 2119 leírtaknak.

## <a name="2-service-diagram"></a>2. Szolgáltatásdiagram
Az alábbi ábrán látható, az élő adatfolyam-szolgáltatást a magas szintű architektúra a Media Services szolgáltatásban:

1. Az élő kódolók olyan leküldéses élő hírcsatornák csatornákat, létrehozása és kiépítése az Azure Media Services SDK-n keresztül.
1. Csatornák, programok és streamvégpontok a Media Services kezeléséhez az összes az élő adások online közvetítéséhez, beleértve a betöltést, a formázást, felhőbeli DVR-Kezelésre, biztonsággal, méretezhetőséggel és redundanciát.
1. Szükség esetén ügyfelek helyezhetnek üzembe egy Azure Content Delivery Network layer a tartalomstreameléshez használt streamvégpont és az ügyfél-végpontok között.
1. Ügyfél-végpontok stream streamvégpontból adaptív Streameléshez a HTTP protokoll használatával. Ilyenek például a Microsoft Smooth Streaming, dinamikus adaptív Streamelés (DASH vagy MPEG-DASH) HTTP- és Apple HTTP Live Streaming (HLS) keresztül.

![a betöltési folyamat][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream formátumban – ISO 14496 – 12 töredékes MP4
A formátumot az élő streaming betöltési tárgyalt [ISO-14496 – 12] Ez a dokumentum alapján. Lásd: a darabolt MP4 formátumban és bővítmények egyaránt részletes ismertetése a rögzített fájlok és élő adatfolyam-feldolgozó, [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Élő betöltés fájlformátum-definíciók
Az alábbi lista ismerteti, amely a alkalmazni élő definíciók tölti be az Azure Media Services speciális formátuma:

1. A **ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** mezők (HTTP POST) minden egyes kérelemmel el kell küldeni. Ezek a mezők kell küldeni a stream elején, és a kódoló csatlakoznia kell a stream folytatása bármikor képes feldolgozni. További információkért lásd a 6. szakasz az [1].
1. [1] 3.3.2 szakaszában határozza meg egy nem kötelező mező nevű **StreamManifestBox** az élő betöltés. Az Azure load balancer logikai útválasztási logikát miatt ez a mező használatával elavult. A box meg nem jelenik meg, ha a Media Services szolgáltatásba fürtjét. Ha ez a mező található, a Media Services csendes figyelmen kívül hagyja azt.
1. A **TrackFragmentExtendedHeaderBox** 3.2.3.2 [1] a megadott box minden töredék a jelen kell lennie.
1. 2-es verziója a **TrackFragmentExtendedHeaderBox** box media szegmensek, amelyek azonos URL-címek több adatközpontban létrehozásához használandó. A töredék index mező kötelező, több adatközpontot feladatátvételi index-alapú adatfolyam-továbbítási formátumokba például Apple HLS és az index-alapú MPEG-DASH. Több adatközpontot feladatátvétel engedélyezéséhez az töredék index között több kódolók szinkronizálnia kell, és akár kódoló újraindítást vagy hibák keresztül minden egyes egymást követő media töredék 1 növekszik.
1. [1] 3.3.6 szakasz határozza meg a egy mezőben nevű **MovieFragmentRandomAccessBox** (**mfra**), amely a rendszer elküldheti élő betöltési végén teljes stream (EOS) jelzi a csatornát. A betöltés logika a Media Services miatt EOS használata elavult, és a **mfra** mezőbe az élő betöltési nem küldhető. Ha küldi el, a Media Services csendes figyelmen kívül hagyja azt. A betöltés pont állapotának alaphelyzetbe állítása, javasoljuk, hogy használjon [csatorna alaphelyzetbe](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Azt is javasoljuk, hogy használjon [Program leállításához](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) bemutatása és a stream közötti.
1. Lehet, hogy az MP4-töredék időtartam állandó, az ügyfél jegyzékek méretének csökkentése érdekében. Egy állandó MP4 töredék időtartama is javítja az ügyfél letöltési heurisztika használatával ismételje meg a címkéket. Az időtartam ingadozhat nem egész képkockasebességet kiegyenlítése érdekében.
1. Az MP4-töredék időtartam körülbelül 2 és 6 másodperc között kell lennie.
1. MP4-töredékben időbélyegeket és az indexek (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` és `fragment_index`) növekvő sorrendben kell érkezik. Bár a Media Services képes legyen ellenállni a duplikált töredék, korlátozott lehetővé teszi töredék az adathordozó ütemterv szerint sorrendjének módosításához.

## <a name="4-protocol-format--http"></a>4. Protokoll-formátum – a HTTP
ISO töredékes MP4-alapú élő betöltési, a Media Services standard hosszú ideig futó HTTP POST-kérelem használatával, amely a szolgáltatás darabolt MP4 formátumban kódolt media adatokat továbbítson. Minden egyes HTTP POST küld a teljes töredékes MP4 bitstream ("stream"), a fejléc mezők kezdetétől fogva (**ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** mezők ), és a egy feladatütemezési töredékek folytatása (**moof** és **mdat** be). A HTTP POST-kérelem URL-cím szintaxisával című részében 9.2 az [1]. A POST URL-cím egy példát a következő: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Követelmények
Az alábbiakban a részletes követelményeket:

1. A kódoló a közvetítés lépésként KÉSZÍTSEN egy üres "törzs" (nulla tartalom hossza) a HTTP POST-kérelmet küld az azonos betöltési URL-cím használatával. Ez segít a kódoló gyors észlelése, hogy érvényes-e az élő betöltési végpontjához, és ha bármely hitelesítési vagy egyéb feltételek megadása kötelező. Egy HTTP-protokoll a kiszolgáló nem küldhet vissza egy HTTP-választ mindaddig, amíg a teljes, többek között a hozzászólás szövegét, a kérelem érkezik. A hosszú ideig futó természeténél élő eseményeket, anélkül, hogy ebben a lépésben a kódoló nem képes észlelni az olyan hibák befejezéséig tartó összes adatot küld.
1. A kódoló esetleges hibák vagy a hitelesítési kihívások kell kezelnie (1) miatt. Ha (1) sikeres 200 választ, továbbra is.
1. A kódoló egy új HTTP POST-kérelmet kell kezdődnie a darabolt MP4-streamet. A hasznos be a fejlécet, töredék követ kell kezdődnie. Vegye figyelembe, hogy a **ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** (az ebben a sorrendben) mezőbe kell küldeni minden egyes kérelemmel, még akkor is, ha a kódoló kell újból, mert az előző kérelem megszakadt a stream lejárta előtt. 
1. A kódoló kell használnia a darabolásos átvitel kódolási fel, mert a teljes tartalom hossza az élő esemény előrejelzése nem lehetséges.
1. Pontba, amennyiben az eseményt, az utolsó részlet elküldése után a kódoló szabályosan a darabolásos átvitel kódolási üzenetsort (HTTP-ügyfél a legtöbb implementációt, automatikusan kezeli) kell végződnie. A kódoló kell várja meg a szolgáltatást, hogy a végső válaszkódot adja vissza, és ezután állítsa le a kapcsolatot. 
1. A kódoló nem használja a `Events()` főnév, a Media Services élő támogatunk 9.2 az [1] leírtak szerint.
1. Ha a HTTP POST-kérés leáll, vagy túllépi az időkorlátot TCP hibával leáll a stream lejárta előtt, a kódoló kell egy új POST kérés kiadása egy új kapcsolat használatával, és kövesse a fenti követelményeknek. Ezenkívül a kódoló kell küldje el újra az előző két MP4 töredékei a Stream minden egyes nyomon követése és obnovení práce bEz bemutatása a média ütemterv kihagyást. Minden egyes nyomon követése az utolsó két MP4 naplóhasználatra újraküldése biztosítja, hogy nincs adatvesztés nélkül. Más szóval egy streamet tartalmaz az audio- és a egy videó nyomon követése, és a jelenlegi POST-kérés sikertelen lesz, ha a kódoló kell újra és küldje el újból az utolsó két naplóhasználatra hangsáv, ami korábban már sikeresen küldött, és az utolsó két töredék a videó nyomon követheti, amely korábban már sikeresen küldtek, annak biztosítására, hogy ne legyen adatvesztés nélkül. A kódoló media töredékek száma, az újracsatlakozáskor újraküldi "előre" puffert kell fenntartani.

## <a name="5-timescale"></a>5. Időskála
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) teljesítési használatát ismerteti **SmoothStreamingMedia** (2.2.2.1. szakasz), **StreamElement** (2.2.2.3. szakasz), **StreamFragmentElement** () 2.2.2.6 szakaszban), és **LiveSMIL** (2.2.7.3.1 szakaszt). Ha a időskálára értéke nem található, a használt alapértelmezett érték 10,000,000 (10 MHz). Bár a Smooth Streaming formátumban specifikációnak nem tiltja a többi időskálára érték használatát, a legtöbb encoder-megvalósításokban ez az alapértelmezett érték (10 MHz) létrehozásához, Smooth Streaming betöltési adatok. Oka az, hogy a [Azure Media a dinamikus csomagolás](media-services-dynamic-packaging-overview.md) funkciót, azt javasoljuk, hogy Ön egy 90-KHz időskálára a video-adatfolyamokat és 44,1 KHz vagy 48.1 KHz audiostreamek lejátszásával. Ha különböző Streamek különböző időskálára értékeket használják, az adatfolyam-szintű időskálán kell küldeni. További információkért lásd: [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. "Stream" meghatározása
Stream élő eseményfeldolgozást összeállítása élő bemutatókat, a művelet alapvető egysége streamelési feladatátvétel és a redundancia a forgatókönyvekhez. Stream számít, ha egy egyedi, darabolt MP4 bitstream tartalmazhat egyetlen nyomon vagy több nyomon követi. Egy teljes élő bemutató tartalmaz egy vagy több streamet, az élő kódolók konfigurációjától függően. Az alábbi példák bemutatják a különböző lehetőségek, adatfolyamok használatával állítsa össze a teljes élő bemutató.

**Példa** 

Szeretne létrehozni, amely tartalmazza a következő hang/kép bitsebességre való átkódolása élő streamelési bemutató:

Videó – 3000 KB/s, 1 500 kbps, 750 KB/s

Hang – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>1. lehetőség: Az összes nyomon követi a több adatfolyam
Ezt a beállítást, az egyetlen kódoló állít elő, az összes hang/kép nyomon követi, és ezután be egy darabolt MP4 bitstream bundles. A darabolt MP4 bitstream egyetlen HTTP POST-kapcsolaton keresztül elküldi. Ebben a példában csak egy stream az élő bemutató van.

![Adatfolyam-egy nyomon követése][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>2. lehetőség: Minden egyes nyomon követése egy külön Stream
Ezt a beállítást a kódoló egy követése beteszi egyes töredék MP4 bitstream, és majd közzéteszi a Streamek mindegyike külön HTTP-kapcsolaton keresztül. Ez elvégezhető Encoder egy vagy több kódolókkal történő továbbítását. Az élő betöltési az élő bemutató látja, mint négy Streamek összetevői.

![Az elkülönített Streamek nyomon követi][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>3. lehetőség: A legalacsonyabb MP4 videó nyomon követése az hangsávra csomagot egy adatfolyamba való
Ez esetben az ügyfél úgy dönt, hogy a hangsávot a legalacsonyabb sávszélességű, MP4 videó nyomon követése a csomagot a egy töredék MP4 bitstream, és hagyja a többi két videó nyomon követi, külön Streamek. 

![Adatfolyam-hang és videó nyomon követi][image4]

### <a name="summary"></a>Összegzés
Ez nem egy kimerítően teljes lista az összes lehetséges Adatbetöltési lehetőségek ebben a példában. Bármely csoportja, nyomon követi az adatfolyamokat, valójában, élő betöltési támogatja. Ügyfelek és a szállítók kódoló választhat saját megvalósításokhoz, mérnöki összetettségét, kódoló kapacitás, és a redundanciát és feladatátvételi szempontokat részletező cikkben alapján. A legtöbb esetben van azonban a teljes élő bemutató csak egy hangsávra. Ezért fontos annak biztosítása érdekében a egészséges legyen, a betöltés Stream, amely tartalmazza a hangsávot. Ez gyakran figyelembe veszi a hangsávot üzembe saját Stream (ahogy a 2. lehetőség) vagy az a legalacsonyabb sávszélességű, MP4 videó követése (ahogy a 3. lehetőség) a kötegelés eredményez. Is, a nagyobb redundancia és a hibatűrést, küldés, az azonos hangsávra két különböző Streamek (2 a redundáns hangsáv. lehetőség) vagy a hangsávot legalább két, a legalacsonyabb sávszélességű, MP4 videó nyomon követi (a 3 beállítást legalább két kötegelve hang-, a kötegelés video-adatfolyamokat) erősen ajánlott az élő betöltési Media Services szolgáltatásba.

## <a name="7-service-failover"></a>7. A szolgáltatás feladatátvételének
Élő streamelés jellegét, a megadott helyes feladatátvétel támogatásáról, kritikus fontosságú, a szolgáltatás rendelkezésre állásának biztosításához. A Media Services különböző típusú hibák, például hálózati hibák, Kiszolgálóhibák és tárolási problémák kezelésére szolgál. Helyes feladatátvétel logikai élőadás-kódoló oldaláról együtt használja, ha ügyfelek érheti el a felhőből rendkívül megbízható élő streamelési szolgáltatás.

Ebben a szakaszban bemutatjuk a szolgáltatás feladatátvételi forgatókönyveket. Ebben az esetben a hiba akkor fordul elő, valahol a szolgáltatáson belül, és azt az ügyfélrendszer, hálózati hiba. Az alábbiakban néhány javaslat a szolgáltatás feladatátvételének kezelésére vonatkozó kódoló végrehajtására:

1. A TCP-kapcsolat létrehozása egy 10 másodperces időkorlát használja. Ha a kapcsolat létrehozására tett kísérlet 10 másodpercnél hosszabb időt vesz igénybe, megszakíthatja a műveletet, és próbálkozzon újra. 
1. A HTTP-kérelem üzenet adattömbök küldéséhez használt rövid időtúllépés. Ha a cél MP4 töredék időtartama N másodperc, használja az N és 2 N másodperc; közötti küldési időtúllépés például ha az MP4-töredék időtartam 6 másodperc, használja egy időtúllépési értéke 6 és 12 másodperc. Időtúllépés történik, ha a kapcsolat alaphelyzetbe állítása, nyisson meg egy új kapcsolatot, és folytathatja a stream betöltési az új kapcsolaton. 
1. A működés közbeni puffer, amelynek az utolsó két naplóhasználatra minden nyomon követheti a szolgáltatás sikeresen és teljes mértékben küldött karbantartása.  Ha a HTTP POST-kérés egy Stream le van állítva, vagy túllépi az időkorlátot előtt, az adatfolyam végén nyisson meg egy új kapcsolatot és egy másik HTTP POST kérelem kezdete, küldje el újra a stream fejlécek, küldje el újra az utolsó két töredékei minden nyomon követése és folytatása a stream-d, anélkül az adathordozó idővonalon iscontinuity. Ez csökkenti az esélyét, hogy az adatvesztést.
1. Azt javasoljuk, hogy a kódoló nem korlátozza az egy kapcsolatot, vagy folytathatja a folyamatos átvitel után a TCP-hiba akkor fordul elő, az újrapróbálkozások számát.
1. Miután egy TCP-hiba:
  
    a. Az aktuális kapcsolatot be kell zárni, és a egy új kapcsolatot kell létrehozni egy új HTTP POST-kérelmet.

    b. Az új HTTP POST URL-cím azonosnak kell lennie, a kezdeti POST URL-cím.
  
    c. Az új HTTP POST tartalmaznia kell a stream fejlécek (**ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** be), amelyek megegyeznek a stream-fejléceket, a kezdeti BEJEGYZÉSBEN található.
  
    d. Az utolsó két töredék a rendszer minden egyes nyomon követése a kell újra el kell küldenie, és a streamelési bemutatása a média ütemterv kihagyást nélkül kell folytatni. MP4 töredék időbélyegei folyamatos növelnie kell még a HTTP POST-kérések között.
1. A kódoló kell megszűnik a HTTP POST-kérelmet, ha az adatot nem küld arányos az MP4-töredék időtartam gyakorisággal.  Egy HTTP POST-kérelmet, amely nem küld adatokat megakadályozhatja, hogy a Media Services gyorsan leválasztása a kódoló szolgáltatás frissítése esetén. Ebből kifolyólag a HTTP POST esetében a ritka (hirdetési jel esetén) nyomon követi kell lennie rövid életű, amint a ritka töredéket küldött leáll.

## <a name="8-encoder-failover"></a>8. Kódoló feladatátvétel
Kódoló feladatátvételi a második típusú feladatátvételi forgatókönyv, amely el nem látott végpontok közötti élő adások közvetítése esetében. Ebben a forgatókönyvben a hibajelzést kiváltó körülmény a kódoló oldalon történik. 

![Kódoló feladatátvétel][image5]

Kódoló feladatátvétel történik, ha az élő betöltési végpontjához alkalmazza az alábbi követelményeinek:

1. Új kódoló példány folytatja a streamelési, hozható létre, ahogyan az ábrán (Stream a videóban, szaggatott vonal 3000-k).
1. Az új kódoló kell használnia az azonos URL-cím HTTP POST-kérelmet, a hibás szolgáltatáspéldányt.
1. Az új kódoló POST-kérés tartalmaznia kell az azonos darabolt MP4 fejléc mezőt is a hibás szolgáltatáspéldányt.
1. Az új kódoló megfelelően szinkronizálnia kell az összes többi futó kódolókkal történő továbbítását az ugyanazon az élő bemutató igazított töredék határokkal szinkronizált hang/kép minták létrehozása.
1. Az új adatfolyamot kell lennie az előző Stream szemantikailag és cserélhető fejléc és töredék szinten.
1. Az új kódoló próbálja meg minimálisra csökkentésre. A `fragment_absolute_time` és `fragment_index` adathordozó töredék növelje a ponttól, ahol a kódoló utolsó leállítás időpontjában. A `fragment_absolute_time` és `fragment_index` folyamatos módon kell növelhető, de érték átadása is megengedett bevezetni kihagyást, ha szükséges. A Media Services figyelmen kívül hagyja azt már fogadása és feldolgozása, töredék, így jobb oldalán, mint a bevezetése az adathordozó idővonalon folytonosság megszakítását töredék újraküldése err. 

## <a name="9-encoder-redundancy"></a>9. Kódoló redundancia
Bizonyos kritikus fontosságú az élő események, hogy igény szerint még magasabb rendelkezésre állást és minőséget, azt javasoljuk, hogy aktív-aktív redundáns kódolókat az adatvesztés nélküli, zökkenőmentes feladatátvétel érdekében használjon.

![Kódoló redundancia][image6]

Az alábbi ábrán látható módon kódolók két csoportját leküldése minden stream két példányát egyszerre az élő szolgáltatás. Ezt a beállítást, mert a Media Services adatfolyam-azonosító és töredék időbélyegző alapján ismétlődő töredék kiszűrheti használata támogatott. Az eredményül kapott az élő stream és az archív egy példányát minden adatfolyamok, amely a legjobb lehetséges összesítés a két forrásból. Például egy elméleti szélsőséges esetben van (nem kell lennie a) egy kódoló is az egyes adatfolyamok idő lekérdezhet fut, a szolgáltatásból az eredményül kapott élő streamet folyamatos adatvesztés nélkül. 

A forgatókönyv követelményei majdnem megegyezik a "Kódoló feladatátvételi" esetben azzal a kivétellel, hogy a második kódolókészlet elsődleges szeretne egyszerre futnak a követelményeknek.

## <a name="10-service-redundancy"></a>10. Szolgáltatás redundanciájára
Magas redundáns globális terjesztés néha rendelkeznie kell régiók közötti biztonsági mentés regionális katasztrófa kezelésére. A "Kódoló redundancia" topológia kibővítve, ügyfelek kiválaszthatják a redundáns szolgáltatások üzembe helyezéséhez rendelkeznie egy másik régióban, amely a második kódolókészlet van csatlakoztatva. Ügyfelek is képes együttműködni a Content Delivery Network szolgáltató üzembe helyezése egy globális Traffic Manager elé a két szolgáltatási telepítéseket zökkenőmentesen továbbítani az ügyfél forgalmát. A kódolók vonatkozó követelmények ugyanazok, mint a "Kódoló redundancia" eset. Az egyetlen kivétel, hogy a második csoporton kódolók kell lennie egy másik élő mutatott betöltési végpont. Az alábbi ábrán látható, a telepítő:

![Szolgáltatás redundanciájára][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Speciális típusú adatfeldolgozást formátumok
Ez a szakasz ismerteti a meghatározott helyzetek kezelésére kifejlesztett élő betöltési formátumok speciális típusú.

### <a name="sparse-track"></a>Ritka nyomon követése
A bemutató élő streamelési gazdag funkciókészletű felhasználói élményt, amikor gyakran ez szükséges időt szinkronizálva események továbbítására vagy sávon a fő media adatokkal jelzi. Ilyen például a dinamikus reklámjelölőket élő. Az ilyen esemény jelzés rendszeres hang/kép ritka jellege miatt streamelési eltér. Más szóval jelképző adatokat általában nem fordulhat elő, folyamatosan, és lehet, hogy az intervallum nehéz előre jelezni. Ritka követése fogalma a sávon kívüli jelképző adatok feldolgozására és úgy lett kialakítva.

A következő lépéseket kell egy ajánlott megvalósítása fürtjét ritka nyomon követése:

1. Hozzon létre egy külön darabolt MP4 bitstream, amely tartalmazza a csak ritka nyomon követi, hang/kép sávok nélkül.
1. Az a **kiszolgáló Manifest mezőbe élő** meghatározott a 6. szakasz az [1], használja a *parentTrackName* paramétert adja meg a szülő követése nevét. További információkért lásd: [1] 4.2.1.2.1.2 szakaszát.
1. Az a **kiszolgáló Manifest mezőbe élő**, **manifestOutput** értékre kell állítani **igaz**.
1. A jelképző esemény ritka természetéből, javasoljuk a következő:
   
    a. Az élő esemény elején a kódoló elküldi a kezdeti fejléc mezők a szolgáltatást, amely lehetővé teszi a szolgáltatás regisztrálja a ritka fájlok nyomon követése az ügyfél-jegyzékfájlban.
   
    b. A kódoló kell leállítani a HTTP POST-kérés, amikor adatot nem küld. Egy hosszú ideig futó HTTP POST, amelyek nem küldenek adatokat megakadályozhatja, hogy a Media Services gyorsan leválasztása a kódoló esetén a szolgáltatás a frissítés vagy a kiszolgáló újraindítását. Ezekben az esetekben a kiszolgáló átmenetileg blokkolva a szoftvercsatorna fogadási művelet.
   
    c. Amikor jelképző nem érhetők el adatok az idő alatt a kódoló SHOULD zárja be a HTTP POST kérelem. Bár a POST-kérés aktív, a kódoló adatokat kell küldeniük.

    d. Ritka töredékeket küld, amikor a kódoló állíthatja be kifejezett content-length fejlécet, ha rendelkezésre áll.

    e. Amikor egy új kapcsolat ritka töredékeket küld, a kódoló el kell küldését be a fejléc, az új töredék követ. Ez az olyan esetekben, ahol feladatátvétel köztes történik, és az új ritka kapcsolat jött létre egy új kiszolgálóra, amely nem észlelte a ritka követése előtt.

    f. A ritka fájlok nyomon követése részlet az ügyfél elérhetővé válnak, amikor a megfelelő szülő követése részlet, amely nagyobb vagy egyenlő időbélyeg értékkel rendelkezik az ügyfél számára hozzáférhető. Például, ha a ritka töredék t időbélyegző = 1000, valószínű, amely után az ügyfél "videó" (feltéve, hogy a szülő követése neve: "videó") töredékben időbélyeg 1000 vagy után töltse le a ritka töredék t látja = 1000. Vegye figyelembe, hogy a tényleges jel használható egy másik pozícióba a bemutató idővonalon a kijelölt célra. Ebben a példában úgy is, amely a ritka részlet t = 1000 rendelkezik egy XML-tartalma, amely egy ad ügyfélsor olyan helyzetben, amely néhány másodperc később.

    g. A ritka fájlok nyomon követése töredék hasznos (például XML, szöveges vagy bináris), a forgatókönyvtől függően különböző formátumokban is lehet.

### <a name="redundant-audio-track"></a>Redundáns hangsávra
Egy tipikus HTTP adaptív adatfolyam-továbbítási forgatókönyv (például Smooth Streaming vagy kötőjel) gyakran van a teljes bemutató csak egy hangsávra. Videó nyomon követi, amelyeket a hibaállapotok közül választhat az ügyfél több minőségi szintet, ellentétben a hangsávot lehet egypontos meghibásodás Ha megszakad a betöltési, az adatfolyam, amely tartalmazza a hangsávra. 

A probléma megoldásához, a Media Services élő betöltési, redundáns hangsáv támogatja. A cél pedig az, hogy az azonos hangsávra küldhető több alkalommal a különböző Streamek. A szolgáltatás csak regisztrálja a hangsávot egyszer az ügyfél-jegyzékfájlban, bár azt használhatja redundáns hangsáv tartalékként hang töredék beolvasása, ha az elsődleges hangsávra problémákkal rendelkezik. Redundáns hangsáv betöltési, a kódoló kell:

1. Hozzon létre több töredékre MP4 bitstreams az azonos hangsávra. A zónaredundáns hangsáv kell az azonos töredék időbélyegzőnél szemantikailag, és cserélhető fejléc és töredék szinten.
1. Győződjön meg arról, hogy a "hang" bejegyzést az élő kiszolgáló jegyzékfájl (6 [1]. szakasz) megegyezik a georedundáns minden hangsáv.

Az alábbi megvalósításra redundáns hangsáv ajánlott:

1. Minden egyedi hangsávra küldenek a stream önmagában. Is, küldjön egy redundáns stream minden ezek hangsávra adatfolyamok, ha a második stream eltér-e az első csak a HTTP POST URL-cím az azonosító által: {protokoll} :// {kiszolgáló-cím} / {pont path}/Streams({identifier}) közzététele.
1. A két legalacsonyabb videó bitsebességre való átkódolása külön Streamek küldhet. Ezekbe az adatfolyamokba mindegyike ugyancsak tartalmaznia kell egy példányát minden egyes egyedi hangsávra. Ha több nyelvet támogat, például ezekbe az adatfolyamokba tartalmaznia kell az egyes nyelvekhez hangsáv.
1. Különálló kiszolgálóra (kódoló) példányok használatával kódolása és a redundáns adatfolyamok említett (1) és (2). 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
