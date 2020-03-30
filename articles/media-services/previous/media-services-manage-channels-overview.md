---
title: Élő közvetítés áttekintése az Azure Media Services használatával | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a Microsoft Azure Media Services használatával történő élő közvetítésről.
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
ms.openlocfilehash: 99efe375fad142963214b09df24be70bc3bc9d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131603"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Élő közvetítés áttekintése a Media Services használatával

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Áttekintés

Az Azure Media Services szolgáltatással élő közvetítési események kézbesítése során általában a következő összetevőket vonják be:

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

A Media Services segítségével kihasználhatja a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)előnyeit, amely lehetővé teszi az élő közvetítések MPEG DASH, HLS és Smooth Streaming formátumban történő közvetítését a szolgáltatásnak küldött hozzájárulási hírcsatornából. A megtekintők lejátszhatják az élő közvetítést bármely HLS, DASH vagy Smooth Streaming kompatibilis lejátszóval. Az Azure Media Player segítségével webes vagy mobilalkalmazásokban is közvetítheti az adatfolyamot a protokollok bármelyikében.

> [!NOTE]
> 2018. május 12-től az élő csatornák már nem támogatják az RTP/MPEG-2 átviteli adatfolyam-betöltési protokollt. Az RTP/MPEG-2 protokollról átkell térni az RTMP-re vagy a töredezett MP4 (Smooth Streaming) protokollokra.

## <a name="streaming-endpoints-channels-programs"></a>Végpontok, csatornák, programok streamelése

Az Azure Media Services szolgáltatásokban a **csatornák**, a **programok** és a **streamvégpontok** felelősek az élő adatfolyamokkal kapcsolatos minden feladatért, beleértve a feldolgozást, a formázást, a DVR-t, a biztosítást, a méretezhetőséget és a redundanciát is.

A **csatorna** egy olyan folyamatot jelent, amely az élő adatfolyamok tartalmát dolgozza fel. A csatornák a következő módokon képesek egy élő adatfolyam-továbbítás bemenetét fogadni:

* A helyszíni élő kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) tartalmakat küldenek a csatornának, amely **áteresztő** továbbításra van konfigurálva. Az **áteresztő** továbbítás azt jelenti, hogy a feldolgozott adatfolyamok további feldolgozás nélkül haladnak át a **csatornán**. A következő élő kódolókat használhatja, amelyek többátviteli sim idejű smooth streaminget adnak ki: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco és Elemental. A következő élő kódolók kimeneti RTMP: Telestream Wirecast, Haivision, Teradek és Tricaster transzkóderek.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
  > 
  > 
* A helyszíni élő kódoló egyegybitrátú adatfolyamot küld a csatornának, amely engedélyezve van az élő kódoláshoz a Media Services szolgáltatással a következő formátumok egyikén: RTMP vagy Smooth Streaming (töredezett MP4). A következő RTMP kimenettel rendelkező élő kódolók működnek az ilyen típusú csatornákkal: Telestream Wirecast. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

A Media Services 2.10 kiadással kezdve, amikor létrehoz egy csatornát, megadhatja, hogy a csatorna milyen módon fogadja a bemeneti adatfolyamot, és hogy szeretné-e, hogy a csatorna élő kódolást hajtson végre az adatfolyamban. Erre két lehetősége van:

* **Nincs** (átadó) – Adja meg ezt az értéket, ha helyszíni élő kódolót kíván használni, amely többátviteli adatfolyamot (átadó adatfolyamot) fog kiadni. Ebben az esetben a bejövő adatfolyam kódolás nélkül haladt át a kimenetre. Ez a viselkedés a csatorna előtt 2.10 kiadás.  
* **Standard** – Akkor válassza ezt az értéket, ha a Media Services használatával szeretné kódolni az egyátviteli sebességet többátviteli adatfolyamba. Ez a módszer gazdaságosabb a ritka események gyors felskálázása. Ne feledje, hogy az élő kódolás számlázási hatással van, és ne feledje, hogy az élő kódolási csatorna "Futás" állapotban hagyása számlázási díjakat von maga után.  Javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje az óránkénti díjakat.

## <a name="comparison-of-channel-types"></a>Csatornatípusok összehasonlítása

Az alábbi táblázat a Media Services által támogatott két csatornatípus összehasonlítását ismerteti

| Szolgáltatás | Átmenő csatorna | Normál csatorna |
| --- | --- | --- |
| Az egyátviteli bemenet több bitsebességbe van kódolva a felhőben |Nem |Igen |
| Maximális felbontás, rétegek száma |1080p, 8 réteg, 60+fps |720p, 6 réteg, 30 képkocka/s |
| Bemeneti protokollok |RTMP, sima streamelés |RTMP, sima streamelés |
| Price |Lásd az [árképzési oldalt,](https://azure.microsoft.com/pricing/details/media-services/) és kattintson a "Live Video" fülre |Az [árképzési oldal](https://azure.microsoft.com/pricing/details/media-services/) megtekintése |
| Maximális futási idő |24 x7 |8 óra |
| Palabehelyezések támogatása |Nem |Igen |
| Hirdetésjelzés támogatása |Nem |Igen |
| Átmenő CEA 608/708 feliratok |Igen |Igen |
| Nem egységes bemeneti gok-k támogatása |Igen |Nem – a bemenetet 2 mp-es GOP-k rögzítették |
| A változó képkockasebesség bemenetének támogatása |Igen |Nem – a bemenetnek rögzített képkockasebességnek kell lennie.<br/>Kisebb variációk tolerálható, például a nagy mozgás jelenetek. De a kódoló nem tud 10 képkockára/mp-re csökkenni. |
| Csatornák automatikus kikapcsolása a bemeneti hírcsatorna elvészek esetén |Nem |12 óra elteltével, ha nincs futó program |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó (áteresztő) csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az **áteresztő** munkafolyamatban.

![Élő munkafolyamat](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Tovább információk: [Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Az Azure Media Services segítségével élő kódolásra képes csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az élő adatfolyam-továbbítási munkafolyamatban, ha a csatorna számára engedélyezett a Media Services használatával végzett élő kódolás.

![Élő munkafolyamat](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

## <a name="description-of-a-channel-and-its-related-components"></a>A csatorna és a hozzá kapcsolódó összetevők leírása

### <a name="channel"></a>Csatorna

A Media Services alkalmazásban [a Csatorna](https://docs.microsoft.com/rest/api/media/operations/channel)felelős az élő közvetítési tartalmak feldolgozásáért. A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet ezután egy élő átkódolónak ad meg. A csatorna élő bemeneti adatfolyamokat fogad az élő átkódertől, és elérhetővé teszi egy vagy több StreamingEndpoints-on keresztüli streamelésre. A csatornák egy előnézeti végpontot (előnézeti URL-t is) biztosítanak, amelyet a stream további feldolgozás és kézbesítés előtt történő előnézeti megtekintéséhez és érvényesítéséhez használhat.

A betöltési URL-címet és az előnézeti URL-t a csatorna létrehozásakor kaphatja meg. Az URL-címek leszámításához a csatornának nem kell elindított állapotban lennie. Ha készen áll arra, hogy élő átkóderből adatokat nyomtasson a csatornába, a csatornát el kell indítani. Miután az élő transzkóder megkezdi az adatok betöltését, megtekintheti az adatfolyam előnézetét.

Minden Media Services-fiók több csatornát, több programot és több StreamingEndpoints pontot tartalmazhat. A sávszélességtől és a biztonsági igényektől függően a StreamingEndpoint-szolgáltatások egy vagy több csatornához is fellehet őket vetni. Bármely StreamingEndpoint bármely csatornáról lehúzható.

Csatorna létrehozásakor az engedélyezett IP-címeket a következő formátumok egyikében adhatja meg: IpV4-cím 4 számmal, CIDR-címtartomány.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) lehetővé teszi a szegmensek közzétételének és tárolásának szabályozását élő közvetítésben. A programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.
Az **ArchiveWindowLength** tulajdonság beállításával megadhatja, hogy hány órát szeretne megőrizni a program rögzített tartalma. Ez az érték 5 perc és 25 óra közötti lehet.

Az ArchiveWindowLength azt is előírja, hogy az ügyfelek legfeljebb mennyi időt kérhetnek vissza az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. A tulajdonság értéke azt is meghatározza, hogy az ügyfél jegyzékfájljai mennyi ideig növekedhetnek.

Minden program egy objektumhoz van társítva. A program közzétételéhez létre kell hoznia egy lokátort a társított eszközhöz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

## <a name="billing-implications"></a>Számlázási vonatkozások
A csatorna akkor kezdi meg a számlázást, amikor az állapot átvált az API-n keresztül a "Futás" állapotra.  

Az alábbi táblázat bemutatja, hogyan csatorna állapotok leképezése számlázási állapotok az API-ban és az Azure Portalon. Vegye figyelembe, hogy az állapotok némileg eltérnek az API és a portál felhasználói felülete között. Amint egy csatorna "Futás" állapotban van az API-n keresztül, vagy a "Ready" vagy a "Streaming" állapotban az Azure Portalon, a számlázás aktív lesz.

Ha le szeretné állítani, hogy a csatorna további számlázási önnek, le kell állítania a csatornát az API-n keresztül vagy az Azure Portalon keresztül.
Ön felelős a csatornák leállításáért, ha végzett a csatornával. Ha nem állítja le a csatornát, az folyamatos számlázást eredményez.

> [!NOTE]
> A szabványos csatornákhasználata során az AMS automatikusan kikapcsol minden olyan csatornát, amely még mindig "Futás" állapotban van, 12 órával a bemeneti hírcsatorna elvészután, és nincsenek futó programok. Azonban továbbra is ki kell számlád a csatorna "Futás" állapotának idejére.
>
>

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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Az Azure Media Services töredezett MP4 élő betöltési specifikációja](../media-services-fmp4-live-ingest-overview.md)

[Élő kódolást az Azure Media Services szolgáltatással való éles kódolásra engedélyezett csatornákkal való együttműködés](media-services-manage-live-encoder-enabled-channels.md)

[Többbites átviteli sebességű élő közvetítést fogadó csatornák kal a helyszíni kódolóktól](media-services-live-streaming-with-onprem-encoders.md)

[Kvóták és korlátozások](media-services-quotas-and-limitations.md).  

[A Médiaszolgáltatások fogalmai](media-services-concepts.md)
