---
title: Gyakori kérdések a Video Indexer-Azure-ról
titleSuffix: Azure Media Services
description: Választ kaphat a Video Indexerával kapcsolatos gyakori kérdésekre.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 95f2af1b192564290551f9ed31f0fe158b4536f6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838227"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk a Video Indexerával kapcsolatos gyakori kérdésekre ad választ.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

Video Indexer egy mesterséges intelligencia-szolgáltatás, amely Microsoft Azure Media Services részét képezi. Video Indexer több gépi tanulási modellt is biztosít, amelyek segítségével könnyedén kinyerheti a videóból a mélyebb betekintést. Ha speciális és pontos elemzéseket szeretne biztosítani, Video Indexer a videó több csatornáját is használja: hang, beszéd és vizualizáció. Video Indexer az ismeretek számos módon használhatók, például a tartalom felderíthetővé tétele és a kisegítő lehetőségek fejlesztése, új bevételi lehetőségek létrehozása vagy új, az adatgyűjtést használó tapasztalatok kiépítése. A Video Indexer webalapú felületet biztosít a fiókban található modellek teszteléséhez, konfigurálásához és testreszabásához. A fejlesztők REST-alapú API-t használhatnak a Video Indexer integrálásához az éles rendszerbe. 

### <a name="what-can-i-do-with-video-indexer"></a>Mire használhatom a Video Indexer?

A médiafájlok Video Indexer által elvégezhető műveletek némelyike többek között a következő műveleteket hajthatja végre:

* A beszédfelismerés azonosítása és kinyerése és a hangszórók azonosítása.
* A képernyőn megjelenő szöveg azonosítása és kibontása egy videóban.
* Objektumok észlelése egy videofájl esetében.
* A márkák (például a Microsoft) hangsávokból és a képernyőn megjelenő szöveg alapján azonosíthatók a videóban.
* Az arcok észlelése és felismerése egy hírességből álló adatbázisból és az arcok felhasználó által definiált adatbázisa.
* Kinyeri a tárgyalt témaköröket, de nem feltétlenül említi a hang-és videotartalom tartalmát.
* Zárt feliratok vagy feliratok létrehozása a hangsávokból.

További információt és további Video Indexer funkciókat a következő témakörben talál: [Áttekintés](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hogyan első lépések a Video Indexer?

Video Indexer tartalmaz egy ingyenes próbaverziós ajánlatot, amely 600 percet biztosít a webalapú felületen, és 2 400 percen belül az API-n keresztül. Bejelentkezhet [a video Indexer webes felületre](https://www.videoindexer.ai/) , és saját kezűleg is kipróbálhatja a webes identitások használatával, és anélkül, hogy Azure-előfizetést kellene beállítania. 

A videók és a hanganyag nagy méretekben történő indexeléséhez Video Indexer csatlakozhat egy fizetős Microsoft Azure előfizetéshez. A díjszabással kapcsolatos további információkért tekintse meg [a díjszabást ismertető oldalt.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

További információt az [első lépések című](video-indexer-get-started.md)témakörben talál.

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Szükségesek-e kódolási ismeretek a Video Indexer használatához?

A Video Indexer webalapú kezelőfelülettel kiértékelheti, konfigurálhatja és kezelheti a fiókját, ha **nincs szükség kódolásra**.  Ha készen áll összetettebb alkalmazások fejlesztésére, a [video INDEXER API](https://api-portal.videoindexer.ai/) -val integrálhatja video Indexereit a saját alkalmazásaiba, webhelyeire vagy [Egyéni munkafolyamataiba olyan kiszolgáló nélküli technológiákkal, mint például a Azure Logic Apps vagy a](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) Azure functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Szükségem van a gépi tanulási szaktudásra a Video Indexer használatához?

Nem, Video Indexer több gépi tanulási modell integrálását is lehetővé teszi egyetlen folyamatba. A videó-vagy hangfájlok indexelése Video Indexer lekéri az egyik megosztott idővonalon kinyert elemzések teljes készletét anélkül, hogy gépi tanulási ismeretekkel vagy az ügyfél részéről szükséges algoritmusokkal kapcsolatos ismeretekkel rendelkezik.

### <a name="what-media-formats-does-video-indexer-support"></a>Milyen adathordozó-formátumokat támogat Video Indexer?

A Video Indexer a legtöbb gyakori médiaformátumot támogatja. További részletekért tekintse meg a [Azure Media Encoder standard formats (szabványos formátumok](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) ) listát.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hogyan tölthetők fel adathordozók a Video Indexerba?

A Video Indexer webalapú portálon feltöltheti a médiafájlokat a fájlfeltöltés párbeszédpanellel, vagy egy olyan URL-re mutatva, amely közvetlenül a forrásfájlt futtatja (lásd a [példát](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Bármely olyan URL-cím, amely iFrame vagy beágyazási kód használatával futtatja az adathordozó tartalmát, nem fog működni (lásd a [példát](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). A Video Indexer API-nak szüksége van a bemeneti fájl megadására egy URL-cím vagy egy bájtos tömb használatával. Az API-t használó URL-címeken keresztüli feltöltések legfeljebb 10 GB méretűek lehetnek, de nem rendelkeznek időtartamra vonatkozó korláttal. További információ: [útmutató](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Mennyi időt vesz igénybe Video Indexer az adatok adathordozóról való kinyerése?

A videó-vagy hangfájlok indexeléséhez szükséges idő, a Video Indexer API-t és a Video Indexer webes felületet is beleértve, több paramétertől függ, például a fájl hosszától és minőségétől, a fájlban talált elemzések számától, a rendelkezésre álló [fenntartott egységek](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) számától, valamint attól, hogy a [folyamatos átviteli végpont](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) engedélyezve van-e. Javasoljuk, hogy futtasson néhány tesztoldalt a saját tartalmával, és készítsen egy átlagot a jobb ötlet megszerzéséhez.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Létrehozhatok testreszabott munkafolyamatokat a folyamatok automatizálásához a Video Indexer használatával?

Igen, integrálhatja a Video Indexert olyan kiszolgáló nélküli technológiákba, mint például a Logic Apps, a flow és a [Azure functions](https://azure.microsoft.com/services/functions/). További részleteket a [Logic app](https://azure.microsoft.com/services/logic-apps/) és a [Flow](https://flow.microsoft.com/en-us/) összekötők video Indexer [itt](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)talál. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Mely Azure-régiók érhetők el a video Indexer szolgáltatásban?

Láthatja, hogy mely Azure-régiók érhetők el Video Indexer a [régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) lapon.

### <a name="what-is-the-sla-for-video-indexer"></a>Mi a Video Indexer SLA-ja?

Az Azure Media Service SLA-ja kiterjed Video Indexerre, és az [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) oldalon található. Az SLA csak Video Indexer fizetős fiókra vonatkozik, és nem vonatkozik az ingyenes próbaverzióra.

## <a name="privacy-questions"></a>Adatvédelmi kérdések

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>A videó-és hangfájlok Video Indexer tárolt adatokkal vannak indexelve?

Igen, hacsak nem törli a fájlt Video Indexerről, a Video Indexer webhelyet vagy API-t használva a rendszer a videó-és hangfájlokat tárolja. Az ingyenes próbaverzióhoz az Ön által indexelt videó-és hangfájlok az USA keleti régiójában található Azure-régióban tárolódnak. Ellenkező esetben a videó-és hangfájlok tárolása az Azure-előfizetés Storage-fiókjában történik.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Törölhetem a Video Indexer portálon tárolt fájlokat?

Igen, bármikor törölheti a videó-és hangfájlokat, valamint a belőlük kinyert metaadatokat és elemzéseket Video Indexer. Ha töröl egy fájlt a Video Indexer, a fájl és a hozzá tartozó metaadatok és az adatok véglegesen törlődnek a Video Indexerból. Ha azonban végrehajtotta saját biztonsági mentési megoldását az Azure Storage-ban, a fájl az Azure Storage-ban marad.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Szabályozható a felhasználói hozzáférés a Video Indexer fiókomhoz?

Igen, csak a rendszergazdák hívhatják meg és nem hívhatják meg a fiókjaikat, valamint hozzárendelhetők a szerkesztési jogosultságokkal rendelkezők és a csak olvasási hozzáféréssel rendelkező felhasználókhoz.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Ki fér hozzá a videó-és hangfájlokhoz, amelyeket Video Indexer indexelt és/vagy tárolt, valamint a metaadatokat és a kinyert elemzéseket?

A saját adatvédelmi beállítással rendelkező videó-vagy hangtartalmat bárki elérheti, aki a videóra vagy hangtartalomra mutató hivatkozással rendelkezik. A személyes adatvédelmi beállítással rendelkező videó-vagy hangtartalom csak olyan felhasználók számára érhető el, akik a videó vagy a hangtartalom fiókját kérték. A tartalom adatvédelmi beállítása a Video Indexer kivonatokkal kapcsolatos metaadatokra és megállapításokra is vonatkozik. A videó-vagy hangfájl feltöltésekor az adatvédelmi beállításokat kell kiosztania. Az indexelés után is módosíthatja az adatvédelmi beállításokat.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Milyen hozzáférésre van a Microsoft a videó-vagy hangfájlokhoz, amelyeket a Video Indexer indexelt és/vagy tárolt, valamint a metaadatokat és a kinyert elemzéseket?

Az [Azure Online Services használati feltételei](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) teljes mértékben saját tartalommal rendelkeznek, és a Microsoft csak a tartalmakat és a metaadatokat és bepillantást nyújtja, hogy az OST és a Microsoft adatvédelmi nyilatkozata alapján video Indexer kivonatokat a tartalomból.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>A Video Indexer fiókomban felépített egyéni modellek más fiókok számára is elérhetők?

 Nem, a fiókban létrehozott egyéni modellek nem érhetők el más fiókok számára. Video Indexer jelenleg lehetővé teszi egyéni [márkák](customize-brands-model-overview.md), [nyelv](customize-language-model-overview.md)és [személy](customize-person-model-overview.md) modellek létrehozását a fiókban. Ezek a modellek csak abban a fiókban érhetők el, amelyben létrehozta a modelleket.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Az Video Indexer indexelt tartalom az Azure-régióban, ahol Video Indexer használok?

Igen, a tartalom és az adatai az Azure-régióban maradnak, hacsak nem rendelkezik olyan manuális konfigurációval az Azure-előfizetésben, amely több Azure-régiót használ. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Mi a Video Indexer adatvédelmi szabályzata?

A Video Indexer a [Microsoft adatvédelmi nyilatkozata](https://privacy.microsoft.com/privacystatement)szabályozza. Az adatvédelmi nyilatkozat ismerteti a Microsoft által feldolgozott személyes adatokat, a Microsoft folyamatait és a Microsoft által feldolgozható célokat. Ha többet szeretne megtudni az adatvédelemről, látogasson el a [Microsoft adatvédelmi központba](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Milyen minősítéseket Video Indexer?

Video Indexer jelenleg a SOC-minősítéssel rendelkezik. Video Indexer minősítésének áttekintéséhez tekintse meg a [Microsoft adatvédelmi központját](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>API-kérdések

### <a name="what-apis-does-video-indexer-offer"></a>Milyen API-kat Video Indexer ajánlatot?

A Video Indexer API-jai lehetővé teszik az indexelést, a metaadatok kinyerését, az eszközök kezelését, a fordítást, a beágyazást, a modellek testreszabását és egyebeket. A Video Indexer API használatával kapcsolatos részletesebb információkért tekintse meg a [video Indexer fejlesztői portált](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Milyen ügyféloldali SDK-kat Video Indexer ajánlatot?

Jelenleg nincsenek elérhető ügyféloldali SDK-k. A Video Indexer csapat az SDK-k és a tervek szerint hamarosan elérhetővé válik.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hogyan Ismerkedés a Video Indexer API-val?

Kövesse [az oktatóanyagot: Ismerkedés a video INDEXER API-val](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Mi a különbség a Video Indexer API és az Azure Media Service V3 API között?

Jelenleg vannak átfedések a Video Indexer API és az Azure Media Service V3 API által kínált funkciókban. A szolgáltatások összehasonlításáról [itt](compare-video-indexer-with-media-services-presets.md)talál további információt.

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Mi az API-hozzáférési jogkivonat, és miért van rá szükségem?

A Video Indexer API egy engedélyezési API-t és egy operatív API-t tartalmaz. Az engedélyezési API olyan hívásokat tartalmaz, amelyek hozzáférési tokent biztosítanak. A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

Hozzáférési jogkivonatok szükségesek a Video Indexer API-k biztonsági célokra való használatához. Ezzel biztosíthatja, hogy minden hívás érkezik Önhöz, vagy azokhoz, akik hozzáférési jogosultságokkal rendelkeznek a fiókjához. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Mi a különbség a fiók hozzáférési jogkivonata, a felhasználói hozzáférési jogkivonat és a videó-hozzáférési jogkivonat között?

* Fiók szintű – a fiók szintű hozzáférési tokenek lehetővé teszik a fiók szintjén vagy a videó szintjén végzett műveletek végrehajtását. Például feltöltheti a videót, listázhatja az összes videót, és beolvashatja a video-felismeréseket.
* Felhasználói szint – a felhasználói szintű hozzáférési tokenek lehetővé teszik a felhasználói szinten végzett műveletek végrehajtását. Lekérhet például társított fiókokat.
* Videó szintje – a videó szintű hozzáférési tokenek lehetővé teszik a műveletek végrehajtását egy adott videón. Lekérhet például videóelemzéseket, letölthet feliratokat, kérhet vezérlőket stb.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Milyen gyakran van szükség új hozzáférési token beszerzésére? Mikor jár le a hozzáférési jogkivonatok érvényessége?

A hozzáférési tokenek óránként lejárnak, ezért minden órában új hozzáférési jogkivonatot kell létrehoznia. 

## <a name="billing-questions"></a>Számlázással kapcsolatos kérdések

### <a name="how-much-does-video-indexer-cost"></a>Mennyibe kerül a Video Indexer?

A Video Indexer egy egyszerű utólagos elszámolású díjszabási modellt használ az indexelt tartalom bevitelének időtartama alapján. A kódolásra, a folyamatos átvitelre, a tárterületre, a hálózati használatra és a Media szolgáltatás számára fenntartott egységekre további díjak vonatkozhatnak. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) oldalt.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Mikor kell fizetnem a Video Indexer használatáról?

Videó indexelésre való elküldésénél a felhasználó eldöntheti, hogy az indexelés videóelemzés, hangelemzés vagy mindkettő legyen-e. Ez határozza meg, hogy mely termékváltozatban történjen a díjak felszámítása. Ha a feldolgozás során kritikus hiba lép fel, válaszként egy hibakód lesz visszaküldve. Ilyen esetben nem számítunk fel díjat.  Kritikus hibát okozhat egy kódbeli hiba vagy a szolgáltatás egyik belső függőségének kritikus meghibásodása. A hibás azonosítás vagy hibás információkinyerés nem számít kritikus hibának, és ilyen esetben választ is küld a rendszer. Díjat számolunk fel minden olyan esetben, amikor egy érvényes (nem hibakódot tartalmazó) választ ad vissza a rendszer.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Kínál Video Indexer ingyenes próbaverziót?

Igen, Video Indexer ingyenes próbaverziót kínál, amely teljes körű szolgáltatást és API-funkciókat biztosít. Az API-felhasználók számára a 600 perces számú, a webalapú felhasználói felülethez és a 2 400 percben található videó. 

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
