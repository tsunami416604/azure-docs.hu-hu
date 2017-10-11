---
title: "Live streaming Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása |} Microsoft Docs"
description: "Ez a témakör ismerteti, hogyan állíthat be egy csatornát, amely egy egyféle sávszélességű élő adatfolyamokat fogad egy helyszíni kódolót, és végrehajtja a Media Services az adaptív sávszélességű adatfolyamot élő kódolás. Az adatfolyam majd lehet juttatni a lejátszás ügyfélalkalmazások keresztül egy vagy több adatfolyam-végpontot, a következő adaptív adatfolyam-továbbítási protokollok egyikének használatával: HLS, Smooth Stream, MPEG DASH."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: d5f76d532b236e67a4e69eb820e2cfc3033a80c6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Többszörös átviteli sebességű streamek létrehozása az Azure Media Services élő streamelési funkciójával
## <a name="overview"></a>Áttekintés
Az Azure Media Services (AMS), egy **csatorna** élő adatfolyam-tartalmak feldolgozására adatcsatorna jelöli. A **csatorna** bemeneti élő adatfolyamok megkapja az alábbi két módszer egyikével:

* Egy helyszíni élő kódoló egy egyfajta sávszélességű adatfolyamot küld a csatornának, amelyen engedélyezve van a Media Services használatával történő élő kódolás a következő formátumok egyikében: RTP (MPEG-TS), RTMP vagy Smooth Streaming (töredékes MP4). A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* Egy helyszíni élő kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) a csatornához, hogy AMS élő kódolás nem engedélyezett. A feldolgozott adatfolyamok továbbítása **csatorna**s további feldolgozás nélkül. Ezt a módszert nevezik **áteresztő**. Használhatja a következő élő kódolók képesek kimeneti többféle sávszélességű Smooth Streaming: MediaExcel, Ateme, kommunikációs képzelhető el, Envivo, Cisco és elemi. RTMP-kimenetre a következő élő kódolók képesek: Adobe Flash Media élő kódoló (FMLE), Telestream Wirecast, Haivision, Teradek és Tricaster kódolók.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
  
  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb megoldás közvetít élő adatfolyamként.
  > 
  > 

A Media Services 2.10 kiadástól kezdve, amikor létrehoz egy csatornát, megadhatja a milyen módon a csatornának a bemeneti adatfolyam fogadni szeretné, és hogy szeretné-e a csatorna élő kódolásra az adatfolyam. Erre két lehetősége van:

* **Nincs** – adja meg ezt az értéket, ha egy helyszíni élő kódoló, amely kimeneteként többféle sávszélességűvé (csatlakoztatott adatfolyam) használatát tervezi. Ebben az esetben a bejövő streamből továbbhalad a kimeneti kódolási nélkül. Ez a csatorna 2,10 kiadás előtti működés.  Talál bővebb információt az ilyen típusú csatornák használata, [élő Stream továbbítása helyszíni kódolókkal, amely többféle sávszélességű adatfolyamok létrehozása](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – válassza ezt az értéket, ha azt tervezi, hogy az egyféle sávszélességű élő adatfolyamot többféle sávszélességűvé kódolása a Media Services segítségével. Ne feledje, hogy egy számlázási hatással az élő kódolás és kell ne feledje, hogy egy élő kódolás csatorna hagyja a "Fut" állapotú adatforgalmi díjak gyakorisága.  Javasoljuk, hogy azonnal leállítja a futó csatornák után az élő adatfolyam-továbbítási esemény extra óránkénti díjak elkerülése érdekében.

> [!NOTE]
> Ez a témakör ismerteti a segítségével élő kódolásra képes csatornák attribútumok (**szabványos** kódolási típus). További információ a nem segítségével élő kódolásra képes csatornák használata: [élő Stream továbbítása helyszíni kódolókkal, amely többféle sávszélességű adatfolyamok létrehozása](media-services-live-streaming-with-onprem-encoders.md).
> 
> Mindenképpen tekintse át a [szempontok](media-services-manage-live-encoder-enabled-channels.md#Considerations) szakasz.
> 
> 

## <a name="billing-implications"></a>Számlázási gyakorolt hatása
Egy élő kódolás csatorna kezdődik, számlázási, amint Állapotváltások "Fut" értékre az API-n keresztül.   Az állapot az Azure portálon, vagy az Azure Media Services Explorer eszközben (http://aka.ms/amse) is megtekintheti.

Az alábbi táblázat bemutatja, hogyan csatorna állapotok hozzárendelését számlázási állapotok az API és az Azure portálon. Vegye figyelembe, hogy az állapot csak kis mértékben eltér az API és a portál UX között Amint egy csatornát a "Fut" állapotú a API-n keresztül, vagy az Azure portálon "Kész" vagy "Streaming" állapotban van, számlázási aktív lesz.
A csatorna további számlázási meg leállítani, akkor állítsa le a csatornát, az API-n keresztül, vagy az Azure portálon.
Ön felelősséggel tartozik a csatornák leállítása, ha elkészült, az élő kódolás csatornák.  Hiba egy kódolási csatornájának leállítására folyamatos számlázási eredményez.

### <a id="states"></a>Csatorna állapotait és az hogyan leképezik a számlázási módra
Csatorna aktuális állapota. A lehetséges értékek:

* **Leállítva**. Ez az a csatorna kezdeti állapotában a létrehozása után (kivéve, ha az automatikus indítási sincs kiválasztva a portálon.) Ebben az állapotban nem számlázási következik be. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdési**. A csatorna indítás alatt van. Ebben az állapotban nem számlázási következik be. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Hiba esetén a csatorna Leállított állapotra vált.
* **Futó**. A csatorna élő streamek feldolgozására alkalmas állapotban van. Azt a használat számlázási most van. Le kell állítania a csatorna további számlázási megelőzése érdekében. 
* **Leállítása**. A csatorna leállítás alatt van. Ebben az átmeneti állapotban nem számlázási következik be. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**. A csatorna törlés alatt van. Ebben az átmeneti állapotban nem számlázási következik be. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód. 

| Csatorna állapota | Jelzése a portál kezelőfelületén | Ennyi az egész számlázási? |
| --- | --- | --- |
| Indulás alatt |Indulás alatt |Nem (átmeneti állapot) |
| Fut |Üzemkész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |IGEN |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatikus leállítási nem használt csatornák
Media Services megkezdődött kezdve a 2016. január 25 automatikusan leáll egy csatorna (élő kódolás engedélyezve), hogy a frissítés után futott nem használt állapotban hosszú ideig. Ez vonatkozik, amelyek nem aktív programot, és amelyek nem érkezett meg egy bemeneti hozzájárulás hosszabb ideig hírcsatorna csatornák.

A küszöbérték, használaton kívüli időszakra névlegesen 12 órában, de változhat.

## <a name="live-encoding-workflow"></a>Élő kódolás munkafolyamat
A következő ábra jelöli, amikor egy csatorna kap az alábbi protokollok egyikével egyféle sávszélességű adatfolyamot élő adatfolyam-továbbítási munkafolyamat: RTMP, Smooth Streaming vagy RTP (MPEG-TS); majd az adatfolyamot többféle sávszélességűvé kódolja. 

![Élő munkafolyamat][live-overview]

## <a id="scenario"></a>Gyakori élő adatfolyam-továbbítási forgatókönyv
A leggyakrabban használt streamelési alkalmazások kialakításához általában az alábbi lépések szükségesek.

> [!NOTE]
> Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Lépjen kapcsolatba az amslived@Microsoft.com e-mail címen, ha szeretné működtetni a csatornát hosszabb ideig. Ne feledje, hogy egy számlázási hatással az élő kódolás és kell ne feledje, hogy egy élő kódolás csatorna hagyja a "Fut" állapotú óránkénti adatforgalmi díjak gyakorisága.  Javasoljuk, hogy azonnal leállítja a futó csatornák után az élő adatfolyam-továbbítási esemény extra óránkénti díjak elkerülése érdekében. 
> 
> 

1. Csatlakoztasson egy videokamerát a számítógéphez. Indítson el és konfiguráljon egy helyszíni élő kódoló a kimenetre küldheti egy **egyetlen** sávszélességű adatfolyamot a következő protokollok valamelyikével: RTMP, Smooth Streaming vagy RTP (MPEG-TS). 
   
    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Hozzon létre és indítson el egy csatornát. 
3. Kérje le a csatorna feldolgozó URL-címét. 
   
    Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.
4. Kérje le a csatorna előnézeti URL-címét. 
   
    Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.
5. Hozzon létre egy programot. 
   
    Az Azure portál használata esetén létrehozhat egy programot egy objektumot is létrehoz. 
   
    .NET SDK és a többi objektum létrehozása, majd adja meg, hogy ez az eszköz egy Program létrehozásakor használt kell használata. 
6. Tegye közzé a programhoz társított adategységet.   
   
    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie. 
    
7. Indítsa el a programot, ha készen áll az adatfolyam-továbbításra és az archiválásra.
8. További lehetőségként jelzést adhat a valós idejű kódolónak egy hirdetés elindítására. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.
10. Törölje a programot (esetlegesen törölje az adategységet is).   

> [!NOTE]
> Nagyon fontos a nem a Live Encoding csatornájának leállítására elfelejtené. Vegye figyelembe, hogy nincs hatással az élő kódolás számlázási egy óránként és kell ne feledje, hogy egy élő kódolás csatorna hagyja a "Fut" állapotú adatforgalmi díjak gyakorisága.  Javasoljuk, hogy azonnal leállítja a futó csatornák után az élő adatfolyam-továbbítási esemény extra óránkénti díjak elkerülése érdekében. 
> 
> 

## <a id="channel"></a>Csatorna bemeneti (betöltési) konfigurációk
### <a id="Ingest_Protocols"></a>Betöltési folyamatos átviteli protokollt
Ha a **kódolótípusához** értéke **szabványos**, érvényes beállítások:

* **RTP** (MPEG-TS): MPEG-2 Transport Stream RTP keresztül.  
* Egyszeres sávszélességű **RTMP**
* Egyszeres sávszélességű **töredékes MP4** (Smooth Streaming)

#### <a name="rtp-mpeg-ts---mpeg-2-transport-stream-over-rtp"></a>RTP (MPEG-TS) - MPEG-2 Transport Stream RTP keresztül.
Tipikus használati eset: 

Szakmai műsorszolgáltatók általában elemi technológiákat, Ericsson, Ateme, Imagine vagy Envivo elküldeni az adatfolyam szállítóktól származó csúcskategóriás helyszíni élő kódolók képesek együttműködni. Az informatikai részleg és magánhálózatokon együtt gyakran használják.

Szempontok:

* Adjon meg egy egyetlen program átviteli adatfolyamot (SPTS) használata ajánlott. 
* Legfeljebb 8 hang adatfolyamok over RTP MPEG-2 TS használatával adjon meg. 
* A video-adatfolyamot rendelkeznie kell egy, a alatt 15 MB/s átlagos átviteli sebesség
* Az összesített átlagos átviteli hang adatfolyam rövidebbek 1 MB/s
* Az alábbiakban a támogatott kodekek:
  
  * MPEG-2 / H.262 videó 
    
    * Fő profil (4:2:0)
    * Erős (4:2:0, 4:2:2)
    * 422 profil (4:2:0, 4:2:2)
  * MPEG-4 AVC / H.264 videó  
    
    * Alapkonfiguráció, fő, nagy profil (8 bites 4:2:0)
    * Magas 10 profil (10 bites 4:2:0)
    * Magas 422 profil (10 bites 4:2:2)
  * MPEG-2 AAC-LC hang 
    
    * Monó, Sztereó, (5.1, 7.1) között legyen
    * MPEG-2 stílus ADTS csomagolás
  * Dolby digitális (AC-3) hang 
    
    * Monó, Sztereó, (5.1, 7.1) között legyen
  * MPEG hang (II és III réteg) 
    
    * Monó, sztereó
* Ajánlott szórás kódolók tartalmazza:
  
  * Képzelje el kommunikációs Selenio ENC 1
  * Képzelje el kommunikációs Selenio ENC 2
  * Élő elemi

#### <a id="single_bitrate_RTMP"></a>Egyszeres sávszélességű RTMP
Szempontok:

* A bejövő adatfolyam nem tartalmazhat többszörös sávszélességű videó
* A video-adatfolyamot rendelkeznie kell egy, a alatt 15 MB/s átlagos átviteli sebesség
* A hangadatfolyam rendelkeznie kell egy, a alatt 1 MB/s átlagos átviteli sebesség
* Az alábbiakban a támogatott kodekek:
* MPEG-4 AVC / H.264 videó
* Alapkonfiguráció, fő, nagy profil (8 bites 4:2:0)
* Magas 10 profil (10 bites 4:2:0)
* Magas 422 profil (10 bites 4:2:2)
* MPEG-2 AAC-LC hang
* Monó, Sztereó, (5.1, 7.1) között legyen
* 44,1 kHz mintavételi ráta
* MPEG-2 stílus ADTS csomagolás
* Ajánlott kódolók a következők:
* Telestream Wirecast
* Élő adás Flash adathordozó

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Single bitrate Fragmented MP4 (Egyszeres sávszélességű, fragmentált MP4) (Smooth Streaming)
Tipikus használati eset:

Helyszíni élő kódolók pl. elemi technológiák Ericsson, Ateme, Envivo nyissa meg az interneten keresztül küldendő bemeneti adatfolyam olyan közeli Azure szállítóktól származó adatközpont.

Szempontok:

Ugyanaz, mint a [egyszeres sávszélességű RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Egyéb szempontok
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A bejövő video-adatfolyamot maximális felbontását 1920 x 1080, és legfeljebb 60 mezők/második ha váltakozó, vagy 30 keretek/másodperc Ha fokozatos.

### <a name="ingest-urls-endpoints"></a>Betöltési URL-címek (végpont)
Egy csatornát biztosít a bemeneti végpontja (feldolgozó URL-CÍMÉT), hogy adjon meg az élő kódoló, így a kódoló tolható adatfolyamokat a csatornák.

A bemeneti URL-címek kaphat a csatorna létrehozása után. Ahhoz, hogy ezek a URL-címeket, a csatorna nem kell lennie a **futtató** állapotát. Amikor készen áll a start kérdez le adatokat a csatornának, kell megadni a **futtató** állapotát. Ha a csatorna indítása adatok bevitele, megtekintheti a kép URL-címet a fájlfolyamot.

Lehetősége van a választásával dolgozhat fel töredékes MP4) (Smooth Streaming) élő adatfolyam SSL-kapcsolaton keresztül. Betöltési SSL-en keresztül, győződjön meg arról, hogy a bemeneti URL-címet frissíteni HTTPS. Vegye figyelembe, hogy jelenleg AMS nem támogatja az SSL az egyéni tartomány.  

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Az IP-címek, amelyek jogosultak videókat közzétenni a csatorna adhat meg. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk segítségével (például „10.0.0.1/22”), vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.

Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

## <a name="channel-preview"></a>Csatorna előnézeti
### <a name="preview-urls"></a>Kép URL-címek
Csatorna előnézeti végpont (előzetes verzió URL-cím), amelyekkel előzetes megtekintéséhez és az adatfolyam érvényesítése előtt további feldolgozás és a szállítási adja meg.

A csatorna létrehozásakor az előnézeti URL-CÍMÉT érheti el. Ahhoz, hogy az URL-címet, a csatorna nem kell lennie a **futtató** állapotát.

Ha a csatorna indítása adatok bevitele, megtekintheti az adatfolyam.

> [!NOTE]
> Jelenleg az előzetes adatfolyam is csak akkor kézbesíti a töredékes MP4) (Smooth Streaming) formátumban, függetlenül a megadott bemeneti típus. Használhatja a [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) player a Smooth Stream teszteléséhez. Egy Azure-portálon üzemeltetett player segítségével is megtekintheti az adatfolyam.
> 
> 

### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja, hogy az IP-címek, amelyek számára engedélyezett az előnézeti végpont való kapcsolódáshoz. Ha IP-cím megadva, az engedélyezett IP-címeket. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk segítségével (például „10.0.0.1/22”), vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.

## <a name="live-encoding-settings"></a>Kódolási beállítások élő
Ez a szakasz ismerteti, hogyan a csatorna élő kódolója beállításait módosítani lehet, ha a **kódolási típus** csatorna értéke **szabványos**.

> [!NOTE]
> Ha a bevitel, több nyelv nyomon követi, valamint arról, hogy az Azure-ral élő kódolás, csak RTP beszélő bemenet esetén támogatott. Megadhatja, hogy MPEG-2 TS over RTP használatával legfeljebb 8 hang adatfolyamokat. Választásával dolgozhat fel RTMP vagy Smooth streaming több zeneszámok jelenleg nem támogatott. Mikor történt az élő kódolás [helyszíni élő kódolja](media-services-live-streaming-with-onprem-encoders.md), nincs korlátozás nélkül, mivel egy csatornán keresztül további feldolgozás nélkül átadja függetlenül AMS küld.
> 
> 

### <a name="ad-marker-source"></a>Az ad jelölőt forrás
Ad reklámkonfiguráció forrást is megadhat. Alapértelmezett érték **Api**, ami azt jelenti, hogy a csatorna élő kódolója egy aszinkron való figyelnie kell **Ad jelölőt API**.

A másik érvényes lehetőség **Scte35** (engedélyezett, csak ha RTP (MPEG-TS) a bemeneti adatfolyam-továbbítási protokoll van beállítva. Scte35 megadása esetén az élő kódoló a bemeneti adatfolyamból RTP (MPEG-TS) SCTE-35 jelek fogja elemezni.

### <a name="cea-708-closed-captions"></a>CEA 708 feliratok bezárása
A bejövő videó beágyazott egy választható jelzőt, amely közli az élő kódoló figyelmen kívül hagyása CEA 708 feliratok adatokat. Ha a jelző értéke hamis (alapértelmezett), a kódoló azonosítja, és újra CEA 708 adatokat beszúrni a kimeneti video-adatfolyamot.

### <a name="video-stream"></a>Video-adatfolyammá alakítja
Választható. A bemeneti video-adatfolyamot ismerteti. Ha ez a mező nincs megadva, az alapértelmezett érték lesz érvényben. Ez a beállítás csak akkor, ha a bemeneti adatfolyam-protokoll beállítása RTP (MPEG-TS) engedélyezett.

#### <a name="index"></a>Index
Arról, hogy mely bemeneti video-adatfolyamot kell feldolgozni a csatorna élő kódolója nulla alapú indexét. Ez a beállítás csak akkor, ha érvényes betöltési adatfolyam-protokoll RTP (MPEG-TS).

Alapértelmezett értéke nulla. Javasoljuk, hogy küldjön egy program transport Stream (SPTS). Ha a bemeneti adatfolyam több programokat is tartalmaz, az élő kódoló értelmezi a Program térkép tábla (részlet) a bemeneti adatok, a bemenetek egy adatfolyam típusú nevű MPEG-2 videó vagy H.264 azonosítja és elrendezi a fizetési meghatározott sorrendben A nulla alapú indexét majd szolgál, hogy a megállapodás az n-edik bejegyzés átvételéhez.

### <a name="audio-stream"></a>Hangadatfolyam
Választható. A bemeneti hang adatfolyamok ismerteti. Ha ez a mező nincs megadva, a megadott alapértelmezett értékek érvényesek. Ez a beállítás csak akkor, ha a bemeneti adatfolyam-protokoll beállítása RTP (MPEG-TS) engedélyezett.

#### <a name="index"></a>Index
Javasoljuk, hogy küldjön egy program transport Stream (SPTS). Ha a bemeneti adatfolyam több programokat is tartalmaz, a csatorna élő kódolója értelmezi a Program térkép tábla (részlet) a bemeneti, azonosítja a bemenetek egy adatfolyam típusú nevű MPEG-2 AAC ADTS vagy AC-3 rendszer-A vagy B AC-3-rendszer vagy MPEG-2 titkos PES vagy MPEG-1 Hang- vagy MPEG-2 hang, és elrendezi a fizetési meghatározott sorrendben A nulla alapú indexét majd szolgál, hogy a megállapodás az n-edik bejegyzés átvételéhez.

#### <a name="language"></a>Nyelv
A hangadatfolyam, ISO 639-2, például a ENG. megfelelő nyelvi azonosítója Ha nem található, az alapértelmezett érték (nincs definiálva) és.

Nem határozható meg legfeljebb 8 hangadatfolyam beállítása adni, ha a csatorna bemeneti MPEG-2 TS RTP keresztül. Azonban lehetnek olyan nem két bejegyzés index ugyanarra az értékre.

### <a id="preset"></a>Rendszer-készlet
Adja meg a készlet által a csatorna élő kódolója használható. Jelenleg az egyetlen engedélyezett érték **Default720p** (alapértelmezett).

Vegye figyelembe, hogy ha egyéni készletek van szüksége, forduljon amslived@Microsoft.com e-mail címen.

**Default720p** a következő 7 rétegekbe a videó kódolja.

#### <a name="output-video-stream"></a>Kimeneti Video-adatfolyamot
| Átviteli sebesség | Szélessége | Magassága | MaxFPS | Profil | Kimeneti adatfolyam neve |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Fő |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Fő |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Fő |Video_512x288_850kbps |
| 550 |384 |216 |30 |Fő |Video_384x216_550kbps |
| 350 |340 |192 |30 |Alapkonfiguráció |Video_340x192_350kbps |
| 200 |340 |192 |30 |Alapkonfiguráció |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Kimeneti hangadatfolyam
Hang sztereó AAC-LC 64 KB/s, mintavételi ráta 44,1 kHz, hogy van kódolva.

## <a name="signaling-advertisements"></a>Hirdetmények jelzés
Ha a csatorna élő kódolás engedélyezve van, a folyamat, amely feldolgozó videó tartalmaz összetevőt, és kezelheti azokat. A csatorna táblagépükkel és/vagy hirdetmények beszúrására a kimenő adaptív sávszélességűvé tudja azt. Táblagépükkel még lemezképeket, amelyek segítségével bizonyos esetekben a bemeneti élő hírcsatorna elfedik (például során kereskedelmi szünet). A kimenő adatfolyamba való mentésre állapítható meg, hogy a videólejátszó beavatkozásra különleges – többek között a megfelelő időben hirdetmény váltás beágyazása idő szinkronizálva jelek hirdetési jelek, vannak. Ez [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) erre a célra használt SCTE-35 jelképző mechanizmus áttekintését. Alább a jellemző forgatókönyv, az élő esemény sikerült megvalósítása van.

1. Egy esemény előtti kép lekérése az esemény indítása előtt a megjelenítők rendelkezik.
2. A megjelenítők egy utáni esemény kép lekérése az esemény befejeződését követően lehet.
3. Az beszerzése egy HIBAESEMÉNY lemezképet, ha probléma van az események (például a áramkimaradás a stadium) megjelenítők rendelkezik.
4. Az élő esemény során egy kereskedelmi break hírcsatorna elrejtése AD-BREAK kép küldése.

Az alábbiakban állíthatja be, amikor a jelzés hirdetmények tulajdonságait. 

### <a name="duration"></a>Időtartam
Az időtartam (másodpercben), a kereskedelmi szünet. Azt egy nullától eltérő pozitív értéket kell ahhoz, hogy a kereskedelmi break elindításához. Ha kereskedelmi szünet folyamatban van, és időtartama nullára van beállítva a CueId a megfelelő folyamatos kereskedelmi break majd, hogy skálamegszakítás meg lett szakítva.

### <a name="cueid"></a>CueId
Egy egyedi azonosítója a kereskedelmi break megfelelő elemek érvénybe alárendelt alkalmazás által használható. Meg kell pozitív egész számnak kell lennie. Érték beállítása minden véletlenszerű pozitív egész szám, vagy egy felsőbb rétegbeli rendszer segítségével nyomon követheti a köteg azonosítók. Ügyeljen arra, hogy bármilyen pozitív egész számok azonosítók normalizálása az API-n keresztül elküldése előtt.

### <a name="show-slate"></a>Lappal megjelenítése
Választható. Váltson át az élő kódoló jelzi a [lappal alapértelmezett](media-services-manage-live-encoder-enabled-channels.md#default_slate) során egy kereskedelmi break rendszerképet, a bejövő videoanyagához elrejtése. Hang is némított lappal során. Alapértelmezett érték a **hamis**. 

A használt lemezkép lesz a csatorna létrehozása idején keresztül az alapértelmezett lappal eszköz Id tulajdonsághoz megadott. A lappal program nyújtsa-megjelenített kép méretének megfelelően. 

## <a name="insert-slate--images"></a>Helyezze be a lappal lemezképek
A csatorna élő kódolója jelzést adhat a lappal lemezkép váltani. Azt is jelzést adhat egy folyamatos lappal befejezéséhez. 

Az élő kódoló beállítható úgy, hogy váltson át a lappal lemezképek és elrejtése a bejövő videó jel bizonyos esetekben – például egy ad break során. Ha ilyen lappal nincs konfigurálva, a bemeneti videó nem adott ad break során van maszkolva.

### <a name="duration"></a>Időtartam
A lappal időtartama másodpercben. Azt egy nullától eltérő pozitív értéket kell ahhoz, hogy indítsa el a lappal. Ha egy folyamatos lappal, és nulla értékű időtartamot van megadva, majd, hogy a folyamatban lévő lappal befejeződik.

### <a name="insert-slate-on-ad-marker"></a>Befutó vagy hirdetésjelző beszúrása
Ha true értéke esetén ez a beállítás konfigurálása a lappal kép beszúrása során egy ad break élő kódoló. Az alapértelmezett érték: igaz. 

### <a id="default_slate"></a>Alapértelmezett lappal eszköz azonosítója

Választható. A Media Services eszköz a lappal lemezképet tartalmazó objektum azonosítóját adja meg. Alapértelmezett értéke null. 


>[!NOTE] 
>A csatorna létrehozása, előtt a lappal kép ezekkel a korlátozásokkal fel kell tölteni egy dedikált eszközként (más fájlokat az eszköz kellene lennie). Ez a rendszerkép csak akkor, ha az élő kódoló szúr be egy lappal egy ad megszakítás miatt, vagy nincs explicit módon jelzi a lappal beszúrására szolgál. Az élő kódoló is végrehajthatja a lappal módba során bizonyos hibakörülményeket – például ha a bemeneti jel elvész. Jelenleg nincs lehetőség egyéni lemezképet használ, amikor az élő kódoló ilyen "bemenő jel elveszett" állapotba kerül. Akkor is szavazzon Ez a szolgáltatás [Itt](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Legfeljebb 1920 x 1080 közben.
* Legfeljebb 3 MB-nál.
* A fájl nevének egy *.jpg kiterjesztésűnek kell lennie.
* A lemezképet fel kell tölteni egy adategységbe, a csak AssetFile abban, hogy az eszköz és a AssetFile az elsődleges fájl állapotúként kell megjelölni. Az eszköz nem lehet alkalmaz.

Ha a **alapértelmezett épületkő eszköz azonosítója** nincs megadva, és **beszúrása az ad jelölőt lappal** értéke **igaz**, alapértelmezett Azure Media Services kép történik a bemeneti videó elrejtése az adatfolyam. Hang is némított lappal során. 

## <a name="channels-programs"></a>Csatorna programok
A csatornákhoz programok vannak társítva. Ezek lehetővé teszik az élő stream szegmenseinek közzétételét és tárolását. A programokat a csatornák kezelik. A csatornák és programok viszonya nagyon hasonlít a hagyományos televíziózáshoz, ahol egy csatorna rendelkezik a tartalom állandó adatfolyam, és a program hatóköre a következő néhány időzített esemény adott csatornán.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam határozza meg azt is, hogy mennyi idővel ugorhatnak vissza az ügyfelek az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program társítva egy eszköz, amely az adatfolyamként továbbított tartalmat tárolja. Egy eszköz egy blokk blob tároló az Azure Storage-fiókhoz van rendelve, és az adott tároló blobként tárolja a fájlokat az eszközt. A program közzétételéhez, így a felhasználók megtekinthetik az adatfolyam létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy csatorna három egyidejűleg zajló program támogatja, így a egy bejövő streamből több archívumot is létrehozhat. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre, és az egyes eseményekhez a programozási élő adatfolyam-alkalmazásokhoz című részben leírtak szerint egy új programot elindítani.

Indítsa el a programot, ha készen áll az adatfolyam-továbbításra és az archiválásra. Amikor le kívánja állítani az esemény streamelését és az archiválását, állítsa le a programot. 

Az archivált tartalmak törléséhez állítsa le és törölje a programot, majd törölje a hozzá társított objektumot. Az objektum nem törölhető, ha a program még használja. Először törölje a programot. 

A program leállítását, majd törlését követően a felhasználók igény szerint lekért videóként képesek lesznek streamelni az archivált tartalmakat, egészen addig, amíg nem törli az objektumot.

Ha szeretné megtartani az archivált tartalmakat, de nem szeretné elérhetővé tenni őket streamelésre, törölje a streamelési lokátort.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Első élő adatcsatornára miniatűr előnézete
Ha élő kódolás engedélyezve van, most kaphat az élő adatcsatorna előnézete módon lehet spórolni a csatorna. Ez akkor lehet hasznos eszköze ellenőrizze, hogy az élő adatcsatorna ténylegesen közel jár a csatornát. 

## <a id="states"></a>Csatorna állapotok és hogyan hozzárendelését az állapotok a számlázási mód
Csatorna aktuális állapota. A lehetséges értékek:

* **Leállítva**. Létrehozása után ez a csatorna kezdeti állapota. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdési**. A csatorna indítás alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Hiba esetén a csatorna Leállított állapotra vált.
* **Futó**. A csatorna élő streamek feldolgozására alkalmas állapotban van.
* **Leállítása**. A csatorna leállítás alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**. A csatorna törlés alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód. 

| Csatorna állapota | Jelzése a portál kezelőfelületén | Számlázandó? |
| --- | --- | --- |
| Indulás alatt |Indulás alatt |Nem (átmeneti állapot) |
| Fut |Üzemkész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |Igen |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

> [!NOTE]
> Jelenleg a csatorna indítása átlagos körülbelül 2 percet, de időnként legfeljebb 20 + percet vehet. Csatorna alaphelyzetbe állítását akár 5 percet is igénybe vehet.
> 
> 

## <a id="Considerations"></a>Szempontok
* Ha a csatorna **szabványos** bemeneti forrás/hozzájárulás adatcsatorna adatvesztést kódolási típus észlel, akkor kiegyenlíti az azáltal, hogy a forrás videó/hang egy hiba lappal és csend. A csatorna továbbra is a lappal hozható létre, amíg a bemeneti hozzájárulás hírcsatorna folytatása. Azt javasoljuk, hogy egy élő csatorna nem hagyható hosszabb, mint 2 órán át alkalmas állapotban. A bemeneti újbóli kapcsolódáskor lesznek elküldve a csatornán viselkedését túli, nem garantált, sem pedig az annak viselkedését a alaphelyzetbe állítása parancsra. Akkor állítsa le a csatornát, törölje azt, és hozzon létre egy újat.
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Minden alkalommal, amikor újrakonfigurálja az élő kódoló, hívja az **alaphelyzetbe** módszer a csatornán. A csatorna visszaállítása előtt akkor állítsa le a programot. A csatorna visszaállítása után indítsa újra a programot.
* Egy csatornát csak akkor, ha a futó állapotban van, és le lett állítva a csatornán minden program állítható le.
* Alapértelmezés szerint csak 5 csatornák adhat hozzá a Media Services-fiók. Ez az enyhe kvóták az összes új fiókot. További információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).
* Ha a csatorna vagy a hozzá tartozó programok már elindultak, a bemeneti protokoll nem módosítható. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Ha a csatorna csak számlázása a **futtató** állapota. További információkért tekintse meg [ez](media-services-manage-live-encoder-enabled-channels.md#states) szakasz.
* Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha hosszabb időtartamon át szeretné működtetni a csatornát, forduljon „amslived”-hez a Microsoft.com oldalon.
* Ügyeljen arra, hogy rendelkezik a streamvégpontján, amelyből el kívánja segítségével a tartalmat a **futtató** állapotát.
* Ha a bevitel, több nyelv nyomon követi, valamint arról, hogy az Azure-ral élő kódolás, csak RTP beszélő bemenet esetén támogatott. Megadhatja, hogy MPEG-2 TS over RTP használatával legfeljebb 8 hang adatfolyamokat. Választásával dolgozhat fel RTMP vagy Smooth streaming több zeneszámok jelenleg nem támogatott. Mikor történt az élő kódolás [helyszíni élő kódolja](media-services-live-streaming-with-onprem-encoders.md), nincs korlátozás nélkül, mivel egy csatornán keresztül további feldolgozás nélkül átadja függetlenül AMS küld.
* A kódolási beállításkészlet használja a "max sebessége" 30 fps fogalmát. Igen, ha a bemeneti érték 60fps / 59.97i, a bemeneti keretek eldobott/inaktiválása-interlaced 30/29,97 fps számára. A bemeneti érték 50fps/50i, ha a bemeneti keretek eldobott/inaktiválása-interlaced 25 fps számára. Ha 25 fps a bemeneti, kimeneti 25 fps értéken marad.
* Ne feledje STOP YOUR csatornák végzett. Ha ezt elmulasztja, számlázási továbbra is.

## <a name="known-issues"></a>Ismert problémák
* Csatorna indítási ideje átlagosan 2 percet javult, de időnként az igényeknek továbbra is legfeljebb 20 + percet vehet.
* RTP támogatási szakemberek műsorszolgáltatók felé van catered. Tekintse át a RTP kiegészítő [ez](https://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/) blog.
* Lappal képek meg kell felelnie leírt korlátozások [Itt](media-services-manage-live-encoder-enabled-channels.md#default_slate). Ha úgy próbálja csatornát létrehozni egy alapértelmezett lappal, amely nagyobb, mint 1920 x 1080, az a kérelem rendszer végül hiba.
* Még egyszer... STOP YOUR csatornák ne feledje, ha befejezte az adatfolyam. Ha ezt elmulasztja, számlázási továbbra is.

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Az Azure Media Services események élő adatfolyamainak továbbítása](media-services-overview.md)

[Létrehozhat csatornákat, amely valós idejű kódolással egy konfigurál egy sávszélességű adaptív sávszélességű adatfolyam portállal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Létrehozhat csatornákat, amely valós idejű kódolással egy konfigurál egy sávszélességű adaptív sávszélességű adatfolyam .NET SDK-val](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[REST API-val csatornák kezelése](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[Media Services-fogalmak](media-services-concepts.md)

[Az Azure Media Services töredezett MP4 élő betöltési meghatározása](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

