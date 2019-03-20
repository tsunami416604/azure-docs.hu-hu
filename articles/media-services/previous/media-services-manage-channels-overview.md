---
title: Az Azure Media Services segítségével élő Streamelés áttekintése |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az élő streamelés az Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a9d0daaacb046df7943202775adc77bc912cce11
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189512"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Media Services segítségével élő Streamelés áttekintése

> [!NOTE]
> 2018. május 12., az élő csatornák indítása fogja többé nem támogatja a RTP/MPEG-2 adatátviteli stream betöltési protokollját. Telepítse át a RTP/MPEG-2 RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollokra.

## <a name="overview"></a>Áttekintés

Ha az Azure Media Services események élő adatfolyamainak továbbítása a következő összetevőket gyakran játszik szerepet:

* Egy kamera, amely az eseményt közvetíti.
* Egy élő videókódoló, amely a kamera jeleit adatfolyammá alakítja, amelyek aztán továbbítódnak egy élő adatfolyam-szolgáltatásra.

    Esetlegesen több, szinkronizált idejű élő kódoló. A kiemelt, magas rendelkezésre állást és minőséget megkövetelő eseményekhez javasolt szinkronizált idejű aktív-aktív redundáns kódolókat alkalmazni az adatvesztés nélküli, zökkenőmentes feladatátvétel érdekében.
* Egy élő adatfolyam-szolgáltatás, amely lehetővé teszi a következőket:

  * élő tartalmak feldolgozása különböző élő adatfolyam-továbbítási protokollok használatával (például RTMP vagy Smooth Streaming);
  * az adatfolyam adaptív sávszélességűvé kódolása (opcionális)
  * az élő adatfolyam-továbbítás előnézete
  * a feldolgozott tartalmak rögzítése és tárolása a későbbi továbbítás érdekében (Video-on-Demand)
  * a tartalom továbbítása gyakori streamprotokollok (például MPEG DASH, Smooth, HLS) használatával közvetlenül az ügyfelek részére, vagy egy tartalomkézbesítési hálózatra (CDN) későbbi terjesztés céljából.

A **Microsoft Azure Media Services** (AMS) lehetőséget nyújt az élő adatfolyam-tartalmak feldolgozására, kódolására, előnézetére, tárolására és továbbítására is.

Az ügyfelek felé történő tartalomtovábbításkor a cél a videók kiváló minőségben történő továbbítása különböző eszközökre, különböző hálózati körülmények között. Ennek érdekében élő kódolók használatával és többféle sávszélességű (adaptív sávszélességűvé alakítják) video-adatfolyamot, az adatfolyam kódolása.  A különböző eszközökre irányuló adatfolyam-továbbításhoz használja a Media Services [dinamikus csomagolási](media-services-dynamic-packaging-overview.md) lehetőségét, amely az adatfolyamot dinamikusan újracsomagolja különböző protokollok szerint. Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Az Azure Media Services szolgáltatásokban a **csatornák**, a **programok** és a **streamvégpontok** felelősek az élő adatfolyamokkal kapcsolatos minden feladatért, beleértve a feldolgozást, a formázást, a DVR-t, a biztosítást, a méretezhetőséget és a redundanciát is.

A **csatorna** egy olyan folyamatot jelent, amely az élő adatfolyamok tartalmát dolgozza fel. A csatornák a következő módokon képesek egy élő adatfolyam-továbbítás bemenetét fogadni:

* A helyszíni élő kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) tartalmakat küldenek a csatornának, amely **áteresztő** továbbításra van konfigurálva. Az **áteresztő** továbbítás azt jelenti, hogy a feldolgozott adatfolyamok további feldolgozás nélkül haladnak át a **csatornán**. Használhatja a következő élő kódolók, és többféle sávszélességű Smooth Streaming-kimenetre: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco és Elemental. A következő élő kódolók RTMP-kimenetre: Az Adobe Flash Media Live Encoder (FMLE), Haivision, Telestream Wirecast, Teradek és Tricaster átkódolók.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
  > 
  > 
* A helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a csatorna, amelyen engedélyezve van a valós idejű kódolás a Media Services a következő formátumok egyikében: RTMP vagy Smooth Streaming (darabolt MP4). Az RTMP-kimenet a következő élő kódolók ismert, hogy ilyen típusú csatornákat: Telestream Wirecast, FMLE. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

A Media Services 2.10 kiadástól kezdve, amikor létrehoz egy csatornát, megadhatja a milyen módon azt szeretné, a bemeneti streamet a csatornának, és hogy szeretné-e a csatorna élő kódolásra a Stream. Erre két lehetősége van:

* **Nincs** (áteresztő) – adja meg ezt az értéket, ha egy helyszíni élő kódoló, amelyek kimenete és többféle sávszélességű adatfolyamot (csatlakoztatott stream) használatát tervezi. Ebben az esetben a beérkező streamben átadni a kimeneti bármely kódolás nélkül. Ez a csatorna 2,10 kiadás előtti viselkedését.  
* **Standard szintű** – válassza ezt az értéket, ha azt tervezi, hogy az egyféle sávszélességű élő adatfolyamot sávszélességűvé kódolása a Media Services használatával. Ez a módszer gazdaságosabb méretezése gyorsan ritkán előforduló eseményeket. Vegye figyelembe, hogy egy számlázási hatással az élő kódolás kell ne feledje, hogy egy élő kódolás csatorna és a "Fut" állapotban számlázási díjat számolunk.  Javasoljuk, hogy azonnal leállítja a futó csatornák az élő adatfolyam-továbbítási esemény nagyon óránkénti díjak elkerülése érdekében befejeződése után.

## <a name="comparison-of-channel-types"></a>Csatorna típusok összehasonlítása

Egy útmutató, amellyel a két csatornatípus esetében támogatott a Media Services összehasonlítása az alábbi táblázatban

| Szolgáltatás | Áteresztő csatornán | Standard szintű csatornák |
| --- | --- | --- |
| Egyféle sávszélességű bemeneti bitsebességekre a felhőben van kódolva. |Nem |Igen |
| Maximális felbontás, a rétegek száma |1080p, 8 rétegek, több mint 60 fps |720p, 6 rétegek 30 képkocka/s |
| A bemeneti protokollok |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Ár |Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap |Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximálisan engedélyezett futási idő |A hét minden napján, 24 órában elérhető |8 óra |
| Befutók beillesztésének támogatását |Nem |Igen |
| Ad jelzés támogatása |Nem |Igen |
| Az átmenő CEA 608/708 feliratok |Igen |Igen |
| Nem egységes bemeneti GOPs támogatása |Igen |Nem – bemeneti ki kell javítani GOPs. 2 mp |
| Változó keret arány bevitel támogatása |Igen |Nem – bemeneti képkockasebessége kell rögzíteni.<br/>Kisebb módosításokat kívánalmakhoz, például magas mozgásban lévő adatoknak egyaránt jelenetek során. De kódoló nem dobható el, a 10 képkockák másodpercenkénti száma. |
| Automatikus – gyors csatornát, amikor a bevitel hírcsatorna elvész. |Nem |Ha nem fut program 12 óra elteltével |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó (áteresztő) csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az **áteresztő** munkafolyamatban.

![Élő munkafolyamat](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Tovább információk: [Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Az Azure Media Services segítségével élő kódolásra képes csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az élő adatfolyam-továbbítási munkafolyamatban, ha a csatorna számára engedélyezett a Media Services használatával végzett élő kódolás.

![Élő munkafolyamat](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

## <a name="description-of-a-channel-and-its-related-components"></a>A csatorna és a kapcsolódó összetevők leírása

### <a name="channel"></a>Csatorna

A Media Services szolgáltatásban [csatorna](https://docs.microsoft.com/rest/api/media/operations/channel)s felelősek az élő adatfolyam-tartalmak feldolgozása. Egy csatorna bemeneti végpontot biztosít (betöltési URL-címe), majd adja át egy élő átkódolók. A csatorna élő bemeneti streamekhez fogadja a élő átkódolók, és lehetővé teszi egy vagy több Streamvégpontok keresztül. Csatorna előnézeti végpont (előzetes verzió URL-cím), amellyel és ellenőrzéséhez a stream előnézetének a feldolgozás folytatása és a továbbítás előtti is biztosítanak.

A csatorna létrehozásakor kérheti le a bemeneti URL-címet és az előnézeti URL-címet. Az URL-címek beszerzéséhez a csatorna nem rendelkezik a lépések állapotban kell lennie. Ha készen áll az adatok elküldése egy élő átkódolók származó és a csatorna indítása, a csatornát kell elindítani. Miután az élő átkódolók elindul az adatok bevitele, megtekintheti a stream.

Minden egyes Media Services-fiók több csatornán, több programok és Streamvégpontok több is tartalmazhat. A sávszélesség- és biztonsági igényeitől függően egy vagy több csatornán dedikálhatja Streamvégpontok szolgáltatások. Bármely Streamvégpontok is lekéréshez tetszőleges csatornán.

A csatorna létrehozásakor engedélyezett IP-címek adhatja meg a következő formátumok egyikében: IpV4-cím 4 számjegyből, CIDR-címtartományt.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) lehetővé teszi, hogy a storage, az élő stream szegmenseinek közzétételét és. A programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.
Megadhatja, hogy meg szeretné őrizni a program felvett tartalmát beállításával órák számát az **ArchiveWindowLength** tulajdonság. Ez az érték 5 perc és 25 óra közötti lehet.

ArchiveWindowLength is előírja az ügyfelek maximális számát is kérhet időbeli az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy objektumhoz van társítva. A program közzétételéhez létre kell hoznia egy lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

## <a name="billing-implications"></a>Számlázás – következmények
Csatorna kezdődik a számlázás, amint az API-n keresztül állapotváltásra "Fut" értékre.  

Az alábbi táblázat bemutatja, hogyan csatornaállapotok számlázási állapotok az API-t és az Azure Portalon. Vegye figyelembe, hogy az államok kissé eltérő, az API-t és a portál UX között Amint egy csatornát a "Fut" állapotban az API-n keresztül, vagy az Azure Portalon "Kész" vagy "Folyamatos átvitel" állapotban van, a számlázás aktív lesz.

Leállítja a csatornát, a számlázási, tovább, akkor leállítja a csatornát, az API-n keresztül vagy az Azure Portalon.
Ön felelős a csatornák leáll, amikor végzett a csatornát. Nem sikerült leállítani a csatorna folyamatos számlázási eredményez.

> [!NOTE]
> Ha standard szintű csatornák dolgozik, az AMS lesz az automatikus gyors bármely csatornán, és 12 óra után a bemeneti hírcsatorna elvész, és nincs futó program még mindig "Fut" állapotban van. Azonban továbbra is díjköteles alkalommal a csatorna "Fut" állapotban volt.
>
>

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

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Specifikáció: darabolt MP4 élő Azure Media Services feldolgozása](media-services-fmp4-live-ingest-overview.md)

[Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

[Helyszíni kódolóktól többszörös átviteli sebességű streameket fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

[Kvóták és korlátozások](media-services-quotas-and-limitations.md).  

[A Media Services alapfogalmai](media-services-concepts.md)
