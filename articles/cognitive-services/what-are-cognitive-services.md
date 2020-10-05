---
title: Mi az Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services a REST API-kkal rendelkező Cloud Services és az ügyféloldali kódtár SDK-k, amelyekkel a Microsoft Azure használatával intelligens alkalmazásokat hozhat létre.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: kognitív szolgáltatások, kognitív intelligencia, kognitív megoldások, AI-szolgáltatások, kognitív ismeretek, kognitív funkciók
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 08/28/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: bcb3fdbe12a2704e585a9b13484c2528c9cdc559
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90894443"
---
# <a name="what-are-azure-cognitive-services"></a>Mi az Azure Cognitive Services?

Az Azure Cognitive Services a REST API-kkal rendelkező felhőalapú szolgáltatások és az ügyféloldali kódtár SDK-k, amelyek segítségével a fejlesztők kognitív intelligenciát hozhatnak létre az alkalmazásokban anélkül, hogy közvetlen mesterséges intelligencia (AI) vagy adattudományi ismereteket vagy ismereteiket kellene létrehozniuk. Az Azure Cognitive Services lehetővé teszi, hogy a fejlesztők könnyen hozzá tudják adni a kognitív funkciókat az alkalmazásaikban olyan kognitív megoldásokkal, amelyek megtekinthetik, meghallgatják, megértették, megértik, sőt, akár

Az AI-szolgáltatások katalógusa, amelyek a kognitív megértést biztosítják, öt fő pillérbe sorolhatók:

* Látás
* Beszéd
* Nyelv
* Web Search
* Döntés

Az új dokumentáció aktuális listája a [Cognitive Services docs újdonságai](whats-new-docs.md)című dokumentumban olvasható.

## <a name="vision-apis"></a>Jövőkép API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Computer Vision")|A Computer Vision szolgáltatás a képek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális kognitív algoritmusokhoz.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|A Custom Vision Service segítségével egyéni rendszerkép-osztályozók hozhatók létre.|
|[Face](https://docs.microsoft.com/azure/cognitive-services/face/ "Arcfelismerés")| A Face szolgáltatás hozzáférést biztosít a speciális arc-algoritmusokhoz, lehetővé téve a Face attribútumok észlelését és felismerését.|
|[Űrlap-felismerő](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer") (előzetes verzió)|Az űrlap-felismerő azonosítja és kigyűjti a kulcs-érték párokat és a táblák adatait az űrlap dokumentumaiból. Ezután a a strukturált adatokat is megjeleníti, beleértve az eredeti fájlban lévő kapcsolatokat is.|
|[Kézírás-felismerő](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer") (kivonás)|A kézírás-felismerő lehetővé teszi a digitális tollvonási adat, az alakzatok és a kézírásos tartalom felismerését és elemzését, valamint a dokumentum-struktúra kimenetét az összes felismert entitással.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|A Video Indexer lehetővé teszi a videóból származó adatok kinyerését.|

## <a name="speech-apis"></a>Beszédfelismerési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Beszédfelismerési szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech szolgáltatás")|A beszédfelismerési szolgáltatás lehetővé teszi a beszédfelismerést támogató funkciók használatát az alkalmazásokhoz.|
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Beszélőfelismerés API") (előzetes verzió)|A Speaker Recognition API algoritmusokat biztosít a hangszórók azonosításához és ellenőrzéséhez.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing – Beszédfelismerés") (kivonás)|A Bing Speech API segítségével egyszerűen hozhat létre beszédfelismerést támogató szolgáltatásokat az alkalmazásaiban.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (kivonás)|Translator Speech egy gépi fordítási szolgáltatás.|

> [!NOTE]
> Az [Azure Cognitive Searcht](https://docs.microsoft.com/azure/search/)keresi? Bár egyes feladatokhoz Cognitive Services használ, ez egy másik keresési technológia, amely más forgatókönyveket is támogat.


## <a name="language-apis"></a>Nyelvi API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|A Language Understanding Service (LUIS) lehetővé teszi az alkalmazás számára, hogy megtudja, mit szeretne a saját szavaiban.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker lehetővé teszi, hogy a félig strukturált tartalomból hozzon létre egy kérdés-válasz szolgáltatást.|
|[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Szövegelemzés")|A Text Analytics természetes nyelvi feldolgozást tesz lehetővé a nyers szövegekben az érzelmek elemzése, a fő kifejezés kinyerése és a nyelvfelismerés terén.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "Fordító")|A Translator gépi alapú szöveges fordítást biztosít közel valós időben.|


## <a name="search-apis"></a>Keresési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing – Hírkeresés](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing – Hírkeresés")|Bing News Search a felhasználó lekérdezéséhez kapcsolódó Hírek listáját adja vissza.|
|[Bing – Videokeresés](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing – Videokeresés")|Bing Video Search a felhasználó lekérdezéséhez tartozó videók listáját adja vissza.|
|[Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing Web Search")|Bing Web Search a felhasználó lekérdezéséhez szükséges keresési eredmények listáját adja vissza.|
|[Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest lehetővé teszi részleges keresési lekérdezési kifejezés küldését a Bing számára, és a javasolt lekérdezések listájának visszaadása.|
|[Bing – Egyéni keresés](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing – Egyéni keresés")|Bing Custom Search lehetővé teszi, hogy testreszabott keresési funkciókat hozzon létre az Ön számára fontos témakörökhöz.|
|[Bing – Entitáskeresés](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing – Entitáskeresés")|Bing Entity Search a Bing által megadott entitásokra vonatkozó információkat ad vissza a felhasználó lekérdezéséhez.|
|[Bing – Képkeresés](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing – Képkeresés")|Bing Image Search visszaadja a felhasználó lekérdezéséhez szükséges képek megjelenítését.|
|[Bing vizuális keresés](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing vizuális keresés")|A Bing Visual Search a képekkel kapcsolatos információkat ad vissza, például vizuálisan hasonló képeket, a lemezképben található termékek vásárlási forrásait és a kapcsolódó kereséseket.|
|[Bing Local Business Search](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| A Bing local Business Search API lehetővé teszi az alkalmazások számára, hogy a keresési lekérdezések alapján megtalálják a helyi vállalkozások kapcsolattartási és tartózkodási információit.|
|[Bing – Helyesírás-ellenőrzés](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing – Helyesírás-ellenőrzés")|Bing Spell Check lehetővé teszi a kontextusos nyelvtan és a helyesírás-ellenőrzés végrehajtását.|

## <a name="decision-apis"></a>Döntési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomália detektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomáliadetektor") (előzetes verzió)|Az anomáliák detektorral figyelheti és azonosíthatja a rendellenességeket az idősorozat-adataiban.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator a lehetséges sértő, nemkívánatos és kockázatos tartalmak figyelését teszi lehetővé.|
|[Metrikai tanácsadó](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (előzetes verzió) | A metrikák Advisor testreszabható anomáliák észlelését teszi lehetővé a többváltozós idősorozat-adatokon, valamint egy teljes körű webes portált, amely a szolgáltatás használatához nyújt segítséget.
|[Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer")|A személyre szabás lehetővé teszi, hogy kiválassza a legjobb élményt, amelyet a felhasználók láthatnak, valós idejű viselkedésük alapján.|

## <a name="learn-with-the-quickstarts"></a>Ismerkedjen meg a gyors útmutatókkal

A Cognitive Services-erőforrások gyakorlati útmutatókkal történő létrehozásával kapcsolatos tudnivalók a használatával:

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-ügyfél kódtárai](cognitive-services-apis-create-account-cli.md?tabs=windows "kognitív-szolgáltatások-API-k-fiók-ügyfél-könyvtár? Pivots = Programming-Language-csharp")
* [Azure Resource Manager- (ARM-) sablonok](resource-manager-template.md?tabs=portal "Azure Resource Manager- (ARM-) sablonok")

## <a name="subscription-management"></a>Előfizetés-kezelés

Miután bejelentkezett a Microsoft-fiókjával, elérheti az [előfizetéseit](https://www.microsoft.com/cognitive-services/subscriptions "Saját előfizetések") , hogy megjelenjenek a használt termékek, a fennmaradó kvóta, valamint az előfizetéshez további termékek is hozzáadhatók.

## <a name="upgrade-to-unlock-higher-limits"></a>Frissítés a magasabb korlátok feloldására

Minden API-nak van egy ingyenes szintje, amelynek használati és átviteli korlátja van.  A korlátokat fizetős ajánlat használatával növelheti, és kiválaszthatja a megfelelő díjszabási csomag lehetőséget a szolgáltatás Azure Portal történő telepítésekor. [További információ az ajánlatokról és a díjszabásról](https://azure.microsoft.com/pricing/details/cognitive-services/ "ajánlatok és díjszabás"). Be kell állítania egy Azure-előfizetői fiókot egy bankkártyával és egy telefonszámmal. Ha különleges követelménye van, vagy egyszerűen csak szeretne kommunikálni a Sales szolgáltatással, kattintson a "Kapcsolatfelvétel" gombra a díjszabás oldal tetején.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A Cognitive Services API-jai a Microsoft által felügyelt adatközpontok egyre növekvő hálózatán futnak. Az egyes API-k regionális elérhetőségét az [Azure-régiók listájában](https://azure.microsoft.com/regions "Azure-régiók listája")találja.

Olyan régiót keresünk, amely még nem támogatott? Tudassa velünk, ha beküld egy szolgáltatási kérelmet a [UserVoice-fórumba](https://cognitive.uservoice.com/ "UserVoice-fórum").

## <a name="supported-cultural-languages"></a>Támogatott kulturális nyelvek

 Cognitive Services számos kulturális nyelvet támogat a szolgáltatási szinten. Az egyes API-k nyelvi elérhetőségét a [támogatott nyelvek listájában](language-support.md "támogatott nyelvek listája")találja.

## <a name="securing-resources"></a>Erőforrások biztonságossá tétele

Az Azure Cognitive Services többrétegű biztonsági modellt biztosít, beleértve a [hitelesítést](authentication.md "hitelesítés") Azure Active Directory hitelesítő adatokon, egy érvényes erőforrás-kulcson és az [Azure virtuális hálózatokon](cognitive-services-virtual-networks.md "Azure virtuális hálózatok")keresztül.

## <a name="container-support"></a>Tárolótámogatás

 A Cognitive Services tárolókat biztosít az Azure-felhőben vagy a helyszínen történő üzembe helyezéshez. További információ a [Cognitive Services tárolóról](cognitive-services-container-support.md "Cognitive Services-tárolók").

## <a name="certifications-and-compliance"></a>Minősítések és megfelelőség

Cognitive Services olyan minősítéseket kapott, mint például a CSA STAR minősítés, a FedRAMP mérsékelt és a HIPAA BAA.

A saját auditokra és biztonsági felülvizsgálatokra vonatkozó tanúsítványokat is [letöltheti](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "letöltés") .

Az adatvédelem és az adatkezelés megismeréséhez nyissa meg a [megbízhatósági központot](https://servicetrust.microsoft.com/ "Adatvédelmi központ").

## <a name="support"></a>Támogatás

A Cognitive Services számos [támogatási lehetőséget](cognitive-services-support-options.md "támogatási lehetőségek")biztosít.




## <a name="next-steps"></a>További lépések

* [Cognitive Services-fiók létrehozása](cognitive-services-apis-create-account.md "Cognitive Services-fiók létrehozása")
