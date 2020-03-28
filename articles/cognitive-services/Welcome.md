---
title: Mik azok az Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services olyan API-k, SDK-k és szolgáltatások, amelyek segítségével a Microsoft Azure intelligens alkalmazásokat hozhat létre.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 12/19/2019
ms.author: nitinme
ms.openlocfilehash: 332f33bb4046a9ca9d6abf9bec75f60bb4ca9e32
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169094"
---
# <a name="what-are-azure-cognitive-services"></a>Mik azok az Azure Cognitive Services?

Az Azure Cognitive Services olyan API-k, SDK-k és szolgáltatások, amelyek segítségével a fejlesztők közvetlen AI- vagy adatelemzési ismeretekkel vagy ismeretekkel nem rendelkeznek intelligens alkalmazások at. Az Azure Cognitive Services lehetővé teszi a fejlesztők számára, hogy egyszerűen adjanak kognitív funkciókat alkalmazásaikhoz. Az Azure Cognitive Services célja, hogy segítse a fejlesztőket olyan alkalmazások létrehozásában, amelyek látják, hallják, beszélik, megértik, és akár érvelni is kezdenek. Az Azure Cognitive Services szolgáltatáskatalógusa öt fő pillérbe sorolható : Vision, Speech, Language, Web Search és Decision.

## <a name="vision-apis"></a>Vision API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Számítógépes látás](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Számítógépes látástechnológia")|A Computer Vision szolgáltatás hozzáférést biztosít a képek feldolgozásához és az információk visszaküldéséhez szükséges speciális algoritmusokhoz.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Egyéni vizuális szolgáltatás")|A Custom Vision Szolgáltatás lehetővé teszi, hogy egyéni képosztályozók.|
|[Arcfelismerés](https://docs.microsoft.com/azure/cognitive-services/face/ "Arcfelismerés")| A Face szolgáltatás hozzáférést biztosít a speciális arcalgoritmusokhoz, lehetővé téve az arcattribútumok észlelését és felismerését.|
|[Űrlapfelismerő](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer") (előzetes verzió)|Az Űrlapfelismerő azonosítja és kinyeri a kulcsérték-párokat és a táblaadatokat űrlapdokumentumokból; ezután strukturált adatokat ad ki, beleértve az eredeti fájlban lévő kapcsolatokat is.|
|[Nagyító (előzetes](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer") verzió)|A Tintafelismerő lehetővé teszi a digitális tintavonás-adatok, alakzatok és kézzel írt tartalom felismerését és elemzését, valamint a dokumentumszerkezet kimenetét az összes felismert entitással.|
|[Videóindexelő](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Videóindexelő")|A Video Indexer lehetővé teszi, hogy elemzéseket nyerj ki a videódból.|

## <a name="speech-apis"></a>Beszéd API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Speech szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech szolgáltatás")|A beszédszolgáltatás beszédfelismerési funkciókat ad az alkalmazásokhoz.|
|[Hangszórófelismerési API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Beszélőfelismerés API") (előzetes verzió)|A hangszóró-felismerési API algoritmusokat biztosít a hangszóró azonosításához és ellenőrzéséhez.|
|[Bing beszéd](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing – Beszédfelismerés") (nyugdíjba vonuló)|A Bing Speech API segítségével egyszerűen hozhat létre beszédfelismerést támogató funkciókat az alkalmazásokban.|
|[Fordító beszéd](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Fordítói beszéd") (nyugdíjba vonuló)|A Translator Speech egy gépi fordítási szolgáltatás.|

> [!NOTE]
> Az [Azure Cognitive Search-et](https://docs.microsoft.com/azure/search/)keresi? Bár bizonyos feladatokhoz a Cognitive Services szolgáltatást használja, ez egy másik keresési technológia, amely más forgatókönyveket is támogat.


## <a name="language-apis"></a>Nyelvi API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Nyelv ismertetése LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|A Language Understanding szolgáltatás (LUIS) lehetővé teszi, hogy az alkalmazás saját szavaival megértse, mit szeretne egy személy.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|A QnA Maker lehetővé teszi, hogy kérdés-válasz szolgáltatást hozzon létre a félig strukturált tartalomból.|
|[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Szövegelemzés")|A Text Analytics természetes nyelvi feldolgozást biztosít a nyers szövegen keresztül a hangulatelemzéshez, a kulcskifejezések kinyeréséhez és a nyelvfelismeréshez.|
|[Fordító szövege](https://docs.microsoft.com/azure/cognitive-services/translator/ "Fordítói szöveg")|A fordítószöveg közel valós idejű gépi szövegfordítást biztosít.|


## <a name="search-apis"></a>API-k keresése

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing Hírek Keresés](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing – Hírkeresés")|A Bing News Search a felhasználó lekérdezése szempontjából relevánsnak minősülő hírcikkek listáját adja vissza.|
|[Bing videokeresés](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing – Videokeresés")|A Bing Video Search a felhasználó lekérdezése szempontjából relevánsnak minősülő videók listáját adja vissza.|
|[Bing – Internetes keresés](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing – Internetes keresés")|A Bing Web Search a felhasználó lekérdezése szempontjából relevánsnak megállapítandó keresési eredmények listáját adja vissza.|
|[Bing – Automatikus kiegészítés](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing – Automatikus kiegészítés")|A Bing Autosuggest lehetővé teszi, hogy részleges keresési lekérdezési kifejezést küldjön a Bingnek, és visszakapja a javasolt lekérdezések listáját.|
|[Bing – Egyéni keresés](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing – Egyéni keresés")|A Bing egyéni keresés lehetővé teszi, hogy személyre szabott keresési élményeket hozzon létre az Ön számára fontos témakörökhöz.|
|[Bing entitás keresése](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing – Entitáskeresés")|A Bing entitáskeresés olyan entitásokra vonatkozó információkat ad vissza, amelyekről a Bing megállapítja, hogy relevánsak a felhasználó lekérdezése szempontjából.|
|[Bing képkeresés](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing – Képkeresés")|A Bing Képkereső a felhasználó lekérdezése szempontjából relevánsnak minősülő képek megjelenítését adja vissza.|
|[Bing vizuális keresés](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing vizuális keresés")|A Bing Visual Search elemzési adatokat ad egy képről, például vizuálisan hasonló képekről, a képen található termékek vásárlási forrásairól és a kapcsolódó keresésekről.|
|[Bing Local Business Search](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| A Bing Local Business Search API lehetővé teszi, hogy az alkalmazások keresési lekérdezések alapján megtalálják a helyi vállalkozásokkal kapcsolatos kapcsolattartási és helyadatokat.|
|[Bing helyesírás-ellenőrzés](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing – Helyesírás-ellenőrzés")|A Bing helyesírás-ellenőrzése lehetővé teszi a környezetfüggő nyelvhelyesség és helyesírás-ellenőrzés elvégzését.|

## <a name="decision-apis"></a>Határozati API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomáliadetektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomaly Detector") (előzetes verzió)|Az anomáliadetektor lehetővé teszi az idősorozat-adatok rendellenességeinek figyelését és észlelését.|
|[Tartalommoderátor](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Tartalommoderátor")|A tartalommoderátor figyeli az esetleges sértő, nemkívánatos és kockázatos tartalmakat.|
|[Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")|Personalizer lehetővé teszi, hogy válassza ki a legjobb élményt mutatni a felhasználók számára, tanulva a valós idejű viselkedés.|

## <a name="use-free-trials"></a>Ingyenes próbaverziók használata

[Az ingyenes próbaverziókra való feliratkozás](https://azure.microsoft.com/try/cognitive-services/ "Jelentkezési súgó") csak egy e-mailt és néhány egyszerű lépést igényel. Ha még nem rendelkezik Microsoft-fiókkal, akkor microsoftos fiókra van szüksége. Minden egyes kért API-hoz egyedi kulcspárt kap. A második csak egy tartalék. Ne oszd meg senkivel a titkos kulcsokat. A próbaverziók díjkorláttal, másodpercenkénti vagy perces tranzakciókkal és havi használati korláttal rendelkeznek. A tranzakció egyszerűen egy API-hívás. A korlátozások feloldásához frissíthet fizetős szintekre.

## <a name="subscription-management"></a>Előfizetés-kezelés

Miután bejelentkezett a Microsoft-fiókjával, a [Saját előfizetések](https://www.microsoft.com/cognitive-services/subscriptions "Saját előfizetések") eléréséhez megjelenítheti a használt termékeket, a fennmaradó kvótát és az előfizetéshez további termékek hozzáadásának lehetőségét.

## <a name="upgrade-to-unlock-limits"></a>Frissítés a korlátok feloldásához

Minden API-k egy ingyenes próbacsomag, amely a használati és átviteli korlátok.  Ezeket a korlátokat növelheti egy fizetős ajánlat használatával, és válassza ki a megfelelő tarifacsomag-beállítást, amikor a szolgáltatást az Azure Portalon telepíti. [További információ az ajánlatokról és az árakról.](https://azure.microsoft.com/pricing/details/cognitive-services/ "ajánlatok és árak") Be kell állítania egy Azure-előfizetői fiókot hitelkártyával és telefonszámmal. Ha különleges követelménye van, vagy egyszerűen csak beszélni szeretne az értékesítéssel, kattintson a "Kapcsolatfelvétel" gombra az árképzési oldal tetején.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A Cognitive Services API-k a Microsoft által felügyelt adatközpontok egyre növekvő hálózatán találhatók. Az [Azure-régiólistában](https://azure.microsoft.com/regions)az egyes API-k regionális elérhetőségét is megtalálhatja.

Olyan régiót keres, amelyet még nem támogatunk? Tudassa velünk a bejelentés a funkció kérésére a mi [UserVoice fórum](https://cognitive.uservoice.com/).

## <a name="supported-cultural-languages"></a>Támogatott kulturális nyelvek

 A Cognitive Services a kulturális nyelvek széles skáláját támogatja szolgáltatási szinten. Az egyes API-k nyelvi elérhetőségét a [támogatott nyelvek listájában](language-support.md)találja.

## <a name="securing-resources"></a>Erőforrások védelme

Az Azure Cognitive Services egy réteges biztonsági modellt biztosít, beleértve az Azure Active Directory hitelesítő adatokon keresztüli [hitelesítést,](authentication.md) egy érvényes erőforráskulcsot és az [Azure virtuális hálózatokat.](cognitive-services-virtual-networks.md)

## <a name="container-support"></a>Tárolótámogatás

 A Cognitive Services tárolókat biztosít az Azure-felhőben vagy a helyszíni telepítéshez. További információ a [Cognitive Services-tárolókról.](cognitive-services-container-support.md)

## <a name="certifications-and-compliance"></a>Tanúsítványok és megfelelőség

A Cognitive Services olyan tanúsítványokat kapott, mint a CSA STAR minősítés, a FedRAMP Moderate és a HIPAA BAA.

[Letöltheti](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) a tanúsítványokat a saját auditok és biztonsági felülvizsgálatok.

Az adatvédelem és az adatkezelés megértéséhez látogasson el az [Adatvédelmi központba.](https://servicetrust.microsoft.com/)

## <a name="support"></a>Támogatás

A Cognitive Services számos [támogatási lehetőséget](cognitive-services-support-options.md)kínál.

## <a name="next-steps"></a>További lépések

* [Cognitive Services-fiók létrehozása](cognitive-services-apis-create-account.md)
