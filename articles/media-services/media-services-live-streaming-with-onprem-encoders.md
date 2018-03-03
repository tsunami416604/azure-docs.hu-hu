---
title: "A helyszíni kódolókkal által létrehozott többféle sávszélességű adatfolyamok - Azure élő adatfolyam |} Microsoft Docs"
description: "Ez a témakör ismerteti a helyszíni kódoló egy többszörös sávszélességű élő streamet fogadó csatornát. Az adatfolyam majd továbbítható lejátszás ügyfélalkalmazások keresztül egy vagy több adatfolyam-továbbítási végpontok, a következő adaptív adatfolyam-továbbítási protokollok egyikének használatával: HLS, Smooth Streaming, DASH."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/12/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 1266c7b6c1539f84eafea1007999fb4360184857
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Élő Stream továbbítása helyszíni kódolókkal, amely többféle sávszélességű adatfolyamok létrehozása
## <a name="overview"></a>Áttekintés
Azure Media Services a *csatorna* live-streaming tartalom feldolgozása csővezeték jelöli. Egy csatorna élő bemeneti adatfolyamok megkapja az alábbi két módszer egyikével:

* Egy helyszíni élő kódoló küld egy többszörös sávszélességű RTMP vagy Smooth Streaming (töredezett MP4) adatfolyamot a csatornát, amely a Media Services élő kódolás nincs engedélyezve a. A feldolgozott adatfolyamok további feldolgozás nélkül továbbítja csatornákon keresztül. Ezt a módszert nevezik *áteresztő*. Az élő kódolók is küldhetnek egy egyszeres sávszélességű streamet egy csatornát, amely az élő kódolás nincs engedélyezve, de nem ajánlott. Az ügyfél számára, akik kérnek, azt a Media Services továbbítja az adatfolyamot.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb megoldás közvetít élő adatfolyamként.


* Egy helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a csatornának, amelyen engedélyezve van-e élő kódolásra Media Services a következő formátumok egyikében: RTP (MPEG-TS), RTMP vagy Smooth Streaming (töredezett MP4). A csatorna ezután a bejövő egyszeres sávszélességű adatfolyamot többféle bitrátájúvá (adaptív) video-adatfolyamot élő kódolás útján. Az ügyfél számára, akik kérnek, azt a Media Services továbbítja az adatfolyamot.

A Media Services 2.10 kiadástól kezdve, amikor létrehoz egy csatornát, megadhatja, hogyan történjen a csatorna a bemeneti adatfolyam fogadására. Megadhatja azt is, hogy kíván-e a csatorna élő kódolásra az adatfolyam. Erre két lehetősége van:

* **Továbbítása**: Adja meg ezt az értéket, ha egy helyszíni élő kódoló, amely rendelkezik egy többszörös sávszélességű streammé (csatlakoztatott adatfolyam) kimenetként használatát tervezi. Ebben az esetben a bejövő streamből haladnak keresztül a kimeneti kódolási nélkül. Ez a csatorna 2,10 kiadása előtt történik. Ez a cikk tájékoztatást nyújt a csatornák a típus használata.
* **Élő kódolás**: válassza ezt az értéket, ha tervezi az egyszeres sávszélességű élő adatfolyamot többféle sávszélességűvé kódolása a Media Services segítségével. Az élő kódolás csatorna elhagyása egy **futtató** állapot számlázási terhel. Azt javasoljuk, hogy azonnal leállítja a futó csatornák után az élő adatfolyam esemény extra óránkénti díjak elkerülése érdekében. Az ügyfél számára, akik kérnek, azt a Media Services továbbítja az adatfolyamot.

> [!NOTE]
> A cikk nem élő kódolásra képes csatornák attribútumait ismerteti. További információ a segítségével élő kódolásra képes csatornák használata: [többféle sávszélességű adatfolyamok létrehozása az Azure Media Services segítségével élő Stream továbbítása](media-services-manage-live-encoder-enabled-channels.md).
>
>Ajánlott helyszíni kódolókkal kapcsolatos információkért lásd: [helyszíni kódolókkal ajánlott](media-services-recommended-encoders.md).

A következő diagram által live-streaming használó munkafolyamatot, egy helyszíni élő kódoló többszörös sávszélességű RTMP vagy töredezett MP4) (Smooth Streaming) adatfolyamok kimenetként.

![Élő munkafolyamat][live-overview]

## <a id="scenario"></a>Live streaming egy gyakori alaphelyzete
Az alábbi lépéseket a gyakori élő-adatfolyam-továbbítási alkalmazások létrehozásához elvégzendő feladatokat írják le.

1. Csatlakoztasson egy videokamerát a számítógéphez. Elindítása és konfigurálása a helyszíni élő kódoló, amely többszörös sávszélességű RTMP vagy töredezett MP4) (Smooth Streaming) adatfolyam output típusúként. További tudnivalók: [Azure Media Services RMTP-támogatása és valós idejű kódolók](http://go.microsoft.com/fwlink/?LinkId=532824)

    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Csatorna létrehozása és elindítása.

3. Kérje le a csatorna feldolgozó URL-CÍMÉT.

    Az élő kódoló a bemeneti URL-címet használ az adatfolyamot küldeni a csatornát.
4. A csatorna előnézeti URL-cím beolvasása.

    Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.
5. Hozzon létre egy programot.

    Az Azure portál használata esetén létrehozhat egy programot egy objektumot is létrehoz.

    A .NET SDK-val vagy a REST használatakor kell hozzon létre egy eszközt, és adja meg, hogy ez az eszköz egy program létrehozásakor használt.
6. Tegye közzé az adategységet, a program társított.   

    >[!NOTE]
    >Az Azure Media Services-fiók létrehozásakor egy **alapértelmezett** adatfolyam-továbbítási végpontra a fiókja hozzáadódik a **leállítva** állapotát. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie.

7. Amikor készen áll a streamelésre és az archiválásra, indítsa el a program.

8. További lehetőségként jelzést adhat a valós idejű kódolónak egy hirdetés elindítására. A hirdetés a kimeneti adatfolyamba lesz beszúrva.

9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

10. Törölje a programot (és esetlegesen törölje az adategységet).     

## <a id="channel"></a>A csatorna és a kapcsolódó összetevők leírása
### <a id="channel_input"></a>Bemeneti csatorna (betöltési) konfigurációk
#### <a id="ingest_protocols"></a>Betöltési folyamatos átviteli protokollt
A Media Services támogatja a választásával dolgozhat fel élő hírcsatornák segítségével többszörös sávszélességű töredékes MP4 és többféle sávszélességű RTMP protokollok streameléshez. Amikor az RTMP betöltési protokollt streaming meg van adva, a két feldolgozó (bemeneti) végpontok csatorna jönnek létre:

* **Elsődleges URL-cím**: Adja meg a teljes URL-CÍMÉT a csatorna elsődleges RTMP betöltési végpont.
* **Másodlagos URL-cím** (nem kötelező): Adja meg a teljes URL-CÍMÉT a csatorna másodlagos RTMP betöltési végpont.

A másodlagos URL-cím Ha azt szeretné, hogy a bemeneti adatfolyam (valamint kódoló feladatátvételi és a hibatűrés tolerancia), a tartósság és a hibatűrés megadott javítására különösen a következő helyzetekben használhatja:

- Egyetlen kódoló dupla küldését elsődleges és másodlagos URL-címek:

    Ebben a forgatókönyvben fő célja, hálózati ingadozását és jitters további erősítése. Néhány RTMP kódolók kezelik hálózati bontja a kapcsolatot is. Történik, a hálózati kapcsolata megszakad, amikor egy kódoló előfordulhat, hogy leállítja a kódolást, majd küldje el a pufferelt adatok újracsatlakozás történik, amikor. Ennek hatására a folytonosság megszakítását és adatvesztés. Hálózati bontja a kapcsolatot a hibás hálózati vagy karbantartás miatt fordulhat elő az Azure oldalán. Elsődleges és másodlagos URL-címek a hálózati problémák csökkentése, és adjon meg egy ellenőrzött frissítési folyamat. Minden alkalommal, amikor egy ütemezett hálózati kapcsolat bontása történik, a Media Services kezeli az elsődleges és másodlagos bontja a kapcsolatot, és biztosítja a késleltetett válassza le a kettő között. Kódolók akkor kell tartani adatküldés és újracsatlakozás újra. Sorrendjét a bontja a kapcsolatot véletlenszerű lehet, de mindig lesz késleltetés elsődleges és másodlagos vagy másodlagos/elsődleges URL-címek között. Ebben a forgatókönyvben a kódoló még mindig a hibaérzékeny pontok kialakulását.

- Több kódolók minden Encoder kérdez le, hogy egy dedikált pontot:

    Ez a forgatókönyv mindkét kódoló biztosít, és redundanciát ingests. Ebben a forgatókönyvben encoder1 leküldi az elsődleges URL-címet, és encoder2 leküldi a másodlagos URL-CÍMÉT. Ha Encoder elemek esetében nem sikerül, a más kódoló is tartsa adatküldés. Mivel a Media Services nem szakítja meg elsődleges és másodlagos URL-címek egyidejűleg adatredundanciát tarthatjuk fenn. Ebben a forgatókönyvben azt feltételezi, hogy, hogy kódolók idő szinkronizálva, és adja meg pontosan ugyanazokat az adatokat.  

- Több kódolók dupla küldését elsődleges és másodlagos URL-címek:

    Ebben a forgatókönyvben mindkét kódolók küldi az adatokat az elsődleges és másodlagos URL-címeket. Ez biztosítja a legjobb megbízhatóság és a hibatűrés, valamint adatredundanciát. Ez a forgatókönyv tűri mindkét kódoló hibák, és megszakítja a kapcsolatot, akkor is, ha egy kódoló nem működik. Feltételezi, hogy kódolók idő szinkronizálva, és adja meg pontosan ugyanazokat az adatokat.  

További információ a RTMP élő kódolók képesek: [Azure Media Services RMTP-támogatása és valós idejű kódolók](http://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Betöltési URL-címek (végpont)
Egy csatornát biztosít a bemeneti végpontja (feldolgozó URL-CÍMÉT), hogy adjon meg az élő kódoló, így a kódoló tolható adatfolyamokat a csatornák.   

A bemeneti URL-címek kaphat a csatorna létrehozásakor. URL-címek lekérése ezek meg, a csatorna nem kell lennie a **futtató** állapotát. Amikor készen áll a start kérdez le adatokat a csatornára, a csatorna szerepelnie kell a **futtató** állapotát. A csatorna indítása adatok bevitele után megtekintheti a kép URL-címet a fájlfolyamot.

Lehetősége van a választásával dolgozhat fel töredezett MP4) (Smooth Streaming) élő adatfolyam SSL-kapcsolaton keresztül. Betöltési SSL-en keresztül, győződjön meg arról, hogy a bemeneti URL-címet frissíteni HTTPS. RTMP jelenleg nem betöltési SSL-en keresztül.

#### <a id="keyframe_interval"></a>Keyframe időköz
Amikor egy helyszíni élő kódoló többféle sávszélességűvé létrehozásához használja, a a keyframe időköz képek (GOP) annak a csoportnak időtartamát határozza meg, hogy külső kódoló által használt. Miután a csatorna a bejövő streamből kap, a következő formátumok valamelyikében lejátszás ügyfélalkalmazások számára az élő adatfolyam biztosíthat: Smooth Streaming, dinamikus adaptív Streameléshez HTTP (kötőjel), és a HTTP-Live Streaming (HLS) keresztül. Akkor, ha az élő adatfolyam-, HLS mindig dinamikusan csomagolni. A Media Services alapértelmezés szerint automatikusan HLS szegmens csomagolás aránya (töredék szegmensenkénti) érkezett az élő kódoló keyframe intervallum alapján számítja ki.

Az alábbi táblázat bemutatja, hogyan számítható ki a szegmens időtartama:

| Keyframe időköz | HLS szegmens csomagolás arány (FragmentsPerSegment) | Példa |
| --- | --- | --- |
| Kisebb vagy egyenlő, mint 3 másodpercenként |3:1 |Ha KeyFrameInterval (vagy GOP) 2 másodperc, alapértelmezett HLS szegmens csomagolás arány, 3: 1. Ezzel létrehoz egy 6 másodperces HLS szegmens. |
| 3-5 másodperc |2:1 |Ha KeyFrameInterval (vagy GOP) 4 másodperc, alapértelmezett HLS szegmens csomagolás arány, 2 és 1 közé esnek. Ezzel létrehoz egy 8 másodperces HLS szegmenst. |
| 5 másodpercnél nagyobb |1:1 |Ha KeyFrameInterval (vagy GOP) 6 másodperc, alapértelmezett HLS szegmens csomagolás arány, 1 és 1 közé esnek. Ezzel létrehoz egy 6 másodperces HLS szegmens. |

A csatorna kimeneti konfigurálásával és a FragmentsPerSegment beállítása ChannelOutputHls töredék száma szegmens aránya módosíthatja.

A keyframe intervallumértéket ChanneInput KeyFrameInterval tulajdonság beállításával is módosíthatja. Ha explicit módon beállítva KeyFrameInterval, a HLS szegmentálhatja csomagolás arány FragmentsPerSegment kiszámítása a korábban meghatározott szabályokat.  

Ha explicit módon beállítva KeyFrameInterval és FragmentsPerSegment is, a Media Services Ön által beállított értékeket használja.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Az IP-címek, amelyek jogosultak videókat közzétenni a csatorna adhat meg. Az alábbi engedélyezett IP-címet adhat meg:

* Egyetlen IP-címet (például 10.0.0.1)
* Az IP-címet és egy CIDR alhálózati maszk (például 10.0.0.1/22) használó IP-tartomány
* Az IP-címet és egy pontozott decimális alhálózati maszk (például 10.0.0.1(255.255.252.0)) használó IP-tartomány

Ha egyetlen IP-címek vannak megadva, és határoz meg szabálydefiníciót, az IP-cím engedélyezett. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

### <a name="channel-preview"></a>Csatorna előnézete
#### <a name="preview-urls"></a>Kép URL-címek
Csatorna előnézeti végpont (előzetes verzió URL-cím), amelyekkel előzetes megtekintéséhez és az adatfolyam érvényesítése előtt további feldolgozás és a szállítási adja meg.

A csatorna létrehozásakor az előnézeti URL-CÍMÉT érheti el. Meg az URL-cím beszerzése a csatorna nem kell lennie a **futtató** állapotát. A csatorna indítása adatok bevitele után megtekintheti az adatfolyam.

Jelenleg az előzetes adatfolyam továbbítható csak a töredezett MP4) (Smooth Streaming) formátumban, függetlenül a megadott bemeneti típus. Használhatja a [Smooth Streaming figyelő](http://smf.cloudapp.net/healthmonitor) player a smooth stream teszteléséhez. Egy player, amely az adatfolyam megtekintéséhez az Azure portálon is használható.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja, hogy az IP-címek, amelyek számára engedélyezett az előnézeti végpont való kapcsolódáshoz. Ha nincs megadva IP-cím, IP-címeket engedélyezett. Az alábbi engedélyezett IP-címet adhat meg:

* Egyetlen IP-címet (például 10.0.0.1)
* Az IP-címet és egy CIDR alhálózati maszk (például 10.0.0.1/22) használó IP-tartomány
* Az IP-címet és egy pontozott decimális alhálózati maszk (például 10.0.0.1(255.255.252.0)) használó IP-tartomány

### <a name="channel-output"></a>A kimeneti csatornát
Csatorna kimeneti kapcsolatos információkért tekintse meg a [Keyframe időköz](#keyframe_interval) szakasz.

### <a name="channel-managed-programs"></a>Programok csatorna által felügyelt
Egy csatorna programok társítva, amelyek segítségével szabályozhatja a élő stream szegmenseinek közzétételét és. Programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol egy csatorna rendelkezik a tartalom állandó adatfolyam, és a program hatóköre a következő néhány időzített esemény adott csatornán.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási időtartam is határozzák meg, hogy idő ügyfelek maximális száma is kérhet az idő az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program társítva, amelyeket az adatfolyamként továbbított tartalmat tárolja. Az eszköz az Azure storage-fiókok blokkolása blob tárolóhoz van leképezve, és az adott tároló blobként tárolja a fájlokat az eszköz. Így az ügyfelek megtekintheti az adatfolyamot a program közzétételéhez létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ez a lokátor segítségével megadhatja, hogy az ügyfelek számára adatfolyam-továbbítási URL-cím létrehozása.

Egy csatorna három egyidejűleg zajló programot, támogatja az, hogy létrehozhasson egy bejövő streamből több archívumot. Közzététele, és archiválja esemény különböző részeinek szükség szerint. Tegyük fel például, hogy az üzleti igény szerint a program 6 órát kell archiválni, de csak az elmúlt 10 perc szórási-e. Ezt két egyidejűleg zajló program létrehozásával érheti el. Egy program archiválására állítsa be az esemény hat órán keresztül, de a program nem lesz közzétéve. A program 10 perc archiválására van beállítva, és a program közzé van téve.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre egy új programot az egyes eseményekhez. Amikor készen áll a streamelésre és az archiválásra, indítsa el a program. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

Archivált tartalom törléséhez állítsa le és törölje a programot, és törölje a hozzá társított objektumot. Egy eszköz nem törölhető, ha a program akkor használja ezt a. A program először törölni kell.

Állítsa le és törölje a programot, után is felhasználók is játszani az archivált tartalmat, igény szerint lekért videóként amíg nem törli az eszköz. Ha szeretné megtartani az archivált tartalmakat, de nem érhető el az adatfolyamként történő, törölje a streamelési lokátort.

## <a id="states"></a>Csatorna állapotok és számlázási
Az aktuális állapot csatorna a lehetséges értékek a következők:

* **Leállítva**: a csatorna kezdeti állapotában Ez az a létrehozása után. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdési**: A csatorna indítása folyamatban van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Ha hiba lép fel, és a csatorna ad vissza a **leállítva** állapotát.
* **Futó**: A csatorna élő adatfolyamok tud feldolgozni.
* **Leállítása**: A csatorna leállítását. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**: A csatorna törlése folyamatban van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód.

| Csatorna állapota | Jelzése a portál kezelőfelületén | Számlázandó? |
| --- | --- | --- | --- |
| **Starting** |**Starting** |Nem (átmeneti állapot) |
| **Fut** |**Készen áll a** (nincs futó programok)<p><p>vagy<p>**Adatfolyam-** (legalább egy futó program) |Igen |
| **Stopping** |**Stopping** |Nem (átmeneti állapot) |
| **Leállt** |**Leállt** |Nem |

## <a id="cc_and_ads"></a>Lezárt feliratok és az ad beszúrási
Az alábbi táblázat ismerteti a támogatott szabványok lezárt feliratok és az ad beszúrásához.

| Standard | Megjegyzések |
| --- | --- |
| CEA-708 és EIA-608 (708/608) |CEA-708 és EIA-608 zárva-feliratok az Amerikai Egyesült Államokon és Kanadán szabványainak.<p><p>Jelenleg feliratok csak támogatott, ha a kódolt bemeneti adatfolyamban végzik. Kell használnia egy élő media encoder, amely a Media Services küldött kódolt adatfolyamban 608 vagy 708 feliratok is beszúrhat. A felhasználóknak a Media Services továbbítja a tartalmat beszúrt feliratok. |
| TTML belül .ismt (a szöveges számok Smooth Streaming) |A Media Services dinamikus becsomagolást lehetővé teszi, hogy az ügyfelek segítségével tartalmat a következő formátumban: DASH, HLS vagy Smooth Streaming. Azonban ha azt a betöltési töredékes MP4) (Smooth Streaming) .ismt (Smooth Streaming a nyomait szöveg) belüli feliratok esetében a biztosíthat az adatfolyam csak a Smooth Streaming-ügyfelek számára. |
| SCTE-35 |SCTE-35 egy digitális hirdetési beszúrási előkészíteni használt jelzésátviteli rendszer. Alsóbb rétegbeli fogadók a jel segítségével splice a adatfolyamba való mentésre hirdetésének a megadott időn belül. A bemeneti adatfolyamban ritka nyomon, SCTE-35 kell elküldeni.<p><p>Jelenleg az egyetlen támogatott bemeneti adatfolyam formázása, hogy rendelkezik ad jelek töredezett MP4) (Smooth Streaming). Az egyetlen támogatott kimeneti formátum Smooth Streaming is. |

## <a id="considerations"></a>Szempontok
Egy helyszíni élő kódoló többféle sávszélességű adatfolyamot küldeni egy csatorna használata esetén alkalmazza a következő korlátozások vonatkoznak:

* Győződjön meg arról, hogy elegendő szabad internetkapcsolat adatokat küldeni a feldolgozást, a pontokat.
* Egy másodlagos használatával feldolgozó URL-CÍMÉT további sávszélesség szükséges.
* A bejövő többféle sávszélességű adatfolyamot legfeljebb 10 videominőséget szintek (réteg) és legfeljebb 5 zeneszámok rendelkezhet.
* A legmagasabb átlagos sávszélességű bármely videominőséget szintjének 10 MB/s alatt kell lennie.
* A video- és adatfolyamok átlagos bitrates összesített 25 MB/s alatt kell lennie.
* A csatorna a bemeneti protokoll nem módosítható, vagy a hozzá tartozó programok futnak. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A csatorna fogadására képes egy egyféle sávszélességű. De a csatorna nem dolgozza fel az adatfolyam, mert az ügyfélalkalmazások számára is kap egy egyféle sávszélességű adatfolyamot. (Nem ajánlott ezt a lehetőséget.)

Az alábbiakban a csatornák és kapcsolódó összetevők kapcsolódó egyéb szempontok:

* Minden alkalommal, amikor újrakonfigurálja az élő kódoló, hívja az **alaphelyzetbe** módszer a csatornán. A csatorna visszaállítása előtt akkor állítsa le a programot. A csatorna visszaállítása után indítsa újra a programot.

  > [!NOTE]
  > A program újraindításakor kell társíthatja egy új eszközt, és hozzon létre egy új lokátort. 
  
* Egy csatornát csak akkor, ha az állítható le a **futtató** állapotot és a csatornán minden program lett leállítva.
* Alapértelmezés szerint csak öt csatornák adhat hozzá a Media Services-fiókhoz. További információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).
* Csak akkor, ha a csatorna kell fizetni az **futtató** állapotát. További információkért lásd: a [állapotok és számlázási csatorna](media-services-live-streaming-with-onprem-encoders.md#states) szakasz.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Visszajelzés
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Ajánlott helyszíni kódolókkal](media-services-recommended-encoders.md)

[Az Azure Media Services töredezett MP4 életét betöltési meghatározása](media-services-fmp4-live-ingest-overview.md)

[Az Azure Media Services áttekintése és gyakori alkalmazási esetei](media-services-overview.md)

[Media Services alapfogalmaiért](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
