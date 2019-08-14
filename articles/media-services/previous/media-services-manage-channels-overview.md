---
title: Az élő közvetítés áttekintése Azure Media Services használatával | Microsoft Docs
description: Ez a témakör áttekintést nyújt az élő közvetítésről Azure Media Services használatával.
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
ms.openlocfilehash: 45089c77ff2fed7ea5903333514f4d4edbad7cb3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "69015545"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Az élő közvetítés áttekintése Media Services használatával

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Áttekintés

Ha élő adatfolyam-továbbítási eseményeket Azure Media Services a következő összetevőket gyakran érintik:

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

A Media Services segítségével kihasználhatja a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)előnyeit, így az élő STREAMEKET MPEG Dash, HLS és Smooth streaming formátumban közvetítheti a szolgáltatásba küldött hozzájárulási csatornából. A megtekintők az élő streamet bármilyen HLS, KÖTŐJELtel vagy Smooth Streaming kompatibilis játékossal játszhatják fel. A webes vagy mobil alkalmazásaiban Azure Media Player használhatja az adatfolyam továbbítására ezen protokollok bármelyikében.

> [!NOTE]
> A 2018. május 12. után az élő csatornák már nem támogatják az RTP/MPEG-2 Transport stream betöltési protokollt. Telepítse át a következőt: RTP/MPEG-2 – RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollok.

## <a name="streaming-endpoints-channels-programs"></a>Folyamatos átviteli végpontok, csatornák, programok

Az Azure Media Services szolgáltatásokban a **csatornák**, a **programok** és a **streamvégpontok** felelősek az élő adatfolyamokkal kapcsolatos minden feladatért, beleértve a feldolgozást, a formázást, a DVR-t, a biztosítást, a méretezhetőséget és a redundanciát is.

A **csatorna** egy olyan folyamatot jelent, amely az élő adatfolyamok tartalmát dolgozza fel. A csatornák a következő módokon képesek egy élő adatfolyam-továbbítás bemenetét fogadni:

* A helyszíni élő kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) tartalmakat küldenek a csatornának, amely **áteresztő** továbbításra van konfigurálva. Az **áteresztő** továbbítás azt jelenti, hogy a feldolgozott adatfolyamok további feldolgozás nélkül haladnak át a **csatornán**. A többszörös sávszélességű Smooth Streaming kimenetét a következő élő kódolók használatával végezheti el: MediaExcel, Ateme, Imagine Communications, envivio, Cisco és Elemental. A következő élő kódolók kimeneti RTMP-kimenete: Az Adobe Flash Media Live Encoder (FMLE), a Wirecast, a Haivision, a Teradek és a Tricaster-átkódolók.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
  > 
  > 
* A helyszíni élő kódoló egy átviteli sebességű streamet küld a csatornának, amely lehetővé teszi, hogy az alábbi formátumok egyikével Media Services élő kódolást végezzen el: RTMP vagy Smooth Streaming (darabolt MP4). A következő, RTMP kimenettel rendelkező élő kódolók ismertek, hogy az ilyen típusú csatornákkal működnek: Wirecast, FMLE. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

A Media Services 2,10 kiadástól kezdve a csatorna létrehozásakor megadhatja, hogy a csatorna milyen módon kapja meg a bemeneti adatfolyamot, és hogy szeretné-e, hogy a csatorna élő kódolást végezzen a streamben. Erre két lehetősége van:

* **Nincs** (átmenő) – adja meg ezt az értéket, ha helyszíni élő kódolót szeretne használni, amely a többszörös átviteli sebességű streamet (egy csatlakoztatott adatfolyamot) fogja kiadni. Ebben az esetben a bejövő adatfolyam kódolás nélkül lett átadva a kimenetnek. Ez egy csatorna viselkedése a 2,10 kiadás előtt.  
* **Standard** – válassza ezt az értéket, ha azt tervezi, hogy Media Services használatával kódolja az egyszeres sávszélességű adatfolyamot a többszörös átviteli sebességű streambe. Ez a módszer gazdaságosabb felskálázást a ritka események esetében. Vegye figyelembe, hogy az élő kódoláshoz van egy számlázási hatás, és ne feledje, hogy a "Running" állapotú élő kódolási csatorna elhagyása esetén a számlázási költségek is felmerülnek.  Azt javasoljuk, hogy az élő közvetítési esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje a felesleges óradíjat.

## <a name="comparison-of-channel-types"></a>A Channel types összehasonlítása

Az alábbi táblázat a Media Services által támogatott két csatorna típusának összehasonlítását ismerteti.

| Funkció | Áteresztő csatorna | Standard szintű csatorna |
| --- | --- | --- |
| Az egyszeres sávszélességű bemenetek a felhőben több bitrátára vannak kódolva |Nem |Igen |
| Maximális felbontás, rétegek száma |1080p, 8 réteg, 60 + fps |720p, 6 réteg, 30 fps |
| Bemeneti protokollok |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Ár |Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/media-services/) , és kattintson az "élő videó" fülre. |Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximális futási idő |24 órában |8 óra |
| A beágyazások behelyezésének támogatása |Nem |Igen |
| Az ad-jelzés támogatása |Nem |Igen |
| Átmenő CEA 608/708 feliratok |Igen |Igen |
| Nem egységes bemeneti Pallagi Péter támogatása |Igen |Nem – a bemenetnek rögzített 2sec Pallagi Péter kell lennie |
| Változó képarány bemenetének támogatása |Igen |Nem – a bemenetnek rögzített képkockasebességnek kell lennie.<br/>A kisebb variációk a nagy teljesítményű jeleneteknél, például a mozgásban vannak. A kódoló azonban nem tudja eldobni a 10 képkockát/másodpercet. |
| Csatornák automatikus kikapcsolása a bemeneti adatcsatorna elvesztésekor |Nem |12 óra elteltével, ha nincs futó program |

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

Media Services a [Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s az élő adatfolyam tartalmának feldolgozásához felelős. A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet aztán egy élő transcoder számára biztosít. A csatorna élő bemeneti streameket fogad az élő átkódolóból, és egy vagy több StreamingEndpoints keresztül elérhetővé teszi a folyamatos átvitelt. A csatornák egy előzetes verziójú végpontot (előzetes verziójú URL-címet) is biztosítanak, amelyet a további feldolgozás és a továbbítás előtt a stream előzetes verziójának megtekintéséhez és érvényesítéséhez használhat.

A csatorna létrehozásakor betöltheti a betöltési URL-címet és az előnézeti URL-címet. Az URL-címek lekéréséhez a csatornának nem kell megkezdett állapotban lennie. Ha készen áll arra, hogy egy élő transcoder-ből elindítsa az adatok csatornába való küldését, el kell indítani a csatornát. Miután az élő transcoder elkezdi az adatfeldolgozást, megtekintheti az adatfolyamot.

Minden Media Services fiók több csatornát, több programot és több StreamingEndpoints is tartalmazhat. A sávszélességtől és a biztonsági igényektől függően a Streamvégpontok-szolgáltatások egy vagy több csatornára is kihasználhatók. Bármely Streamvégpontok bármely csatornáról lehívható.

Csatorna létrehozásakor megadhatja az engedélyezett IP-címeket a következő formátumok egyikében: IpV4-címek 4 számmal, CIDR címtartomány.

### <a name="program"></a>Program
A [program](https://docs.microsoft.com/rest/api/media/operations/program) segítségével szabályozhatja a szegmensek közzétételét és tárolását egy élő adatfolyamban. A programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.
Megadhatja, hogy hány óra elteltével szeretné megőrizni a program rögzített tartalmát a **ArchiveWindowLength** tulajdonság beállításával. Ez az érték 5 perc és 25 óra közötti lehet.

A ArchiveWindowLength azt is diktálja, hogy az ügyfelek legfeljebb hány időt tudnak visszakeresni az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy objektumhoz van társítva. A program közzétételéhez létre kell hoznia egy lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

## <a name="billing-implications"></a>Számlázási következmények
A csatorna a számlázást azonnal megkezdi, amint az az API-n keresztül a "Running" állapotra vált.  

Az alábbi táblázat azt mutatja be, hogyan képezhetők le a csatornák az API-ban és Azure Portal a számlázási állapotokra. Vegye figyelembe, hogy az állapotok kis mértékben eltérnek az API és a Portal UX között. Amint a csatorna "Running" állapotban van az API-n keresztül, vagy a Azure Portal "Ready" vagy "streaming" állapotban van, a számlázás aktív lesz.

Ha a csatornát továbbra is le szeretné állítani a számlázási adatokból, le kell állítania a csatornát az API-n vagy a Azure Portal.
Ön felelős a csatornák leállításához, amikor elkészült a csatornával. Ha nem sikerül leállítani a csatornát, a rendszer folyamatos számlázást eredményez.

> [!NOTE]
> A standard szintű csatornák használatakor az AMS automatikusan kikapcsolja az összes olyan csatornát, amely még mindig "fut" állapotban van – 12 órával a bemeneti csatorna elvesztése után, és nincs futó program. Azonban továbbra is díjat számítunk fel, amikor a csatorna "Running" állapotban volt.
>
>

### <a id="states"></a>Csatornák állapota és a számlázási módra való leképezésük módja
Csatorna aktuális állapota. A lehetséges értékek:

* **Leállítva**. Ez a csatorna kezdeti állapota a létrehozás után (kivéve, ha az autostart ki lett választva a portálon.) Ebben az állapotban nem történik számlázás. Ebben az állapotban a csatorna tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.
* **Kezdés**: A csatorna indítás alatt van. Ebben az állapotban nem történik számlázás. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Hiba esetén a csatorna Leállított állapotra vált.
* **Fut**. A csatorna élő streamek feldolgozására alkalmas állapotban van. Most már számlázási használat. A további számlázás elkerülése érdekében le kell állítania a csatornát.
* **Leállítás folyamatban**. A csatorna leállítás alatt van. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.
* **Törlés**folyamatban. A csatorna törlés alatt van. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.

Az alábbi táblázat azt ismerteti, hogy az egyes csatornaállapotok esetében miként alakul a számlázási mód.

| Csatorna állapota | Jelzése a portál kezelőfelületén | Számlázási? |
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
[Azure Media Services darabolt MP4 élő betöltési specifikáció](../media-services-fmp4-live-ingest-overview.md)

[Olyan csatornák használata, amelyek engedélyezve vannak a Live Encoding végrehajtásához Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Helyszíni kódolóktól többszörös átviteli sebességű streameket fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

[Kvóták és korlátozások](media-services-quotas-and-limitations.md).  

[Media Services fogalmak](media-services-concepts.md)
