---
title: Élő adatfolyam továbbítása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása a |} A Microsoft Docs
description: 'Ez a témakör ismerteti, hogyan állítható be egy csatornát, amely egy egyféle sávszélességű élő adatfolyamot fogad egy helyszíni kódolót átvizsgál, majd a Media Services az adaptív sávszélességű adatfolyamot élő kódolás. A stream majd kézbesíthető lejátszási ügyfélalkalmazások legalább egy adatfolyam-végpontokon keresztül, a következő adaptív adatfolyam-továbbítási protokollok egyikének használatával: MPEG-DASH, HLS és Smooth Stream.'
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
ms.date: 08/20/2018
ms.author: juliako;anilmur
ms.openlocfilehash: ada5f55cf356ca79efb7cb45c76a4812804400c5
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222063"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Többszörös átviteli sebességű streamek létrehozása az Azure Media Services élő streamelési funkciójával

> [!NOTE]
> 2018. május 12., az élő csatornák indítása fogja többé nem támogatja a RTP/MPEG-2 adatátviteli stream betöltési protokollját. Telepítse át a RTP/MPEG-2 RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollokra.

## <a name="overview"></a>Áttekintés
Az Azure Media Services (AMS), egy **csatorna** élőben közvetített tartalmak feldolgozási folyamat képviseli. A **csatorna** bemeneti élő Streamek kap a két módszer egyikével:

* Egy helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a csatornának, amely engedélyezve van a valós idejű kódolás a Media Services a következő formátumok egyikében: RTMP vagy Smooth Streaming (töredékes MP4). A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* A helyszíni élő kódoló küld egy többszörös sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) a csatornát, amely a valós idejű kódolás az AMS nincs engedélyezve. A feldolgozott adatfolyamok haladnak keresztül **csatorna**s további feldolgozás nélkül. Ezt a metódust meghívják **átmenő**. Használhatja a következő élő kódolók, és többféle sávszélességű Smooth Streaming-kimenetre: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco és Elemental. RTMP-kimenetre a következő élő kódolók: Adobe Flash Media Live Encoder (FMLE), Haivision, Telestream Wirecast, Teradek és Tricaster kódolókkal történő továbbítását.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
  
  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb élő közvetítést végezni.
  > 
  > 

A Media Services 2.10 kiadástól kezdve, amikor létrehoz egy csatornát, megadhatja a milyen módon azt szeretné, a bemeneti streamet a csatornának, és hogy szeretné-e a csatorna élő kódolásra a Stream. Erre két lehetősége van:

* **Nincs** – adja meg ezt az értéket, ha egy helyszíni élő kódoló, amelyek kimenete és többféle sávszélességű adatfolyamot (csatlakoztatott stream) használatát tervezi. Ebben az esetben a beérkező streamben átadni a kimeneti bármely kódolás nélkül. Ez a csatorna 2,10 kiadás előtti viselkedését.  További részletes információ a csatornák a típus használata: [élő Stream továbbítása többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal történő továbbítását](media-services-live-streaming-with-onprem-encoders.md).
* **Standard szintű** – válassza ezt az értéket, ha azt tervezi, hogy az egyféle sávszélességű élő adatfolyamot sávszélességűvé kódolása a Media Services használatával. Vegye figyelembe, hogy egy számlázási hatással az élő kódolás kell ne feledje, hogy egy élő kódolás csatorna és a "Fut" állapotban számlázási díjat számolunk.  Javasoljuk, hogy azonnal leállítja a futó csatornák az élő adatfolyam-továbbítási esemény nagyon óránkénti díjak elkerülése érdekében befejeződése után.

> [!NOTE]
> Ebben a témakörben ismertetett élő kódolásra képes csatornák jellemzői (**Standard** kódolás típusa). További információ a nem élő kódolásra képes csatornák használata: [élő Stream továbbítása többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal történő továbbítását](media-services-live-streaming-with-onprem-encoders.md).
> 
> Mindenképpen tekintse át a [szempontok](media-services-manage-live-encoder-enabled-channels.md#Considerations) szakaszban.
> 
> 

## <a name="billing-implications"></a>Számlázás – következmények
Élő kódolás csatornát kezdődik a számlázás, amint az API-n keresztül állapotváltásra "Fut" értékre.   Megtekintheti az állapotát az Azure Portalon vagy az Azure Media Services Explorer eszköz (http://aka.ms/amse).

Az alábbi táblázat bemutatja, hogyan csatornaállapotok számlázási állapotok az API-t és az Azure Portalon. Az állapotok a kissé eltérő, az API-t és a portál UX között Amint egy csatornát a "Fut" állapotban az API-n keresztül, vagy az Azure Portalon "Kész" vagy "Folyamatos átvitel" állapotban van, a számlázás aktív lesz.
Leállítja a csatornát, a számlázási, tovább, akkor leállítja a csatornát, az API-n keresztül vagy az Azure Portalon.
Ön felelős a csatornák leállítása, ha elkészült, az élő kódolás Channel.  Nem sikerült leállítani egy kódolási csatorna folyamatos számlázási eredményez.

### <a id="states"></a>Csatorna állapotok, és hogyan leképezik a számlázási mód
Csatorna aktuális állapota. A lehetséges értékek:

* **Leállítva**. Ez a csatorna kezdeti állapota létrehozása után (kivéve, ha az autostart lett kiválasztva a portálon.) Ebben az állapotban nem számlázási akkor fordul elő. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdési**. A csatorna indítás alatt van. Ebben az állapotban nem számlázási akkor fordul elő. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Hiba esetén a csatorna Leállított állapotra vált.
* **Futó**. A csatorna élő streamek feldolgozására alkalmas állapotban van. Azt a használat számlázási most már van. A csatorna további számlázás elkerülése érdekében le kell állítania. 
* **Leállítása**. A csatorna leállítás alatt van. Nincs számlázási ez átmeneti állapot történik. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**. A csatorna törlés alatt van. Nincs számlázási ez átmeneti állapot történik. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód. 

| Csatorna állapota | Jelzése a portál kezelőfelületén | Ennyi az egész számlázás? |
| --- | --- | --- |
| Indítás |Indítás |Nem (átmeneti állapot) |
| Fut |Üzemkész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |IGEN |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatikus leállítási a fel nem használt csatornákat.
2016. január 25-én kezdve a Media Services egyik tagján jelennek meg automatikusan leállítja a csatornát (valós idejű kódolás funkcióval engedélyezve), hogy a frissítés után nem futott már nem használt állapotban hosszabb ideig. Ez a csatorna, amely nem rendelkezik aktív programokkal, és amelyek nem érkezett meg a hosszabb ideig hírcsatorna egy bemeneti közreműködői vonatkozik.

A használaton kívüli időszakra vonatkozó küszöbértéket névlegesen 12 óra, de változhat.

## <a name="live-encoding-workflow"></a>Élő kódolás munkafolyamat
Az alábbi ábrán egy élő adatfolyam-továbbítási munkafolyamat, ahol a csatorna fogadja a következő protokoll egyféle sávszélességű adatfolyamot jelöli: RTMP vagy Smooth Streaming; majd az adatfolyam egy többszörös sávszélességű streammé kódolja. 

![Élő munkafolyamat][live-overview]

## <a id="scenario"></a>Streamelés egy gyakori alaphelyzete
A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

> [!NOTE]
> Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amslived@microsoft.com e-mail-címen. Ez egy számlázási hatással az élő kódolás és kell ne feledje, hogy egy élő kódolás csatorna és a "Fut" állapotban számlázási óránkénti díjat számolunk.  Javasoljuk, hogy azonnal leállítja a futó csatornák az élő adatfolyam-továbbítási esemény nagyon óránkénti díjak elkerülése érdekében befejeződése után. 
> 
> 

1. Csatlakoztasson egy videokamerát a számítógéphez. Indítsa el, és konfigurálja, amelyek kimenete is a helyszíni élő kódoló egy **egyetlen** sávszélességű adatfolyamot, a következő protokoll: RTMP vagy Smooth Streaming. 
   
    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Hozzon létre és indítson el egy csatornát. 
3. Kérje le a csatorna feldolgozó URL-címét. 
   
    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
4. Kérje le a csatorna előnézeti URL-címét. 
   
    Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.
5. Hozzon létre egy programot. 
   
    Ha az Azure portal használatával létrehozhat egy programot egy objektumot is létrehoz. 
   
    .NET SDK-t vagy a REST használata esetén hozzon létre egy objektumot, és adja meg, hogy az objektum használja, ha a Program létrehozása szüksége. 
6. Tegye közzé a programhoz társított adategységet.   
   
    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie. 
    
7. Indítsa el a programot, ha készen áll az adatfolyam-továbbításra és az archiválásra.
8. További lehetőségként jelzést adhat a valós idejű kódolónak egy hirdetés elindítására. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.
10. Törölje a programot (esetlegesen törölje az adategységet is).   

> [!NOTE]
> Nagyon fontos, hogy ne felejtse el leállítani az élő kódolás csatorna. Vegye figyelembe, hogy nincs hatással az élő kódolás számlázási óránkénti kell ne feledje, hogy egy élő kódolás csatorna és a "Fut" állapotban számlázási díjat számolunk.  Javasoljuk, hogy azonnal leállítja a futó csatornák az élő adatfolyam-továbbítási esemény nagyon óránkénti díjak elkerülése érdekében befejeződése után. 
> 
> 

## <a id="channel"></a>Csatorna bemeneti (betöltését) konfigurációk
### <a id="Ingest_Protocols"></a>Betöltési streamelési protokoll
Ha a **Kódolótípus** értékre van állítva **Standard**, érvényes beállítások a következők:

* Egyféle sávszélességű **RTMP**
* Egyféle sávszélességű **töredékes MP4** (Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>Egyféle sávszélességű RTMP
Szempontok:

* A beérkező streamben többszörös sávszélességű MP4 videó nem tartalmazhat.
* A video-adatfolyamot rendelkeznie kell egy alatti 15 MB/s átlagos átviteli
* Az audio-adatfolyam-alább 1 MB/s átlagos átviteli kell rendelkeznie.
* Az alábbiakban a támogatott kodekek:
* MPEG-4 AVC / H.264 videó
* Alapterv, a fő, a nagy profil (8 bites 4:2:0)
* Magas 10 profil (10 bites 4:2:0)
* Magas 422-es Profilig (10 bites 4:2:2)
* MPEG-2 AAC-LC hang
* Mono, Sztereó, legyen (5.1, 7.1-es)
* 44,1 kHz mintavételi ráta
* MPEG-2 stílus ADTS csomagolás
* Ajánlott kódolók a következők:
* Telestream Wirecast
* Flash Media Live kódoló

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
Tipikus használati eset:

Használja a helyszíni élő kódolók a szállítók, például elemi technológiák, Ericsson, Ateme, Envivio adatfolyam egy közeli Azure a nyílt interneten keresztül elküldi az adatközpontban.

Szempontok:

Ugyanaz, mint a [egyszeres sávszélességű RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Egyéb szempontok
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A bejövő video-adatfolyamok maximális felbontás 1920 × 1080 képpont, és legfeljebb 60 mezők másodpercenként ha váltakozó, vagy 30 képkocka/másodperc Ha fokozatos.

### <a name="ingest-urls-endpoints"></a>Betöltési URL-címek (végpontok)
Egy csatorna bemeneti végpontot biztosít (betöltési URL-címe), hogy Ön adja meg az élő kódoló, leküldheti a kódoló Streamek a saját csatornákhoz.

Csatorna létrehozása után a betöltés URL-címek kérheti le. Ezen URL-címeket, a csatorna nem rendelkezik kell lennie a **futó** állapota. Ha adatokat küld a csatornának készen áll, legyen az a **futó** állapota. Miután a csatorna elindul, adatok, megtekintheti az előnézeti URL-címet a fájlfolyamot.

Lehetősége van feldolgozására töredékes MP4 (Smooth Streaming) élő streamet SSL-kapcsolaton keresztül. Hogy SSL-en keresztül, ügyeljen arra, hogy a bemeneti URL-címet frissíteni a HTTPS-re. Jelenleg az AMS nem támogatja az SSL egyéni tartománnyal rendelkező.  

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja, hogy az IP-címek, amelyek jogosultak videókat közzétenni ezt a csatornát. Engedélyezett IP-cím adható meg egyetlen IP-címként (például "10.0.0.1"),-egy IP-címtartományt, IP-címmel és egy CIDR alhálózati maszk (például "10.0.0.1/22"), vagy egy IP-címtartomány IP-cím és a egy pontozott decimális alhálózati maszk segítségével (például , "10.0.0.1(255.255.252.0)').

Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

## <a name="channel-preview"></a>Csatorna előnézete
### <a name="preview-urls"></a>Előnézeti URL-címek
Csatorna előnézeti végpont (előzetes verzió URL-cím), amellyel és ellenőrzéséhez a stream előnézetének a feldolgozás folytatása és a továbbítás előtti adja meg.

Az előnézeti URL-cím kérheti le a csatorna létrehozásakor. Az URL-Címének lekéréséhez, a csatorna nem kell lennie a **futó** állapota.

Ha a csatorna elindul, adatok, megtekintheti a stream.

> [!NOTE]
> Jelenleg az előzetes verzió adatfolyamot csak lehet nyújtani töredékes MP4 (Smooth Streaming) formátumban, a megadott bemeneti típusától függetlenül.  A lejátszó az Azure Portalon lévő üzemeltetett használhatja a stream megtekintése.
> 
> 

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címei
Megadhatja, hogy az IP-címek, amelyek jogosultak az előzetes verzió végponthoz csatlakozik. Ha nincsenek IP-címek meg van adva, IP-címeket engedélyezett lesz. Engedélyezett IP-cím adható meg egyetlen IP-címként (például "10.0.0.1"),-egy IP-címtartományt, IP-címmel és egy CIDR alhálózati maszk (például "10.0.0.1/22"), vagy egy IP-címtartomány IP-cím és a egy pontozott decimális alhálózati maszk segítségével (például , "10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Élő kódolási beállításai
Ez a szakasz ismerteti, hogy a csatorna élő kódolója beállításait módosítani lehet, amikor a **kódolásának típusát** csatorna értékre van állítva **Standard**.

> [!NOTE]
> A közreműködői hírcsatorna tartalmazhat egy egyetlen hangsávra – több hangsáv a fürtjét jelenleg nem támogatott. Ha ezt a valós idejű kódolás az [a helyszíni élő kódol](media-services-live-streaming-with-onprem-encoders.md), elküldheti a Smooth Streaming protokoll több hangsáv tartalmazó hírcsatorna hozzájárulást.
> 
> 

### <a name="ad-marker-source"></a>Az ad jelölőt forrás
Megadhatja a hirdetésjelölő jelek forrását. Alapértelmezett érték **Api**, ami azt jelenti, hogy a csatorna élő kódolója egy aszinkron, figyelnie kell-e **Hirdetésjelölő API-t**.

### <a name="cea-708-closed-captions"></a>CEA 708 feliratokkal
Egy nem kötelező jelzőt, amely arra kéri az élő kódoló CEA-708-feliratok adatokról figyelmen kívül a bejövő videó ágyazva. Ha a jelző false (alapértelmezett) értékre van állítva, a kódoló érzékeli, és újra CEA-708-adatok beszúrása a kimeneti video-adatfolyamokat.

#### <a name="index"></a>Index
Javasoljuk, hogy az egyetlen program transport stream (SPTS) küldése. Ha a bemeneti stream több programot tartalmaz, a csatorna élő kódolója elemzi a Program térkép tábla (részlet) a bemeneti adatok, azonosítja a bemenetek, amelyek egy stream típusnév MPEG-2 AAC ADTS vagy AC-3 System-A vagy B AC-3-System vagy MPEG-2 privát PES vagy MPEG-1 Hang- vagy MPEG-2 hang-, és elrendezi a fizetési a megadott sorrendben A nulla alapú index szolgál majd, hogy pedig vízszintes folytattuk a munkát az n-edik bejegyzést.

#### <a name="language"></a>Nyelv
Az audio-adatfolyamot, ISO 639-2, például a ENG. mintának megfelelő nyelvi azonosítója Ha nem található, akkor az alapértelmezett érték (nem meghatározott) és.

### <a id="preset"></a>Rendszer-Előbeállítás
Adja meg a készletet, amelyet ez a csatorna élő kódolója kell használni. Jelenleg az egyetlen megengedett érték **Default720p** (alapértelmezett).

Vegye figyelembe, hogy ha egyéni előbeállításokat van szüksége, forduljon amslived@microsoft.com.

**Default720p** lesz a videó kódolandó a következő 7 rétegek.

#### <a name="output-video-stream"></a>Kimeneti videót Stream
| Átviteli sebesség | Szélesség | Magasság | MaxFPS | Profil | Kimeneti Stream neve |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Elsődleges |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Elsődleges |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Elsődleges |Video_512x288_850kbps |
| 550 |384 |216 |30 |Elsődleges |Video_384x216_550kbps |
| 350 |340 |192 |30 |Alapterv |Video_340x192_350kbps |
| 200 |340 |192 |30 |Alapterv |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Kimeneti Audio Stream

Hang, sztereó AAC-LC, 128 kb/s, mintavételi ráta 48 kHz van kódolva.

## <a name="signaling-advertisements"></a>Hirdetmények jelzés
Ha a csatorna élő kódolás engedélyezve van, van egy összetevő, amely a videó feldolgozása a folyamatban, és kezelheti azokat. Akkor is jelezze a befutók és/vagy hirdetmények beszúrása a kimenő adaptív sávszélességűvé csatorna. Befutók olyan állóképek, amelyekkel elfedhető a bemeneti élő adás bizonyos esetekben a (például reklámszünet). Hirdetési jel, vannak, a kimenő adatfolyam állapítható meg, hogy intézkedjen speciális – mint például a megfelelő időben alkalmazásban a hirdetmény forráshierarchiából való váltáshoz a videólejátszó beágyazása időt szinkronizálva jelek. Ez [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) az erre a célra használt SCTE – 35 jelképző mechanizmus áttekintését. Alább egy gyakori forgatókönyvet is Megvalósíthat az élő esemény van.

1. A nézők egy üzem előtti esemény lemezkép beolvasása, az esemény kezdete előtt van.
2. Egy esemény utáni lemezkép beolvasása, az esemény befejezése után a nézők rendelkezik.
3. A nézők UDÁLOST CHYBY képet kaphat, ha probléma merül fel az esemény (például a áramkimaradás biztosítani) alatt van.
4. AD-BREAK kép elrejtése az élő esemény reklámszünet hírcsatorna küldése.

Az alábbiakban a Tulajdonságok beállíthatja, amikor a jelzés a hirdetményeket. 

### <a name="duration"></a>Időtartam
Az időtartam (másodpercben), a a kereskedelmi szünet. A kereskedelmi szünet megkezdéséhez nullától eltérő pozitív számnak kell azt. Amikor egy kereskedelmi break folyamatban van, és az időtartam értéke nulla a CueId a folyamatos kereskedelmi break megfelelő majd, hogy eseti meg lett szakítva.

### <a name="cueid"></a>CueId
Egy egyedi Azonosítót a kereskedelmi szünet megfelelő művelet(ek) érvénybe alsóbb rétegbeli alkalmazás által használható. Kell egy pozitív egész szám lehet. Ezt az értéket a véletlenszerű pozitív egész szám, vagy egy felsőbb rétegbeli rendszer segítségével nyomon követheti a köteg azonosítóját. Ügyeljen arra, hogy bármilyen pozitív egész számok azonosítók normalizálása az API-n keresztül történő elküldése előtt.

### <a name="show-slate"></a>Befutó show
Választható. Váltson át az élő kódoló jelzi a [befutó alapértelmezett](media-services-manage-live-encoder-enabled-channels.md#default_slate) reklámszünet kép és a bejövő videoanyagához elrejtése. Hang is némított befutó során. Alapértelmezett érték a **hamis**. 

A használt lemezkép lesz megadott keresztül az alapértelmezett befutókép objektum "Id" tulajdonság a csatornák létrehozásának időpontjában. A lappal illeszkedés megjelenítési kép méretének megfelelően. 

## <a name="insert-slate--images"></a>Befutó lemezképek beszúrása
Váltson át egy befutókép a csatorna élő kódolója jelzést. Azt is jelzést egy folyamatos befutó befejezéséhez. 

Az élő kódoló beállítható úgy, hogy váltson át egy befutókép, és a bejövő videó jel bizonyos esetekben – például elrejtése az ad/csere közben. Ha egy ilyen befutó nincs konfigurálva, a bemeneti videó nem során, hogy az ad/csere típusú van maszkolva.

### <a name="duration"></a>Időtartam
A lappal időtartama másodpercben. Azt nem nulla értékű pozitív számnak kell a lappal megkezdéséhez. Ha van egy folyamatos lappal, és a egy időtartamot a nulla van megadva, majd a folyamatos befutó befejeződik.

### <a name="insert-slate-on-ad-marker"></a>Befutó vagy hirdetésjelző beszúrása
Ha igaz értékű, ez a beállítás konfigurálása az élő kódoló egy befutókép beszúrása egy ad break során. Az alapértelmezett érték: igaz. 

### <a id="default_slate"></a>Alapértelmezett befutókép eszközazonosító

Választható. A Media Services eszköz a befutókép tartalmazó objektum azonosítóját adja meg. Alapértelmezett értéke null. 


>[!NOTE] 
>Mielőtt létrehozná a csatornát, a következő korlátozások a befutókép fel kell tölteni egy dedikált eszközként (más fájlokat az adategység kell lennie). Ez a rendszerkép csak akkor lehetséges, ha az élő kódoló szúr be egy lappal egy ad megszakítás miatt, vagy explicit módon a beszúrása egy befutó lett jelzést szolgál. Az élő kódoló úgy is megtehetik állóképek módba során bizonyos hibakörülményeket – például ha a bemeneti jel elvész. Jelenleg nincs lehetőség egyéni rendszerkép használatával, ha az élő kódoló ilyen bemeneti jel elveszett állapotba kerül. Ennek a funkciónak szavazhat [Itt](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Egyszerre legfeljebb 1920 × 1080 képpont névfeloldási.
* Legfeljebb 3 MB-nál.
* A fájlnév *.jpg kiterjesztéssel kell rendelkeznie.
* A lemezképet fel kell tölteni egy adategységbe, az csak AssetFile annak, hogy az eszköz és a AssetFile kell megjelölni az elsődleges fájllal. Az eszköz nem lehet titkosított tárolása.

Ha a **alapértelmezett épületkő eszközazonosító** nincs megadva, és **hirdetésjelző beszúrása** értékre van állítva **igaz**, egy alapértelmezett Azure Media Services lemezképpel fog a bemeneti videó elrejtése a Stream. Hang is némított befutó során. 

## <a name="channels-programs"></a>Csatorna programok
A csatornákhoz programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. A programokat a csatornák kezelik. A csatornák és programok viszonya nagyon hasonlít a hagyományos televíziózáshoz, ahol csatorna tartalmaz egy állandó stream a tartalom és a egy programot az adott csatornán néhány időzített esemény hatókörét.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Archiválási időszak hossza is szabja meg, az ügyfelek maximális számát is kérhet időbeli az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden egyes program társítva egy eszköz, amely a adatfolyamként továbbított tartalmat tárolja. Egy eszköz block blob-tárolóba az Azure Storage-fiókban van hozzárendelve, és a tárolóban lévő blobok formájában tárolja az adategységben található fájlokat. A program közzétételéhez, így az ügyfelek tekintheti meg a streamet, létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy csatorna három egyidejűleg zajló program támogatja, így az egy bejövő streamből több archívumot is létrehozhat. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre, és indítsa el a programozási élő Streamelési alkalmazások szakaszban leírtak szerint egyes esemény egy új programot.

Indítsa el a programot, ha készen áll az adatfolyam-továbbításra és az archiválásra. Amikor le kívánja állítani az esemény streamelését és az archiválását, állítsa le a programot. 

Az archivált tartalmak törléséhez állítsa le és törölje a programot, majd törölje a hozzá társított objektumot. Az objektum nem törölhető, ha a program még használja. Először törölje a programot. 

A program leállítását, majd törlését követően a felhasználók igény szerint lekért videóként képesek lesznek streamelni az archivált tartalmakat, egészen addig, amíg nem törli az objektumot.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Miniatűr előzetes verziója egy élő csatorna beolvasása
Ha Live Encoding funkcióval engedélyezve van, most már beszerezheti a előzetes verziója az élő adás módon éri el a csatornát. Ellenőrizze, hogy az élő adás ténylegesen eléri-e a csatorna biztosításának hasznos eszköze is lehet. 

## <a id="states"></a>Csatorna állapotok, és hogyan állapotok leképezése a számlázási mód
Csatorna aktuális állapota. A lehetséges értékek:

* **Leállítva**. Létrehozása után ez a csatorna kezdeti állapota. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdési**. A csatorna indítás alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Hiba esetén a csatorna Leállított állapotra vált.
* **Futó**. A csatorna élő streamek feldolgozására alkalmas állapotban van.
* **Leállítása**. A csatorna leállítás alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**. A csatorna törlés alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód. 

| Csatorna állapota | Jelzése a portál kezelőfelületén | Számlázandó? |
| --- | --- | --- |
| Indítás |Indítás |Nem (átmeneti állapot) |
| Fut |Üzemkész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |Igen |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

> [!NOTE]
> Jelenleg a csatorna indítása átlag körülbelül 2 percet, de néha percet is igénybe vehet fel mint 20. Csatorna alaphelyzetbe akár 5 percet is igénybe vehet.
> 
> 

## <a id="Considerations"></a>Megfontolandó szempontok
* Ha a csatorna **Standard** kódolás típusa során lép fel egy bemeneti forrás/közreműködői hírcsatorna elvesztését, kompenzálja, azáltal, hogy a forrás videó vagy hang egy hiba befutó és csend. A csatorna kibocsátható egy lappal, amíg a bemeneti hozzájárulás hírcsatorna folytatja továbbra is. Azt javasoljuk, hogy egy élő csatorna nem hagyható több mint 2 óra állapotban. Ezen a ponton a bemeneti újbóli kapcsolódáskor lesznek elküldve a csatorna viselkedését nem garantált, sem pedig annak viselkedését egy alaphelyzetbe állítása parancsra adott válaszként. Meg kell leállítja a csatornát, törölnie kell, és hozzon létre egy újat.
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Minden alkalommal, amikor újrakonfigurálja az élő kódoló, hívja a **alaphelyzetbe** metódus a csatornán. Mielőtt alaphelyzetbe állítja a csatornát, akkor állítsa le a programot. Ha alaphelyzetbe állítja a csatornát, indítsa újra a program.
* Egy csatornát csak akkor, ha a futó állapotban van, és le lett állítva a csatornán minden program állítható le.
* Alapértelmezés szerint csak 5 csatornák adhat hozzá a Media Services-fiók. Ez az enyhe kvóták az összes új fiókot. További információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Csak számítunk fel díjat, ha a csatornán belül van a **futó** állapota. További információkért tekintse meg [ez](media-services-manage-live-encoder-enabled-channels.md#states) szakaszban.
* Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amslived@microsoft.com e-mail-címen.
* Győződjön meg arról, hogy a streamelési végpontot, ahonnan a tartalomátvitelre a **futó** állapota.
* A kódolási előbeállítás használja a "max képkockasebesség" 30 képkocka/s fogalma. Igen, ha a bemenet 60 képkocka / 59.94i, a bemeneti keretek kihagyott/kivonási-interlaced, 30/29,97 fps. Ha a bemeneti 50 képkocka/50i, a bemeneti keretek is eldobott/kivonási-interlaced, 25 fps. Ha 25 fps a bemeneti, kimeneti 25 fps továbbra is.
* Ne felejtse el állítsa le a csatornák, ha ezzel elkészült. Ha nem, akkor a számlázás továbbra is.

## <a name="known-issues"></a>Ismert problémák
* Csatorna indítási idő lett továbbfejlesztve, hogy átlagosan 2 percet, azonban esetenként megnövekedett igények továbbra is percet is igénybe vehet fel mint 20.
* Állóképek lemezképek meg kell felelnie ismertetett korlátozások [Itt](media-services-manage-live-encoder-enabled-channels.md#default_slate). Ha megpróbálja csatornát létrehozni az egy alapértelmezett lappal, amely nagyobb, mint 1920 × 1080 képpont, a kérelem rendszer végül hibát.
* Ismét... ne felejtse el állítsa le a CSATORNÁT, amikor elkészült streamelési. Ha nem, akkor a számlázás továbbra is.

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Az Azure Media Services események élő adatfolyamainak továbbítása](media-services-overview.md)

[Valós idejű kódolás egy konfigurált replikához sávszélességű az adaptív sávszélességű streamformátumba portállal csatornák létrehozása](media-services-portal-creating-live-encoder-enabled-channel.md)

[Valós idejű kódolás egy konfigurált replikához sávszélességű a .NET SDK használatával adaptív sávszélességű streamformátumba csatornák létrehozása](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Kezeléséhez csatornákat REST API-val](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[A Media Services alapfogalmai](media-services-concepts.md)

[Specifikáció: darabolt MP4 élő Azure Media Services feldolgozása](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

