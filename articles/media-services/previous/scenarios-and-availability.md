---
title: Microsoft Azure Media Services-forgatókönyvek és a szolgáltatások rendelkezésre állása az egyes adatközpontokban | Microsoft Docs
description: Ez a témakör a Microsoft Azure Media Services-forgatókönyvek áttekintését és a funkciók és szolgáltatások rendelkezésre állását mutatja be az egyes adatközpontokban.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: f62e2d482ca6258821cb800d2650a5f0c6a27e7c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762520"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Forgatókönyvek és a Media Services-szolgáltatások rendelkezésre állása az egyes adatközpontokban

A Microsoft Azure Media Services (AMS) lehetővé teszi különböző videó- és hangtartalmak biztonságos feltöltését, tárolását, kódolását és becsomagolását, majd igény szerinti és élő streamként történő továbbítását különböző ügyfelek részére (például tévékészülékekre, számítógépekre és mobileszközökre).

Az AMS világszerte számos adatközpontban működik. Ezek az adatközpontok földrajzi régiók szerint vannak csoportosítva, ami kellő mozgásteret biztosít az alkalmazások létrehozási helyének megválasztásához. [A régiók és a kapcsolódó helyek listáját itt](https://azure.microsoft.com/regions/) tekintheti meg. 

Ez a témakör bemutatja a tartalom továbbításának leggyakoribb eseteit [élő](#live_scenarios) vagy igény szerinti. Ez a témakör a médiafunkciók és szolgáltatások adatközpontok közötti rendelkezésre állásáról is részleteket nyújt.

## <a name="overview"></a>Áttekintés

### <a name="prerequisites"></a>Előfeltételek

Az Azure Media Services használatának megkezdéséhez rendelkeznie kell a következőkkel:

* Egy Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com).
* Egy Azure Media Services-fiók. További információ: [Fiók létrehozása](media-services-portal-create-account.md)
* A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie.

    Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a streamvégpontnak **Fut** állapotban kell lennie.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Az AMS OData-modellen alapuló fejlesztések során leggyakrabban használt objektumok

A következő kép a Media Services OData-modellen alapuló fejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

A teljes modellt [itt](https://media.windows.net/API/$metadata?api-version=2.15) tekintheti meg.  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Tartalom védelme a tárolón és folyamatos médiatovábbítás tisztán (titkosítatlanul)

![VoD-munkafolyamat](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe.

    Javasolt az adategységen tárolótitkosítást alkalmazni, ezáltal védve a tartalmat feltöltés és tárolás közben.
2. A kódolás kimenete egy adaptív sávszélességű MP4-fájlsorozat legyen.

    Javasolt a kimeneti adategységen tárolótitkosítást alkalmazni, ezáltal védve a tartalmat tárolás közben.
3. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás használ).

    Ha az adategységen tárolótitkosítást alkalmaz, konfigurálnia **kell** az adategység továbbítási házirendjét.
4. Tegye közzé az adategységet egy OnDemand-kereső létrehozásával.
5. Továbbítsa a közzétett tartalmat.

Az adatközpontokban lévő rendelkezésre állásról információért lásd a [Rendelkezésre állás](#availability) című szakaszt.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Tartalom védelme a tárolón és dinamikusan titkosított folyamatos médiatovábbítás

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe. Alkalmazzon az adategységen tárolótitkosítást.
2. A kódolás kimenete egy adaptív sávszélességű MP4-fájlsorozat legyen. Alkalmazzon a kimeneti adategységen tárolótitkosítást.
3. Hozzon létre egy titkosítási tartalomkulcsot az adategységhez, amelyet a lejátszás során dinamikusan titkosítani kíván.
4. Konfigurálja a tartalomkulcs-engedélyezési házirendet.
5. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás és a dinamikus titkosítás használ).
6. Tegye közzé az adategységet egy OnDemand-kereső létrehozásával.
7. Továbbítsa a közzétett tartalmat.

Az adatközpontokban lévő rendelkezésre állásról információért lásd a [Rendelkezésre állás](#availability) című szakaszt.

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Gyakorlatban használható elemzések készítése videófájlokból a Médiaelemzés használatával

A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. További információk: [Az Azure Media Services Elemző áttekintése](media-services-analytics-overview.md)

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe.
2. Feldolgozhatja a videóit [A Media Analytics áttekintése](media-services-analytics-overview.md) szakaszban leírt egyik Media Analytics-szolgáltatással.
3. A Médiaelemzés médiafeldolgozói MP4- vagy JSON-fájlokat hoznak létre. A médiafeldolgozók által létrehozott MP4-fájlokat fokozatosan lehet letölteni. A médiafeldolgozók által létrehozott JSON-fájlokat az Azure-blobtárolóból lehet letölteni.

Az adatközpontokban lévő rendelkezésre állásról információért lásd a [Rendelkezésre állás](#availability) című szakaszt.

## <a name="deliver-progressive-download"></a>Progresszív letöltés továbbítása

1. Töltsön fel egy kiváló minőségű médiafájlt egy adategységbe.
2. A kódolás kimenete egyetlen MP4-fájl legyen.
3. Tegye közzé az adategységet egy OnDemand- vagy SAS-kereső létrehozásával.

    Az SAS-kereső használata esetén a tartalmat az Azure-blobtárolóból lehet letölteni. Ebben az esetben nincs szükség elindított állapotú streamvégpontokra.
4. Töltse le fokozatosan a tartalmat.

## <a id="live_scenarios"></a>Események élő közvetítése 

1. Élő tartalmakat dolgozhat fel különböző élő streamelési protokollok (például RTMP vagy Smooth Streaming) használatával.
2. A streamet adaptív sávszélességűvé kódolhatja (opcionális).
3. Megtekintheti az élő stream előnézetét.
4. Továbbíthatja a tartalmat gyakori streamelési protokollok (például MPEG DASH, Smooth, HLS) használatával közvetlenül az ügyfelek részére, vagy egy tartalomkézbesítési hálózatra (CDN) későbbi terjesztés céljából.

    – vagy –

    A feldolgozott tartalmakat rögzítheti és tárolhatja a későbbi streamelés érdekében (Video-on-Demand).

Élő streameléskor a következő útvonalak egyikét választhatja:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Helyszíni kódolóktól többszörös átviteli sebességű adatfolyamot fogadó (áteresztő) csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az **áteresztő** munkafolyamatban.

![Élő munkafolyamat](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Tovább információk: [Helyszíni kódolóktól többféle sávszélességű adatfolyamot fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Az Azure Media Services segítségével élő kódolásra képes csatornák használata

A következő diagramon láthatók a AMS platform azon fontosabb részei, amelyek szerepet játszanak az élő adatfolyam-továbbítási munkafolyamatban, ha a csatorna számára engedélyezett a Media Services használatával végzett élő kódolás.

![Élő munkafolyamat](./media/scenarios-and-availability/media-services-live-streaming-new.png)

További információk: [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)

Az adatközpontokban lévő rendelkezésre állásról információért lásd a [Rendelkezésre állás](#availability) című szakaszt.

## <a name="consuming-content"></a>Tartalmak felhasználása

Az Azure Media Services biztosította eszközökkel részletes, dinamikus ügyféloldali lejátszóalkalmazások hozhatók létre a legtöbb platformra, köztük a következőkre: iOS, Android, Windows, Windows Phone, Xbox és dekóderek. A következő témakor hivatkozásokat tartalmaz azokhoz az SDK-khoz és lejátszó-keretrendszerekhez, amelyekkel kifejlesztheti a saját ügyfélalkalmazásait a Media Services médiafolyamainak fogadására. További információkért lásd a [videólejátszó alkalmazások fejlesztését](media-services-develop-video-players.md) ismertető cikket.

## <a name="enabling-azure-cdn"></a>Az Azure CDN engedélyezése

A Media Services támogatja az Azure CDN-integrációt. További információk az Azure CDN engedélyezéséről: [Adatfolyam-továbbítási végpontok kezelése egy Media Services-fiókban](media-services-portal-manage-streaming-endpoints.md)

## <a id="scaling"></a>Media Services-fiók méretezése

Az AMS-ügyfelek méretezhetik a streamvégpontokat, a médiafeldolgozást és a tárolást az AMS-fiókjukon.

* A Media Services ügyfelei **standard** szintű streamvégpontot vagy **prémium** szintű streamvégpontot választhatnak. A **standard** streamvégpont a legtöbb streamelési feladat ellátására alkalmas. Ugyanazokkal a jellemzőkkel rendelkezik, mint a **prémium** szintű streamvégpontok, és automatikusan méretezi a kimenő sávszélességet. 

    A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. A **prémium** streamvégponttal rendelkező ügyfelek alapértelmezés szerint kapnak egy adategységet (SU-t). A streamvégpont adategységek hozzáadásával méretezhető. Mindegyik adategység további sávszélesség-kapacitást nyújt az alkalmazásnak. A **prémium** szintű streamvégpontok méretezéséről további információt a [streamvégpontok méretezését](media-services-portal-scale-streaming-endpoints.md) ismertető témakörben talál.

* A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: **S1**, **S2**, vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett.

    A Fenntartott egység típusának meghatározása mellett megadhatja, hogy ellátja-e a fiókot **Fenntartott egységekkel** (RU-kkal). A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

    >[!NOTE]
    >A Fenntartott egységek az összes médiafeldolgozás párhuzamossá tételéért felelősek, beleértve az Azure Media Indexerrel végzett indexelési feladatokat is. De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.

    További információkért olvassa el a [médiafeldolgozás méretezését](media-services-portal-scale-media-processing.md) ismertető cikket.
* A Media Services-fiókját tárfiókok hozzáadásával is méretezheti. Minden tárfiók legfeljebb 500 TB kapacitású lehet. Ha a tárolót az alapértelmezett határérték fölé szeretné bővíteni, több tárfiókot is társíthat ugyanahhoz a Media Services-fiókhoz. További információkért olvassa el a [tárfiókok kezelését](meda-services-managing-multiple-storage-accounts.md) ismertető cikket.

## <a id="availability"></a> A Media Services-funkciók rendelkezésre állása az egyes adatközpontokban

Ez a szakasz a Media Services-funkciók az adatközpontok közötti rendelkezésre állásáról nyújt részleteket.

### <a name="ams-accounts"></a>AMS-fiókok

#### <a name="availability"></a>Rendelkezésre állás

Annak megállapításához, hogy a Media Services elérhető-e az adatközpontban, lépjen a https://azure.microsoft.com/status/ helyre, és görgessen a MEDIA táblázathoz.

### <a name="streaming-endpoints"></a>Streamvégpontok 

A Media Services ügyfelei **standard** szintű streamvégpontot vagy **prémium** szintű streamvégpontot választhatnak. További információt a [méretezésről](#scaling) szóló szakaszban talál.

#### <a name="availability"></a>Rendelkezésre állás

|Name (Név)|status|Adatközpontok
|---|---|---|
|Standard|FE|Összes|
|Prémium|FE|Összes|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Rendelkezésre állás

Kivételével minden adatközpontban elérhető: Németország, Dél-Brazília, Nyugat-India, Dél-India és közép-India. 

### <a name="encoding-media-processors"></a>Médiafeldolgozók kódolása

Az AMS két igény szerinti kódolót nyújt: a **Media Encoder Standard** kódolót és a **Media Encoder Premium-munkafolyamatot**. További információkért olvassa el [az Azure igény szerinti médiakódolók áttekintését és összehasonlítását](media-services-encode-asset.md). 

#### <a name="availability"></a>Rendelkezésre állás

|Médiafeldolgozó neve|status|Adatközpontok
|---|---|---|
|Media Encoder Standard|FE|Összes|
|Media Encoder Premium-munkafolyamat|FE|Kína kivételével|

### <a name="analytics-media-processors"></a>Elemzési médiafeldolgozók

A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. További információk: [Az Azure Media Services Elemző áttekintése](media-services-analytics-overview.md)

#### <a name="availability"></a>Rendelkezésre állás

|Médiafeldolgozó neve|status|Adatközpontok
|---|---|---|
|Azure Media Face Detector|Előzetes verzió|Összes|
|Azure Media Hyperlapse|Előzetes verzió|Összes|
|Azure Media Indexer|FE|Összes|
|Azure Media Motion Detector|Előzetes verzió|Összes|
|Azure Media OCR|Előzetes verzió|Összes|
|Azure Media Redactor|Előzetes verzió|Összes|
|Azure Media Stabilizer|Előzetes verzió|Összes|
|Azure Media Video Thumbnails|Előzetes verzió|Összes|
|Azure Media Indexer 2|Előzetes verzió|Kína és a szövetségi kormányzati régió kivételével|

### <a name="protection"></a>Védelem

A Microsoft Azure Media Services lehetővé teszi a médiatartalmak védelmét attól a ponttól kezdve, ahogy az elhagyja a számítógépét, egészen a tároláson, a feldolgozáson és a továbbításon át. További információért olvassa el az [AMS-tartalmak védelmét](media-services-content-protection-overview.md) ismertető cikket.

#### <a name="availability"></a>Rendelkezésre állás

|Titkosítás|status|Adatközpontok|
|---|---|---| 
|Storage|FE|Összes|
|AES-128-kulcsok|FE|Összes|
|FairPlay|FE|Összes|
|PlayReady|FE|Összes|
|Widevine|FE|Mindenhol, kivéve Németországot, a szövetségi kormányzati régiót és Kínát.

### <a name="reserved-units-rus"></a>Fenntartott egységek (RU-k)

A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. 

További információt a [méretezésről](#scaling) szóló szakaszban talál.

#### <a name="availability"></a>Rendelkezésre állás

Minden adatközpontban elérhető.

### <a name="reserved-unit-ru-type"></a>Fenntartott egység (RU) típusa

A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: S1, S2 vagy S3.

További információt a [méretezésről](#scaling) szóló szakaszban talál.

#### <a name="availability"></a>Rendelkezésre állás

|RU típusának neve|status|Adatközpontok
|---|---|---|
|S1|FE|Összes|
|S2|FE|Mindenhol, kivéve Dél-Brazíliát és Nyugat-Indiát|
|S3|FE|Mindenhol, kivéve Nyugat-Indiát|

## <a name="next-steps"></a>További lépések

Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

