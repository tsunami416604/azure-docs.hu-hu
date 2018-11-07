---
title: Többszörös átviteli sebességű Streamek --Azure létrehozó helyszíni kódolókkal történő továbbítását az élő Stream |} A Microsoft Docs
description: 'Ez a témakör ismerteti, hogyan állítható be egy csatornát, amely egy többszörös sávszélességű élő streamet fogad egy helyszíni kódolót. A stream lejátszás ügyfélalkalmazások legalább egy streamelési végpontok, a következő adaptív adatfolyam-továbbítási protokollok egyikével majd kézbesíthető: HLS, Smooth Streaming és DASH.'
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/12/2017
ms.author: cenkd;juliako
ms.openlocfilehash: e2d65c107d57d50bc15d5a1cd1698491bb607e25
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262233"
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Élő Stream továbbítása többszörös átviteli sebességű streamet létrehozó helyszíni kódolókkal

> [!NOTE]
> 2018. május 12., az élő csatornák indítása fogja többé nem támogatja a RTP/MPEG-2 adatátviteli stream betöltési protokollját. Telepítse át a RTP/MPEG-2 RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollokra.

## <a name="overview"></a>Áttekintés
Az Azure Media Services egy *csatorna* folyamat feldolgozásának live-streaming-tartalmat jelöli. Egy csatorna bemeneti élő Streamek kap a két módszer egyikével:

* Egy helyszíni élőadás-kódoló elküldi a csatornára, amely nincs engedélyezve a valós idejű kódolás a Media Services adatfolyam egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4). A feldolgozott adatfolyamok további feldolgozás nélkül csatornákon keresztül adja át. Ezt a metódust meghívják *átmenő*. Az élő kódolók olyan is küldhet egy egyszeres átviteli sebességű streamet fogadó csatornát, amely az élő kódolás nincs engedélyezve, de nem ajánlott, amelyek. A Media Services továbbítja az adatfolyamot, akiknek kérésre.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb élő közvetítést végezni.


* Egy helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a csatornának, amely engedélyezve van a valós idejű kódolás a Media Services a következő formátumok egyikében: RTMP vagy Smooth Streaming (darabolt MP4). A csatorna végez a bejövő egyszeres átviteli sebességű streamet, és többféle sávszélességű (adaptív) video-adatfolyamot élő kódolás. A Media Services továbbítja az adatfolyamot, akiknek kérésre.

A Media Services 2.10 kiadástól kezdve, amikor létrehoz egy csatornát, megadhatja a bemeneti streamet a csatorna módját. Megadhatja azt is, hogy kívánja-e a csatorna élő kódolásra a Stream. Erre két lehetősége van:

* **Továbbítása**: Adja meg ezt az értéket, ha azt tervezi, hogy a helyszíni élő kódoló, amely rendelkezik egy többszörös sávszélességű streammé (csatlakoztatott stream) kimenetként használja. Ebben az esetben a beérkező streamben áthalad a kimeneti bármely kódolás nélkül. Ez a csatorna 2,10 kiadása előtt viselkedését. Ez a cikk tájékoztatást nyújt a csatornák a típus használata.
* **Live Encoding**: válassza ezt az értéket, ha azt tervezi, hogy az egyféle sávszélességű élő adatfolyamot egy többszörös sávszélességű streamet kódolása a Media Services használatával. A csatorna élő kódolási és egy **futó** állapota tekintetében számlázási számítunk fel díjat. Azt javasoljuk, hogy azonnal leállítja a futó csatornák után az élő közvetítés esemény nagyon óránkénti díjak elkerülése érdekében. A Media Services továbbítja az adatfolyamot, akiknek kérésre.

> [!NOTE]
> Ez a cikk nem élő kódolásra képes csatornák attribútumait ismerteti. További információ az élő kódolásra képes csatornák használata: [élő adatfolyam továbbítása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása a](media-services-manage-live-encoder-enabled-channels.md).
>
>Ajánlott helyszíni kódolók használatával kapcsolatos információkért lásd: [ajánlott helyszíni kódolók](media-services-recommended-encoders.md).

A következő ábra jelöli egy élő közvetítés munkafolyamatot, amely egy helyszíni élő kódoló használatával és többféle sávszélességű RTMP vagy darabolt MP4 (Smooth Streaming) Streamek kimenetként.

![Élő munkafolyamat][live-overview]

## <a id="scenario"></a>Élő közvetítés forgatókönyve
Az alábbi lépések bemutatják a gyakori élő közvetítés alkalmazások létrehozásához elvégzendő feladatokat.

1. Csatlakoztasson egy videokamerát a számítógéphez. Indítsa el, és konfigurálja a helyszíni élő kódoló, amely rendelkezik egy többszörös sávszélességű RTMP vagy darabolt MP4 (Smooth Streaming) stream kimenetként. További tudnivalók: [Azure Media Services RMTP-támogatása és valós idejű kódolók](https://go.microsoft.com/fwlink/?LinkId=532824)

    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Csatorna létrehozása és elindítása.

3. Kérje le a csatorna betöltési URL-címe.

    Az élő kódoló a bemeneti URL-címet használ a stream küldésére a csatornát.
4. A csatorna előnézeti URL-Címének beolvasása.

    Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.
5. Hozzon létre egy programot.

    Az Azure portal használatakor létrehozhat egy programot egy objektumot is létrehoz.

    Ha használja a .NET SDK-t vagy a REST, hozzon létre egy objektumot, és adja meg, hogy az objektum használja, ha a program létrehozása szüksége.
6. Tegye közzé a a programhoz társított adategységet.   

    >[!NOTE]
    >Az Azure Media Services-fiók létrehozásakor egy **alapértelmezett** streamvégpontot a fiókhoz a **leállítva** állapota. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie.

7. Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot.

8. További lehetőségként jelzést adhat a valós idejű kódolónak egy hirdetés elindítására. A hirdetés a kimeneti adatfolyamba lesz beszúrva.

9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

10. Törölje a programot (és ha szükséges, törölje az objektumot).     

## <a id="channel"></a>A csatorna és a kapcsolódó összetevők leírása
### <a id="channel_input"></a>Bemeneti Channel (betöltését) konfigurációk
#### <a id="ingest_protocols"></a>Betöltési streamelési protokoll
Media Services támogatja az élő hírcsatornák fürtjét többszörös átviteli sebességű töredékes MP4 és a többszörös sávszélességű RTMP, streamelési protokollok használatával. Ha az RTMP betöltési streamelési protokoll van megadva, a két (bemeneti) végpontok betöltési csatorna jön létre:

* **Elsődleges URL-cím**: Itt adhatja meg a teljes URL-címét a csatorna elsődleges RTMP betöltési végpont.
* **Másodlagos URL-cím** (nem kötelező): Itt adhatja meg a teljes URL-címét a csatorna másodlagos RTMP betöltési végpont.

A másodlagos URL-cím használata növelheti a tartósság és a tartalék tűréshatár, a betöltés stream (valamint kódoló feladatátvételi és hibatűréssel kezelik), ha kifejezetten rendszergazdák számára a következő esetekben:

- Egyetlen kódoló double-leküldése az elsődleges és másodlagos URL-címek:

    Ez a forgatókönyv fő célja, hogy további rugalmasságot biztosítanak a hálózati ingadozások által megkövetelt és jitters. Néhány RTMP kódolók kezelik a hálózati kimaradások is. Ha egy hálózati kapcsolat bontása történik, a kódoló előfordulhat, hogy leállítja a kódolást, és majd újracsatlakozás történik, ha nem küldenek a pufferelt adatokat. Ennek hatására a folytonosság megszakítását és az adatvesztést. Hálózati kimaradások hibás hálózati vagy karbantartás miatt fordulhat elő az Azure részéről. Elsődleges és másodlagos URL-címek csökkentheti a hálózati problémák, és adjon meg egy ellenőrzött frissítési folyamat. Minden alkalommal, amikor egy ütemezett hálózati kapcsolat bontása történik, a Media Services kezeli az elsődleges és másodlagos bontja a kapcsolatot, és biztosít egy késleltetett válassza le a kettő között. Kódolók majd kell tartani az adatok küldésének és újracsatlakozás újra. A sorrend a bontja a kapcsolatot véletlenszerű lehetnek, de mindig lesz késleltetés elsődleges és másodlagos vagy másodlagos/elsődleges URL-címek között. Ebben a forgatókönyvben a kódoló még mindig a hibaérzékeny pont.

- Több kódolóval, minden egyes Encoder, hogyan lehet továbbítani rá egy dedikált pontra:

    Ebben a forgatókönyvben két kódoló biztosít, és feltölti a redundanciát. Ebben a forgatókönyvben encoder1 leküldi az elsődleges URL-címet, és encoder2 leküldi a másodlagos URL-CÍMÉT. Ha egy kódoló nem sikerül, a többi kódoló is tartsa adatküldés. Adatredundancia tárolható, mert a Media Services nem szakítja meg elsődleges és másodlagos URL-címek egy időben. Ebben a forgatókönyvben azt feltételezi, hogy kódolók időt szinkronizálva, és adja meg ugyanazt az adatot.  

- Több kódolók double-leküldése az elsődleges és másodlagos URL-címek:

    Ebben a forgatókönyvben mindkét kódolók adatok elküldése az elsődleges és másodlagos URL-címeket. Ez biztosítja a legjobb megbízhatóság és a hibatűrést, valamint redundáns adattárolás érdekében. Ez a forgatókönyv tűri mindkét kódolóhibák, és megszakítja a kapcsolatot, akkor is, ha egy kódoló is leáll. Azt feltételezi, hogy kódolók időt szinkronizálva, és adja meg ugyanazt az adatot.  

Élő kódolók RTMP kapcsolatos információkért lásd: [Azure Media Services RMTP-támogatása és valós idejű kódolók](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Betöltési URL-címek (végpontok)
Egy csatorna bemeneti végpontot biztosít (betöltési URL-címe), hogy Ön adja meg az élő kódoló, leküldheti a kódoló Streamek a saját csatornákhoz.   

A betöltés URL-címek kérheti le a csatorna létrehozásakor. Is ezek URL-címeket, a csatorna nem kell lennie a **futó** állapota. Ön megkezdheti az adatok leküldése a csatornát, amikor a csatorna kell lennie a **futó** állapota. Adatok bevitele a csatorna indítása után megtekintheti a fájlfolyamot az előnézeti URL-címet.

Lehetősége van feldolgozására darabolt MP4 (Smooth Streaming) élő streamet SSL-kapcsolaton keresztül. Hogy SSL-en keresztül, ügyeljen arra, hogy a bemeneti URL-címet frissíteni a HTTPS-re. SSL-en keresztül jelenleg nem képes feldolgozni RTMP.

#### <a id="keyframe_interval"></a>Kulcsképkocka időköz
Egy helyszíni élő kódoló és többféle sávszélességű adatfolyamot létrehozásához használja, a kulcsképkocka időköz Itt adhatja meg a csoport a képek (Képcsoporttal) időtartamát, hogy külső kódoló által használt. A csatorna fogadja a bejövő streamből, miután biztosíthat az élő stream lejátszás ügyfélalkalmazások, a következő formátumok egyikében: Smooth Streaming, dinamikus adaptív Streamelés HTTP (DASH) vagy HTTP Live Streaming (HLS) keresztül. Amikor élő streaming, HLS mindig dinamikusan csomagolva. A Media Services alapértelmezés szerint automatikusan HLS szegmens csomagolási arány (szegmensenként szilánkok) érkező élő kódolója kulcsképkocka intervallum alapján számítja ki.

Az alábbi táblázat bemutatja, hogyan kerül kiszámításra a szegmens időtartama:

| Kulcsképkocka időköz | HLS szegmens csomagolási arány (FragmentsPerSegment) | Példa |
| --- | --- | --- |
| Kisebb vagy egyenlő, mint 3 másodperc |3:1 |Ha KeyFrameInterval (vagy Képcsoporttal) 2 másodperc, alapértelmezett HLS szegmens csomagolási arány, 3, 1. Ez létrehoz egy 6 másodperc HLS szegmens. |
| a 3-5 másodperc |2:1 |Ha KeyFrameInterval (vagy Képcsoporttal) 4 másodperc, alapértelmezett HLS szegmens csomagolási arány, 2: 1. Ez létrehoz egy 8 másodperces HLS-szegmenst. |
| Nagyobb, mint 5 másodperc |1:1 |Ha KeyFrameInterval (vagy Képcsoporttal) 6 másodperc, alapértelmezett HLS szegmens csomagolási arány, 1 és 1 között. Ez létrehoz egy 6 másodperc HLS szegmens. |

A töredékek-per-szegmens arány a csatorna kimeneti konfigurálásával, valamint ChannelOutputHls FragmentsPerSegment beállítást módosíthatja.

A kulcsképkocka intervallum értéke módosíthatja a ChanneInput KeyFrameInterval tulajdonságának beállításával is. Ha explicit módon KeyFrameInterval, a HLS oszthatja a csomagolási arány FragmentsPerSegment kiszámítása a korábban meghatározott szabályokat.  

Ha explicit módon KeyFrameInterval és FragmentsPerSegment is, a Media Services használ az Ön által beállított értékek.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja, hogy az IP-címek, amelyek jogosultak videókat közzétenni ezt a csatornát. Engedélyezett IP-címet a következők szerint adható meg:

* Egyetlen IP-címet (például 10.0.0.1)
* Az IP-címtartomány által használt IP-címet és egy CIDR alhálózati maszk (például 10.0.0.1/22)
* Egy IP-címtartományt, amely egy IP-címet és egy pontozott decimális alhálózati maszk (például 10.0.0.1(255.255.252.0)) használ

Ha nincsenek IP-címek vannak megadva, és nem határoz meg szabálydefiníciót, IP-cím nem engedélyezett. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

### <a name="channel-preview"></a>Csatorna előnézete
#### <a name="preview-urls"></a>Előnézeti URL-címek
Csatorna előnézeti végpont (előzetes verzió URL-cím), amellyel és ellenőrzéséhez a stream előnézetének a feldolgozás folytatása és a továbbítás előtti adja meg.

Az előnézeti URL-cím kérheti le a csatorna létrehozásakor. Meg az URL-Címének lekéréséhez a csatorna nem kell lennie a **futó** állapota. Adatok bevitele a csatorna indítása után megtekintheti a stream.

Az előzetes verzió adatfolyam letöltéséhez jelenleg csak a darabolt MP4 (Smooth Streaming) formátumban, a megadott bemeneti típusától függetlenül. Használhatja a [Smooth Streaming állapotfigyelőjének](http://playready.directtaps.net/smoothstreaming/) lejátszó a smooth stream kipróbálásához. A stream megtekintése az Azure Portalon üzemeltetett játékos is használható.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja, hogy az IP-címek, amelyek jogosultak az előzetes verzió végponthoz csatlakozik. Nincsenek IP-címek meg van adva, ha engedélyezett IP-címeket. Engedélyezett IP-címet a következők szerint adható meg:

* Egyetlen IP-címet (például 10.0.0.1)
* Az IP-címtartomány által használt IP-címet és egy CIDR alhálózati maszk (például 10.0.0.1/22)
* Egy IP-címtartományt, amely egy IP-címet és egy pontozott decimális alhálózati maszk (például 10.0.0.1(255.255.252.0)) használ

### <a name="channel-output"></a>Csatorna kimenet
Csatorna kimeneti kapcsolatos információkért tekintse meg a [Kulcsképkocka időköz](#keyframe_interval) szakaszban.

### <a name="channel-managed-programs"></a>Csatorna által felügyelt programok
A csatorna programok társítva, amely a storage, az élő stream szegmenseinek közzétételét és használhatja. Programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol csatorna tartalmaz egy állandó stream a tartalom és a egy programot az adott csatornán néhány időzített esemény hatókörét.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Archiválási időszak hossza is szabja meg, az ügyfelek maximális számát is kérhet időbeli az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden egyes program társítva egy eszköz, amely a adatfolyamként továbbított tartalmat tárolja. Az eszköz le van képezve egy block blob-tárolóba az Azure storage-fiókban, és a tárolóban lévő blobok formájában tárolja az adategységben található fájlokat. A program közzétételéhez, így az ügyfelek tekintheti meg a streamet, létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ezt a keresőt használhatja a streamelési URL-cím összeállítását biztosíthat az ügyfelek számára.

Egy csatorna három egyidejűleg zajló programot, támogatja, így az egy bejövő streamből több archívumot is létrehozhat. Tehet közzé és archív tárolási szint esemény különböző részeit, igény szerint. Képzeljünk el például, hogy az üzleti igény szerint-e a program 6 órájának archiválására, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Egy program van beállítva, az esemény 6 órájának archiválására, de ne tegye közzé a programot. A másik programot 10 perc archiválására van beállítva, és a program közzé van téve.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre egy új programot az egyes eseményekhez. Amikor készen áll a streamelésre és az archiválásra, indítsa el a programot. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

Archivált tartalom törléséhez állítsa le és törölje a programot, és ezután törölje a hozzá társított objektumot. Egy eszköz nem törölhető, ha egy programot használ, azt. A program először törölni kell.

Állítsa le és törölje a programot, után is felhasználói is streamelni az archivált tartalmakat, igény szerinti videóként, amíg nem törli az eszköz. Ha szeretné megtartani az archivált tartalmakat, de nem rendelkezik elérhető streamelésre, törölje a streamelési lokátort.

## <a id="states"></a>Csatorna állapotok és számlázás
A csatorna aktuális állapota lehetséges értékei a következők:

* **Leállítva**: Ez a csatorna kezdeti állapota a létrehozása után. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdési**: A csatorna indítás alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Hiba esetén a csatorna visszatér a **leállítva** állapota.
* **Futó**: A csatorna élő Streamek feldolgozására képes.
* **Leállítása**: A csatorna leállítás alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**: A csatorna törlés alatt van. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód.

| Csatorna állapota | Jelzése a portál kezelőfelületén | Számlázandó? |
| --- | --- | --- | --- |
| **Indítása** |**Indítása** |Nem (átmeneti állapot) |
| **Fut** |**Kész** (nincs futó program)<p><p>vagy<p>**Streamelési** (legalább egy futó program) |Igen |
| **Leállítása** |**Leállítása** |Nem (átmeneti állapot) |
| **Leállítva** |**Leállítva** |Nem |

## <a id="cc_and_ads"></a>Feliratok és az ad beszúrási lezárva
Az alábbi táblázat bemutatja a lezárt feliratozás és az ad beszúráshoz támogatott szabványok.

| Standard | Megjegyzések |
| --- | --- |
| CEA-708 és EIA-608 (708/608) |CEA-708 EIA-608 jsou feliratozáshoz az Egyesült Államok és Kanada szabványainak.<p><p>Jelenleg feliratok támogatott csak akkor, ha a kódolású bemeneti Stream végezni. Egy 608 vagy 708 feliratot szúrhat be a kódolt Stream, amelyet elküld a Media Services élő media encoder használni kell. A felhasználóknak a Media Services továbbítja a tartalmat a beszúrt feliratok. |
| TTML belül .ismt (szövegsávok Smooth Streaming) |A Media Services dinamikus becsomagolást lehetővé teszi, hogy az ügyfelek stream tartalmát, a következő formátumok egyikében: DASH, HLS vagy Smooth Streaming. Azonban, ha Ön a betöltési töredékes MP4 (Smooth Streaming) .ismt (szövegsávok Smooth Streaming) belüli feliratok, a csak a Smooth Streaming-ügyfelek is nyújtani a streamet. |
| SCTE-35 |SCTE – 35 egy rendszer digitális jelképző, amellyel egy táblázatvizualizációban adható hirdetési beszúrási. Alsóbb rétegbeli fogadók a jel használatával splice a megszabott időben hirdetésének a streambe. SCTE – 35, a bemeneti Stream ritka nyomon kell küldeni.<p><p>Jelenleg az egyetlen támogatott bemeneti stream formázhatja a, hogy rendelkezik ad jelek töredezett MP4 (Smooth Streaming). Az egyetlen támogatott kimeneti formátum Smooth Streaming is. |

## <a id="considerations"></a>Megfontolandó szempontok
Abban az esetben, ha egy helyszíni élő kódoló és többféle sávszélességű adatfolyamot küldeni egy csatornát használja, a következő korlátozások vonatkoznak:

* Győződjön meg arról, hogy rendelkezik elegendő szabad Internet adatokat küldeni a betöltés pontokat.
* Egy másodlagos használatával betöltési URL-címe további sávszélesség szükséges.
* A bejövő és többféle sávszélességű adatfolyamot legfeljebb 10 videó minősége szintek (réteg) és a egy legfeljebb 5 hangsáv is rendelkezhet.
* Bármely videó minősége szintjének legmagasabb átlagos átviteli 10 MB/s alatt kell lennie.
* Az átlagos bitsebességre való átkódolása a video- és adatfolyamok összesített 25 MB/s alatt kell lennie.
* A csatorna a bemeneti protokoll nem módosítható, vagy a hozzá tartozó programok már elindultak. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A csatorna feldolgozására képes egy egyféle sávszélességű. Azonban mivel a csatorna nem dolgozza fel az adatfolyamot, az ügyfélalkalmazások is kap egy egyféle sávszélességű adatfolyamot. (Ezt a beállítást nem ajánlott.)

Az alábbiakban az egyéb csatornákkal és kapcsolódó összetevők működő kapcsolatos szempontok:

* Minden alkalommal, amikor újrakonfigurálja az élő kódoló, hívja a **alaphelyzetbe** metódus a csatornán. Mielőtt alaphelyzetbe állítja a csatornát, akkor állítsa le a programot. Ha alaphelyzetbe állítja a csatornát, indítsa újra a program.

  > [!NOTE]
  > A program újraindításakor kell társíthatja azt egy új eszközt, és hozzon létre egy új lokátort. 
  
* Egy csatornát csak akkor, amikor az van állítható le a **futó** állapotot és a csatorna a Minden program rendelkezik le lett állítva.
* Alapértelmezés szerint csak öt csatornák adhat hozzá a Media Services-fiók. További információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).
* Csak akkor, ha a csatorna a számlázás a **futó** állapota. További információkért lásd: a [állapotok és számlázási Channel](media-services-live-streaming-with-onprem-encoders.md#states) szakaszban.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Visszajelzés
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Ajánlott helyszíni kódolók](media-services-recommended-encoders.md)

[Az Azure Media Services darabolt MP4 életét specifikáció feldolgozása](media-services-fmp4-live-ingest-overview.md)

[Az Azure Media Services áttekintése és gyakori alkalmazási esetei](media-services-overview.md)

[A Media Services – alapelvek](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
