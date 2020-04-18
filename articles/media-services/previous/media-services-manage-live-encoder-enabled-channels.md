---
title: Élő közvetítés az Azure Media Services használatával többbites adatfolyamok létrehozásához | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként állítható be egy olyan csatorna, amely egyetlen sávszélességű élő közvetítést fogad egy helyszíni kódolótól, majd élő kódolást hajt végre adaptív sávszélességű adatfolyamként a Media Services szolgáltatással.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6210d6ee4877c6ba84178340cf0a6610e402da31
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641105"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Többszörös átviteli sebességű streamek létrehozása az Azure Media Services élő streamelési funkciójával

> [!NOTE]
> 2018. május 12-től az élő csatornák már nem támogatják az RTP/MPEG-2 átviteli adatfolyam-betöltési protokollt. Az RTP/MPEG-2 protokollról átkell térni az RTMP-re vagy a töredezett MP4 (Smooth Streaming) protokollokra.

## <a name="overview"></a>Áttekintés
Az Azure Media Services (AMS) szolgáltatásban a **csatorna** az élő streamelési tartalom feldolgozására szolgáló folyamatot jelöli. A **csatorna** kétféleképpen fogad élő bemeneti adatfolyamokat:

* A helyszíni élő kódoló egyegybitrátú adatfolyamot küld a csatornának, amely engedélyezve van az élő kódoláshoz a Media Services szolgáltatással a következő formátumok egyikén: RTMP vagy Smooth Streaming (Töredezett MP4). A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* A helyszíni élő kódoló többátviteli sebességű **RTMP-t** vagy **sima streamelést** (töredezett MP4) küld a csatornának, amely nincs engedélyezve az élő kódoláshoz az AMS-sel. A bevitt adatfolyamok további feldolgozás nélkül haladnak át a **csatornákon.** Ezt a módszert **pass-through-nak nevezzük.** A következő élő kódolókat használhatja, amelyek többátviteli sim idejű smooth streaminget adnak ki: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco és Elemental. A következő élő kódolók kimeneti RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek kódolók.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

  > [!NOTE]
  > Az áthaladási módszer használata a leggazdaságosabb módja az élő közvetítésnek.
  > 
  > 

A Media Services 2.10 kiadással kezdve, amikor létrehoz egy csatornát, megadhatja, hogy a csatorna milyen módon fogadja a bemeneti adatfolyamot, és hogy szeretné-e, hogy a csatorna élő kódolást hajtson végre az adatfolyamban. Erre két lehetősége van:

* **Nincs** – Adja meg ezt az értéket, ha azt tervezi, hogy egy helyszíni élő kódoló, amely a kimeneti több-bitráta adatfolyam (átadó adatfolyam). Ebben az esetben a bejövő adatfolyam kódolás nélkül haladt át a kimenetre. Ez a viselkedés a csatorna előtt 2.10 kiadás.  Az ilyen típusú csatornákkal való munkáról további információt az [Élő közvetítés többátviteli adatfolyamokat létrehozó helyszíni kódolókkal című](media-services-live-streaming-with-onprem-encoders.md)témakörben talál.
* **Standard** – Akkor válassza ezt az értéket, ha a Media Services használatával szeretné kódolni az egyátviteli sebességet többátviteli adatfolyamba. Ne feledje, hogy az élő kódolás számlázási hatással van, és ne feledje, hogy az élő kódolási csatorna "Futás" állapotban hagyása számlázási díjakat von maga után.  Javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje az óránkénti díjakat.

> [!NOTE]
> Ez a témakör az élő kódolásra engedélyezett csatornák attribútumait (**szabványos** kódolási típus) ismerteti. Az élő kódolásra nem engedélyezett csatornákkal való munkáról az [Élő közvetítés többbites adatfolyamokat létrehozó helyszíni kódolókkal](media-services-live-streaming-with-onprem-encoders.md)című témakörben talál.
> 
> Győződjön meg arról, hogy áttekinti a [Szempontok szakaszt.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Számlázási vonatkozások
Az élő kódolási csatorna akkor kezdi meg a számlázást, amikor az állapot átvált az API-n keresztül a "Futás" állapotra.   Az állapotot az Azure Portalon vagy az Azure Mediahttps://aka.ms/amse)Services Explorer eszközben ( is megtekintheti.

Az alábbi táblázat bemutatja, hogyan csatorna állapotok leképezése számlázási állapotok az API-ban és az Azure Portalon. Az állapotok némileg eltérnek az API és a portál felhasználói felülete között. Amint egy csatorna "Futás" állapotban van az API-n keresztül, vagy a "Ready" vagy a "Streaming" állapotban az Azure Portalon, a számlázás aktív lesz.
Ha le szeretné állítani, hogy a csatorna további számlázási önnek, le kell állítania a csatornát az API-n keresztül vagy az Azure Portalon keresztül.
Ön felelős a csatornák leállításáért, ha végzett az élő kódolási csatornával.  Ha nem állítja le a kódolási csatornát, az folyamatos számlázást eredményez.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Csatornaállapotok és a számlázási módra való leképezésük
A csatorna aktuális állapota. A lehetséges értékek a következők:

* **Megállt.** Ez a csatorna létrehozása utáni kezdeti állapota (kivéve, ha az automatikus indítás lett kiválasztva a portálon.) Ebben az állapotban nem történik számlázás. Ebben az állapotban a csatorna tulajdonságai frissíthetők, de a streamelés nem engedélyezett.
* **Kezdő .** A csatorna elindult. Ebben az állapotban nem történik számlázás. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés. Hiba esetén a csatorna visszaáll a Leállított állapotba.
* **Futás**. A Csatorna képes élő közvetítések feldolgozására. Ez most számlázási használat. A további számlázás megakadályozása érdekében le kell állítania a csatornát. 
* **Megállás .** A csatornát leállítják. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés.
* **Törlés .** A csatorna törlődik. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés.

Az alábbi táblázat bemutatja, hogyan történik a Csatorna állapotaa a számlázási módra való leképezés. 

| Csatorna állapota | Portál felhasználói felületi mutatói | Ez számlázás? |
| --- | --- | --- |
| Indítás |Indítás |Nem (átmeneti állapot) |
| Fut |Kész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |IGEN |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatikus kikapcsolás a nem használt csatornákhoz
2016. január 25-től kezdődően a Media Services olyan frissítést adott ki, amely automatikusan leállít egy csatornát (élő kódolás engedélyezve van), miután az már hosszú ideje nem használt állapotban fut. Ez azokra a csatornákra vonatkozik, amelyeknem rendelkeznek aktív programokkal, és amelyek hosszabb ideig nem kaptak bemeneti hozzájárulási hírcsatornát.

A fel nem használt időszakra vonatkozó küszöbérték névlegesen 12 óra, de változhat.

## <a name="live-encoding-workflow"></a>Élő kódolási munkafolyamat
Az alábbi ábra egy élő streamelési munkafolyamatot jelöl, ahol egy csatorna egyetlen bitráta-adatfolyamot kap a következő protokollok egyikében: RTMP vagy Smooth Streaming; ezután egy többbitrátású adatfolyamba kódolja az adatfolyamot. 

![Élő munkafolyamat][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Gyakori élő közvetítési forgatókönyv
A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

> [!NOTE]
> Jelenleg az élő stream maximális javasolt időtartama 8 óra.
>
> Az élő kódolás számlázási hatása van, és ne feledje, hogy az élő kódolási csatorna "Futás" állapotban hagyása óránkénti számlázási díjakat von maga után. Javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje az óránkénti díjakat. 

1. Csatlakoztasson a számítógéphez egy videokamerát. Indítson el és konfiguráljon egy helyszíni élő kódolót, amely **egyetlen** bitráta-adatfolyamot képes kiállítani az alábbi protokollok egyikében: RTMP vagy Smooth Streaming. 

    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Hozzon létre és indítson el egy csatornát. 
3. Kérje le a Channel ingest URL (Csatorna betöltési URL-címe) értékét. 

    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
4. Kérje le a csatorna előnézeti URL-címét. 

    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.
5. Hozzon létre egy programot. 

    Az Azure Portal használatakor egy program létrehozása is létrehoz egy eszközt. 

    A .NET SDK vagy REST használatakor létre kell hoznia egy eszközt, és meg kell adnia, hogy ezt az eszközt használja a Program létrehozásakor. 
6. A programhoz társított eszköz közzététele.   

    >[!NOTE]
    >Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie. 

7. Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot.
8. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.
10. Törölje a programot (ha kívánja, törölje az objektumot is).   

> [!NOTE]
> Nagyon fontos, hogy ne felejtsük el, hogy állítsa le a Live Encoding Channel. Ne feledje, hogy az élő kódolás óránkénti számlázási hatása van, és ne feledje, hogy az élő kódolási csatorna "Futás" állapotban hagyása számlázási díjakat von maga után.  Javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje az óránkénti díjakat. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Csatorna bemeneti (betöltési) konfigurációi
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Streamelési protokoll betöltése
Ha a **kódoló típusa** **Normál,** érvényes beállítások vannak:

* Egybitrábos **RTMP**
* Egybitráta **töredezett MP4** (Smooth Streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>Single bitrate RTMP (Egyszeres sávszélességű RTMP)
Szempontok:

* A bejövő adatfolyam nem tartalmazhat többbitrátású videót
* A videostream átlagos bitrátája 15 Mbps alatt kell
* A hangfolyam átlagos bitrátája 1 Mbps alatt kell legyen.
* A támogatott kodekek a következők:
* MPEG-4 AVC / H.264 videó
* Alapvonal, Fő, Nagy profil (8 bites 4:2:0)
* Magas 10 profil (10 bites 4:2:0)
* Magas 422 profil (10 bites 04:2:2)
* MPEG-2 AAC-LC hang
* Monó, sztereó, térhatású (5.1, 7.1)
* 44,1 kHz-es mintavételi sebesség
* MPEG-2 stílusú ADTS csomagolás
* Az ajánlott kódolók a következők:
* [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md)
* Flash Media Élő kódoló

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
Tipikus használati eset:

Az Elemental Technologies, az Ericsson, az Ateme, az Envivio helyszíni élő kódolóival elküldheti a bemeneti adatfolyamot a nyílt interneten keresztül egy közeli Azure-adatközpontba.

Szempontok:

Ugyanaz, mint az [egybitrátos RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Egyéb szempontok
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A bejövő videoadatfolyam maximális felbontása 1920x1080, és legfeljebb 60 mező/másodperc, ha váltottsoros, vagy 30 képkocka/másodperc, ha progresszív.

### <a name="ingest-urls-endpoints"></a>BETÖLTÉSI URL-címek (végpontok)
A csatorna biztosítja az élő kódolóban megadott bemeneti végpontot (betöltési URL-címet), így a kódoló leküldéses adatfolyamokat a csatornákba.

A betöltési URL-eket a csatorna létrehozása után kaphatja meg. Az URL-címek leeléréséhez a csatornának nem kell **futó** állapotban lennie. Ha készen áll az adatok csatornába való lenyomásának megkezdésére, annak **futó** állapotban kell lennie. Miután a csatorna megkezdi az adatok betöltését, megtekintheti az adatfolyam előnézeti URL-címén keresztüli előnézeti előnézeti előnézeti előnézeti előnézeti előnézetét.

Lehetősége van a töredezett MP4 (Smooth Streaming) élő közvetítés TLS-kapcsolaton keresztül történő betöltésére. A TLS-en keresztül történő betöltéshez frissítse a betöltési URL-címet HTTPS-re. Jelenleg az AMS nem támogatja a TLS-t egyéni tartományokkal.  

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek közzétehetik a videót erre a csatornára. Az engedélyezett IP-címek egyetlen IP-címként is megadhatók (például '10.0.0.1), IP-címet és CIDR alhálózati maszkot (például "10.0.0.1/22"), vagy IP-címet és pontozott decimális alhálózati maszkot használó IP-tartomány (például "10.0.0.1(255.255.252.0)").

Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

## <a name="channel-preview"></a>Csatorna előnézete
### <a name="preview-urls"></a>URL-ek előnézete
A csatornák egy előnézeti végpontot (előnézeti URL-t) biztosítanak, amelyet a stream további feldolgozás és kézbesítés előtt történő előnézeti megtekintéséhez és érvényesítéséhez használ.

A csatorna létrehozásakor megkaphatja az előnézeti URL-címet. Az URL-cím leéséhez a csatornának nem kell **futó** állapotban lennie.

Miután a csatorna elkezdi az adatok betöltését, megtekintheti az adatfolyam előnézetét.

> [!NOTE]
> Jelenleg az előnézeti adatfolyam csak töredezett MP4 (Smooth Streaming) formátumban szállítható, függetlenül a megadott bemeneti típustól.  Az Azure Portalon üzemeltetett lejátszósegítségével megtekintheti az adatfolyamot.
> 
> 

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek csatlakozhatnak az előnézeti végponthoz. Ha nincs megadva IP-cím, akkor bármely IP-cím engedélyezett lesz. Az engedélyezett IP-címek egyetlen IP-címként is megadhatók (például '10.0.0.1), IP-címet és CIDR alhálózati maszkot (például "10.0.0.1/22"), vagy IP-címet és pontozott decimális alhálózati maszkot használó IP-tartomány (például "10.0.0.1(255.255.252.0)").

## <a name="live-encoding-settings"></a>Élő kódolási beállítások
Ez a szakasz azt ismerteti, hogy a csatornán belüli élő kódoló beállításai hogyan állíthatók be, ha a csatorna **kódolási típusa** **Normál**.

> [!NOTE]
> A hozzájárulási hírcsatorna csak egyetlen hangsávot tartalmazhat – több hangsáv betöltése jelenleg nem támogatott. Ha élő kódolást végez [a helyszíni élő kódokkal,](media-services-live-streaming-with-onprem-encoders.md)a smooth streaming protokollban több hangsávot tartalmazó hozzájárulási hírcsatornát küldhet.
> 
> 

### <a name="ad-marker-source"></a>Hirdetésjelölő forrása
Megadhatja a hirdetésjelölők jeleinek forrását. Az alapértelmezett érték az **Api**, amely azt jelzi, hogy a csatornán belüli élő kódolónak aszinkron **Hirdetésjelölő API-t kell hallgatnia.**

### <a name="cea-708-closed-captions"></a>CEA 708 Feliratok
Egy opcionális zászló, amely arra utasítja az élő kódolót, hogy hagyja figyelmen kívül a CEA 708 feliratokat a bejövő videóba ágyazott adatokat. Ha a jelző értéke hamis (alapértelmezett), a kódoló észleli és újra beilleszti a CEA 708 adatokat a kimeneti videoadatfolyamokba.

#### <a name="index"></a>Index
Javasoljuk, hogy küldjön egy program átviteli patak (SPTS). Ha a bemeneti adatfolyam több programot tartalmaz, a csatorna élő kódolója elemzi a programtábla táblázatát (PMT) a bemenetben, azonosítja azokat a bemeneteket, amelyek adatfolyam-típusa MPEG-2 AAC ADTS vagy AC-3 System-A vagy AC-3 System-B vagy MPEG-2 Private PES vagy MPEG-1 Audio vagy MPEG-2 Audio, és a PMT-ben meghatározott sorrendben rendezi azokat. A nulla-alapú index ezután az n-edik tétel felvételére szolgál ebben a megállapodásban.

#### <a name="language"></a>Nyelv
Az audioadatfolyam nyelvi azonosítója, amely megfelel az ISO 639-2 szabványnak, például AZ ENG. Ha nincs jelen, az alapértelmezett az UND (nem definiált).

### <a name="system-preset"></a><a id="preset"></a>Rendszerkészlet
Megadja az élő kódoló által a csatornán belül használandó készletet. Jelenleg az egyetlen engedélyezett érték **Default720p** (alapértelmezett).

**A Default720p** a következő 6 rétegbe kódolja a videót.

#### <a name="output-video-stream"></a>Kimeneti videóstream

| Bitráta | Szélesség | Height (Magasság) | MaxFPS | Profil | Kimeneti adatfolyam neve |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magasság |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Magasság |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Magasság |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Magasság |Video_512x288_850kbps |
| 550 |384 |216 |30 |Magasság |Video_384x216_550kbps |
| 200 |340 |192 |30 |Magasság |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Kimeneti hangfolyam

A hang sztereó AAC-LC-be van kódolva 128 kbps sebességgel, a mintavételi sebesség 48 kHz.

## <a name="signaling-advertisements"></a>Jelző hirdetések
Ha a csatornádon engedélyezve van az Élő kódolás funkció, akkor a folyamatban van egy összetevő, amely a videót dolgozza fel, és módosíthatja azt. Jelezheti, hogy a csatorna pala- és/vagy hirdetéseket szúr hat be a kimenő adaptív sávszélességű adatfolyamba. A palaképek állóképek, amelyek segítségével bizonyos esetekben (például kereskedelmi szünet ben) elfedheti a bemeneti élő hírcsatornát. A hirdetési jelek időszinkronizált jelek, amelyeket a kimenő adatfolyamba ágyaz, és azt jelzi a videólejátszónak, hogy tegyen különleges műveleteket – például a megfelelő időben váltson hirdetésre. Lásd ezt a [blogot](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) egy áttekintést a SCTE-35 jelző mechanizmus erre a célra. Az alábbiakban egy tipikus forgatókönyv, amelyet az élő eseményben valósíthat meg.

1. Az esemény kezdete előtt a megtekintők kapjanak egy PRE-EVENT képet.
2. Az esemény befejezése után a megtekintők kapjanak egy AFTER-EVENT képet.
3. A megtekintők kapnak egy ERROR-EVENT képet, ha probléma merül fel az esemény során (például áramkimaradás a stadionban).
4. Küldjön egy AD-BREAK képet, hogy elrejtse az élő esemény feed alatt kereskedelmi szünet.

A hirdetések jelzése során az alábbi tulajdonságokat állíthatja be. 

### <a name="duration"></a>Időtartam
A reklámszünet időtartama másodpercben. A kereskedelmi szünet megkezdéséhez nem nulla pozitív értéknek kell lennie. Ha egy kereskedelmi szünet van folyamatban, és az időtartam nullára van állítva, a CueId megfelel a folyamatban lévő kereskedelmi szünetnek, akkor a szünet megszakad.

### <a name="cueid"></a>Végszó
Egyedi azonosító a kereskedelmi szünethez, amelyet az alsóbb rétegbeli alkalmazás a megfelelő intézkedések meghozására használ. Pozitív egész számnak kell lennie. Ezt az értéket beállíthatja bármely véletlenszerű pozitív egész számra, vagy használhat egy upstream rendszert a Cue-azonosítók nyomon követésére. Győződjön meg arról, hogy normalizálja az azonosítókat a pozitív egész számokra, mielőtt az API-n keresztül küldene.

### <a name="show-slate"></a>Pala megjelenítése
Választható. Jelzi az élő kódolónak, hogy a kereskedelmi szünet ben az [alapértelmezett palaképre](media-services-manage-live-encoder-enabled-channels.md#default_slate) váltson, és elrejtse a bejövő videocsatornát. A hang is elnémul a pala alatt. Az alapértelmezett érték **hamis**. 

A használt kép lesz az alapértelmezett pala eszköz azonosító tulajdonsága a csatorna létrehozásakor megadott kép lesz. A pala a kijelző képméretének megfelelően lesz nyújtva. 

## <a name="insert-slate--images"></a>Palaképek beszúrása
A csatornán belüli élő kódoló jelezhető, hogy átváltson egy palaképre. Azt is jelezheti, hogy vessen véget a folyamatos pala. 

Az élő kódoló beállítható úgy, hogy palaképre váltson, és bizonyos helyzetekben elrejtse a bejövő videojelet – például egy hirdetésszünet során. Ha egy ilyen pala nincs konfigurálva, a bemeneti videó nem maszkolja el a hirdetésszünet alatt.

### <a name="duration"></a>Időtartam
A pala időtartama másodpercben. A pala elindításához nem nulla pozitív értéknek kell lennie. Ha van egy folyamatos pala, és nulla időtartam van megadva, akkor a folyamatos pala megszűnik.

### <a name="insert-slate-on-ad-marker"></a>Pala beszúrása a hirdetésjelölőbe
Ha igaz értékre van állítva, ez a beállítás úgy konfigurálja az élő kódolót, hogy a hirdetésszünet során palaképet szúrjon be. Az alapértelmezett érték az igaz. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Alapértelmezett lappal eszközazonosító

Választható. A palaképet tartalmazó Media Services-eszköz eszközazonosítóját adja meg. Az alapértelmezett érték null. 


> [!NOTE] 
> A csatorna létrehozása előtt a következő megkötésekkel rendelkező palaképet dedikált eszközként kell feltölteni (nincs más fájl ebben az eszközben). Ez a kép csak akkor használatos, ha az élő kódoló hirdetéstörés miatt beszúr egy palát, vagy kifejezetten jelezték a pala beszúrásához. Jelenleg nincs lehetőség egyéni lemezkép használatára, amikor az élő kódoló ilyen "elveszett bemeneti jel" állapotba lép. Lehet szavazni ezt a funkciót [itt](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Legtöbb 1920x1080 felbontásban.
* Legbőleg3 Mbyte méretű.
* A fájlnévnek *.jpg kiterjesztésűnek kell lennie.
* A lemezképet fel kell tölteni egy eszköz, mint az egyetlen AssetFile az adott eszköz, és ezt assetfile kell megjelölni az elsődleges fájl. Az eszköz nem titkosítható.

Ha az **alapértelmezett laptáreszköz-azonosító** nincs megadva, és **a hirdetésjelölő behelyezése** **igaz**értékre van állítva, a rendszer a bemeneti videóadatfolyam elrejtéséhez alapértelmezett Azure Media Services-lemezképet használ. A hang is elnémul a pala alatt. 

## <a name="channels-programs"></a>A csatorna programjai
A csatornákhoz programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. A programokat a csatornák kezelik. A Csatorna és a Program kapcsolat nagyon hasonlít a hagyományos média, ahol a csatorna állandó adatfolyam, és a program hatóköre egy időzített esemény, hogy a csatorna.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archív ablak hossza azt is meghatározza, hogy az ügyfelek legfeljebb hányszel kereshetnek időben az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy olyan eszközhöz van társítva, amely tárolja az adatfolyamként imitált tartalmat. Egy eszköz le van képezve egy blokk blob tároló az Azure Storage-fiókban, és az eszköz ben tárolt fájlok blobok a tárolóban. A program közzétételéhez, hogy az ügyfelek megtekinthessék az adatfolyamot, létre kell hoznia egy OnDemand lokátort a társított eszközhöz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatorna legfeljebb három egyidejűleg futó programot támogat, így több archívumot hozhat létre ugyanak a bejövő adatfolyamból. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre és indítson el egy új programot minden eseményhez a Programozási élő közvetítési alkalmazások szakaszban leírtak szerint.

Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt. 

Az archivált tartalmak törléséhez állítsa le és törölje a programot, majd törölje a hozzá társított objektumot. Az objektum nem törölhető, ha a program még használja. Először törölje a programot. 

A program leállítását, majd törlését követően a felhasználók igény szerint lekért videóként képesek lesznek streamelni az archivált tartalmakat, egészen addig, amíg nem törli az objektumot.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Élő hírfolyam miniatűr előnézetének beszerzése
Ha az Élő kódolás engedélyezve van, most már megtekintheti az élő hírfolyam előnézetét, amint az eléri a csatornát. Ez értékes eszköz lehet annak ellenőrzésére, hogy az élő közvetítés valóban eléri-e a Csatornát. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Csatornaállapotok és az államok számlázási módra való leképezése
A csatorna aktuális állapota. A lehetséges értékek a következők:

* **Megállt.** Ez a csatorna kezdeti állapota a létrehozása után. Ebben az állapotban a csatorna tulajdonságai frissíthetők, de a streamelés nem engedélyezett.
* **Kezdő .** A csatorna elindult. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés. Hiba esetén a csatorna visszaáll a Leállított állapotba.
* **Futás**. A Csatorna képes élő közvetítések feldolgozására.
* **Megállás .** A csatornát leállítják. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés.
* **Törlés .** A csatorna törlődik. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés.

Az alábbi táblázat bemutatja, hogyan történik a Csatorna állapotaa a számlázási módra való leképezés. 

| Csatorna állapota | Portál felhasználói felületi mutatói | Számlázott? |
| --- | --- | --- |
| Indítás |Indítás |Nem (átmeneti állapot) |
| Fut |Kész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |Igen |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

> [!NOTE]
> Jelenleg a Csatorna kezdési átlaga körülbelül 2 perc, de néha akár 20 + percet is igénybe vehet. A csatorna alaphelyzetbe állítása akár 5 percet is igénybe vehet.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Megfontolandó szempontok
* Ha egy **szabványos** kódolási csatorna bemeneti forrás/hozzájárulás identatát tapasztal, kompenzálja azt azáltal, hogy a forrásvideót/hangot egy hibapalettával és némítással helyettesíti. A csatorna továbbra is kibocsát egy pala, amíg a bemeneti / hozzájárulás feed folytatódik. Azt javasoljuk, hogy egy élő csatorna ne legyen ilyen állapotban 2 óránál hosszabb ideig. Ezen a ponton túl a csatorna viselkedése a bemeneti újracsatlakozáskor nem garantált, és a visszaállítási parancsra adott válaszként sem. Le kell állítania a csatornát, törölnie kell, és újat kell létrehoznia.
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Minden alkalommal, amikor újrakonfigurálja az élő kódolót, hívja meg a **reset** metódust a csatornán. A csatorna alaphelyzetbe állítása előtt le kell állítania a programot. A csatorna alaphelyzetbe állítása után indítsa újra a programot.
* Egy csatorna csak akkor állítható le, ha futó állapotban van, és a csatorna összes programja leállt.
* Alapértelmezés szerint csak 5 csatornát adhat hozzá a Media Services-fiókhoz. Ez egy puha kvóta minden új fiókra. További információ: [Quotas and Limitations](media-services-quotas-and-limitations.md).
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A számlázás csak akkor történik meg, ha a csatorna **futó** állapotban van. További információt ebben a [szakaszban](media-services-manage-live-encoder-enabled-channels.md#states) talál.
* Jelenleg az élő stream maximális javasolt időtartama 8 óra. 
* Győződjön meg arról, hogy a streamelési végpont, ahonnan szeretné streamelni a tartalmat a **futó** állapotban.
* A kódolási készlet a "maximális képkockasebesség" 30 képkocka/mp fogalmát használja. Tehát, ha a bemenet 60fps/59.94i, a bemeneti keretek eldobják / de-interlaced a 30/29.97 fps. Ha a bemenet 50fps/50i, a bemeneti keretek 25 fps-re csökkennek/delaced. Ha a bemenet 25 fps, a kimenet 25 fps marad.
* Ne felejtsük el, hogy állítsa le a csatornákat, ha kész. Ha nem teszi, a számlázás folytatódik.

## <a name="known-issues"></a>Ismert problémák
* Csatorna indítási idő javult átlagosan 2 perc, de idején a megnövekedett kereslet még mindig eltarthat akár 20 + perc.
* A palaképeknek meg kell felelniük az [itt](media-services-manage-live-encoder-enabled-channels.md#default_slate)leírt korlátozásoknak. Ha 1920x1080-nál nagyobb alapértelmezett palapal rendelkező csatornát próbál létrehozni, a kérés végül hibát jelez.
* mégegyszer.... ne felejtsd el leállítani a csatornákat, ha befejezted a streamelést. Ha nem teszi, a számlázás folytatódik.

## <a name="need-help"></a>Segítségre van szüksége?

Támogatási jegyet úgy nyithat meg, hogy az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigál.

## <a name="next-step"></a>Következő lépés

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Események élő adatfolyamainak továbbítása az Azure Media Services használatával](media-services-overview.md)

[Élő kódolást végző csatornák létrehozása singe bitrátaés adaptív sávszélességű adatfolyam között a Portal segítségével](media-services-portal-creating-live-encoder-enabled-channel.md)

[Élő kódolást végző csatornák létrehozása singe bitrátaés adaptív sávszélességű adatfolyam között .](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Csatornák kezelése REST API-val](https://docs.microsoft.com/rest/api/media/operations/channel)

[A Médiaszolgáltatások fogalmai](media-services-concepts.md)

[Az Azure Media Services töredezett MP4 élő betöltési specifikációja](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

