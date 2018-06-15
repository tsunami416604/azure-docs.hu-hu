---
title: Élő adatfolyam-továbbítási az Azure Media Services áttekintése |} Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Services segítségével élő adatfolyam.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 2d3219f6f2617fec941e308febb2e47efa184170
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33943229"
---
# <a name="overview-of-live-streaming-using-azure-media-services"></a>Élő adatfolyam-továbbítási az Azure Media Services áttekintése

> [!NOTE]
> 2018. május 12., az élő csatornák indítása rendszer már nem támogatja a RTP/MPEG-2 transport stream betöltési protokollt. Végezzen áttelepítést az RTP/MPEG-2 RTMP vagy töredezett MP4) (Smooth Streaming) betöltési protokollok.

## <a name="overview"></a>Áttekintés
Ha az Azure Media Services események élő adatfolyamainak továbbítása a következő összetevők általában érintett:

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

Az ügyfelek felé történő tartalomtovábbításkor a cél a videók kiváló minőségben történő továbbítása különböző eszközökre, különböző hálózati körülmények között. Ennek érdekében használja az élő kódolók kódolására az adatfolyamot többféle bitrátájúvá (adaptív sávszélességűvé) video-adatfolyamot.  A különböző eszközökre irányuló adatfolyam-továbbításhoz használja a Media Services [dinamikus csomagolási](media-services-dynamic-packaging-overview.md) lehetőségét, amely az adatfolyamot dinamikusan újracsomagolja különböző protokollok szerint. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH.

Az Azure Media Services szolgáltatásokban a **csatornák**, a **programok** és a **streamvégpontok** felelősek az élő adatfolyamokkal kapcsolatos minden feladatért, beleértve a feldolgozást, a formázást, a DVR-t, a biztosítást, a méretezhetőséget és a redundanciát is.

A **csatorna** egy olyan folyamatot jelent, amely az élő adatfolyamok tartalmát dolgozza fel. A csatornák a következő módokon képesek egy élő adatfolyam-továbbítás bemenetét fogadni:

* A helyszíni élő kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) tartalmakat küldenek a csatornának, amely **áteresztő** továbbításra van konfigurálva. Az **áteresztő** továbbítás azt jelenti, hogy a feldolgozott adatfolyamok további feldolgozás nélkül haladnak át a **csatornán**. Használhatja a következő élő kódolók képesek kimeneti többféle sávszélességű Smooth Streaming: MediaExcel, Ateme, kommunikációs képzelhető el, Envivo, Cisco és elemi. RTMP-kimenetre a következő élő kódolók képesek: Adobe Flash Media élő kódoló (FMLE), Telestream Wirecast, Haivision, Teradek és Tricaster átkódolók.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
  > 
  > 
* Egy helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a csatornának, amelyen engedélyezve van-e élő kódolásra Media Services a következő formátumok egyikében: RTMP vagy Smooth Streaming (töredezett MP4). A következő élő kódolók képesek RTMP kimenet ismert, hogy az ilyen típusú csatornák használata: Telestream Wirecast, FMLE. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

A Media Services 2.10 kiadástól kezdve, amikor létrehoz egy csatornát, megadhatja a milyen módon a csatornának a bemeneti adatfolyam fogadni szeretné, és hogy szeretné-e a csatorna élő kódolásra az adatfolyam. Erre két lehetősége van:

* **Nincs** (áteresztő) – Ha egy helyszíni élő kódoló, amely kimeneteként többféle sávszélességűvé (csatlakoztatott adatfolyam) használatát tervezi, adja meg ezt az értéket. Ebben az esetben a bejövő streamből továbbhalad a kimeneti kódolási nélkül. Ez a csatorna 2,10 kiadás előtti működés.  
* **Standard** – válassza ezt az értéket, ha azt tervezi, hogy az egyféle sávszélességű élő adatfolyamot többféle sávszélességűvé kódolása a Media Services segítségével. Ez a módszer a gazdaságosabb, a vertikális felskálázásával gyorsan ritka eseményekhez. Ne feledje, hogy egy számlázási hatással az élő kódolás és kell ne feledje, hogy egy élő kódolás csatorna hagyja a "Fut" állapotú adatforgalmi díjak gyakorisága.  Javasoljuk, hogy azonnal leállítja a futó csatornák után az élő adatfolyam-továbbítási esemény extra óránkénti díjak elkerülése érdekében.

## <a name="comparison-of-channel-types"></a>Csatornatípus összehasonlítása
A Media Services támogatott csatorna kétféle összehasonlításával való az alábbi táblázatban

| Szolgáltatás | Átmenő csatornát | Standard csatorna |
| --- | --- | --- |
| Egyszeres sávszélességű bemeneti van kódolva több bitrates a felhőben |Nem |Igen |
| Maximális felbontás, a rétegek száma |1080p, 8 rétegek 60 + fps |720p, 6 rétegek 30 fps |
| Bemeneti protokollok |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Ár |Tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Live videó" lap |Tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximálisan engedélyezett futási idő |A hét minden napján, 24 órában elérhető |8 óra |
| Táblagépükkel beszúrása támogatása |Nem |Igen |
| Ad-jelzés támogatása |Nem |Igen |
| Áteresztő CEA 608/708 feliratok |Igen |Igen |
| A hírcsatorna hozzájárulás a rövid leállások esetén lehetősége |Igen |Nem (csatorna megkezdődik slating másodperc 6 + bemeneti adatok nélkül) |
| Nem egységes bemeneti GOPs támogatása |Igen |Nem – bemeneti javítani kell a 2 mp GOPs |
| Változó keretének aránya bemeneti támogatása |Igen |Nem – bemeneti képkockasebessége kell meghatározni.<br/>Kisebb módosításokat elviseli, például magas mozgásérzékelési színfalak során. De kódoló nem dobható el, hogy 10 képkockák másodpercenkénti száma. |
| Automatikus – gyors csatornát, amikor a bemeneti hírcsatorna elvész. |Nem |Ha nincs fut Program 12 óra elteltével |

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
A Media Services szolgáltatásban [csatorna](https://docs.microsoft.com/rest/api/media/operations/channel)s felelősek az élő adatfolyam-tartalmak feldolgozása. Egy csatornát biztosít a bemeneti végpontja (feldolgozó URL-CÍMÉT), akkor adja meg az élő transcoder. A csatorna élő transcoder bemeneti élő adatfolyamokat fogad, és lehetővé teszi az adatfolyamként történő egy vagy több Streamvégpontok keresztül. Csatornák előnézeti végpont (előzetes verzió URL-cím), amelyekkel előzetes megtekintéséhez és az adatfolyam érvényesítése előtt további feldolgozás és a szállítási is megadhatja.

A bemeneti URL-címet és a kép URL-címet a csatorna létrehozásakor kérheti le. Ahhoz, hogy az URL-címek, a csatorna nem kell a elindított állapotban. Amikor készen áll a start kérdez le adatokat az élő transcoder a csatornának, el kell indítani a csatornát. Ha az élő transcoder megkezdi az adatok bevitele, megtekintheti az adatfolyam.

Minden egyes Media Services-fiók több csatornát, több alkalmazás, és több Streamvégpontok tartalmazhat. Attól függően, hogy a sávszélesség- és biztonsági igényeket StreamingEndpoint szolgáltatások is lehetne rendelni egy vagy több csatornát. Bármely StreamingEndpoint is lekéréses bármely csatornán.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) teszi lehetővé a közzétételt és élő stream szegmenseinek tárolását. A programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol a csatornák folyamatosan közvetítik a különböző tartalmakat, amelyek adott időtartamon át tartó részeit programoknak nevezzük.
Megadhatja, hogy hány órán át szeretné megőrizni a program felvett tartalmát úgy, hogy a **ArchiveWindowLength** tulajdonság. Ez az érték 5 perc és 25 óra közötti lehet.

ArchiveWindowLength is határozzák meg, hogy az ügyfelek maximális száma ugorhatnak vissza az idő, az az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy objektumhoz van társítva. A program közzétételéhez létre kell hoznia egy lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

## <a name="billing-implications"></a>Számlázási gyakorolt hatása
Egy csatorna kezdődik, számlázási, amint Állapotváltások "Fut" értékre az API-n keresztül.  

Az alábbi táblázat bemutatja, hogyan csatorna állapotok hozzárendelését számlázási állapotok az API és az Azure portálon. Vegye figyelembe, hogy az állapot csak kis mértékben eltér az API és a portál UX között Amint egy csatornát a "Fut" állapotú a API-n keresztül, vagy az Azure portálon "Kész" vagy "Streaming" állapotban van, számlázási aktív lesz.

A csatorna további számlázási meg leállítani, akkor állítsa le a csatornát, az API-n keresztül, vagy az Azure portálon.
Ön felelősséggel tartozik a csatornák leállítása, ha elkészült, a csatornák. Állítsa le a csatornát nem sikerült folyamatos számlázási eredményez.

> [!NOTE]
> Standard csatornák használata, amikor AMS lesz az automatikus gyors bármely csatorna, amely még a "Fut" állapotú 12 óra után a bemeneti hírcsatorna elvész, és nincsenek futó programok. Azonban továbbra is a számlázás történik a csatorna "Fut" állapotú volt alkalommal.
>
>

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
| Indítás |Indítás |Nem (átmeneti állapot) |
| Fut |Üzemkész (nincs futó program)<br/>vagy<br/>Streamelés (legalább egy futó program) |IGEN |
| Leállítás |Leállítás |Nem (átmeneti állapot) |
| Leállítva |Leállítva |Nem |

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Az Azure Media Services töredezett MP4 élő betöltési meghatározása](media-services-fmp4-live-ingest-overview.md)

[Segítségével az Azure Media Services élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

[Helyszíni kódolóktól többszörös átviteli sebességű streameket fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

[Kvóták és korlátozások](media-services-quotas-and-limitations.md).  

[Media Services-fogalmak](media-services-concepts.md)
