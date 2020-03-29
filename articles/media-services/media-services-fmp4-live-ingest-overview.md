---
title: Az Azure Media Services töredezett MP4 élő betöltési specifikációja | Microsoft dokumentumok
description: Ez a specifikáció ismerteti a protokoll és a formátum töredezett MP4-alapú élő streamelés az Azure Media Services. Ez a dokumentum a rendkívül redundáns és robusztus élő betöltési mechanizmusok létrehozásának ajánlott gyakorlatait is ismerteti.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 507afad294e8233ea4de4130795f29925870fcdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888053"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Az Azure Media Services töredezett MP4 élő betöltési specifikációja 

Ez a specifikáció ismerteti a protokoll és a formátum töredezett MP4-alapú élő streamelés az Azure Media Services. A Media Services olyan élő streamelési szolgáltatást biztosít, amelyet az ügyfelek az Azure felhőplatformként való használatával valós időben streamelhetnek élő eseményekés tartalmak közvetítésére. Ez a dokumentum a rendkívül redundáns és robusztus élő betöltési mechanizmusok létrehozásának ajánlott gyakorlatait is ismerteti.

## <a name="1-conformance-notation"></a>1. Konformáció jelölés
A dokumentumban a "MUST", "MUST", "REQUIRED", "SHALL", "NOT", "NOT", "NOT", "RECOMMENDED", "May" és "OPTIONAL" kulcsszavakat a 2119.

## <a name="2-service-diagram"></a>2. Szolgáltatási diagram
Az alábbi ábra a Media Services élő közvetítési szolgáltatásának magas szintű architektúráját mutatja be:

1. Az élő kódoló leküldéses élő hírcsatornák csatornák, amelyek az Azure Media Services SDK-n keresztül létrehozott és kiépített csatornákra.
1. A Media Services csatornái, programjai és streamelési végpontjai kezelik az összes élő streamelési funkciót, beleértve a betöltést, a formázást, a felhőalapú dvr-t, a biztonságot, a méretezhetőséget és a redundanciát.
1. Szükség esetén az ügyfelek választhatnak, hogy egy Azure Content Delivery Network réteget telepítenek a streamelési végpont és az ügyfélvégpontok között.
1. Az ügyfélvégpontok http Adaptive Streaming protokollok használatával streamelnek a streamelési végpontról. Ilyenek például a Microsoft Smooth Streaming, a Dynamic Adaptive Streaming http-n (DASH vagy MPEG-DASH) és az Apple HTTP Live Streaming (HLS).

![betöltési folyamat][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream formátum – ISO 14496-12 töredezett MP4
Az ebben a dokumentumban tárgyalt élő közvetítésbetöltés vezetékes formátuma az [ISO-14496-12] alapul. A töredezett MP4 formátum és bővítmények részletes magyarázatát mind az igény szerinti videofájlok, mind az élő közvetítés ekor történő letöltése esetén lásd az [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Élő betöltési formátumdefiníciók
Az alábbi lista az Azure Media Services betöltésére vonatkozó speciális formátumdefiníciókat ismerteti:

1. Az **ftyp**, **Live Server Manifest Box**és **moov** dobozokat minden kéréssel együtt el kell küldeni (HTTP POST). Ezeket a mezőket az adatfolyam elején kell elküldeni, és bármikor, amikor a kódolónak újra kell csatlakoznia az adatfolyam-betöltés folytatásához. További információ: [1] 6.
1. A[1] 3.3.2 szakasza egy **StreamManifestBox** nevű, az élő betöltéshez kötelező mezőt határoz meg. Az Azure terheléselosztó útválasztási logikája miatt ezzel a mezővel elavult. A doboz nem lehet jelen a Media Services szolgáltatásba való betöltéskor. Ha ez a mező jelen van, a Media Services csendben figyelmen kívül hagyja azt.
1. A [1] 3.2.3.2-ben definiált **TrackFragmentExtendedHeaderBox** mezőnek minden töredékhez jelen kell lennie.
1. A **TrackFragmentExtendedHeaderBox** mező 2-es verziója olyan médiaszegmensek létrehozásához használható, amelyek több adatközpontban azonos URL-címekkel rendelkeznek. A töredékindex-mező szükséges az indexalapú streamelési formátumok, például az Apple HLS és az indexalapú MPEG-DASH adatkapcsolat-átvételéhez. A több adatközpont-feladatátvétel engedélyezéséhez a töredékindexet több kódolóközött kell szinkronizálni, és minden egyes egymást követő adathordozó-töredék esetében 1-vel növelni kell, még a kódoló újraindítása vagy meghibásodása esetén is.
1. Szakasz 3.3.6 [1] határozza meg a doboz nevű **MovieFragmentRandomAccessBox** (**mfra**), hogy lehet küldeni a végén az élő betöltés, hogy jelezze end-of-stream (EOS) a csatornára. A Media Services betöltési logikája miatt az EOS használata elavult, és az élő betöltési **mfra** doboz nem kerül elküldésre. Ha elküldi, a Media Services csendben figyelmen kívül hagyja azt. A betöltési pont állapotának visszaállításához azt javasoljuk, hogy használja a [Csatorna alaphelyzetbe állítását.](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels) Azt is javasoljuk, hogy a [Program leállítása](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) programhasználatával fejezze be a bemutatót és streameljen.
1. Az MP4-töredék időtartamának állandónak kell lennie, hogy csökkentse az ügyféljegyzékek méretét. Az állandó MP4 töredék időtartama is javítja az ügyfél letöltési heurisztika használatával ismételt címkéket. Az időtartam ingadozhat, hogy kompenzálja a nem egész képkockasebesség.
1. Az MP4-töredék időtartamának körülbelül 2 és 6 másodperc között kell lennie.
1. MP4 töredék időbélyegek és indexek `fragment_index`**(TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` és ) kell érkeznie növekvő sorrendben. Bár a Media Services rugalmas a duplikált töredékek, korlátozott képes átrendezni töredékek szerint a média idővonal.

## <a name="4-protocol-format--http"></a>4. Protokoll formátum – HTTP
Az ISO töredezett MP4-alapú élő betöltése a Media Services számára egy szabványos, hosszú ideig futó HTTP POST-kérést használ a töredezett MP4 formátumban csomagolt kódolt médiaadatok nak a szolgáltatásba történő továbbítására. Minden HTTP POST küld egy teljes töredezett MP4 bitstream ("stream"), kezdve a fejléc dobozok (**ftyp**, **Live Server Manifest Box**, és **moov** dobozok), és továbbra is egy sor töredékek (**moof** és **mdat** dobozok). A HTTP POST kérelem URL-szintaxisát lásd a[1] 9.2 szakaszában. Egy példa a POST URL-címre: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Követelmények
Itt vannak a részletes követelmények:

1. A kódolónak egy HTTP POST-kérelem elküldésével kell kezdenie az adást egy üres "törzs" (nulla tartalomhossz) használatával, ugyanazzal a betöltési URL-címmel. Ez segíthet a kódoló gyorsan észlelni, hogy az élő betöltési végpont érvényes,, és ha vannak olyan hitelesítési vagy egyéb feltételek szükségesek. HTTP protokollonként a kiszolgáló nem küldhet vissza HTTP-választ, amíg a teljes kérés, beleértve a POST törzset is, meg nem érkezik. Tekintettel egy élő esemény hosszú ideig futó jellegére, e lépés nélkül előfordulhat, hogy a kódoló nem észleli a hibát, amíg be nem fejezi az összes adat küldését.
1. A kódolónak kezelnie kell az (1) miatt felmerülő hibákat vagy hitelesítési kihívásokat. Ha (1) sikerül a 200 válasz, folytassa.
1. A kódolónak új HTTP POST-kérelmet kell indítania a töredezett MP4-adatfolyammal. A hasznos adatnak a fejlécmezőkkel kell kezdődnie, majd töredékekkel kell kezdenie. Ne feledje, hogy az **ftyp**, **Live Server Manifest Box**és **moov** dobozokat (ebben a sorrendben) minden kéréssel együtt el kell küldeni, még akkor is, ha a kódolónak újra kell csatlakoznia, mert az előző kérés az adatfolyam vége előtt megszakadt. 
1. A kódoló nak darabolt átviteli kódolást kell használnia a feltöltéshez, mert lehetetlen megjósolni az élő esemény teljes tartalomhosszát.
1. Amikor az esemény véget ér, az utolsó töredék elküldése után a kódolónak kecsesen véget kell vetnie a darabolt átviteli kódolási üzenetsorozatnak (a legtöbb HTTP-ügyfélhalom automatikusan kezeli). A kódolónak meg kell várnia, hogy a szolgáltatás visszaadja a végső válaszkódot, majd meg kell szakítania a kapcsolatot. 
1. A kódoló nem `Events()` használhatja a főnév a 9.2 a [1] az élő betöltés a Media Services.
1. Ha a HTTP POST kérés az adatfolyam vége előtt tcp-hibával szakít le vagy időtúllépés tcp-hibával jár le, a kódolónak új POST-kérelmet kell kiadnia egy új kapcsolat használatával, és követnie kell az előző követelményeket. Ezenkívül a kódolónak újra kell küldenie az előző két MP4-töredéket az adatfolyam minden egyes sávjára vonatkozóan, és folytatnia kell anélkül, hogy a médiaidővonalon folytonosságmegszakadságot vezetne be. Az utolsó két MP4-töredékek újraküldése minden sávhoz biztosítja, hogy ne legyen adatvesztés. Más szóval, ha egy adatfolyam hang- és videosávot is tartalmaz, és az aktuális POST-kérelem sikertelen, a kódolónak újra kell csatlakoznia, és újra el kell küldenie a hangsáv utolsó két töredékét, amelyeket korábban sikeresen elküldött, valamint a videó utolsó két töredékét. a korábban sikeresen elküldött nyomon követ, hogy ne legyen adatvesztés. A kódolónak fenn kell tartania az adathordozó-töredékek "továbbítási" pufferét, amelyet újraküld, amikor újra csatlakozik.

## <a name="5-timescale"></a>5. Időkeret
[Az [MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) a **SmoothStreamingMedia** (2.2.2.1. szakasz), a **StreamElement** (2.2.2.3. szakasz), a **StreamFragmentElement** (2.2.2.6. szakasz) és a **LiveSMIL** (2.2.7.3.1 szakasz) időskálájának használatát írja le. Ha az időskála értéke nincs jelen, az alapértelmezett érték 10 000 000 (10 MHz). Bár a Smooth Streaming formátum specifikáció nem blokkolja a használatát más időskála értékek, a legtöbb kódoló megvalósítások használja ezt az alapértelmezett értéket (10 MHz) zökkenőmentes streamelési betöltési adatok létrehozásához. Az [Azure Media dinamikus csomagolási](media-services-dynamic-packaging-overview.md) funkciója miatt azt javasoljuk, hogy 90 KHz-es időskálát használjon a videostreamekhez, és 44,1 KHz-et vagy 48,1 KHz-et az audiostreamekhez. Ha különböző időskálaértékeket használ a különböző adatfolyamok, az adatfolyam-szintű időskála kell küldeni. További információ: [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. A "patak" meghatározása
Az adatfolyam az élő bemutatók írásához, a folyamatos átvitelfeladat-továbbítás kezeléséhez és a redundanciaforgatókönyvekhez való élő betöltési művelet alapvető egysége. Az adatfolyam egy egyedi, töredezett MP4 bitfolyam, amely egyetlen vagy több sávot tartalmazhat. A teljes élő bemutató az élő kódolók konfigurációjától függően egy vagy több adatfolyamot tartalmazhat. Az alábbi példák bemutatják a streamek használatának különböző lehetőségeit a teljes élő bemutató összeállításához.

**Példa:** 

Az ügyfél élő közvetítési bemutatót szeretne létrehozni, amely a következő hang- és videobitrátákat tartalmazza:

Videó – 3000 kbps, 1500 kbps, 750 kbps

Hang – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>1. lehetőség: Az összes sáv egy adatfolyamban
Ebben a beállításban egyetlen kódoló generálja az összes hang- és videosávot, majd egyetlen töredezett MP4 bitfolyamba csomagolja őket. A töredezett MP4 bitfolyamot a rendszer egyetlen HTTP POST-kapcsolaton keresztül küldi el. Ebben a példában csak egy adatfolyam van ehhez az élő bemutatóhoz.

![Streams-one sáv][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>2. lehetőség: Minden szám külön adatfolyamban
Ebben a beállításban a kódoló minden egyes MP4-bitfolyamba egy sávot helyez el, majd az összes adatfolyamot külön HTTP-kapcsolatokon keresztül teszi közzé. Ezt egy kódolóval vagy több kódolóval teheti meg. Az élő lenyelés úgy látja, hogy ez az élő bemutató négy streamből áll.

![Streamek-külön számok][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>3. lehetőség: A legkisebb bitrátaú videosávdal rendelkező hangsáv egyetlen adatfolyamba való kötegelése
Ebben a beállításban az ügyfél úgy dönt, hogy a hangsávot az mp4 bitfolyam töredékében a legalacsonyabb bitrátaú videosávdal kötegeli, a másik két videosávot pedig külön adatfolyamként hagyja. 

![Hang- és videosávok streamelése][image4]

### <a name="summary"></a>Összefoglalás
Ez nem egy teljes lista az összes lehetséges betöltési lehetőségek ebben a példában. Ami azt illeti, a sávok streamekbe történő csoportosítását élő lenyelés támogatja. Az ügyfelek és a kódoló-szállítók a mérnöki összetettség, a kódoló kapacitása, valamint a redundancia- és feladatátvételi szempontok alapján választhatják ki saját implementációikat. A legtöbb esetben azonban csak egy hangsáv tartozik a teljes élő bemutatóhoz. Ezért fontos, hogy a hangsávot tartalmazó betöltési adatfolyam egészséges legyen. Ez a megfontolás gyakran azt eredményezi, hogy a hangsávot a saját adatfolyamába helyezik (mint a 2. lehetőségben), vagy a legalacsonyabb bitrátású videosávhoz (mint a 3. lehetőségnél) kötegelik. Továbbá, a jobb redundancia és hibatűrés érdekében ugyanazt a hangsávot két különböző adatfolyamban kell elküldeni (2. lehetőség redundáns hangsávokkal), vagy a hangsávot legalább két legalacsonyabb bitrátású videosávgal kell elhangozni (3. lehetőség legalább két hanggal videostreamek) erősen ajánlott a Media Services-be való élő betöltéshez.

## <a name="7-service-failover"></a>7. Szolgáltatás feladatátvétel
Tekintettel az élő streamelés jellegére, a szolgáltatás rendelkezésre állásának biztosításához elengedhetetlen a jó feladatátvételi támogatás. A Media Services különböző típusú hibák kezelésére szolgál, beleértve a hálózati hibákat, a kiszolgálóhibákat és a tárolási problémákat. Ha az élő kódoló oldal megfelelő feladatátvételi logikájával együtt használja, az ügyfelek rendkívül megbízható élő streamelési szolgáltatást érhetnek el a felhőből.

Ebben a szakaszban a szolgáltatás feladatátvételi forgatókönyveit tárgyaljuk. Ebben az esetben a hiba történik valahol a szolgáltatáson belül, és hálózati hibaként nyilvánul meg. Íme néhány javaslat a kódoló megvalósításához a szolgáltatás feladatátvételének kezeléséhez:

1. A TCP-kapcsolat létrehozásához használjon 10 másodperces időtúllépést. Ha a kapcsolat létesítésére tett kísérlet 10 másodpercnél tovább tart, szakítsa meg a műveletet, és próbálkozzon újra. 
1. Rövid időkérési időkéréssel küldheti el a HTTP-kérelemüzenet-adattömböket. Ha a cél MP4 töredék időtartama N másodperc, használjon küldési időoutot N és 2 N másodperc között; Ha például az MP4-töredék időtartama 6 másodperc, 6–12 másodperces időhosszabbítást használjon. Időtúllépés esetén állítsa alaphelyzetbe a kapcsolatot, nyisson meg egy új kapcsolatot, és folytassa az adatfolyam betöltését az új kapcsolaton. 
1. Gördülési puffer karbantartása, amely rendelkezik az utolsó két töredéket minden olyan számhoz, amelyet sikeresen és teljesen elküldtek a szolgáltatásnak.  Ha a HTTP POST-adatfolyam-kérés megszakad, vagy az adatfolyam vége előtt túllép az időtúllépés, nyisson meg egy új kapcsolatot, és nyisson meg egy másik HTTP POST-kérelmet, küldje el újra az adatfolyam-fejléceket, küldje el újra az utolsó két töredéket az egyes számadatokhoz, és folytassa az adatfolyamot anélkül, hogy bevezetne egy a média idővonalának folytonosságának hiánya. Ez csökkenti az adatvesztés esélyét.
1. Azt javasoljuk, hogy a kódoló NE korlátozza az újrapróbálkozások számát a kapcsolat létrehozásához vagy a tcp-hiba esetén a streamelés folytatásához.
1. TCP-hiba után:
  
    a. Az aktuális kapcsolatot be kell zárni, és új kapcsolatot kell létrehozni egy új HTTP POST-kérelemhez.

    b. Az új HTTP POST URL-nek meg kell egyeznie a kezdeti POST URL-lel.
  
    c. Az új HTTP POST-nek tartalmaznia kell az adatfolyam fejléceit (**ftyp**, **Live Server Manifest Box**és **moov** dobozok), amelyek megegyeznek a kezdeti POST stream fejléceivel.
  
    d. Az egyes vágányokra küldött utolsó két töredéket újra kell küldeni, és a streamelésnek a médiaidővonal folytonosságának megszakítása nélkül kell folytatódnia. Az MP4-töredékidőbélyegeknek folyamatosan növekedniük kell, még a HTTP POST-kérelmekben is.
1. A kódolónak meg kell szakítania a HTTP POST-kérelmet, ha az adatok küldése nem az MP4-töredék időtartamának arányával arányos.  Egy olyan HTTP POST-kérelem, amely nem küld adatokat, megakadályozhatja, hogy a Media Services szolgáltatásfrissítés esetén gyorsan lekapcsolódjon a kódolóról. Emiatt a ritka (hirdetési jel) sávjainak HTTP POST-jának rövid életűnek kell lennie, és a ritka töredék elküldése után megszűnik.

## <a name="8-encoder-failover"></a>8. Kódoló feladatátvétel
A kódoló feladatátvétela a feladatátvétel második típusa, amelyet a végpontok között élő közvetítés kézbesítése esetén meg kell oldani. Ebben az esetben a hibafeltétel a kódoló oldalán fordul elő. 

![kódoló feladatátvétele][image5]

A következő elvárások az élő betöltési végponttól érvényesek, amikor kódoló feladatátvétel történik:

1. Egy új kódoló példányt kell létrehozni, hogy továbbra is streaming, amint azt az ábrán (Stream 3000k videó, szaggatott vonal).
1. Az új kódolónak ugyanazt az URL-címet kell használnia a HTTP POST-kérelmekhez, mint a sikertelen példánynak.
1. Az új kódoló POST-kérelmének ugyanazokat a töredezett MP4 fejlécmezőket kell tartalmaznia, mint a sikertelen példány.
1. Az új kódolót megfelelően szinkronizálni kell az összes többi futó kódolóval ugyanahhoz az élő bemutatóhoz, hogy szinkronizált hang- és videomintákat hozzon létre a töredék határokhoz igazított kódokkal.
1. Az új adatfolyamnak szemantikailag egyenértékűnek kell lennie az előző adatfolyammal, és felcserélhetőnek kell lennie a fejléc- és töredékszinteken.
1. Az új kódolónak meg kell próbálnia minimalizálni az adatvesztést. A `fragment_absolute_time` `fragment_index` médiatöredékek és a médiatöredékek száma növekednie kell attól a ponttól kezdve, ahol a kódoló utoljára leállt. A `fragment_absolute_time` `fragment_index` és növekednie kell a folyamatos módon, de megengedett, hogy vezessenek be a folytonosság hiánya, ha szükséges. A Media Services figyelmen kívül hagyja a már fogadott és feldolgozott töredékeket, ezért jobb, ha a töredékek újraküldése oldalán erkedik, mint a média idővonalában a megszakítások bevezetése. 

## <a name="9-encoder-redundancy"></a>9. Kódoló redundancia
Bizonyos kritikus élő események, amelyek a kereslet még nagyobb rendelkezésre állás és a tapasztalat minősége, azt javasoljuk, hogy aktív-aktív redundáns kódolók zökkenőmentes feladatátvétel t adatvesztés nélkül.

![kódoló redundancia][image6]

Amint azt az ábrán is szemlélteti, a kódolók két csoportja egyszerre két példányt továbbít az egyes adatfolyamokból az élő szolgáltatásba. Ez a beállítás azért támogatott, mert a Media Services kiszűrheti az ismétlődő töredékeket az adatfolyam-azonosító és a töredék időbélyege alapján. Az eredményül kapott élő közvetítés és archívum az összes adatfolyam egyetlen példánya, amely a lehető legjobb összesítés a két forrásból. Például egy hipotetikus szélsőséges esetben, amíg van egy kódoló (nem kell, hogy ugyanaz legyen) fut egy adott időpontban minden egyes adatfolyam, az eredményül kapott élő közvetítés a szolgáltatás folyamatos adatvesztés nélkül. 

A követelmények ebben a forgatókönyvben szinte ugyanazok, mint a követelmények a "Kódoló feladatátvétel" esetben, azzal a kivétellel, hogy a második kódolók futnak egy időben az elsődleges kódolók.

## <a name="10-service-redundancy"></a>10. Szolgáltatás redundancia
A rendkívül redundáns globális elosztás, néha kell régióközi biztonsági mentés a regionális katasztrófák kezelésére. Az "Encoder redundancia" topológiára bontva az ügyfelek dönthetnek úgy, hogy egy redundáns szolgáltatás központi telepítését egy másik régióban, amely kapcsolódik a második kódolók. Az ügyfelek is együttműködhetnek a Content Delivery Network szolgáltató üzembe helyezése a globális traffic manager előtt a két szolgáltatás központi telepítések zökkenőmentesen irányítsa az ügyfélforgalmat. A kódolók követelményei megegyeznek a "Kódoló redundancia" esetkövetelményeivel. Az egyetlen kivétel az, hogy a kódolók második készletét egy másik élő betöltési végpontra kell mutatni. Az alábbi ábra ezt a beállítást mutatja be:

![szolgáltatás redundanciája][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Különleges típusú lenyelési formátumok
Ez a szakasz az egyes forgatókönyvek kezelésére tervezett élő betöltési formátumok speciális típusait ismerteti.

### <a name="sparse-track"></a>Ritka pálya
Ha gazdag ügyfélélményt tartalmazó élő streamelési bemutatót tart, gyakran időszinkronizált eseményeket vagy jeleket kell továbbítani a fő médiaadatokkal. Erre példa a dinamikus élő hirdetésbeillesztés. Ez a fajta esemény jelzés eltér a rendszeres audio / video streaming miatt ritka jellegű. Más szóval, a jelzőadatok általában nem történik meg folyamatosan, és az intervallum nehéz lehet megjósolni. A ritka sáv koncepcióját úgy tervezték, hogy benyelje és sugározza a sávon keresztüli jelzőadatokat.

A következő lépések a ritka vágányok betöltésének ajánlott implementációját ajánlják:

1. Hozzon létre egy különálló töredezett MP4 bitfolyamot, amely csak ritka zeneszámokat tartalmaz, hang- és videosávok nélkül.
1. A(z) [1] 6. **Live Server Manifest Box** *parentTrackName* További információ a [1] 4.2.1.2.1.2.
1. A **Live Server manifest boxban**a **manifestOutput értéket** **igaz**értékre kell állítani.
1. Tekintettel a jelzési esemény ritka jellegére, a következőket ajánlottuk:
   
    a. Az élő esemény elején a kódoló elküldi a kezdeti fejlécdobozokat a szolgáltatásnak, amely lehetővé teszi a szolgáltatás számára a ritka pálya regisztrálását az ügyféljegyzékben.
   
    b. A kódolónak meg kell szakítania a HTTP POST-kérelmet, ha az adatok nem kerülnek elküldésre. Egy hosszú ideig futó HTTP-posta, amely nem küld adatokat, megakadályozhatja, hogy a Media Services gyorsan lekapcsolódjon a kódolóról szolgáltatásfrissítés vagy a kiszolgáló újraindítása esetén. Ezekben az esetekben a médiakiszolgáló ideiglenesen le van tiltva a szoftvercsatorna fogadási műveletében.
   
    c. A jelzőadatok nem érhető el, a kódolónak be kell zárnia a HTTP POST-kérelmet. Amíg a POST kérés aktív, a kódolónak adatokat kell küldenie.

    d. Ritka töredékek küldésekor a kódoló explicit tartalomhosszúságú fejlécet állíthat be, ha az elérhető.

    e. Amikor ritka töredékeket küld új kapcsolattal, a kódolónak meg kell kezdenie a fejlécmezőkből történő küldést, majd az új töredékeket. Ez olyan esetekben fordul elő, amikor a feladatátvétel köztes állapotban történik, és az új ritka kapcsolat egy olyan új kiszolgálóhoz jön létre, amely korábban nem látta a ritka sávot.

    f. A ritka sávtöredék akkor válik elérhetővé az ügyfél számára, ha az ügyfél számára elérhetővé válik a megfelelő szülő-sávtöredék, amely azonos vagy nagyobb időbélyeg-értékkel rendelkezik. Ha például a ritka töredék időbélyege t=1000, várható, hogy miután az ügyfél látja a "video" (feltéve, hogy a szülő pálya neve "videó") 1000-es vagy annál nagyobb időbélyeget lát, letöltheti a ritka töredéket t=1000. Vegye figyelembe, hogy a tényleges jel használható egy másik helyzetben a bemutató idővonal a kijelölt célra. Ebben a példában lehetséges, hogy a t=1000 ritka töredéke XML-tartalommal rendelkezik, amely néhány másodperccel később megjelenő hirdetés beszúrására alkalmas.

    g. A ritka nyomtöredékek hasznos terhelése a forgatókönyvtől függően különböző formátumokban (például XML, szöveg vagy bináris) lehet.

### <a name="redundant-audio-track"></a>Redundáns hangsáv
Egy tipikus HTTP adaptív streamelési forgatókönyvben (például sima streamelés vagy DASH) gyakran csak egy hangsáv van a teljes bemutatóban. Ellentétben a videosávokkal, amelyek több minőségi szinttel rendelkeznek, amelyek közül az ügyfél hibakörülmények között választhat, a hangsáv egyetlen meghibásodási pont lehet, ha a hangsávot tartalmazó adatfolyam betöltése megszakad. 

A probléma megoldásához a Media Services támogatja a redundáns hangsávok élő betöltését. Az ötlet az, hogy ugyanazt a hangsávot lehet küldeni többször különböző patakok. Bár a szolgáltatás csak egyszer regisztrálja a hangsávot az ügyféljegyzékben, használhat redundáns hangsávokat biztonsági másolatként a hangtöredékek beolvasásához, ha az elsődleges hangsáv problémákat okoz. A redundáns hangsávok betöltéséhez a kódolónak a következőket kell tennie:

1. Ugyanazt a hangsávot több töredék MP4 bitfolyamban hozza létre. A redundáns hangsávoknak szemantikailag egyenértékűnek kell lenniük, ugyanazzal a töredékidőbélyeggel, és felcserélhetőknek kell lenniük a fejléc és a töredék szintjén.
1. Győződjön meg arról, hogy a Live Server Manifest "audio" bejegyzése (a[1]-ben 6. szakasz) megegyezik az összes redundáns hangsáv esetében.

A redundáns hangsávok hoz a következő implementáció ajánlott:

1. Minden egyes egyedi hangsávot önmagában küldhet el egy adatfolyamba. Emellett küldjön egy redundáns adatfolyamot mindegyik hangsávfolyamhoz, ahol a második adatfolyam csak a HTTP POST URL-címazonosítója szerint tér el az elsőtől: {protocol}://{server address}/{publishing point path}/Streams({identifier}).
1. A két legalacsonyabb videobitráta küldéséhez külön adatfolyamok használatával küldheti el a két legalacsonyabb átviteli sebességet. Minden ilyen patakok is tartalmaznia kell egy példányát minden egyedi hangsávot. Ha például több nyelv is támogatott, ezekaz adatfolyamok minden nyelvhez hangsávokat kell tartalmazniuk.
1. Az (1) és (2) bekezdésben említett redundáns adatfolyamok kódolásához és elküldéséhez külön kiszolgálói (kódoló) példányokat használjon. 

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
