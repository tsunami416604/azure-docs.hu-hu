---
title: "Az Azure Media Services áttekintése és gyakori alkalmazási esetei | Microsoft Docs"
description: "Ezen témakör áttekintést nyújt az Azure Media Services szolgáltatásairól"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: f22b87fc5bdfe2db5de39adaafe9c71d8c32b26a


---
# <a name="azure-media-services-overview-and-common-scenarios"></a>Az Azure Media Services áttekintése és gyakori alkalmazási esetei

Microsoft Azure Media Services egy bővíthető, felhőalapú platform, amely lehetővé teszi a fejlesztők számára méretezhető médiafelügyeleti és -továbbítási alkalmazások létrehozását. A Media Services alapjai a REST API-k, amelyek lehetővé teszik különböző videó- és audiotartalmak feltöltését, tárolását, kódolását és becsomagolását, majd igény szerinti és élő adatfolyamként történő továbbítását különböző ügyfelek részére (például tévékészülékekre, számítógépekre és mobileszközökre).

A Media Services használatával teljes, átfogó munkafolyamatokat hozhat létre. A munkafolyamatok bizonyos részeihez harmadik féltől származó összetevőket is szabadon felhasználhat. Például egy harmadik féltől származó kódolóval végezhet kódolást. Ezután a Media Services használatával intézheti a feltöltést, a védelmet, a csomagolást és a továbbítást.

A tartalmakat továbbíthatja élő adásban, illetve az ügyfelek igénye szerint is. Ezen témakör a tartalmak [élő](media-services-overview.md#live_scenarios) vagy [igény szerinti](media-services-overview.md#vod_scenarios) továbbításának leggyakoribb eseteit mutatja be. Emellett a témakör hivatkozásokat is tartalmaz egyéb, kapcsolódó témakörökre.

## <a name="sdks-and-tools"></a>SDK-k és eszközök

A Media Services-megoldások létrehozásához a következőket használhatja:

* [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Az elérhető ügyféloldali SDK-k valamelyike:
    * [.NET-keretrendszerhez készült Azure Media Services SDK](https://github.com/Azure/azure-sdk-for-media-services),
    * [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js-hez készült Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Ez a Node.js SDK nem Microsoft által készített verziója. Ennek a karbantartását egy közösség végzi, és jelenleg nem fedi le 100%-osan az AMS API-k tartalmát.)
* Meglévő eszközök:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Az Azure Media Services Explorer (AMSE) egy Winforms/C#-alkalmazás Windows rendszerre)

A következő kép a Media Services OData-modellen alapuló fejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

A teljes modellt [itt](https://media.windows.net/API/$metadata?api-version=2.15) tekintheti meg.  


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
Az AMS képzési terveket itt tekintheti meg:

* [AMS élő adatfolyam-továbbítási munkafolyamat](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [AMS igény szerinti adatfolyam-továbbítási munkafolyamat](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Media Services használatának megkezdéséhez rendelkeznie kell a következőkkel:

1. Egy Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com).
2. Egy Azure Media Services-fiók. Azure Media Services-fiókot az Azure Portal, a .NET vagy a REST API használatával hozhat létre. További információ: [Fiók létrehozása](media-services-portal-create-account.md)
3. (Választható:) A fejlesztési környezet beállítása Válassza ki, hogy a .NET vagy a REST API fejlesztési környezetet kívánja-e használni. További információ: [Környezet beállítása](media-services-dotnet-how-to-use.md)

    Emellett megismerheti a [programozott csatlakozást](media-services-dotnet-connect-programmatically.md) is.
4. Elindított állapotú standard vagy prémium szintű streamvégpont.  További információ: [Streamvégpontok felügyelete](https://docs.microsoft.com/en-us/azure/media-services/media-services-portal-manage-streaming-endpoints)

## <a name="concepts-and-overview"></a>Alapfogalmak és áttekintés
Az Azure Media Services alapfogalmaiért lásd: [Fogalmak](media-services-concepts.md)

Az Azure Media Services összes fő összetevőjét bemutató útmutató-sorozat: [Az Azure Media Services részletes oktatóprogramjai](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series) Ez a sorozat átfogó áttekintést nyújt a fogalmakról, és az AMSE eszköz használatával mutatja be az AMS-feladatokat. Megjegyzés: Az AMSE eszköz egy Windows-eszköz. Az eszköz támogatja a legtöbb olyan műveletet, amelyek a [.NET-keretrendszerhez készült AMS SDK](https://github.com/Azure/azure-sdk-for-media-services), a [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java) vagy az [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) használatával programozás útján megvalósíthatók.

## <a name="a-idvodscenariosadelivering-media-on-demand-with-azure-media-services-common-scenarios-and-tasks"></a><a id="vod_scenarios"></a>Igény szerinti médiatovábbítás az Azure Media Services használatával: gyakori forgatókönyvek és műveletek
Ez a szakasz gyakori forgatókönyveket ismertet, és hivatkozásokat tartalmaz a kapcsolódó témakörökre. A következő diagramon láthatók a Media Services platform azon fontosabb részei, amelyek szerepet játszanak az igény szerinti tartalomtovábbításban.

![VoD-munkafolyamat](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

### <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Tartalom védelme a tárolón és folyamatos médiatovábbítás tisztán (titkosítatlanul)
1. Töltsön fel egy kiváló minőségű mezzanine-fájlt egy adategységbe.

    Javasolt az adategységen tárolótitkosítást alkalmazni, ezáltal védve a tartalmat feltöltés és tárolás közben.
2. A kódolás kimenete egy adaptív sávszélességű MP4-fájlsorozat legyen.

    Javasolt a kimeneti adategységen tárolótitkosítást alkalmazni, ezáltal védve a tartalmat tárolás közben.
3. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás használ).

    Ha az adategységen tárolótitkosítást alkalmaz, konfigurálnia **kell** az adategység továbbítási házirendjét.
4. Tegye közzé az adategységet egy OnDemand-kereső létrehozásával.
5. Továbbítsa a közzétett tartalmat.

### <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Tartalom védelme a tárolón és dinamikusan titkosított folyamatos médiatovábbítás

1. Töltsön fel egy kiváló minőségű mezzanine-fájlt egy adategységbe. Alkalmazzon az adategységen tárolótitkosítást.
2. A kódolás kimenete egy adaptív sávszélességű MP4-fájlsorozat legyen. Alkalmazzon a kimeneti adategységen tárolótitkosítást.
3. Hozzon létre egy titkosítási tartalomkulcsot az adategységhez, amelyet a lejátszás során dinamikusan titkosítani kíván.
4. Konfigurálja a tartalomkulcs-engedélyezési házirendet.
5. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás és a dinamikus titkosítás használ).
6. Tegye közzé az adategységet egy OnDemand-kereső létrehozásával.
7. Továbbítsa a közzétett tartalmat.

### <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Gyakorlatban használható elemzések készítése videófájlokból a Médiaelemzés használatával
A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. További információk: [Az Azure Media Services Elemző áttekintése](media-services-analytics-overview.md)

1. Töltsön fel egy kiváló minőségű mezzanine-fájlt egy adategységbe.
2. Használja az alábbi Médiaelemzés-szolgáltatások egyikét a videók feldolgozásához:

   * **Indexer** – [Videók feldolgozása az Azure Media Indexer 2 használatával](media-services-process-content-with-indexer2.md)
   * **Hyperlapse** – [Médiafájlok feldolgozása az Azure Media Hyperlapse használatával](media-services-hyperlapse-content.md)
   * **Mozgásérzékelés** – [Mozgásérzékelés az Azure Médiaelemzés használatával](media-services-motion-detection.md).
   * **Arcfelismerés és érzelemfelismerés** – [Arcfelismerés és érzelemfelismerés az Azure Médiaelemzés használatával](media-services-face-and-emotion-detection.md).
   * **Videóösszegzés** – [Egy videó összegzésének létrehozása az Azure Media videóindexképek használatával](media-services-video-summarization.md)
3. A Médiaelemzés médiafeldolgozói MP4- vagy JSON-fájlokat hoznak létre. A médiafeldolgozók által létrehozott MP4-fájlokat fokozatosan lehet letölteni. A médiafeldolgozók által létrehozott JSON-fájlokat az Azure-blobtárolóból lehet letölteni.

### <a name="deliver-progressive-download"></a>Progresszív letöltés továbbítása
1. Töltsön fel egy kiváló minőségű mezzanine-fájlt egy adategységbe.
2. A kódolás kimenete egyetlen MP4-fájl legyen.
3. Tegye közzé az adategységet egy OnDemand- vagy SAS-kereső létrehozásával.

    Az SAS-kereső használata esetén a tartalmat az Azure-blobtárolóból lehet letölteni. Ebben az esetben nincs szükség elindított állapotú streamvégpontokra.
4. Töltse le fokozatosan a tartalmat.

## <a name="a-idlivescenariosadelivering-live-streaming-events-with-azure-media-services"></a><a id="live_scenarios"></a>Események élő adatfolyamainak továbbítása az Azure Media Services használatával
Az élő adatfolyam-továbbításban általában a következő összetevők játszanak szerepet:

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

Az ügyfelek felé történő tartalomtovábbításkor a cél a videók kiváló minőségben történő továbbítása különböző eszközökre, különböző hálózati körülmények között. A minőségi és hálózati körülményekhez való igazodáshoz használjon élő kódolókat, amelyek a video-adatfolyamot többféle bitrátájúvá (adaptív sávszélességűvé) alakítják.  A különböző eszközökre irányuló adatfolyam-továbbításhoz használja a Media Services [dinamikus csomagolási](media-services-dynamic-packaging-overview.md) lehetőségét, amely az adatfolyamot dinamikusan újracsomagolja különböző protokollok szerint. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH.

Az Azure Media Services szolgáltatásokban a **csatornák**, a **programok** és a **streamvégpontok** felelősek az élő adatfolyamokkal kapcsolatos minden feladatért, beleértve a feldolgozást, a formázást, a DVR-t, a biztosítást, a méretezhetőséget és a redundanciát is.

A **csatorna** egy olyan folyamatot jelent, amely az élő adatfolyamok tartalmát dolgozza fel. A csatornák a következő módokon képesek egy élő adatfolyam-továbbítás bemenetét fogadni:

* A helyszíni élő kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) tartalmakat küldenek a csatornának, amely **áteresztő** továbbításra van konfigurálva. Az **áteresztő** továbbítás azt jelenti, hogy a feldolgozott adatfolyamok további átkódolás vagy titkosítás nélkül haladnak át a **csatornán**. Többféle sávszélességű Smooth Streaming-kimenetre a következő élő kódolók képesek: MediaExcel, Imagine Communications, Ateme, Envivio, Cisco és Elemental. RTMP-kimenetre a következő élő kódolók képesek: Adobe Flash Live Encoder, Haivision, Telestream Wirecast, Teradek és Tricaster átkódolók.  Az élő kódolók olyan csatornákra is tudnak egyféle sávszélességű adatfolyamot küldeni, amelyeken az élő kódolás nincs engedélyezve, ez azonban nem ajánlott. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
>
>

* Egy helyszíni élő kódoló egy egyfajta sávszélességű adatfolyamot küld a csatornának, amelyen engedélyezve van a Media Services használatával történő élő kódolás a következő formátumok egyikében: RTP (MPEG-TS), RTMP vagy Smooth Streaming (töredékes MP4). A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó (áteresztő) csatornák használata
A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az **áteresztő** munkafolyamatban.

![Élő munkafolyamat][live-overview2]

Tovább információk: [Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Az Azure Media Services segítségével élő kódolásra képes csatornák használata
A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az élő adatfolyam-továbbítási munkafolyamatban, ha a csatorna számára engedélyezett a Media Services használatával végzett élő kódolás.

![Élő munkafolyamat][live-overview1]

További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

## <a name="consuming-content"></a>Tartalmak felhasználása
Az Azure Media Services biztosította eszközökkel részletes, dinamikus ügyféloldali lejátszóalkalmazások hozhatók létre a legtöbb platformra, köztük a következőkre: iOS, Android, Windows, Windows Phone, Xbox és dekóderek. A következő témakor hivatkozásokat tartalmaz azokhoz az SDK-khoz és lejátszó-keretrendszerekhez, amelyekkel kifejlesztheti a saját ügyfélalkalmazásait a Media Services médiafolyamainak fogadására.

[Videolejátszó alkalmazások fejlesztése](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Az Azure CDN engedélyezése
A Media Services támogatja az Azure CDN-integrációt. További információk az Azure CDN engedélyezéséről: [Adatfolyam-továbbítási végpontok kezelése egy Media Services-fiókban](media-services-portal-manage-streaming-endpoints.md)

## <a name="scaling-a-media-services-account"></a>Media Services-fiók méretezése

A **Media Services** méretezése oly módon lehetséges, ha megadja a **folyamatos átvitelhez fenntartott egységek** és a **kódoláshoz fenntartott egységek** számát, amelyekkel a fiókját ki szeretné építeni.

A Media Services-fiókját tárfiókok hozzáadásával is méretezheti. Minden tárfiók legfeljebb 500 TB kapacitású lehet. Ha a tárolót az alapértelmezett határérték fölé szeretné bővíteni, több tárfiókot is társíthat ugyanahhoz a Media Services-fiókhoz.
A Media Services ügyfelei általában egy **standard** szintű streamvégpontot vagy egy vagy több **prémium** szintű streamvégpontot választanak, saját igényeiknek megfelelően. A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. Ugyanazokat a szolgáltatásokat tartalmazza, mint a prémium szintű streamelési egységek.
A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. Ha speciális feladatokat végez, vagy a standard streamvégpontok teljesítménycéljai nem felelnek meg a streamelési kapacitásra vonatkozó követelményeknek, esetleg a növekvő sávszélesség-igény kezelése érdekében a skálázási egységek (más néven prémium streamelési egységek) beállításával szabályozni szeretné a streamvégpontok szolgáltatás kapacitását, akkor érdemes skálázási egységeket kiosztania.

[Ez a témakör](media-services-portal-scale-streaming-endpoints.md) hivatkozásokat tartalmaz a kapcsolódó témakörökre.

## <a name="support"></a>Támogatás
[Az Azure-támogatási szolgáltatások](https://azure.microsoft.com/support/options/) támogatási lehetőségeket biztosítanak az Azure szolgáltatásaival, köztük a Media Services szolgáltatással kapcsolatban.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)
* A Media Services kódolási funkciójához szükséges REST API-tranzakciókhoz 99,9%-os rendelkezésre állást garantálunk.
* A streamelési funkciók szolgáltatási kéréseinek sikeres kiszolgálására 99,9%-os rendelkezésre állást garantálunk a létező médiatartalmak esetében egy standard vagy prémium szintű streamvégpont megvásárlásakor.
* Az élő csatornák esetében garantáljuk, hogy a futó csatornák az idő legalább 99,9%-ában elérhetők lesznek kívülről.
* A Content Protection esetében garantáljuk, hogy a kulcskéréseket az idő 99,9%-ában sikeresen teljesítjük.
* Az indexelő esetében sikeresen, 99,9%-os rendelkezésre állási garanciával kiszolgáljuk az egy kódolási egységgel feldolgozott indexelési kéréseket.

További információ: [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png



<!--HONumber=Jan17_HO2-->


