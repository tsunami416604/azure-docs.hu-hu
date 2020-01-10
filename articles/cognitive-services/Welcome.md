---
title: Mi az Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services olyan API-k, SDK-k és szolgáltatások, amelyek az Microsoft Azure használatával intelligens alkalmazások készítésére használhatók.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 12/19/2019
ms.author: nitinme
ms.openlocfilehash: 544ca198b846ca5032bdf400b300ac076d729d88
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834148"
---
# <a name="what-are-azure-cognitive-services"></a>Mi az Azure Cognitive Services?

Az Azure Cognitive Services olyan API-k, SDK-k és szolgáltatások, amelyek segítségével a fejlesztők intelligens alkalmazásokat hozhatnak létre anélkül, hogy közvetlen AI-vagy adatelemzési ismeretekkel vagy ismeretekkel rendelkeznek Az Azure Cognitive Services lehetővé teszi a fejlesztők számára, hogy az alkalmazásaikban egyszerűen vegyenek fel kognitív funkciókat. Az Azure Cognitive Services célja, hogy segítse a fejlesztőket az olyan alkalmazások létrehozásában, amelyek megtekinthetik, meghallgatják, megértik, megértették és megkezdik az okot. Az Azure Cognitive Servicesban található szolgáltatások katalógusa öt fő pillérbe osztható – jövőkép, beszéd, nyelv, Web Search és döntés.

## <a name="vision-apis"></a>Vision APIs

|Szolgáltatás neve|A szolgáltatás leírása|
|:-----------|:------------------|
|[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Computer Vision")|A Computer Vision szolgáltatás a rendszerképek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális algoritmusokhoz.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|A Custom Vision Service segítségével egyéni rendszerkép-osztályozók hozhatók létre.|
|[Face API](https://docs.microsoft.com/azure/cognitive-services/face/ "Face API")|Face API hozzáférést biztosít a speciális arc-algoritmusokhoz, lehetővé téve a Face attribútumok észlelését és felismerését.|
|[Űrlap-felismerő](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer") (előzetes verzió)|Az űrlap-felismerő azonosítja és kigyűjti a kulcs-érték párokat és a táblák adatait az űrlap dokumentumaiból. Ezután a a strukturált adatokat is megjeleníti, beleértve az eredeti fájlban lévő kapcsolatokat is.|
|[Kézírás-felismerő](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer") (előzetes verzió)|A kézírás-felismerő lehetővé teszi a digitális tollvonási adat, az alakzatok és a kézírásos tartalom felismerését és elemzését, valamint a dokumentum-struktúra kimenetét az összes felismert entitással.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|A Video Indexer lehetővé teszi a videóból származó adatok kinyerését.|

## <a name="speech-apis"></a>Beszédfelismerő API-k

|Szolgáltatás neve|A szolgáltatás leírása|
|:-----------|:------------------|
|[Beszédfelismerési szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech szolgáltatás")|A beszédfelismerési szolgáltatás lehetővé teszi a beszédfelismerést támogató funkciók használatát az alkalmazásokhoz.|
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Hangfelismerő API") (előzetes verzió)|A Speaker Recognition API algoritmusokat biztosít a hangszórók azonosításához és ellenőrzéséhez.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech") (kivonás)|A Bing Speech API segítségével egyszerűen hozhat létre beszédfelismerést támogató szolgáltatásokat az alkalmazásaiban.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Fordítói beszéd") (kivonás)|Translator Speech egy gépi fordítási szolgáltatás.|

> [!NOTE]
> Az [Azure Cognitive Searcht](https://docs.microsoft.com/azure/search/)keresi? Bár egyes feladatokhoz Cognitive Services használ, ez egy másik keresési technológia, amely más forgatókönyveket is támogat.


## <a name="language-apis"></a>Nyelvi API-k

|Szolgáltatás neve|A szolgáltatás leírása|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Hangfelismerés")|A Language Understanding Service (LUIS) lehetővé teszi az alkalmazás számára, hogy megtudja, mit szeretne a saját szavaiban.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker lehetővé teszi, hogy a félig strukturált tartalomból hozzon létre egy kérdés-válasz szolgáltatást.|
|[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Text Analytics")|A Text Analytics természetes nyelvi feldolgozást tesz lehetővé a nyers szövegekben az érzelmek elemzése, a fő kifejezés kinyerése és a nyelvfelismerés terén.|
|[Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/ "Translator Text")|A fordítói szöveg a gépi alapú szöveges fordítást biztosítja közel valós időben.|


## <a name="search-apis"></a>Keresési API-k

|Szolgáltatás neve|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing News Search](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing News Search")|Bing News Search a felhasználó lekérdezéséhez kapcsolódó Hírek listáját adja vissza.|
|[Bing Video Search](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing Video Search")|Bing Video Search a felhasználó lekérdezéséhez tartozó videók listáját adja vissza.|
|[Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing Web Search")|Bing Web Search a felhasználó lekérdezéséhez szükséges keresési eredmények listáját adja vissza.|
|[Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest lehetővé teszi részleges keresési lekérdezési kifejezés küldését a Bing számára, és a javasolt lekérdezések listájának visszaadása.|
|[Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing egyéni keresés")|Bing Custom Search lehetővé teszi, hogy testreszabott keresési funkciókat hozzon létre az Ön számára fontos témakörökhöz.|
|[Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing Entity Search")|Bing Entity Search a Bing által megadott entitásokra vonatkozó információkat ad vissza a felhasználó lekérdezéséhez.|
|[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing Image Search")|Bing Image Search visszaadja a felhasználó lekérdezéséhez szükséges képek megjelenítését.|
|[Bing Visual Search](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing Visual Search")|A Bing Visual Search a képekkel kapcsolatos információkat ad vissza, például vizuálisan hasonló képeket, a lemezképben található termékek vásárlási forrásait és a kapcsolódó kereséseket.|
|[Bing – helyi üzleti keresés](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| A Bing local Business Search API lehetővé teszi az alkalmazások számára, hogy a keresési lekérdezések alapján megtalálják a helyi vállalkozások kapcsolattartási és tartózkodási információit.|
|[Bing helyesírás-ellenőrzés](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing Spell Check")|Bing Spell Check lehetővé teszi a kontextusos nyelvtan és a helyesírás-ellenőrzés végrehajtását.|

## <a name="decision-apis"></a>Döntési API-k

|Szolgáltatás neve|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomália detektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomáliadetektor") (előzetes verzió)|Az anomáliák detektorral figyelheti és azonosíthatja a rendellenességeket az idősorozat-adataiban.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator a lehetséges sértő, nemkívánatos és kockázatos tartalmak figyelését teszi lehetővé.|
|[Személyre szabás](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")|A személyre szabás lehetővé teszi, hogy kiválassza a legjobb élményt, amelyet a felhasználók láthatnak, valós idejű viselkedésük alapján.|

## <a name="use-free-trials"></a>Ingyenes próbaverziók használata

[Az ingyenes próbaverzióra való regisztráció](https://azure.microsoft.com/try/cognitive-services/ "Feliratkozási Súgó") csak egy e-mailt és néhány egyszerű lépést vesz igénybe. Ha még nem rendelkezik ilyennel, Microsoft-fiókra van szüksége. Minden kért API-hoz egyedi kulcspárt fog kapni. A második egy csak tartalék. A titkos kulcsokat Ne ossza meg senkivel. A kísérletek esetében a díjszabás, a tranzakciók másodpercenkénti száma és a havi használati korlát is érvényes. Egy tranzakció egyszerűen egy API-hívás. A korlátozások feloldásához frissíthet a fizetős csomagokra.

## <a name="subscription-management"></a>Előfizetés-kezelés

Miután bejelentkezett a Microsoft-fiókjával, elérheti az [előfizetéseit](https://www.microsoft.com/cognitive-services/subscriptions "Saját előfizetések") , hogy megjelenjenek a használt termékek, a fennmaradó kvóta, valamint az előfizetéshez további termékek is hozzáadhatók.

## <a name="upgrade-to-unlock-limits"></a>Frissítés feloldási korlátokra

Minden API-nak van egy ingyenes próbaverziója, amely a használat és az átviteli sebesség korlátozásával rendelkezik.  A korlátokat fizetős ajánlat használatával növelheti, és kiválaszthatja a megfelelő díjszabási csomag lehetőséget a szolgáltatás Azure Portal történő telepítésekor. [További információ az ajánlatokról és a díjszabásról](https://azure.microsoft.com/pricing/details/cognitive-services/ "ajánlatok és díjszabás"). Be kell állítania egy Azure-előfizetői fiókot egy bankkártyával és egy telefonszámmal. Ha különleges követelménye van, vagy egyszerűen csak szeretne kommunikálni a Sales szolgáltatással, kattintson a "Kapcsolatfelvétel" gombra a díjszabás oldal tetején.

## <a name="regional-availability"></a>Regionális elérhetőség

A Cognitive Services API-jai a Microsoft által felügyelt adatközpontok egyre növekvő hálózatán futnak. Az egyes API-k regionális elérhetőségét az [Azure-régiók listájában](https://azure.microsoft.com/regions)találja.

Olyan régiót keresünk, amely még nem támogatott? Tudassa velünk, ha beküld egy szolgáltatási kérelmet a [UserVoice-fórumba](https://cognitive.uservoice.com/).

## <a name="supported-cultural-languages"></a>Támogatott kulturális nyelvek

 Cognitive Services számos kulturális nyelvet támogat a szolgáltatási szinten. Az egyes API-k nyelvi elérhetőségét a [támogatott nyelvek listájában](language-support.md)találja.

## <a name="securing-resources"></a>Erőforrások biztonságossá tétele

Az Azure Cognitive Services többrétegű biztonsági modellt biztosít, beleértve a [hitelesítést](authentication.md) Azure Active Directory hitelesítő adatokon, egy érvényes erőforrás-kulcson és az [Azure virtuális hálózatokon](cognitive-services-virtual-networks.md)keresztül.

## <a name="container-support"></a>Tárolótámogatás

 A Cognitive Services tárolókat biztosít az Azure-felhőben vagy a helyszínen történő üzembe helyezéshez. További információ a [Cognitive Services tárolóról](cognitive-services-container-support.md).

## <a name="certifications-and-compliance"></a>Minősítések és megfelelőség

Cognitive Services olyan minősítéseket kapott, mint például a CSA STAR minősítés, a FedRAMP mérsékelt és a HIPAA BAA.

A saját auditokra és biztonsági felülvizsgálatokra vonatkozó tanúsítványokat is [letöltheti](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) .

Az adatvédelem és az adatkezelés megismeréséhez nyissa meg a [megbízhatósági központot](https://servicetrust.microsoft.com/).

## <a name="support"></a>Támogatás

Cognitive Services több [támogatási lehetőség](cognitive-services-support-options.md)is rendelkezésre áll.

## <a name="next-steps"></a>Következő lépések

* [Cognitive Services fiók létrehozása](cognitive-services-apis-create-account.md)
