---
title: Élő közvetítés a helyszíni kódolókkal, amelyek többbitrátású adatfolyamokat hoznak létre - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogyan állíthat be egy csatornát, amely többbites átviteli sebességű élő közvetítést fogad egy helyszíni kódolótól.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c0d19d68d016a47762fb5d2646ea6ccf74d3ef75
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476552"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Többbites átviteli sebességű élő közvetítést fogadó csatornák kal a helyszíni kódolóktól

> [!NOTE]
> 2018. május 12-től az élő csatornák már nem támogatják az RTP/MPEG-2 átviteli adatfolyam-betöltési protokollt. Az RTP/MPEG-2 protokollról átkell térni az RTMP-re vagy a töredezett MP4 (Smooth Streaming) protokollokra.

## <a name="overview"></a>Áttekintés
Az Azure Media Servicesben a *csatorna* egy folyamatot jelöl az élő közvetítésű tartalom feldolgozásához. A csatorna kétféleképpen fogad élő bemeneti adatfolyamokat:

* A helyszíni élő kódoló többátviteli sebességű RTMP vagy smooth stream (töredezett MP4) adatfolyamot küld arra a csatornára, amely nincs engedélyezve élő kódolásra a Media Services szolgáltatással. A bevitt adatfolyamok további feldolgozás nélkül haladnak át a csatornákon. Ezt a módszert *pass-through-nak nevezzük.* Az élő kódoló is küldhet egy egybitráta-adatfolyamot egy olyan csatornára, amely nincs engedélyezve az élő kódoláshoz, de ezt nem javasoljuk. A Media Services az adatfolyamot az azt kérő ügyfeleknek továbbítja.

  > [!NOTE]
  > Az áthaladási módszer használata a leggazdaságosabb módja az élő közvetítésnek.


* A helyszíni élő kódoló egyátviteli adatfolyamot küld a csatornának, amely engedélyezve van az élő kódoláshoz a Media Services szolgáltatással a következő formátumok egyikén: RTMP vagy Smooth Streaming (töredezett MP4). A csatorna ezután élő kódolást hajt végre a bejövő egysávszélességű adatfolyamról egy többsávszélességű (adaptív) videoadatfolyamba. A Media Services az adatfolyamot az azt kérő ügyfeleknek továbbítja.

A Media Services 2.10 kiadással kezdve, amikor létrehoz egy csatornát, megadhatja, hogy a csatorna hogyan fogadja a bemeneti adatfolyamot. Azt is megadhatja, hogy a csatorna végrehajtsa-e az adatfolyam élő kódolását. Erre két lehetősége van:

* **Áthaladás:** Adja meg ezt az értéket, ha azt tervezi, hogy egy helyszíni élő kódoló, amely egy több-bitráta-adatfolyam (egy átadó stream) kimenetként. Ebben az esetben a bejövő adatfolyam kódolás nélkül halad át a kimenetre. Ez a 2.10-es kiadás előtti csatorna viselkedése. Ez a cikk az ilyen típusú csatornákkal való munkáról ad részletes információt.
* **Élő kódolás:** Akkor válassza ezt az értéket, ha a Media Services használatával szeretné kódolni az egybitrátús élő adatfolyamot egy többátviteli adatfolyamba. Az élő kódolási csatorna **futó** állapotban hagyása számlázási díjakat von maga után. Javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje az óránkénti díjakat. A Media Services az adatfolyamot az azt kérő ügyfeleknek továbbítja.

> [!NOTE]
> Ez a cikk az élő kódolásra nem engedélyezett csatornák attribútumait ismerteti. Az élő kódolásra engedélyezett csatornákkal való munkáról az [Élő közvetítés az Azure Media Services használatával többbites adatfolyamok létrehozásáról](media-services-manage-live-encoder-enabled-channels.md)nyújt tájékoztatást.
>
>A helyszíni kódolók ajánlott használata a [helyszíni kódolók](media-services-recommended-encoders.md)használata című témakörben található.

Az alábbi ábra egy élő közvetítésű munkafolyamatot ábrázol, amely egy helyszíni élő kódolót használ a többbitrátású RTMP vagy a töredezett MP4 (Smooth Streaming) adatfolyamok kimenetként való lejátszásához.

![Élő munkafolyamat][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Gyakori élő közvetítési forgatókönyv
A következő lépések a gyakori élő közvetítéses alkalmazások létrehozásával kapcsolatos feladatokat ismertetik.

1. Csatlakoztasson a számítógéphez egy videokamerát. Indítson el és konfiguráljon egy helyszíni élő kódolót, amely többátviteli RTMP-t vagy töredezett MP4 -adatfolyamot (Smooth Streaming) rendelkezik kimenetként. További tájékoztatást az [Azure Media Services RTMP Support and Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824) (Az Azure Media Services RTMP-támogatása és az élő kódolók) című cikk nyújt.

    Ezt a lépést a csatorna létrehozása után is végrehajthatod.
2. Csatorna létrehozása és elindítása.

3. A csatornabetöltési URL-cím beolvasása.

    Az élő kódoló a betöltési URL-címet használja az adatfolyam nak a csatornára küldéséhez.
4. A csatorna előnézeti URL-címének beolvasása.

    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.
5. Hozzon létre egy programot.

    Az Azure Portal használatakor egy program létrehozása is létrehoz egy eszközt.

    A .NET SDK vagy REST használatakor létre kell hoznia egy eszközt, és meg kell adnia, hogy ezt az eszközt használja a program létrehozásakor.
6. Tegye közzé a programhoz társított eszközt.   

    >[!NOTE]
    >Az Azure Media Services-fiók létrehozásakor egy **alapértelmezett** streamelési végpont ot ad hozzá a fiókjához **a Leállított** állapotban. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie.

7. Indítsa el a programot, ha készen áll a streamelésre és az archiválásra.

8. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés a kimeneti adatfolyamba lesz beszúrva.

9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

10. Törölje a programot (és tetszés szerint törölje az eszközt).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>A csatorna és a hozzá kapcsolódó összetevők leírása
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Csatornabemeneti (betöltési) konfigurációk
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Streamelési protokoll betöltése
A Media Services támogatja az élő hírfolyamok betöltését a többbitrátású töredezett MP4 és a többbitrátású RTMP folyamatos átviteli protokollok használatával. Ha az RTMP betöltési streamelési protokoll tválasztja el, két betöltési (bemeneti) végpont jön létre a csatornához:

* **Elsődleges URL:** Megadja a csatorna elsődleges RTMP-betöltési végpontjának teljesen minősített URL-címét.
* **Másodlagos URL (nem** kötelező): Megadja a csatorna másodlagos RTMP-betöltési végpontjának teljesen minősített URL-címét.

Használja a másodlagos URL-címet, ha javítani szeretné a betöltési adatfolyam (valamint a kódoló feladatátvétele és a hibatűrés) tartósságát és hibatűrését, különösen a következő esetekben:

- Egyetlen kódoló dupla rámenős mind az elsődleges, mind a másodlagos URL-címekre:

    Ennek a forgatókönyvnek a fő célja, hogy nagyobb rugalmasságot biztosítson a hálózati ingadozásokés idegesség szempontjából. Egyes RTMP kódolók nem kezelik jól a hálózati kapcsolatbontásokat. Hálózati kapcsolat bontásakor előfordulhat, hogy a kódoló leállítja a kódolást, majd nem küldi el a pufferelt adatokat, amikor újra csatlakozik. Ez megszakításokat és adatvesztést okoz. Hálózati kapcsolatbontások akkor fordulhat elő, mert egy rossz hálózat vagy karbantartás az Azure oldalán. Az elsődleges/másodlagos URL-címek csökkentik a hálózati problémákat, és ellenőrzött frissítési folyamatot biztosítanak. Minden alkalommal, amikor egy ütemezett hálózati kapcsolat bontása történik, a Media Services kezeli az elsődleges és másodlagos kapcsolatbontást, és késleltetett kapcsolatot biztosít a kettő között. A kódolóknak ezután van idejük az adatok küldésére és az újracsatlakozásra. A kapcsolatbontások sorrendje véletlenszerű lehet, de mindig lesz késleltetés az elsődleges/másodlagos vagy másodlagos/elsődleges URL-címek között. Ebben a forgatókönyvben a kódoló továbbra is az egyetlen hibapont.

- Több kódoló, minden kódoló egy kijelölt pontra:

    Ebben a forgatókönyvben a kódoló és a betöltési redundancia is biztosít. Ebben a forgatókönyvben a kódoló1 leküldéses az elsődleges URL-címet, és kódoló2 leküldéses a másodlagos URL-címet. Ha egy kódoló meghibásodik, a másik kódoló tovább küldhet adatokat. Az adatredundancia megtartható, mert a Media Services nem bontja le egyszerre az elsődleges és másodlagos URL-címeket. Ebben a forgatókönyvben feltételezi, hogy a kódolók idő szinkronizált, és adja meg pontosan ugyanazokat az adatokat.  

- Több kódoló kétszer nyomja mind az elsődleges, mind a másodlagos URL-eket:

    Ebben a forgatókönyvben mindkét kódoló leküldéses adatokat az elsődleges és másodlagos URL-címekre. Ez biztosítja a legjobb megbízhatóságot és hibatűrést, valamint az adatredundanciát. Ez a forgatókönyv a kódoló hibákat és a kapcsolatbontásokat is eltűrheti, még akkor is, ha egy kódoló leáll. Feltételezi, hogy a kódolók időszinkronizált, és pontosan ugyanazokat az adatokat.  

Az RTMP élő kódolóiról az [Azure Media Services RTMP-támogatás és a Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824)című témakörben talál további információt.

#### <a name="ingest-urls-endpoints"></a>BETÖLTÉSI URL-címek (végpontok)
A csatorna biztosítja az élő kódolóban megadott bemeneti végpontot (betöltési URL-címet), így a kódoló leküldéses adatfolyamokat a csatornákba.   

A betöltési URL-eket a csatorna létrehozásakor kaphatja meg. Ahhoz, hogy ezeket az URL-eket, a csatorna nem kell a **Futó** állapotban. Ha készen áll az adatok csatornára történő lenyomására, a csatornának **Futó** állapotban kell lennie. Miután a csatorna elkezdte az adatok betöltését, megtekintheti az adatfolyam előnézeti URL-címén keresztüli előnézeti előnézeti előnézeti előnézeti előnézeti előnézetét.

Lehetősége van egy töredezett MP4 (Smooth Streaming) élő közvetítés TLS-kapcsolaton keresztül történő betöltésére. A TLS-en keresztül történő betöltéshez frissítse a betöltési URL-címet HTTPS-re. Jelenleg nem lehet rtmp-t bebefekni a TLS-en keresztül.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Kulcskép időköze
Ha egy helyszíni élő kódolót használ a többátviteli adatfolyam létrehozásához, a kulcskép-időközed határozza meg a képek csoportjának (GOP) időtartamát, ahogy azt a külső kódoló használja. Miután a csatorna megkapta ezt a bejövő adatfolyamot, az élő közvetítést az alábbi formátumok bármelyikében továbbíthatja az ügyféllejátszási alkalmazásoknak: Sima streamelés, dinamikus adaptív streamelés HTTP-n (DASH) és HTTP Live Streaming (HLS). Élő közvetítés közben a HLS mindig dinamikusan van csomagolva. Alapértelmezés szerint a Media Services automatikusan kiszámítja a HLS szegmens csomagolási arányát (szegmensenkénti töredékek) az élő kódolótól kapott kulcskép-időköz alapján.

Az alábbi táblázat a szegmens időtartamának kiszámítását mutatja be:

| Kulcskép időköze | HLS szegmens csomagolási arány (FragmentsPerSegment) | Példa |
| --- | --- | --- |
| Legfeljebb 3 másodperc |3:1 |Ha a KeyFrameInterval (vagy GOP) 2 másodperc, az alapértelmezett HLS szegmens csomagolási arány 3 az 1-hez. Ez létrehoz egy 6 másodperces HLS szegmenst. |
| 3-5 másodperc |2:1 |Ha a KeyFrameInterval (vagy GOP) 4 másodperc, az alapértelmezett HLS szegmens csomagolási arány 2:1. Ez létrehoz egy 8 másodperces HLS szegmenst. |
| 5 másodpercnél hosszabb |1:1 |Ha a KeyFrameInterval (vagy GOP) 6 másodperc, az alapértelmezett HLS szegmens csomagolási arány 1:1. Ez létrehoz egy 6 másodperces HLS szegmenst. |

A szegmensenkénti töredékek aránya a csatorna kimenetének konfigurálásával és a FragmentsPerSegment beállítással módosítható a ChannelOutputHLs-en.

A keyframe-intervallum értékét a ChannelInput KeyFrameInterval tulajdonságának beállításával is módosíthatja. Ha explicit módon állítja be a KeyFrameInterval-ot, a HLS szegmens csomagolási aránya FragmentsPerSegment a korábban leírt szabályok alapján kerül kiszámításra.  

Ha explicit módon állítja be a KeyFrameInterval és a FragmentsPerSegment értékét is, a Media Services a beállított értékeket használja.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek közzétehetik a videót erre a csatornára. Az engedélyezett IP-cím a következők egyikeként adható meg:

* Egyetlen IP-cím (például 10.0.0.1)
* IP-címet és CIDR alhálózati maszkot használó IP-tartomány (például 10.0.0.1/22)
* IP-címet és pontozott decimális alhálózati maszkot használó IP-tartomány (például 10.0.0.1(255.255.252.0))

Ha nincs megadva IP-cím, és nincs szabálydefiníció, akkor nem engedélyezett AZ IP-cím. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

### <a name="channel-preview"></a>Csatorna előnézete
#### <a name="preview-urls"></a>URL-ek előnézete
A csatornák egy előnézeti végpontot (előnézeti URL-t) biztosítanak, amelyet a stream további feldolgozás és kézbesítés előtt történő előnézeti megtekintéséhez és érvényesítéséhez használ.

A csatorna létrehozásakor megkaphatja az előnézeti URL-címet. Ahhoz, hogy megkapja az URL-címet, a csatornának nem kell **futó** állapotban lennie. Miután a csatorna elkezdte az adatok betöltését, megtekintheti az adatfolyam előnézetét.

Jelenleg az előnézeti adatfolyam csak töredezett MP4 (Smooth Streaming) formátumban szállítható, függetlenül a megadott bemeneti típustól. Használhatja a [Smooth Streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) lejátszó, hogy teszteljék a sima stream. Az Azure Portalon üzemeltetett lejátszót is használhatja az adatfolyam megtekintéséhez.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek csatlakozhatnak az előnézeti végponthoz. Ha nincs megadva IP-cím, akkor bármely IP-cím engedélyezett. Az engedélyezett IP-cím a következők egyikeként adható meg:

* Egyetlen IP-cím (például 10.0.0.1)
* IP-címet és CIDR alhálózati maszkot használó IP-tartomány (például 10.0.0.1/22)
* IP-címet és pontozott decimális alhálózati maszkot használó IP-tartomány (például 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Csatorna kimenete
A csatornakimenettel kapcsolatos további tudnivalókért tekintse meg a [Kulcsképkocka-intervallum](#keyframe_interval) című szakaszt.

### <a name="channel-managed-programs"></a>Csatorna által kezelt programok
A csatorna olyan programokhoz van társítva, amelyek segítségével szabályozhatja a szegmensek közzétételét és tárolását egy élő közvetítésben. A csatornák kezelik a programokat. A csatorna- és programkapcsolat hasonló a hagyományos médiához, ahol a csatorna állandó tartalomfolyammal rendelkezik, és a program hatóköre az adott csatorna bizonyos időzített eseményére terjed ki.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archív ablak hossza azt is meghatározza, hogy az ügyfelek legfeljebb hányszel kereshetnek időben az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy olyan eszközhöz van társítva, amely tárolja az adatfolyamként imitált tartalmat. Egy eszköz le van képezve egy blokk blob tároló az Azure storage-fiókban, és az eszköz ben tárolt fájlok blobok a tárolóban. A program közzétételéhez, hogy az ügyfelek megtekinthessék az adatfolyamot, létre kell hoznia egy OnDemand lokátort a társított eszközhöz. Ezzel a lokátorral hozhat létre egy streamelési URL-címet, amely et biztosíthat az ügyfeleknek.

Egy csatorna legfeljebb három egyidejűleg futó programot támogat, így több archívumot is létrehozhat ugyanaszhoz a bejövő adatfolyamhoz. Szükség szerint közzéteheti és archiválhatja az esemény különböző részeit. Tegyük fel például, hogy az üzleti követelmény az, hogy archiválja a program 6 óráját, de csak az utolsó 10 percet sugározza. Ezt két egyidejűleg zajló program létrehozásával érheti el. Az egyik program úgy van beállítva, hogy archiválja az esemény hat óráját, de a program nincs közzétéve. A másik program van beállítva, hogy archív 10 percig, és ez a program közzé.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre egy új programot minden eseményhez. Indítsa el a programot, ha készen áll a streamelésre és az archiválásra. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

Az archivált tartalom törléséhez állítsa le és törölje a programot, majd törölje a kapcsolódó eszközt. Egy eszköz nem törölhető, ha egy program használja. Először a programot törölni kell.

Még a program leállítása és törlése után is a felhasználók igény szerint videóként streamelhetik az archivált tartalmat, amíg nem törli az eszközt. Ha meg szeretné őrizni az archivált tartalmat, de nem szeretné elérhetővé tenni streamelésre, törölje a streamelési lokátort.

## <a name="channel-states-and-billing"></a><a id="states"></a>Csatornaállapotok és számlázás
A csatorna aktuális állapotának lehetséges értékei a következők:

* **Leállítva**: Ez a csatorna kezdeti állapota a létrehozása után. Ebben az állapotban a csatorna tulajdonságai frissíthetők, de a streamelés nem engedélyezett.
* **Indítás**: A csatorna indítása folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés. Hiba esetén a csatorna visszaáll **a Leállított** állapotba.
* **Futás**: A csatorna képes feldolgozni az élő közvetítéseket.
* **Megállás**: A csatorna leállítása folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés.
* **Törlés**: A csatorna törlése folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a streamelés.

Az alábbi táblázat bemutatja, hogyan felelnek meg a csatornaállapotok a számlázási módnak.

| Csatorna állapota | Portál felhasználói felületi mutatói | Számlázott? |
| --- | --- | --- |
| **Kezdő** |**Kezdő** |Nem (átmeneti állapot) |
| **Fut** |**Kész** (nincs futó program)<p><p>vagy<p>**Streamelés** (legalább egy futó program) |Igen |
| **Leállítás** |**Leállítás** |Nem (átmeneti állapot) |
| **Leállítva** |**Leállítva** |Nem |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Feliratozás és hirdetés beillesztése
Az alábbi táblázat a feliratozás és a hirdetésbeillesztés támogatott szabványait mutatja be.

| Standard | Megjegyzések |
| --- | --- |
| CEA-708 és EIA-608 (708/608) |A CEA-708 és az EIA-608 az Egyesült Államok és Kanada feliratozási szabványai.<p><p>Jelenleg a feliratozás csak akkor támogatott, ha a kódolt bemeneti adatfolyamban van. Olyan élő médiakódolót kell használnia, amely 608 vagy 708-as feliratokat szúrhat be a Media Services nek küldött kódolt adatfolyamba. A Media Services a beszúrt feliratokkal ellátott tartalmat a megtekintők számára biztosítja. |
| TTML belül .ismt (Smooth Streaming szöveges pályák) |A Media Services dinamikus csomagolása lehetővé teszi az ügyfelek számára, hogy a következő formátumok bármelyikében streameljenek tartalmat: DASH, HLS vagy Smooth Streaming. Ha azonban töredezett MP4-et (Smooth Streaming) ad be az .ismt (Smooth Streaming szöveges sávok) feliratokkal, az adatfolyamot csak a Smooth Streaming ügyfeleknek juttathatja el. |
| SCTE-35 között |Az SCTE-35 egy digitális jelzőrendszer, amelyet reklámbehelyezésre használnak. A downstream vevők a jelet használják, hogy a hirdetést a megadott időre a streambe súgják. Az SCTE-35-öt ritka sávként kell elküldeni a bemeneti adatfolyamban.<p><p>Jelenleg az egyetlen támogatott beviteli adatfolyam-formátum, amely hirdetési jeleket hordoz, a töredezett MP4 (Smooth Streaming). Az egyetlen támogatott kimeneti formátum a Smooth Streaming is. |

## <a name="considerations"></a><a id="considerations"></a>Megfontolások
Ha helyszíni élő kódolót használ többátviteli sebességű adatfolyam küldéséhez egy csatornára, a következő korlátozások érvényesek:

* Győződjön meg arról, hogy elegendő ingyenes internetkapcsolattal rendelkezik az adatok betöltési pontokra történő küldéséhez.
* A másodlagos betöltési URL-cím használata további sávszélességet igényel.
* A bejövő többbitrátafolyam legfeljebb 10 videominőségi szinttel (réteggel) és legfeljebb 5 hangsávgal rendelkezhet.
* A legmagasabb átlagos bitráta bármely videó minőségi szint alatt kell lennie 10 Mbps.
* Az összes video- és hangadatfolyam átlagos bitrátájának 25 Mbps alatt kell lennie.
* A bemeneti protokoll nem módosítható, amíg a csatorna vagy a hozzá tartozó programok futnak. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* Egyetlen bitrátát is betudsz önteni a csatornádba. De mivel a csatorna nem dolgozza fel az adatfolyamot, az ügyfélalkalmazások egyetlen bitráta-adatfolyamot is kapnak. (Nem javasoljuk ezt a lehetőséget.)

A csatornákkal és a kapcsolódó összetevőkkel kapcsolatos további szempontok:

* Minden alkalommal, amikor újrakonfigurálja az élő kódolót, hívja meg a **reset** metódust a csatornán. A csatorna alaphelyzetbe állítása előtt le kell állítania a programot. A csatorna alaphelyzetbe állítása után indítsa újra a programot.

  > [!NOTE]
  > Amikor újraindítja a programot, hozzá kell rendelnie egy új eszközhöz, és létre kell hoznia egy új lokátort. 
  
* Egy csatorna csak akkor állítható le, ha **futó** állapotban van, és a csatorna összes programja leállt.
* Alapértelmezés szerint csak öt csatornát adhat hozzá a Media Services-fiókhoz. További információt a [Kvóták és korlátozások](media-services-quotas-and-limitations.md)című témakörben talál.
* A számlázás csak akkor történik meg, ha a csatorna **futó** állapotban van. További információt a [Csatorna állapotai és számlázása](media-services-live-streaming-with-onprem-encoders.md#states) című részben talál.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Visszajelzés
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Ajánlott helyszíni kódolók](media-services-recommended-encoders.md)

[Az Azure Media Services töredezett MP4-es alkalmazási előírása](../media-services-fmp4-live-ingest-overview.md)

[Az Azure Media Services áttekintése és gyakori forgatókönyvei](media-services-overview.md)

[A Media Services fogalmai](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
