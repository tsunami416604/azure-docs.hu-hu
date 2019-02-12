---
title: A Video Indexer – Azure kapcsolatos gyakori kérdések
titlesuffix: Azure Media Services
description: A Video Indexer kapcsolatos gyakori kérdésekre adott válaszok.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: bc52c4eaf77b9441683eea8e5f899c6ef34d62e8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002357"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk a Video Indexer kapcsolatos gyakori kérdésekre ad választ.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

A video Indexer olyan mesterséges intelligenciát használó szolgáltatás, amely a Microsoft Azure Media Services része. A video Indexer hangolja több machine learning-modellek, amelyek segítségével könnyen tisztább képet kaphat kinyerése egy videót. A speciális és pontos szolgálhat, Video Indexer segítségével használja, a videó több csatornán: hang, a beszéd, és a Vizualizáció. Számos módon javítása tartalom észlelhetőség és elérhetőség, létrehozása új bevételszerzési lehetőségek, például a video Indexer insights is használhatnak, vagy új létrehozása során lép fel, amelyek használják az elemzéseket. A video Indexer webes felületet biztosít a tesztelési, konfigurációs és testre szabhatja a fiókjában modellek. A fejlesztők egy REST-alapú API segítségével integrálja a Video Indexer éles rendszerbe. 

### <a name="what-can-i-do-with-video-indexer"></a>Mire használhatom a Video Indexer?

A Video Indexer médiafájlok hajthatnak végre műveleteket a következők:

* Azonosítása és kibontása speech, és azonosíthatja a beszélőket.
* Azonosítására és a képernyőn látható kibontása egy videó szövegében.
* Objektumok észlelése a videót.
* Azonosíthatja a márka (például: A videó Microsoft) származó hangsáv és a képernyőn megjelenő szöveg.
* Észlelése és a egy hírességek, és egy felhasználó által definiált adatbázis téglalapot arcokat felismerve.
* Témakörök kibontása tárgyalt, de nem feltétlenül említett audio- és videotartalmakhoz.
* Kódolt feliratok létrehozása a hangsávra.

További információ és a Video Indexer további funkciók: [áttekintése](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hogyan kezdhetem a Video Indexer?

A video Indexer, amely 600 perc, a webes felületen és az API-n keresztül 2400 perc nyújt ingyenes próbaverzió magában foglalja. Is [jelentkezzen be a Video Indexer webes felület](https://www.videoindexer.ai/) , és próbálja ki a saját minden olyan webes identitás használatával és anélkül, hogy kellene egy Azure-előfizetést. 

Index videó és hang közlekednek ipari méretekben a Video Indexer csatlakozhat egy díjköteles Microsoft Azure-előfizetésbe. Az árképzés további tájékoztatást talál a [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) lap.

Első lépésekről további információt talál [Ismerkedés](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Szükséges kódolási ismeretek a Video Indexer használatára?

A Video Indexer webes felület segítségével kiértékelheti, konfigurálását és kezelését a fiókját **kódírás nélkül**.  Amikor készen áll a fejleszthetnek összetettebb alkalmazásokat, használhatja a [Video Indexer API](https://api-portal.videoindexer.ai/) Video Indexer integrálhatók a saját alkalmazások, webhelyek, vagy [egyéni munkafolyamatok, például a kiszolgáló nélküli technológiák segítségével Az Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) vagy az Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Machine learning képességek a Video Indexer használatára van szükségem?

Nem, a Video Indexer biztosít a különböző gépi tanulási modellek egy folyamatba, az integráció. Kibontott a megosztott egyik ütemtervben bármely machine learning-ismeretek vagy algoritmust az ügyfél részére szükséges ismeretek nélkül insights teljes körű egy videó vagy hang fájlt a Video Indexer segítségével kérdezi le.

### <a name="what-media-formats-does-video-indexer-support"></a>Milyen médiaformátumok támogatja a Video Indexer?

A video Indexer leggyakoribb médiaformátumok támogatja. Tekintse meg a [Azure Media Encoder standard-formátumok](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) lista további részletekért.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hogyan ehhez tölthetők fel egy media Video Indexer felhasználóihoz való?

A Video Indexer webes portálon feltölthet egy médiafájlt a fájlfeltöltési párbeszédpaneljére használatával, vagy egy URL-címen, amely közvetlenül a forrásfájl futtat (lásd: [példa](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Gazdagépek, a media tartalom iFrame használatával, vagy a beágyazási kód nem fog működni bármilyen URL-cím (lásd: [példa](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). A Video Indexer API megköveteli, hogy adja meg a bemeneti fájlt egy URL-címet vagy egy bajtového Pole. Az API-val, egy URL-CÍMEN keresztül feltöltések legfeljebb 10 GB-os, de nem rendelkezik a határidő időtartamát. További információkért tekintse meg a [gyakorlati útmutató](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Mennyi ideig tart a Video Indexer-adatokat nyerhet ki az adathordozó a?

Mennyi ideig tart a video- vagy fájl, mind a Video Indexer API és a Video Indexer webes felület használatával index attól függ, például a fájl hossza és minősége, több paraméter insights száma található a fájlban, száma[szolgáltatás számára fenntartott egységek](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) érhető el, és hogy a [streamvégpont](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) engedélyezve van-e. Azt javasoljuk, hogy néhány tesztfájlok futtatása a saját tartalommal, és a megvilágításához átlagát.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>A Video Indexer folyamatok automatizálása a testre szabott munkafolyamatokat hozhat létre?

Igen, integrálható a Video Indexer, például a Logic Apps, a folyamat, kiszolgáló nélküli technológiákat és [Azure Functions](https://azure.microsoft.com/services/functions/). A további részleteket talál a [logikai alkalmazás](https://azure.microsoft.com/services/logic-apps/) és [Flow](https://flow.microsoft.com/en-us/) összekötők a Video Indexer [Itt](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Melyik Azure-régióban érhető el a Video indexer?

Láthatja, hogy mely Azure-régiók a Video Indexer érhető el a [régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) lapot.

### <a name="what-is-the-sla-for-video-indexer"></a>Mit jelent az SLA-t a Video Indexer?

Az Azure Media Services az SLA kiterjed a Video Indexer, és találhatók a [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) lapot. A szolgáltatói szerződés csak a Video Indexer fiókok fizetős vonatkozik, és nem vonatkozik az ingyenes próbaverzióra.

## <a name="privacy-questions"></a>Adatvédelmi kérdések

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Video Indexer, tárolja által indexelt videó-és hangfájlok?

Igen, hacsak nem törli a fájlt a Video Indexer, vagy használja a Video Indexer webhely vagy az API-t, video- és tárolt fájlok. Az ingyenes próbaverzióra, hang- és fájlokat, amikor az indexet az Azure-régió USA keleti Régiójában vannak tárolva. Ellenkező esetben a video- és fájlok vannak tárolva a tárfiókot az Azure-előfizetés.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Törli a Video Indexer portálon tárolt fájlok is?

Igen, bármikor törölheti a videó és hangfájlokat, valamint bármely metaadatok és insights Video Indexer által kinyert őket. Ha töröl egy fájlt a Video Indexer, a fájl, és a metaadatok és elemzési eredmények véglegesen eltávolítja a Video Indexer. Azonban a saját biztonsági mentési megoldás megvalósítását az Azure storage-ban, ha a fájl marad az Azure storage-ban.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Szabályozhatom felhasználói hozzáférés a Video Indexer-fiókot?

Igen, csak a fiók rendszergazdák is meghívása és a meghívás visszavonása személyek fiókjait, valamint kinek van szerkesztési jogosultságot, és csak olvasási hozzáférés hozzárendelése.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>A video- és fájlok, amelyek indexelése, illetve a Video Indexer, és a metaadatok és, amelyek könyvtárban találhatók által tárolt férhet hozzá?

A video- és tartalom nyilvános, mint saját adatvédelmi beállítással rendelkező bárki, aki a hivatkozásra kattintva a videó vagy hang tartalom és az elemzések hozzáférhető. A video- és audiotartalmak tartalom privát as-annak adatvédelmi beállítással rendelkező csak a fiók a video- és audiotartalmak tartalom lettek hívva a felhasználók által érhető el. Az adatvédelmi beállítás tartalmat is a metaadatokat és az elemzések, amely kinyeri a Video Indexer vonatkozik. Az adatvédelmi beállítás rendelje hozzá, amikor a video- vagy fájl feltöltése. Az adatvédelmi beállítás indexelése követően is módosíthatja.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Milyen hozzáféréssel rendelkezik a Microsoft saját video- vagy fájlok indexelése, illetve a Video Indexer, és a metaadatok és, amelyek könyvtárban találhatók által tárolt?

Egy a [Azure Online szolgáltatások használati feltételeit](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), a saját teljesen a tartalmat, és a Microsoft csak akkor fér hozzá a tartalom és a metaadatokat és, amelyek a Video Indexer kinyeri az OST és a Microsoft szerint a tartalomból Adatvédelmi nyilatkozatot.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Érhetők el az egyéni modellek, hogy a Video Indexer-fiókot a más fiókokhoz?

 Az egyéni modellek a fiókban létrehozott nem, minden más fiókhoz nem érhetők el. A video Indexer jelenleg lehetővé teszi, hogy hozhat létre egyéni [márkái](customize-brands-model-overview.md), [nyelvi](customize-language-model-overview.md), és [személy](customize-person-model-overview.md) modellek a fiókjában. Ezek a modellek csak a fiók, amelyben létrehozta a modellek érhető el.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Azure-régióban, használom a Video Indexer tartani a Video Indexer által indexelt a tartalmat?

Igen, a tartalom és az elemzések tartományán belül maradjanak az Azure-régióban, kivéve, ha manuális konfigurációs van az Azure-előfizetés által használt több Azure-régióban. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Mi az az adatvédelmi szabályzatot, a Video Indexer?

A video Indexer hatálya alá tartozik a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement). Az adatvédelmi nyilatkozat ismerteti a Microsoft dolgozza fel a személyes adatokat, hogyan Microsoft dolgozza fel, és milyen célokra a Microsoft feldolgozza azt. További információ az adatvédelemről, látogasson el a [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Milyen minősítések rendelkezik a Video Indexer?

A video Indexer jelenleg rendelkezik a SOC-tanúsítvány. A Video Indexer hitelesítő áttekintéséhez tekintse meg a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>API-kérdések

### <a name="what-apis-does-video-indexer-offer"></a>Milyen API-kat kínál a Video Indexer?

A video Indexer API-k lehetővé teszi, hogy az indexelés, kibontása a metaadatok, eszközkezelés, fordítás, végez beágyazást, modellek és további testre szabhatja. További részletes információ a Video Indexer API használatával megkereséséhez tekintse meg a [Video Indexer fejlesztői portál](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Milyen ügyfél SDK-kat kínál a Video Indexer?

Jelenleg nem tartoznak SDK-k érhető el. A Video Indexer csapat dolgozik a SDK-k és a tervek szerint hamarosan elérhetővé őket.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hogyan kezdhetem Video Indexer API-val?

Hajtsa végre a [oktatóprogram: Ismerkedés a Video Indexer API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Mi a különbség a Video Indexer API és az Azure Media Services v3 API-t?

Jelenleg nincsenek néhány átfedésben van a Video Indexer API és az Azure Media Services v3 API által kínált szolgáltatások. További információ a mindkét szolgáltatás összehasonlítási módja annak [Itt](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Mi az API hozzáférési jogkivonatot, és miért szüksége van rá?

A Video Indexer API engedélyezési API-k és műveletek API-kkal tartalmazza. Az engedélyek API-t tartalmaz, amelyek hívásokat hozzáférési jogkivonatot. A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

Hozzáférési jogkivonatok biztonsági okokból a Video Indexer API-k használatára van szükség. Ez biztosítja, hogy a beléptetés-visszavonásokat érkeznek-e, vagy azok, akik a fiókokra vonatkozó hozzáférési jogosultságait. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Mi a különbség a fiók hozzáférési jogkivonatának, a felhasználói jogkivonat és a hozzáférési jogkivonatot a videó között?

* Fiók szintjén – az Objektumszintű hozzáférési jogkivonatainak lehetővé teszik a műveletek az a fiók vagy a videó szintjén. Például videó feltöltése, listázása az összes videó, videó elemzések lekérése.
* Felhasználói szinten - felhasználói hozzáférési tokenek lehetővé teszik a műveletek felhasználói szinten. Lekérhet például társított fiókokat.
* Videószintű – videó az Objektumszintű hozzáférési tokenek lehetővé teszik a műveletek az adott videó. Lekérhet például videóelemzéseket, letölthet feliratokat, kérhet vezérlőket stb.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Milyen gyakran kell egy új hozzáférési jogkivonat lekérése? Amikor lejár a hozzáférési jogkivonatok?

Hozzáférési jogkivonatok lejár óránként, ezért létre kell hoznia egy új hozzáférési jogkivonat óránként. 

## <a name="billing-questions"></a>Számlázással kapcsolatos kérdések

### <a name="how-much-does-video-indexer-cost"></a>A Video Indexer mennyibe?

A video Indexer használ egy egyszerű használatalapú díjszabási modell, a tartalom bemeneti, amely indexeli időtartama alapján. További merülhetnek fel a kódolást, streaming, tárolási, hálózati használati és media szolgáltatás számára fenntartott egységeket. További információkért lásd: a [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) lapot.

### <a name="does-video-indexer-offer-a-free-trial"></a>A Video Indexer kínál egy ingyenes próbaverzióra?

A Video Indexer Igen, a teljes szolgáltatás- és API-funkciókat biztosító ingyenes próbaidőszakot is kínál. Nincs webes felületen felhasználók esetében, és API-t felhasználók 2400 perc videók érdemes 600 perc-kvótát. 

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
