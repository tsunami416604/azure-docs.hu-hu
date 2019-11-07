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
ms.date: 11/04/2019
ms.author: nitinme
ms.openlocfilehash: 081951e2e77c7fdd077dc92fe0368423db85f641
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606960"
---
# <a name="what-are-azure-cognitive-services"></a>Mi az Azure Cognitive Services?

Az Azure Cognitive Services olyan API-k, SDK-k és szolgáltatások, amelyek segítségével a fejlesztők intelligens alkalmazásokat hozhatnak létre anélkül, hogy közvetlen AI-vagy adatelemzési ismeretekkel vagy ismeretekkel rendelkeznek Az Azure Cognitive Services lehetővé teszi a fejlesztők számára, hogy az alkalmazásaikban egyszerűen vegyenek fel kognitív funkciókat. Az Azure Cognitive Services célja, hogy segítse a fejlesztőket az olyan alkalmazások létrehozásában, amelyek megtekinthetik, meghallgatják, megértik, megértették és megkezdik az okot. Az Azure Cognitive Servicesban található szolgáltatások katalógusa öt fő pillérbe osztható – jövőkép, beszéd, nyelv, Web Search és döntés.

## <a name="vision-apis"></a>Vision APIs

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Számítógépes látástechnológia")|A Computer Vision szolgáltatás a rendszerképek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális algoritmusokhoz.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Egyéni vizuális szolgáltatás")|A Custom Vision Service segítségével egyéni rendszerkép-osztályozók hozhatók létre.|
|[Face API](https://docs.microsoft.com/azure/cognitive-services/face/ "Face API")|Face API hozzáférést biztosít a speciális arc-algoritmusokhoz, lehetővé téve a Face attribútumok észlelését és felismerését.|
|[Űrlap-felismerő](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer") (előzetes verzió)|Az űrlap-felismerő azonosítja és kigyűjti a kulcs-érték párokat és a táblák adatait az űrlap dokumentumaiból. Ezután a a strukturált adatokat is megjeleníti, beleértve az eredeti fájlban lévő kapcsolatokat is.|
|[Kézírás-felismerő](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer") (előzetes verzió)|A kézírás-felismerő lehetővé teszi a digitális tollvonási adat, az alakzatok és a kézírásos tartalom felismerését és elemzését, valamint a dokumentum-struktúra kimenetét az összes felismert entitással.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|A Video Indexer lehetővé teszi a videóból származó adatok kinyerését.|

## <a name="speech-apis"></a>Beszédfelismerő API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Beszédszolgáltatások")|A beszédfelismerési szolgáltatás lehetővé teszi a beszédfelismerést támogató funkciók használatát az alkalmazásokhoz.|
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Beszélőfelismerés API") (előzetes verzió)|A Speaker Recognition API algoritmusokat biztosít a hangszórók azonosításához és ellenőrzéséhez.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing – Beszédfelismerés") (kivonás)|A Bing Speech API segítségével egyszerűen hozhat létre beszédfelismerést támogató szolgáltatásokat az alkalmazásaiban.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Fordítói beszéd") (kivonás)|Translator Speech egy gépi fordítási szolgáltatás.|

## <a name="language-apis"></a>Nyelvi API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Hangfelismerés")|A Language Understanding Service (LUIS) lehetővé teszi az alkalmazás számára, hogy megtudja, mit szeretne a saját szavaiban.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker lehetővé teszi, hogy a félig strukturált tartalomból hozzon létre egy kérdés-válasz szolgáltatást.|
|[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Szövegelemzés")|A Text Analytics természetes nyelvi feldolgozást tesz lehetővé a nyers szövegekben az érzelmek elemzése, a fő kifejezés kinyerése és a nyelvfelismerés terén.|
|[Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/ "Fordítói szöveg")|A fordítói szöveg a gépi alapú szöveges fordítást biztosítja közel valós időben.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|A Video Indexer lehetővé teszi a videóból származó adatok kinyerését.|

## <a name="search-apis"></a>Keresési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing News Search](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing – Hírkeresés")|Bing News Search a felhasználó lekérdezéséhez kapcsolódó Hírek listáját adja vissza.|
|[Bing Video Search](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing – Videokeresés")|Bing Video Search a felhasználó lekérdezéséhez tartozó videók listáját adja vissza.|
|[Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing – Internetes keresés")|Bing Web Search a felhasználó lekérdezéséhez szükséges keresési eredmények listáját adja vissza.|
|[Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing – Automatikus kiegészítés")|Bing Autosuggest lehetővé teszi részleges keresési lekérdezési kifejezés küldését a Bing számára, és a javasolt lekérdezések listájának visszaadása.|
|[Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing – Egyéni keresés")|Bing Custom Search lehetővé teszi, hogy testreszabott keresési funkciókat hozzon létre az Ön számára fontos témakörökhöz.|
|[Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing – Entitáskeresés")|Bing Entity Search a Bing által megadott entitásokra vonatkozó információkat ad vissza a felhasználó lekérdezéséhez.|
|[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing – Képkeresés")|Bing Image Search visszaadja a felhasználó lekérdezéséhez szükséges képek megjelenítését.|
|[Bing Visual Search](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing vizuális keresés")|A Bing Visual Search a képekkel kapcsolatos információkat ad vissza, például vizuálisan hasonló képeket, a lemezképben található termékek vásárlási forrásait és a kapcsolódó kereséseket.|
|[Bing – helyi üzleti keresés](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| A Bing local Business Search API lehetővé teszi az alkalmazások számára, hogy a keresési lekérdezések alapján megtalálják a helyi vállalkozások kapcsolattartási és tartózkodási információit.|
|[Bing helyesírás-ellenőrzés](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing – Helyesírás-ellenőrzés")|Bing Spell Check lehetővé teszi a kontextusos nyelvtan és a helyesírás-ellenőrzés végrehajtását.|

## <a name="decision-apis"></a>Döntési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomália detektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomaly Detector") (előzetes verzió)|Az anomáliák detektorral figyelheti és azonosíthatja a rendellenességeket az idősorozat-adataiban.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Tartalommoderátor")|Content Moderator a lehetséges sértő, nemkívánatos és kockázatos tartalmak figyelését teszi lehetővé.|
|[Személyre szabás](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")|A személyre szabás lehetővé teszi, hogy kiválassza a legjobb élményt, amelyet a felhasználók láthatnak, valós idejű viselkedésük alapján.|

## <a name="use-free-trials"></a>Ingyenes próbaverziók használata

[Az ingyenes próbaverzióra való regisztráció](https://azure.microsoft.com/try/cognitive-services/ "Feliratkozási Súgó") csak egy e-mailt és néhány egyszerű lépést vesz igénybe. Ha még nem rendelkezik ilyennel, Microsoft-fiókra van szüksége. Minden kért API-hoz egyedi kulcspárt fog kapni. A második egy csak tartalék. A titkos kulcsokat Ne ossza meg senkivel. A kísérletek esetében a díjszabás, a tranzakciók másodpercenkénti száma és a havi használati korlát is érvényes. Egy tranzakció egyszerűen egy API-hívás. A korlátozások feloldásához frissíthet a fizetős csomagokra.

## <a name="subscription-management"></a>Előfizetés-kezelés

Miután bejelentkezett a Microsoft-fiókjával, elérheti az [előfizetéseit](https://www.microsoft.com/cognitive-services/subscriptions "Saját előfizetések") , hogy megjelenjenek a használt termékek, a fennmaradó kvóta, valamint az előfizetéshez további termékek is hozzáadhatók.

## <a name="upgrade-to-unlock-limits"></a>Frissítés feloldási korlátokra

Minden API-nak van egy ingyenes próbaverziója, amely a használat és az átviteli sebesség korlátozásával rendelkezik.  A korlátokat fizetős ajánlat használatával növelheti, és kiválaszthatja a megfelelő díjszabási csomag lehetőséget a szolgáltatás Azure Portal történő telepítésekor. [További információ az ajánlatokról és a díjszabásról](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings és díjszabás "). Be kell állítania egy Azure-előfizetői fiókot egy bankkártyával és egy telefonszámmal. Ha különleges követelménye van, vagy egyszerűen csak szeretne kommunikálni a Sales szolgáltatással, kattintson a "Kapcsolatfelvétel" gombra a díjszabás oldal tetején.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A Cognitive Services API-jai a Microsoft által felügyelt adatközpontok egyre növekvő hálózatán futnak. Az egyes API-k regionális elérhetőségét az [Azure-régiók listájában](https://azure.microsoft.com/regions)találja.

Olyan régiót keresünk, amely még nem támogatott? Tudassa velünk, ha beküld egy szolgáltatási kérelmet a [UserVoice-fórumba](https://cognitive.uservoice.com/).

## <a name="supported-cultural-languages"></a>Támogatott kulturális nyelvek

 Cognitive Services számos kulturális nyelvet támogat a szolgáltatási szinten. Az egyes API-k nyelvi elérhetőségét a [támogatott nyelvek listájában](language-support.md)találja.

## <a name="container-support"></a>Tárolótámogatás

 A Cognitive Services tárolókat biztosít az Azure-felhőben vagy a helyszínen történő üzembe helyezéshez. További információ a [Cognitive Services tárolóról](cognitive-services-container-support.md).

## <a name="certifications-and-compliance"></a>Minősítések és megfelelőség

Cognitive Services olyan minősítéseket kapott, mint például a CSA STAR minősítés, a FedRAMP mérsékelt és a HIPAA BAA.

A saját auditokra és biztonsági felülvizsgálatokra vonatkozó tanúsítványokat is [letöltheti](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) .

Az adatvédelem és az adatkezelés megismeréséhez nyissa meg a [megbízhatósági központot](https://servicetrust.microsoft.com/).

## <a name="support"></a>Támogatás

* Támogatási és technikai kérdésekben a post on [stack overflow](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
* A visszajelzések és a szolgáltatások iránti kérelmek esetében lépjen a következőre: [UserVoice](https://cognitive.uservoice.com/)

## <a name="next-steps"></a>További lépések

* [Cognitive Services fiók létrehozása](cognitive-services-apis-create-account.md)
