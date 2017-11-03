---
title: "Az Azure Media Services töredezett MP4 élő betöltési specification |} Microsoft Docs"
description: "Ez az előírás protokoll és töredezett MP4-alapú élő adatfolyam-továbbítási adatfeldolgozást Azure Media Services formátumát mutatja be. Azure Media Services segítségével élő eseményeket adatfolyam és valós időben tartalom szórási Azure, a cloud platform használatával. Ez a dokumentum is ismerteti, ajánlott eljárások a magas redundáns és robusztus működés közbeni felépítése betöltési mechanizmusokat."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 6250b73504bec765b8299060a29e84e771791cc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Az Azure Media Services töredezett MP4 élő betöltési meghatározása
Ez az előírás protokoll és töredezett MP4-alapú élő adatfolyam-továbbítási adatfeldolgozást Azure Media Services formátumát mutatja be. A Media Services egy élő adatfolyam-szolgáltatás, amellyel az ügyfelek adatfolyam-élő eseményeket, és a felhő platform Azure segítségével valós idejű tartalom szórási nyújt. Ez a dokumentum is ismerteti, ajánlott eljárások a magas redundáns és robusztus működés közbeni felépítése betöltési mechanizmusokat.

## <a name="1-conformance-notation"></a>1. Megfelelési jelöléssel
A kulcs "," kell nem"lehet" "Kötelező," "SHALL," "kell nem," "SHOULD," "Kell nem," szavak "Ajánlott," "Lehet, hogy," és "Választható" Ebben a dokumentumban az RFC 2119 leírtak kell értelmezni.

## <a name="2-service-diagram"></a>2. Szolgáltatás ábrája
Az alábbi ábrán látható az élő adatfolyam-továbbítási szolgáltatás magas szintű architektúra a Media Services:

1. Az élő kódolók leküldi élő hírcsatornák csatornák létrehozott és üzembe az Azure Media Services SDK használatával.
2. Csatornák, programok és a Media Services streamvégpontok összes élő adatfolyam-továbbítási funkcióját, beleértve a feldolgozást, formázás kezelni, a felhő DVR-t, a biztonság, a méretezhetőség és a redundancia.
3. Szükség esetén az ügyfelek központi telepítése az Azure Content Delivery Network layer streamvégpontra és az ügyfél-végpontok közötti választhat.
4. Ügyfél-végpontok adatfolyam adaptív Streameléshez HTTP protokoll használatával streamvégpontból. Például a Microsoft Smooth Streaming, dinamikus adaptív adatfolyam-(kötőjel vagy MPEG-DASH) HTTP és a Apple HTTP Live Streaming (HLS) keresztül.

![a betöltési folyamat][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream formátumban – ISO 14496-12 töredékes MP4
A egybeírt az élő adatfolyam-betöltési ismertet a dokumentum [ISO-14496-12] alapul. A részletes ismertetése a töredezett MP4 formátumban és bővítmények egyaránt videotartalom fájl-és élő adatfolyam-továbbítási adatfeldolgozást [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Működés közbeni betöltési formátum definíciók
Az alábbi lista ismerteti az Azure Media Services betöltési élő definíciókat különleges formátumú:

1. A **ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** mezőkbe (HTTP POST) minden egyes kérelemmel el kell küldeni. Ezek a mezők kell elküldeni az adatfolyam elején, és a kódoló csatlakoznia kell az adatfolyam folytatása bármikor betöltési. További információkért lásd a 6 [1].
2. [1] 3.3.2 című meghatározása nevű egy nem kötelező mező **StreamManifestBox** a működés közbeni betöltési. Az Azure load balancer útválasztási logika, miatt ebben a mezőben használata elavult. A mezőben kell akkor jelenik meg, ha a Media Services választásával dolgozhat fel. Ha ezt a jelölőnégyzetet, a rendszer a Media Services csendes figyelmen kívül hagyja azt.
3. A **TrackFragmentExtendedHeaderBox** [1] 3.2.3.2 definiált mezőben minden töredék jelen kell lennie.
4. 2-es verziójának a **TrackFragmentExtendedHeaderBox** be kell használni a media szegmenseket, amelyeknek több adatközpontot azonos URL-címeket. A töredék index mezője REQUIRED formátumban index alapján például az Apple HLS cross-datacenter feladatátvételt és az index alapján MPEG-DASH. Ahhoz, hogy a kereszt-datacenter feladatátvétel, a töredék index több kódolók keresztül kell szinkronizálva, és minden egymást követő adathordozók töredék 1 akár kódoló újraindítások vagy hibák keresztül növelhető.
5. [1] 3.3.6 című határozza meg egy nevű listában **MovieFragmentRandomAccessBox** (**mfra**), amely küldhet élő adatfeldolgozást végén megjelenítésével jelzi a csatorna end adatfolyamot (EOS). A Media Services bemeneti logika, miatt EOS használata elavult, és a **mfra** mezőben, az élő adatfeldolgozást nem lesz elküldve. Ha kap, a Media Services csendes figyelmen kívül hagyja azt. Alaphelyzetbe állítja a feldolgozást, a pontjának állapota, azt javasoljuk, hogy használjon [csatorna alaphelyzetbe](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Azt javasoljuk, hogy használjon [Program leállításához](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) bemutatása és az adatfolyam végére.
6. Kell, hogy a MP4-töredéket időtartama állandó, az ügyfél jegyzékfájljai méretének csökkentése érdekében. Egy állandó MP4-töredéket időtartama is javítja az ügyfél letöltési heurisztikus használatával ismételje meg a címkék. Az időtartam nem egész keret díjszabás kompenzálják előfordulhat, hogy hajlamos.
7. A MP4-töredéket időtartama körülbelül 2 – 6 másodperc között kell lennie.
8. MP4 darabolható időbélyegeket és indexek (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` és `fragment_index`) növekvő sorrendben kell érkeznek. Bár a Media Services is lehetséges legyen ismétlődő töredék, korlátozott újra sorrendbe állítja a media ütemterv szerint töredék képességét.

## <a name="4-protocol-format--http"></a>4. Protokoll-formátum – a HTTP
ISO töredékes MP4-alapú működés közbeni célokra Media Services standard hosszan futó HTTP POST-kérelmet küldött, amely a szolgáltatás töredezett MP4 formátumban kódolt media adatok betöltési. Minden egyes HTTP POST küld, teljes töredékes MP4 bitstream ("stream"), a fejléc mezőkbe az elejétől kezdve (**ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** mezők ), és töredék sorozatát folytatása (**moof** és **mdat** be). A HTTP POST kérelem URL-szintaxisa című rész 9.2 in [1]. Példa a FELADÁS egy vagy több URL-CÍMRE van: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Követelmények
Az alábbiakban a részletes követelményeket:

1. A kódoló kell válaszolnia a szórás egy üres "törzs" (nulla tartalom hossza) HTTP POST kérést küld a azonos adatfeldolgozást URL-cím segítségével. Ez segítheti a kódoló gyorsan észleli, hogy az élő adatfeldolgozást végpont érvényes, és semmilyen hitelesítési vagy egyéb szükséges feltételeket. / HTTP protokollt, a kiszolgáló nem küldött vissza egy HTTP-válasz addig, amíg a teljes kérelemfeldolgozási, beleértve a FELADÁS egy vagy több szervezet érkezik. Élő esemény, ebben a lépésben nélkül hosszan futó jellegéből a kódoló nem feltétlenül bármilyen hiba észleli, míg leáll az adatok küldése.
2. A kódoló kell kezelni esetleges hibák vagy a hitelesítés kihívást miatt (1). Ha (1) sikeres 200 választ, továbbra is.
3. A kódoló egy új HTTP POST-kérelmet kell kezdődnie az töredezett MP4-adatfolyam. A tartalom a fejléc jelölőnégyzetéből, töredék követnie kell kezdődnie. Vegye figyelembe, hogy a **ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** mezőiben (ebben a sorrendben) kell küldött minden egyes kérelemmel, még akkor is, ha a kódoló kell csatlakozzon újra, mert az előző kérelem le lett állítva, az adatfolyam végét előtt. 
4. A kódoló darabolásos átviteli kódolás feltöltése, mert nem lehet előre jelezni az élő esemény teljes tartalom hossza kell használnia.
5. Ha az esemény keresztül, a legutóbbi töredéke elküldése után a kódoló szabályosan végén szerepelnie kell a darabolásos átviteli kódolás üzenetsort (HTTP-ügyfél a legtöbb verem kezelnie automatikusan). A kódoló kell a végső válaszkód vissza a szolgáltatás Várjon, majd állítsa le a kapcsolatot. 
6. A kódoló kell nem használja a `Events()` főnév élő szempontjából a Media Services 9.2 in [1] leírtak szerint.
7. Ha a HTTP POST-kérelmet megszakítja, vagy időtúllépése előtt az adatfolyam végét TCP-hiba, a kódoló kell adjon ki egy új POST kérelmet egy új kapcsolat használatával, és kövesse a fenti követelmények. Emellett a kódoló kell küldje újra az előző két MP4 részlete minden nyomon követése az adatfolyamban, majd folytassa a media ütemterv kihagyást, anélkül. Küldje el újra az utolsó két MP4 részlete minden követése biztosítja, hogy van-e az adatvesztés. Más szóval adatfolyam tartalmaz egy hang- és a videó nyomon, és az aktuális POST kérelem sikertelen lesz, ha a kódoló kell újra és küldje el újból a hang nyomon követése, a az utolsó két kódrészletek, amelyeket korábban már sikeresen megtörtént, és az utolsó két töredék a videó követheti nyomon, amely korábban már sikeresen küldtek, győződjön meg arról, hogy van-e az adatvesztés. A kódoló kezelnie kell a "továbbítás" puffer media töredékek, amely azt az újracsatlakozáskor újraküldése.

## <a name="5-timescale"></a>5. időskálára
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) teljesítési használatát ismerteti **SmoothStreamingMedia** (2.2.2.1. szakaszát), **StreamElement** (2.2.2.3 szakaszát), **StreamFragmentElement**(2.2.2.6 szakaszban), és **LiveSMIL** (2.2.7.3.1 szakaszt). A időskálára érték nincs jelen, az alapértelmezett értéket, akkor 10,000,000 (10 MHz). Bár a Smooth Streaming fájlformátum-specifikációnak nem tiltja a használati más időskálára értékek, a legtöbb kódoló megvalósítások használja-e ez az alapértelmezett érték (10 MHz) létrehozására, Smooth Streaming betöltik az adatokat. Miatt a [Azure Media dinamikus becsomagolás](media-services-dynamic-packaging-overview.md) funkciót, azt javasoljuk, hogy használjon egy 90-KHz időskálára a video-adatfolyamot és 44,1 KHz vagy 48.1 KHz hang adatfolyamokat. Ha másik időskálára értékeket fogja használni a különböző adatfolyamokba, az adatfolyam-szintű időskálára kell elküldeni. További információkért lásd: [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. "Stream" meghatározása
A adatfolyama alapvető egysége élő bemutatók létrehozására élő adatfeldolgozást művelet adatfolyam feladatátvételi és a redundancia forgatókönyvek kezelésével. Az adatfolyam egy egyedi, töredezett MP4 bitstream tartalmazhat egyetlen nyomon vagy több számok típusúként van definiálva. Teljes bemutató tartalmazhat egy vagy több adatfolyamot, az élő kódolók konfigurációjától függően. A következő példák bemutatják a különböző lehetőségek használatával adatfolyamok teljes bemutató létrehozásához.

**Példa** 

Egy ügyfél szeretne létrehozni, amely tartalmazza a következő hang-és videófolyamot bitrates élő adatfolyam-továbbítási bemutató:

Videó – 3000 kbit/s, 1500 kbit/s, 750 KB/s

Hang – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>1. lehetőség: Az összes nyomon követi a egy adatfolyam
Ezt a beállítást, az egyetlen kódoló hoz létre minden hang-és videófolyamot nyomon követi, és majd azokat egy töredezett MP4 bitstream bundles. A töredezett MP4 bitstream küldi el a rendszer egyetlen HTTP POST-kapcsolaton keresztül. Ebben a példában a élő bemutató csak egy adatfolyam van.

![Adatfolyam-egy nyomon követése][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>2. lehetőség: Minden követése külön Stream
Ezt a beállítást a kódoló egy követése elhelyezi minden töredék MP4 bitstream, és majd visszaküldés összes adatfolyamokat külön HTTP-kapcsolaton keresztül. Ez egy Encoder és több kódolókkal is végrehajthatja. Az élő adatfeldolgozást az élő adás látja, négy adatfolyamokat, ahogy.

![Az elkülönített adatfolyamok nyomon követi][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>3. lehetőség: Köteg hang nyomon követése a legalacsonyabb sávszélességű videó követése egy adatfolyamba való mentésre
Az ezt a beállítást az ügyfél úgy dönt, hogy a hang nyomon követése a legalacsonyabb sávszélességű videó nyomon követése a csomagot a egy töredék MP4 bitstream, és hagyja a többi két videó nyomon követi, külön adatfolyamokat. 

![Adatfolyam-hang- és videofunkcióinak nyomon követi][image4]

### <a name="summary"></a>Összefoglalás
Ez a nem egy lista tartalmazza az ebben a példában az összes lehetséges adatfeldolgozást lehetőség. Bármely csoportosítási adatfolyamok való felvételek, valójában, élő adatfeldolgozást támogatja. Az ügyfelek és a szállítók kódoló választhatja a saját megvalósítások mérnöki összetettségét, a kódoló kapacitás, és a redundancia és a feladatátvételi szempontokat részletező cikkben alapján. A legtöbb esetben van azonban csak egy hang, a teljes élő bemutató nyomon követése. Igen fontos a egészséges legyen a bemeneti adatfolyam, amely tartalmazza a hang nyomon követése érdekében. Gyakran ez a lépés a hang nyomon követése és a saját adatfolyamban (ahogy a 2. lehetőség), illetve kötegelése azt a legkisebb sávszélességű videó nyomon követése (ahogy a 3. lehetőség) a eredményez. Is, jobb redundancia és a hibatűrést, küldésekor a azonos hang nyomon követése a két különböző adatfolyamokba (redundáns zeneszámok beállítás 2) vagy a hang nyomon követése és legalább két a legalacsonyabb sávszélességű videó követi nyomon (a 3 beállítás közül legalább kettő kötegelve hanggal kötegelése video-adatfolyamot) erősen ajánlott a működés közbeni a Media Services betöltési.

## <a name="7-service-failover"></a>7. Szolgáltatás feladatátvétele
Az élő adatfolyam jellegű, a megadott jó a feladatátvételre fontos a szolgáltatás rendelkezésre állásának biztosításához. A Media Services különböző típusú hibák, beleértve a hálózati hibák, a kiszolgáló hibákat és a tárolási problémák kezelésére terveztek. Az élő kódoló oldalán a megfelelő feladatátvételi logika együtt használják, amikor az ügyfelek érhető el a felhőből nagymértékben megbízható élő adatfolyam-továbbítási szolgáltatás.

Ebben a szakaszban arról lesz szó szolgáltatás feladatátvételi forgatókönyvek. Ebben az esetben a hiba akkor fordul elő, valahol a szolgáltatáson belül, és akkor jelentkezik hálózati hiba. Az alábbiakban néhány javaslatot a kódoló megvalósítás szolgáltatás feladatátvétele kezelése:

1. Használjon egy 10 másodperces időtúllépési a TCP-kapcsolat létrehozása. Kísérlet a kapcsolat létrehozása 10 másodpercnél hosszabb időt vesz igénybe, ha megszakítja a műveletet, és próbálkozzon újra. 
2. Használjon egy rövid időtúllépési küldése a HTTP-kérelem üzenet adattömböket. Ha a cél MP4 töredék időtartama N másodperces, akkor egy küldési határidő közötti N és 2 N másodperces; például ha az MP4-töredéket időtartam 6 másodperc, használja egy időtúllépési értéke 6 – 12 másodperc. Időtúllépés történik, ha a kapcsolatot alaphelyzetbe állítani, egy új kapcsolat megnyitásához, és folytathatja az adatfolyam betöltési az új kapcsolaton. 
3. A működés közbeni puffer, amelynek minden nyomon követése az utolsó két részlete volt sikeres, és teljesen küldött a szolgáltatás karbantartása.  Ha a HTTP POST-kérelmet az adatfolyam megszakadt, vagy a időtúllépése az adatfolyam végét egy új kapcsolat megnyitásához és egy másik HTTP POST-kérelmet megkezdéséhez, újra elküldeni az adatfolyam fejlécek, küldje el újból az utolsó két részlete minden nyomon követése és folytatása az adatfolyamot a d, anélkül az adathordozó idővonal iscontinuity. Ez csökkenti az adatvesztés esélyét.
4. Azt javasoljuk, hogy a kódoló nem korlátozza a kapcsolatot, vagy folytatása a streaming után TCP-hiba akkor fordul elő az újrapróbálkozások száma.
5. Miután a TCP-hiba:
  
    a. Az aktuális kapcsolat be kell zárni, és egy új kapcsolatot kell létrehozni egy új HTTP POST-kérelmet.

    b. Az új HTTP POST URL-cím azonosnak kell lennie az első POST URL-címet.
  
    c. Az új HTTP POST tartalmaznia kell az adatfolyam fejlécek (**ftyp**, **kiszolgáló Manifest mezőbe élő**, és **moov** mezőkbe), amely a kezdeti FELADÁS egy vagy több adatfolyam fejlécének azonosak.
  
    d. A két legutóbbi töredék minden követése küldött kell küldeni, és adatfolyam-kell folytatni az adathordozó idővonal kihagyást, anélkül. A MP4-töredéket időbélyegeket folyamatosan, növelnie kell akár HTTP POST-kérésnél keresztül.
6. A kódoló kell leáll a HTTP POST-kérelmet, ha az adatok küldése nem működik az MP4-töredéket időtartam arányos sebességgel.  Egy HTTP POST-kérelmet, amely nem küld adatokat megakadályozhatja, hogy a Media Services gyorsan leválasztása a kódoló szolgáltatás frissítése esetén. Emiatt a HTTP POST a ritka (ad jel) nyomon követi lehet rövid életű, amint a ritka töredéket küldött leáll.

## <a name="8-encoder-failover"></a>8. kódoló feladatátvétel
Kódoló feladatátvétel során a Feladatátvevőfürt-forgatókönyvekben, amelyek kell figyelembe venni a végpontok közötti élő adatfolyamként történő továbbítását a második típusú. Ebben a forgatókönyvben a hiba állapot akkor fordul elő, a kódoló oldalon. 

![kódoló feladatátvétel][image5]

A következő elvárásainak érvényesek a élő adatfeldolgozást végpontról kódoló feladatátvétel történik:

1. Új kódoló példányt kell hozható létre adatfolyam-, a folytatáshoz, mert (adatfolyam a video-szaggatott vonallal 3000-k) ábra szemlélteti.
2. Az új kódoló kell használnia az URL-CÍMÉRE HTTP POST-kérésnél sikertelen példányt.
3. Az új kódoló POST-kérelmet a azonos töredezett MP4 fejléc mezőiben a sikertelen előfordulás tartalmaznia kell.
4. Az új kódoló kell megfelelően szinkronizálva minden más futó kódolókkal az azonos élő bemutató szinkronizált hang-és videófolyamot minták igazított töredék határok létrehozására.
5. Lehet, hogy az új adatfolyam szemantikailag megfelelője az előző stream, és cserélhető fejléc-és töredéket.
6. Az új kódoló adatveszteség minimálisra próbálja meg. A `fragment_absolute_time` és `fragment_index` adathordozó töredék növelje a ponttól, ahol a kódoló utolsó leállítás. A `fragment_absolute_time` és `fragment_index` folyamatosan növelje, de megengedett bevezetni kihagyást, ha szükséges. Media Services figyelmen kívül hagyja azt már fogadása és feldolgozása, töredék, hogy jobb oldalán, mint a bevezetése az adathordozó idővonal folytonosság megszakítását töredék Újraküldés err. 

## <a name="9-encoder-redundancy"></a>9. kódoló redundancia
Az egyes kritikus élő eseményeket, hogy igény szerint még magasabb rendelkezésre állást és minőséget felhasználói felület, azt javasoljuk, hogy aktív-aktív redundáns kódolók adatvesztés nélküli, zökkenőmentes feladatátvétel használja.

![kódoló redundancia][image6]

Ezen a diagramon módon kódolók két csoportját leküldéses két példányt készítsen az egyes adatfolyamokkal egyidejűleg a élő szolgáltatásba. A telepítő utasítás támogatott, mert a Media Services ismétlődő töredék adatfolyam-Azonosítót és töredék időbélyeg alapján is szűrheti. Az eredményül kapott élő adatfolyam kapcsolatos és archiválási egy példányt az adatfolyamokat, amely a legjobb lehetséges összesítési a két forrásból. Például elméleti szélsőséges esetben van (nem kell azonosnak kell lennie egy) egy kódoló is az egyes adatfolyamok idő álljon fut, a szolgáltatás az eredményül kapott élő adatfolyam folyamatos adatvesztés nélkül. 

A forgatókönyv követelményei majdnem megegyezik a követelmények az "Kódoló feladatátvételi" esetében, azzal a kivétellel kódolók második együttesét a elsődleges kódolók egyszerre futnak.

## <a name="10-service-redundancy"></a>10. szolgáltatás redundancia
Magas redundáns globális terjesztési néha rendelkeznie kell kereszt-régió biztonsági mentés regionális katasztrófák kezelésére. A "Kódoló redundancia" topológia kibővítve, az ügyfelek beállíthatja úgy a redundáns szolgáltatástelepítésben egy másik régióban található, amely csatlakozik a második kódolók vannak beállítva. Az ügyfelek is dolgozhat a Content Delivery Network szolgáltató telepítéséhez egy globális Traffic Manager elé a két szolgáltatástelepítések zökkenőmentesen ügyfélforgalom történő irányításához. A kódolók vonatkozó követelmények ugyanazok, mint a "Kódoló redundancia" esetében. Az egyetlen kivétel, hogy a második készlet kell emelni egy másik élő kódolók kell betöltési végpont. Az alábbi ábrán ez a beállítás:

![szolgáltatás redundancia][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Speciális típusú adatfeldolgozást formátumok
Ez a szakasz ismerteti a meghatározott helyzetek kezelésére kifejlesztett élő adatfeldolgozást formátumok speciális típusokat.

### <a name="sparse-track"></a>Ritka nyomon követése
Ha a bemutató élő adatfolyam-továbbítási gazdagügyfél nyújthassunk, gyakran szükséges idő szinkronizálva események átvitele vagy sávon a fő media adatokkal jelzi. Ilyen például a dinamikus élő ad beszúrási. Az ilyen típusú esemény jelzés rendszeres hang-és videófolyamot ritka jellegénél streaming eltér. Ez azt jelenti a jelképző adatok általában nem fordulhat elő, folyamatosan, és nehezen becsülhető lehet. Ritka követése fogalma betöltési és szórási sávon jelzésátviteli adatokat úgy lett kialakítva.

Az alábbi lépéseket kell a következő ajánlott megvalósítása választásával dolgozhat fel ritka nyomon követése:

1. Hozzon létre egy külön töredezett MP4 bitstream csak ritka nyomon követi, hang-és videófolyamot számok nélkül tartalmazó.
2. Az a **kiszolgáló Manifest mezőbe élő** meghatározása a 6 [1], használja a *parentTrackName* paramétert adja meg a szülő követése nevét. További információkért lásd: [1] 4.2.1.2.1.2 szakasz.
3. Az a **kiszolgáló Manifest mezőbe élő**, **manifestOutput** értékre kell állítani **igaz**.
4. A jelképző esemény ritka jellegéből, ajánlott a következők:
   
    a. Az élő esemény elején a kódoló elküldi a kezdeti fejléc mezőkbe a szolgáltatást, amely lehetővé teszi a szolgáltatás a ritka követése regisztrálja az ügyfél jegyzékben.
   
    b. A kódoló kell állítanunk a HTTP POST-kérelmet, amikor az adatok küldése nem működik. Amely adatokat küldeni a hosszan futó HTTP POST megakadályozhatja, hogy a Media Services gyorsan leválasztása a kódoló esetén a szolgáltatás frissítése vagy a kiszolgáló újraindítását. Ebben az esetben a kiszolgáló átmenetileg blokkolva van a szoftvercsatorna fogadási művelet.
   
    c. Az időre, amíg jelképző adatok esetén nem érhető el a kódoló SHOULD zárja be a HTTP POST kérelmet. Bár a POST-kérelmet aktív, a kódoló küldjön adatokat.

    d. Ritka töredék küldésekor a kódoló beállíthatja egy explicit content-length fejlécet, amennyiben az rendelkezésre áll.

    e. Az új kapcsolat ritka töredék küldésekor a kódoló webalkalmazásokba történő küldéséhez a fejléc mezőbe az új töredék követ. Ez az olyan esetekben, ahol feladatátvételi között történik, és az új ritka kapcsolat létrehozása folyamatban van egy új kiszolgálóra, amely nem észlelte a ritka követése előtt.

    f. A ritka követése töredéke kerül az ügyfél számára elérhető, ha a megfelelő szülő követése töredéke egyenlő vagy nagyobb timestamp érték szeretné elérhetővé tenni az ügyfélnek. Például, ha a ritka töredéke t időbélyegző = 1000, várható, amely után az ügyfél számára megjelenített "videó" (feltéve, hogy a szülő követése neve: "videó") darabolható időbélyeg 1000, vagy túl, töltse le a ritka töredék t = 1000. Vegye figyelembe, hogy a tényleges jel felhasználhatók egy másik pozícióba a bemutató ütemterv a kijelölt célra. Ebben a példában az is lehetséges, hogy a ritka töredéke t = 1000 rendelkezik egy XML-adattartalmat, amely beszúrásához ad egy helyen, amely néhány másodperc később.

    g. Ritka követése töredék hasznos lehet a különböző formátumokban (pl. XML, szöveges vagy bináris), a helyzettől függően.

### <a name="redundant-audio-track"></a>Redundáns hang nyomon követése
Egy tipikus HTTP adaptív adatfolyam-továbbítási forgatókönyv (például Smooth Streaming vagy kötőjel) gyakran van a teljes bemutató csak egy hang követése. Videó nyomon követi, amelyeket az ügyfél hibaüzenet feltételekben választhat több szolgáltatásminőségi szinteket, ellentétben a hang nyomon követése az adatfeldolgozást az adatfolyam, amely tartalmazza a hang követése megszakad esetén használható a hibaérzékeny pontok kialakulását. 

A probléma megoldásához, a Media Services támogatja a redundáns zeneszámok élő adatfeldolgozást. A lényege, hogy az azonos hang követése küldhető többször a különböző adatfolyamokba. Bár a szolgáltatás csak regisztrálja a hang követése egyszer az ügyfél jegyzékben, azt is használhatja redundáns zeneszámok tartalékként hang töredék lekéréséhez, ha az elsődleges hang követése problémákkal rendelkezik. Redundáns zeneszámok betöltési, a kódoló kell megfelelnie:

1. Hozzon létre több töredék MP4 bitstreams az azonos hang követése. A redundáns zeneszámok kell lennie a azonos töredék Timestamp szemantikailag, és cserélhető fejléc-és töredéket.
2. Győződjön meg arról, hogy a "hang" bejegyzést a működés közbeni Server Manifest (6 [1]. szakasz) összes redundáns zeneszámok azonos.

A következő megvalósítási redundáns zeneszámok ajánlott:

1. Minden egyedi hang követése küldése-adatfolyamot ad át önmagába. Is, az egyes ezek zenei adatfolyamot, ha a második adatfolyam eltér-e az első a redundáns adatfolyam küldése csak a HTTP POST URL-címet az azonosító által: {protokoll} :// {server-cím} / {pont path}/Streams({identifier}) közzététele.
2. A két legalacsonyabb videó bitrates külön adatfolyamok küldhet. Egyes ezekbe az adatfolyamokba ugyancsak tartalmaznia kell egy példányát minden egyedi hang nyomon követése. Ha több nyelvet is támogat, például ezekbe az adatfolyamokba tartalmaznia kell az egyes nyelvekhez zeneszámok.
3. Külön kiszolgálópéldányok (kódoló) segítségével kódolására, és a redundáns adatfolyamok szerepel (1) és (2). 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
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
