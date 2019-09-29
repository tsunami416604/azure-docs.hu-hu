---
title: Azure Media Services Video Indexer kibocsátási megjegyzései | Microsoft Docs
description: Ha naprakészen szeretne maradni a legújabb fejleményekkel, ez a cikk a Azure Media Services Video Indexer legújabb frissítéseit tartalmazza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: 4953160a30e45b9be1ff249b6cd1f74e01bb14f3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672658"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer kibocsátási megjegyzései

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="september-2019"></a>Szeptember 2019
 
Több előrelépés is jelent meg az IBC 2019-ben:
 
* Animált karakterek felismerése (nyilvános előzetes verzió)

    Lehetővé teszi a csoportos hirdetések felismerési karaktereinek észlelését az animált tartalomban az egyéni jövőképtel való integráción keresztül. További információ: [animált karakterek észlelése](animated-characters-recognition.md).
* Többnyelvű azonosítás (nyilvános előzetes verzió)

    A hangsávokban több nyelven is felderítheti a szegmenseket, és a rajtuk alapuló többnyelvű átiratot hozhat létre. Kezdeti támogatás: Angol, spanyol, német és francia. További információt a [többnyelvű tartalom automatikus azonosítása és](multi-language-identification-transcription.md)átírása című témakörben talál.
* Elnevezett entitások kinyerése személyekhez és helyekhez

    Természetes nyelvi feldolgozás (NLP) használatával kinyerheti a márkákat, a helyszíneket és a beszéd-és vizualizációs szövegből származó személyeket.
* Szerkesztési shot típusú besorolás

    A felvételek címkézése olyan szerkesztési típusokkal, mint például a közelkép, közepes méretű felvétel, két lövés, beltéri, kültéri stb. További információ: [szerkesztési shot típusú észlelés](scenes-shots-keyframes.md#editorial-shot-type-detection).
* A fejlesztéssel foglalkozó témakör (2. szint)
    
    A "viszonyítási modell" példa mostantól támogatja az IPTC-rendszertan mélyebb részletességét. Olvassa el részletesen [Azure Media Services új AI-alapú innovációt](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Augusztus 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer üzembe helyezése Egyesült Királyság déli régiója

Most már létrehozhat egy Video Indexer fizetős fiókot az Egyesült Királyság déli régiójában.

### <a name="new-editorial-shot-type-insights-available"></a>Új szerkesztői shot típusú bepillantások érhetők el

A videó felvételekhez hozzáadott új címkék a "shot Types" lehetőséget biztosítják a tartalom-létrehozási munkafolyamatban használt általános szerkesztési kifejezésekkel való azonosításhoz, például: Extreme Vértes, Vértes, széles, közepes, két lövés, kültéri, beltéri, bal oldali és jobb oldali (elérhető a JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Új személyek és helyszínek entitások kinyerése elérhető

A Video Indexer azonosított helyszíneket és személyeket azonosít természetes nyelvi feldolgozással (NLP) a videó OCR és átirat használatával. A Video Indexer gépi tanulási algoritmust használ arra, hogy felismerje, ha egy videóban egy adott helyet (például az Eiffel-tornyot) vagy személyeket (például John Doe) kell meghívni.

### <a name="keyframes-extraction-in-native-resolution"></a>Kulcsképek kibontása natív felbontásban

A Video Indexer által kinyert kulcsképek a videó eredeti felbontásában érhetők el.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Az egyéni arc modellek betanítása képekből

Az előnézet módból a GA-ba (az API-n és a portálon keresztül elérhető) képekből származó arcok betanítása.

> [!NOTE]
> Az "előzetes verzióról a GA-re" való áttérésre nincs hatással az árképzés.

### <a name="hide-gallery-toggle-option"></a>A katalógus váltógomb elrejtése lehetőség

A felhasználó dönthet úgy, hogy elrejti a katalógus fület a portálról (a minták lap elrejtéséhez hasonlóan).
 
### <a name="maximum-url-size-increased"></a>Az URL-cím maximális mérete megnövelve

A videó indexeléséhez a 4096-es URL-lekérdezési karakterlánc (2048 helyett) támogatása.
 
### <a name="support-for-multi-lingual-projects"></a>Többnyelvű projektek támogatása

A projektek mostantól különböző nyelveken indexelt videók alapján is létrehozhatók (csak API-val).

## <a name="july-2019"></a>Július 2019

### <a name="editor-as-a-widget"></a>Szerkesztő widgetként

A Video Indexer AI-Editor mostantól elérhető widgetként, amely beágyazható az ügyfelek alkalmazásaiba.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Egyéni nyelvi modell frissítése a lezárt képaláírás-fájlból a portálról

Az ügyfelek a portál Testreszabás lapján VTT, SRT és TTML fájlformátumokat is biztosíthatnak a nyelvi modellekhez.

## <a name="june-2019"></a>Június 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer üzembe helyezése Kelet-Japánban

Mostantól létrehozhat egy Video Indexer fizetős fiókot a Kelet-japán régióban.

### <a name="create-and-repair-account-api-preview"></a>Account API létrehozása és javítása (előzetes verzió)

Új API hozzáadása, amely lehetővé teszi [Az Azure Media Service-kapcsolat végpontjának vagy kulcsának frissítését](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>A hibajavítások javítása a feltöltéskor 

A rendszer a mögöttes Azure Media Services fiók helytelen konfigurálása esetén leíró üzenetet ad vissza.

### <a name="player-timeline-keyframes-preview"></a>Játékos idősor-kulcsképek – előzetes verzió 

Most már láthat egy képelőnézett a lejátszó idővonalán.

### <a name="editor-semi-select"></a>Szerkesztő pontosvesszővel kiválasztva

Most már megtekintheti az összes olyan elemzést, amelyet a szerkesztőben megadott betekintési időkeret kiválasztásának eredményeképpen választott ki.

## <a name="may-2019"></a>2019. május

### <a name="update-custom-language-model-from-closed-caption-file"></a>Egyéni nyelvi modell frissítése a lezárt képaláírás-fájlból

[Egyéni nyelvi modell létrehozása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) és az [Egyéni nyelvi modellek frissítése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) mostantól támogatja az VTT, az SRT és a TTML fájlformátumokat a nyelvi modellek bemenetének megfelelően.

Az [Update video ÁTIRAT API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)meghívásakor a rendszer automatikusan hozzáadja az átiratot. A videóhoz társított betanítási modell is automatikusan frissül. A nyelvi modellek testreszabásával és betanításával kapcsolatos információkért lásd: [nyelvi modell testreszabása video Indexer](customize-language-model-overview.md)használatával.

### <a name="new-download-transcript-formats--txt-and-csv"></a>Új letöltési átirat formátuma – TXT és CSV

A már támogatott lezárt feliratozási formátum (SRT, VTT és TTML) mellett a Video Indexer mostantól támogatja az átirat TXT-és CSV-formátumokban való letöltését.

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
