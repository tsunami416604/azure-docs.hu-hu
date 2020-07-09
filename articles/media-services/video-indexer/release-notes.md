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
ms.date: 06/02/2020
ms.author: juliako
ms.openlocfilehash: 5bd4c9aa3fde9e3fa596ce5a18b892edfab60af5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325065"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer kibocsátási megjegyzései

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` az RSS-hírcsatorna-olvasóba.

A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő információkat tartalmazza:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="may-2020"></a>2020. május

### <a name="video-indexer-deployed-in-the-east-us"></a>Az USA keleti régiójában üzembe helyezett Video Indexer

Most már létrehozhat egy Video Indexer fizetős fiókot az USA keleti régiójában.
 
### <a name="video-indexer-url"></a>Video Indexer URL-cím

Video Indexer regionális végpontok mind egységesek voltak, hogy csak a www használatával induljon el. Nincs szükség műveleti tételre.

Mostantól elérhetővé válik a www.videoindexer.ai, függetlenül attól, hogy a widgetek beágyazásához vagy Video Indexer webalkalmazásokhoz való bejelentkezéshez.

A rendszer a wus.videoindexer.ai is átirányítja a www-re. További információ a [beágyazási video Indexer widgetek](video-indexer-embed-widgets.md)alkalmazásokban érhető el.

## <a name="april-2020"></a>2020. április

### <a name="new-widget-parameters-capabilities"></a>Új widget-paraméterek képességei

Az **adatáttekintési** widget új paramétereket tartalmaz: `language` és `control` .

A **Player** widget új `locale` paraméterrel rendelkezik. `locale`A és `language` a paraméterek egyaránt vezérlik a lejátszó nyelvét.

További információ: [widget-típusok](video-indexer-embed-widgets.md#widget-types) szakasz. 

### <a name="new-player-skin"></a>Új lejátszó-felszín

Új, frissített kialakítással ellátott lejátszó-felszín.

### <a name="prepare-for-upcoming-changes"></a>Felkészülés a közelgő változásokra

* Napjainkban a következő API-k adnak vissza egy fiók objektumot:

    * [Fizetős fiók létrehozása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [Fiók beolvasása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [Get-accounts-Authorization](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [Get-accounts-with-token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    A fiók objektumhoz tartozik egy mező, amely a `Url` [video Indexer webhely](https://www.videoindexer.ai/)helyét mutat.
Fizetős fiókok esetén a `Url` mező jelenleg a nyilvános webhely helyett egy belső URL-címre mutat.
Az elkövetkező hetekben módosítani fogjuk, és a [video Indexer webhely](https://www.videoindexer.ai/) URL-címét adja vissza az összes fiókhoz (próbaverzió és fizetős).

    Ne használja a belső URL-címeket, a [video Indexer nyilvános API-kat](https://api-portal.videoindexer.ai/)kell használnia.
* Ha Video Indexer URL-címeket ágyaz be az alkalmazásaiba, és az URL-címek nem a [video Indexer webhelyére](https://www.videoindexer.ai/) vagy a video Indexer API-végpontra () mutatnak `https://api.videoindexer.ai` , hanem egy regionális végpontra (például: `https://wus2.videoindexer.ai` ), akkor az URL-eket újra létrehozza.

   Ezt a következők egyikével teheti meg:

    * Az URL-cím cseréje a Video Indexer widget API-ra mutató URL-címmel (például az adatellenőrzési [widgettel](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget))
    * A Video Indexer webhely használata új beágyazott URL-cím létrehozásához:
         
         Nyomja meg a **Lejátszás** gombot a videó oldalának megtekintéséhez – > kattintson a ** &lt; / &gt; beágyazás** gombra – > másolja az URL-címet az alkalmazásba:
   
    A regionális URL-címek nem támogatottak, és az elkövetkező hetekben le lesznek tiltva.

## <a name="january-2020"></a>2020. január
 
### <a name="custom-language-support-for-additional-languages"></a>Egyéni nyelvi támogatás további nyelvekhez

A Video Indexer mostantól támogatja az egyéni nyelvi modelleket a `ar-SY` , a `en-UK` és a `en-AU` (csak API-hoz).
 
### <a name="delete-account-timeframe-action-update"></a>Fiók időkeretének törlése művelet frissítése

A fiók törlése művelettel mostantól 90 napon belül törli a fiókot, 48 óra helyett.
 
### <a name="new-video-indexer-github-repository"></a>Új Video Indexer GitHub-adattár

Mostantól elérhető egy új Video Indexer GitHub különböző projektekkel, az első lépéseket ismertető útmutatók és a kód példákkal:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Hencegő frissítés

Az egységes **hitelesítések** és **műveletek** egyetlen [video Indexer OpenAPI-specifikációba (hencegő)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)video Indexer. A fejlesztők megtalálhatják az API-kat [video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>2019. december

### <a name="update-transcript-with-the-new-api"></a>Átirat frissítése az új API-val

Frissítsen egy adott szakaszt az átiratban az [Update-video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API használatával.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>A fiók konfigurációjának javítása a Video Indexer portálon

Most már frissítheti Media Services a kapcsolódási konfigurációt, hogy önsegítse az alábbi problémákkal: 

* helytelen Azure Media Services erőforrás
* jelszó módosítása
* Media Services erőforrások áthelyezve az előfizetések között  

A fiók konfigurációjának kijavításához a Video Indexer-portálon navigáljon a beállítások > fiók lapra (tulajdonosként).

### <a name="configure-the-custom-vision-account"></a>Egyéni látási fiók konfigurálása

A Video Indexer portálon konfigurálhatja a fizetős fiókokra vonatkozó egyéni jövőképi fiókot (korábban ez az API által támogatott). Ehhez jelentkezzen be a Video Indexer portálra, válassza a modell testreszabása > animált karakterek > Konfigurálás lehetőséget. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Jelenetek, felvételek és kulcsképek – most egy betekintési panelen

A jelenetek, a felvételek és a kulcsképek mostantól egyetlen pillantással egyesülnek, így könnyebb a használat és a Navigálás. A kívánt jelenet kiválasztásával megtekintheti, hogy mely felvételek és kulcsképek alkotják. 

### <a name="notification-about-a-long-video-name"></a>Értesítés hosszú videó nevéről

Ha a videó neve 80 karakternél hosszabb, Video Indexer a feltöltésnél leíró hibát jelez.

### <a name="streaming-endpoint-is-disabled-notification"></a>A folyamatos átviteli végpont értesítése letiltva

Ha a folyamatos átviteli végpont le van tiltva, Video Indexer egy leíró hibát jelez a Player oldalon.

### <a name="error-handling-improvement"></a>Hibakezelés javítása

A 409-as állapotkódot a rendszer most [Visszaindexeli a videóból](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) , és frissíti a video [index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) API-kat arra az esetre, ha egy videó aktívan van indexelve, hogy megakadályozza a jelenlegi újraindexelési változások véletlenre váltását.

## <a name="november-2019"></a>2019. november
 
* Egyéni koreai nyelvi modellek támogatása

    A video Indexer mostantól támogatja az egyéni nyelvi modelleket a Koreai ( `ko-KR` ) verzióban az API-ban és a portálon is. 
* Beszéd – szöveg (STT) által támogatott új nyelvek

    Video Indexer API-k mostantól támogatják a STT-t az Arab levantei (AR-SY), az angol Egyesült királyságbeli dialektusban (en-GB) és az angol ausztráliai nyelvjárásban (en-AU).
    
    A videó feltöltésekor a zh-HANS-t zh-CN-re cseréljük, de a zh-CN ajánlott és pontosabb.
    
## <a name="october-2019"></a>2019. október
 
* Animált karakterek keresése a gyűjteményben

    Az animált karakterek indexelése mostantól megkeresheti őket a fiók videós hasábjában. További információ: [animált karakterek felismerése](animated-characters-recognition.md).

## <a name="september-2019"></a>2019. szeptember
 
Több előrelépés is jelent meg az IBC 2019-ben:
 
* Animált karakterek felismerése (nyilvános előzetes verzió)

    Lehetővé teszi a csoportos hirdetések felismerési karaktereinek észlelését az animált tartalomban az egyéni jövőképtel való integráción keresztül. További információ: [animált karakterek észlelése](animated-characters-recognition.md).
* Többnyelvű azonosítás (nyilvános előzetes verzió)

    A hangsávokban több nyelven is felderítheti a szegmenseket, és a rajtuk alapuló többnyelvű átiratot hozhat létre. Kezdeti támogatás: angol, spanyol, német és francia. További információt a [többnyelvű tartalom automatikus azonosítása és](multi-language-identification-transcription.md)átírása című témakörben talál.
* Elnevezett entitások kinyerése személyekhez és helyekhez

    Természetes nyelvi feldolgozás (NLP) használatával kinyerheti a márkákat, a helyszíneket és a beszéd-és vizualizációs szövegből származó személyeket.
* Szerkesztési shot típusú besorolás

    A felvételek címkézése olyan szerkesztési típusokkal, mint például a közelkép, közepes méretű felvétel, két lövés, beltéri, kültéri stb. További információ: [szerkesztési shot típusú észlelés](scenes-shots-keyframes.md#editorial-shot-type-detection).
* A fejlesztéssel foglalkozó témakör (2. szint)
    
    A témakörre hivatkozó modell mostantól támogatja az IPTC-rendszertan mélyebb részletességét. Olvassa el részletesen [Azure Media Services új AI-alapú innovációt](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>2019. augusztus
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer üzembe helyezése Egyesült Királyság déli régiója

Most már létrehozhat egy Video Indexer fizetős fiókot az Egyesült Királyság déli régiójában.

### <a name="new-editorial-shot-type-insights-available"></a>Új szerkesztői shot típusú bepillantások érhetők el

A videó felvételekhez hozzáadott új címkék szerkesztői "shot Types" lehetőséget biztosítanak a tartalom-létrehozási munkafolyamatban használt általános szerkesztési kifejezésekkel való azonosításhoz, például: Extreme Vértes, Vértes, széles, közepes, két lövés, kültéri, beltéri, bal oldali és jobb oldali (a JSON-ban elérhető).

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

## <a name="july-2019"></a>2019. július

### <a name="editor-as-a-widget"></a>Szerkesztő widgetként

A Video Indexer AI-Editor mostantól elérhető widgetként, amely beágyazható az ügyfelek alkalmazásaiba.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Egyéni nyelvi modell frissítése a lezárt képaláírás-fájlból a portálról

Az ügyfelek a portál Testreszabás lapján VTT, SRT és TTML fájlformátumokat is biztosíthatnak a nyelvi modellekhez.

## <a name="june-2019"></a>2019. június

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
