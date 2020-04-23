---
title: Az Azure Media Services videoindexelőkiadási megjegyzések | Microsoft dokumentumok
description: A legújabb fejlemények naprakészen maradásához ez a cikk az Azure Media Services videoindexelőjének legújabb frissítéseit tartalmazza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: f7ce5f5086bcf2d577fb998df307ee684d536c15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870103"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Az Azure Media Services videoindexelőkiadási megjegyzések

>Kap értesítést, hogy mikor kell újra ezt az oldalt a `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` frissítések másolásával és beillesztése ezt az URL-t: az RSS feed olvasó.

Ahhoz, hogy naprakész legyen a legújabb fejleményekkel, ez a cikk az ön számára tájékoztatást nyújt:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="april-2020"></a>2020. április

### <a name="new-widget-parameters-capabilities"></a>Új widget paraméterek képességek

Az **Insights** widget új `language` paramétereket tartalmaz: és `control`.

A **Lejátszó** widget `locale` új paraméterrel rendelkezik. Mind `locale` `language` a paraméterek szabályozzák a játékos nyelvét.

További információt a [widgettípusok](video-indexer-embed-widgets.md#widget-types) című szakaszban talál. 

### <a name="new-player-skin"></a>Új játékos bőr

Egy új játékos bőr indított frissített design.

## <a name="january-2020"></a>2020. január
 
### <a name="custom-language-support-for-additional-languages"></a>Egyéni nyelvi támogatás további nyelvekhez

A Video Indexer mostantól `ar-SY` támogatja `en-UK`a `en-AU` , és (csak API) egyéni nyelvi modelleket.
 
### <a name="delete-account-timeframe-action-update"></a>Fiók időkeretének törlése műveletfrissítés

A fiók törlése művelet mostantól 48 óra helyett 90 napon belül törli a fiókot.
 
### <a name="new-video-indexer-github-repository"></a>Új Videó Indexelő GitHub-tárház

Egy új Video Indexer GitHub különböző projektekkel, első lépések útmutatók és kódminták már elérhető:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger frissítés

Video Indexer egyesített **hitelesítések** és **műveletek** egyetlen [Video Indexer OpenAPI Specification (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). A fejlesztők megtalálhatják az API-kat a [Video Indexer Developer Portal webhelyen.](https://api-portal.videoindexer.ai/)

## <a name="december-2019"></a>2019. december

### <a name="update-transcript-with-the-new-api"></a>Átirat frissítése az új API-val

Frissítsen egy adott szakaszt az átiratban az [Update-Video-Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API használatával.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Fiókkonfiguráció javítása a Video Indexer portálról

Most már frissítheti a Media Services kapcsolatkonfigurációját, hogy önsegítséget nyújtson az olyan problémákkal kapcsolatban, mint például: 

* helytelen Azure Media Services-erőforrás
* jelszó módosítása
* A Media Services-erőforrások áthelyezve az előfizetések között  

A fiókkonfiguráció javításához a Video Indexer portálon keresse meg a Beállítások > Fiók lapot (tulajdonosként).

### <a name="configure-the-custom-vision-account"></a>Az egyéni látási fiók konfigurálása

Konfigurálja az egyéni látási fiókot a fizetős fiókokon a Video Indexer portál használatával (korábban ezt csak az API támogatta). Ehhez jelentkezzen be a Video Indexelő portálra, és válassza a Modell testreszabása > animált karakterek > konfigurálása lehetőséget. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Jelenetek, felvételek és kulcsképek – mostantól egyetlen betekintési panelen

A jelenetek, a felvételek és a kulcsképek mostantól egyetlen betekintésbe olvadnak a könnyebb felhasználás és navigáció érdekében. Amikor kiválasztja a kívánt jelenetet láthatja, hogy milyen felvételek és kulcsképek áll. 

### <a name="notification-about-a-long-video-name"></a>Értesítés egy hosszú videónévről

Ha egy videó név 80 karakternél hosszabb, a Video Indexer leíró hibát jelenít meg a feltöltéskor.

### <a name="streaming-endpoint-is-disabled-notification"></a>A streamelési végpont le van tiltva értesítés

Ha a streamelési végpont le van tiltva, a Video Indexer leíró hibát jelenít meg a lejátszó oldalán.

### <a name="error-handling-improvement"></a>Hibakezelés javítása

A 409-es állapotkód most antól a Video- és Videoindex [API-k újraindexelési](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) és [frissítési](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) API-jaiból kerül vissza, ha egy videó aktívan indexelhető, hogy ne lehessen véletlenül felülkerekedni az aktuális újraindexváltozásokon.

## <a name="november-2019"></a>2019. november
 
* A koreai egyéni nyelvi modellek támogatása

    A videoindexelő mostantól támogatja az`ko-KR`egyéni nyelvi modelleket koreai ( ) nyelven az API-ban és a portálon is. 
* A szöveggé bírja (STT) új nyelvek

    A Video Indexer API-k mostantól támogatják az STT-t arab Levantine (ar-SY), angol angol angol nyelvjárásban (en-GB) és angol ausztrál nyelvjárásban (en-AU).
    
    A videó feltöltéshez a zh-HANS-t zh-CN-re cseréltük, mindkettő támogatott, de a zh-CN ajánlott és pontosabb.
    
## <a name="october-2019"></a>2019. október
 
* Animált karakterek keresése a galériában

    Animált karakterek indexelésekénél most már megkeresheti őket a fiók videokonyhájában. További információ: [Animált karakterek felismerése](animated-characters-recognition.md).

## <a name="september-2019"></a>2019. szeptember
 
Több fejlesztést jelentettek be az IBC 2019-en:
 
* Animált karakterfelismerés (nyilvános előnézet)

    Képes felismerni a csoportos hirdetés felismerni karakterek animált tartalom, integráció révén az egyéni látás. További információ: [Animált karakterfelismerés](animated-characters-recognition.md).
* Többnyelvű azonosítás (nyilvános előzetes verzió)

    A szegmensek több nyelven is észlelhetők a hangsávban, és többnyelvű átiratot hozhatnak létre azok alapján. Kezdeti támogatás: angol, spanyol, német és francia. További információt a [Többnyelvű tartalom automatikus azonosítása és átírása](multi-language-identification-transcription.md)című témakörben talál.
* Elnevezett entitás kinyerése személyek és hely számára

    Kivonja a márkákat, helyeket és embereket a beszédből és a vizuális szövegből természetes nyelvi feldolgozással (NLP).
* Szerkesztői felvétel típusának besorolása

    Címkézés a felvételek szerkesztői típusú, mint a közelről, közepes lövés, két lövés, beltéri, kültéri stb. További információ: [Szerkesztői felvétel típusának észlelése](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Téma-következtetés javítása - most, amely szinten 2
    
    A témakör-pontozó modell mostmár támogatja az IPTC-taxonómia mélyebb részletességét. Olvassa el a részleteket az [Azure Media Services új, a i-alapú innovációjában.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>2019. augusztus
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer telepített Egyesült Királyság déli

Most már létrehozhat egy Video Indexer fizetős fiókot az Egyesült Királyság déli régiójában.

### <a name="new-editorial-shot-type-insights-available"></a>Új Szerkesztői Shot Type insights elérhető

Új címkék hozzá video felvételek biztosít szerkesztői "lövés típusok", hogy azonosítsa őket a közös szerkesztői kifejezéseket használt tartalom létrehozása munkafolyamat, mint például: extrém közelkép, közelkép, széles, közepes, két lövés, kültéri, beltéri, bal és jobb arc (Elérhető a JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Új személyek és helyek entitások kinyerése elérhető

A Video Indexer azonosítja a megnevezett helyeket és személyeket természetes nyelvi feldolgozással (NLP) a videó OCR-jében és átírásában. A Video Indexer gépi tanulási algoritmust használ annak felismerésére, hogy egy videóban mikor hívnak ki bizonyos helyeket (például az Eiffel-tornyot) vagy embereket (például John Doe-t).

### <a name="keyframes-extraction-in-native-resolution"></a>Kulcsképek kibontása natív felbontásban

A Video Indexer által kinyert kulcsképek a videó eredeti felbontásában érhetők el.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA az egyéni arcmodellek képekből történő betanításához

Az előnézeti módból ga-ba áthelyezett képek betanítási arcai (API-n keresztül és a portálon érhető el).

> [!NOTE]
> Nincs díjszabási hatás az "Előzetes ga" átmenethez kapcsolódóan.

### <a name="hide-gallery-toggle-option"></a>Galéria váltógombjának elrejtése beállítás

A felhasználó elrejtheti a galéria lapot a portálról (hasonlóan a minták lap elrejtéséhez).
 
### <a name="maximum-url-size-increased"></a>Az URL maximális mérete megnövelve

4096-os URL-lekérdezési karakterlánc támogatása (2048 helyett) a videó indexeléséhez.
 
### <a name="support-for-multi-lingual-projects"></a>Többnyelvű projektek támogatása

A projektek mostantól különböző nyelveken indexelt videók alapján hozhatók létre (csak API-ban).

## <a name="july-2019"></a>2019. július

### <a name="editor-as-a-widget"></a>Szerkesztő, mint widget

A Video Indexer AI-szerkesztő már elérhető, mint egy widget kell ágyazni az ügyfél alkalmazásokat.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Egyéni nyelvi modell frissítése a portálról származó feliratfájlból

Az ügyfelek a portál testreszabási lapján biztosíthatnak VTT, SRT és TTML fájlformátumokat a nyelvi modellek beviteleként.

## <a name="june-2019"></a>2019. június

### <a name="video-indexer-deployed-to-japan-east"></a>Videó Indexer telepített Japán keleti

Most már létrehozhat egy videoindexer fizetős fiókot a Japán keleti régiójában.

### <a name="create-and-repair-account-api-preview"></a>Fiók API létrehozása és javítása (előzetes verzió)

Hozzáadott egy új API-t, amely lehetővé teszi [az Azure Media Service kapcsolatvégpontjának vagy kulcsának frissítését.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)

### <a name="improve-error-handling-on-upload"></a>A feltöltési hibakezelés javítása 

Az alapul szolgáló Azure Media Services-fiók helytelen konfigurálása esetén egy leíró üzenetet ad vissza.

### <a name="player-timeline-keyframes-preview"></a>A játékosok idővonalának kulcsképének előnézete 

Mostantól minden egyes alkalommal megjelenik egy kép előnézete a játékos idővonalán.

### <a name="editor-semi-select"></a>A szerkesztő félig kiválasztja

Most már megtekintheti az összes olyan elemzés előnézetét, amely egy adott elemzési időkeret kiválasztásának eredményeként van kiválasztva a szerkesztőben.

## <a name="may-2019"></a>2019. május

### <a name="update-custom-language-model-from-closed-caption-file"></a>Egyéni nyelvi modell frissítése feliratfájlból

[Hozzon létre egyéni nyelvi modellt,](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) és frissítse az egyéni nyelvi [modellek](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API-k most antól támogatja a VTT, SRT és TTML fájlformátumok bemeneti nyelvi modellek.

Az [Update Video transcript API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)hívásakor az átirat automatikusan hozzáadódik. A videóhoz társított betanítási modell is automatikusan frissül. A nyelvi modellek testreszabásáról és betanításáról a [Nyelvi modell testreszabása a Video Indexelő vel](customize-language-model-overview.md)című témakörben talál.

### <a name="new-download-transcript-formats--txt-and-csv"></a>Új letöltési átirat formátumok – TXT és CSV

A már támogatott feliratozási formátumon (SRT, VTT és TTML) kívül a Video Indexer támogatja az átirat letöltését TXT és CSV formátumban.

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
