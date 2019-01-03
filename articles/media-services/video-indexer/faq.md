---
title: A Video Indexer – Azure kapcsolatos gyakori kérdések
titlesuffix: Azure Media Services
description: A Video Indexer kapcsolatos gyakori kérdésekre adott válaszok.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/19/2018
ms.author: juliako
ms.openlocfilehash: 972858b4bae995b6e9073cf7ee451a317e9f3909
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731318"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk a Video Indexer kapcsolatos gyakori kérdésekre ad választ.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

Video Indexer szolgáltatás metaadatok kinyerése a hang- és médiafájlokat az Azure Media Services. Számos gépi tanulási algoritmusok használatával könnyen besorolni, elemzése és információkhoz juthat a video- és tartalom használatával [előre meghatározott minták](video-indexer-overview.md). Elemzéseket akkor használhatja ezeket javítása tartalom észlelhetőség és elérhetőség, például számos módon létrehozásával új bevételi lehetőségeket jeleníthetnek meg, vagy új létrehozása során lép fel, hogy az elemzéseket.

A video Indexer webes felületet biztosít a tesztelést, a konfigurációs és a testreszabási és a egy REST-alapú API, a fejlesztők beépíthetik az éles rendszerbe.

### <a name="what-can-i-do-with-video-indexer"></a>Mire használhatom a Video Indexer?

A video Indexer hajthatja végre a következő típusú műveletek az adathordozó-fájlok:

* Azonosíthatja és bontsa ki a beszéd, és azonosíthatja a beszélőket.
* Azonosítsa, és bontsa ki a képernyőn látható szöveg egy videóban.
* Azonosításához, és a egy videó-fájlban található objektumok címkézését.
* Azonosítsa a márkákat, például a Microsoft a hangsáv és a képernyőn megjelenő szöveg egy videóban.
* És arcokat hírességek, adatbázis és a egy felhasználói adatbázist az arcok felismerésére.
* Kulcsszavak kinyerése a kimondott és egy Vizualizáció szöveg alapján audio- és videotartalmakhoz.
* Bontsa ki a külön nem feltétlenül az audio- és videotartalmakhoz kimondott és egy Vizualizáció szöveg alapján is említik, de tárgyalt témaköröket.
* Kódolt feliratok létrehozása a hangsávra.

További információkért lásd az [Áttekintést](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hogyan kezdhetem a Video Indexer?

Video Indexer eszközt ingyenesen kipróbálható. Az ingyenes próbaidőszakot biztosít a webes felületen 600 perc, és az API-n keresztül 2400 perc.

Index videó és hang közlekednek ipari méretekben a Video Indexer csatlakozhat egy díjköteles Microsoft Azure-előfizetésbe. Az árképzés további tájékoztatást talál a [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) lap.

Első lépésekről további információt talál [Ismerkedés](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Szükséges kódolási ismeretek a Video Indexer használatára?

A Video Indexer API-k integrálása folyamatába, vagy a Video Indexer a portálon, és nem kell minden bármilyen kód megírását. 

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Machine learning képességek a Video Indexer használatára van szükségem?

Nem, akkor is nyerhet ki a video- és audiotartalmak teljesen bármely machine learning-ismeretek vagy algoritmusok ismeretek nélkül. 

### <a name="what-media-formats-does-video-indexer-support"></a>Milyen médiaformátumok támogatja a Video Indexer?

A video Indexer leggyakoribb médiaformátumok támogatja. Az Azure Media Encoder standard-formátumok lista további részletekért tekintse meg.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hogyan ehhez tölthetők fel egy media Video Indexer felhasználóihoz való?

A Video Indexer webes portálon feltölthet egy médiafájlt a fájlfeltöltési párbeszédpaneljére használatával, vagy egy URL-címen, amely közvetlenül Megadja azt a fájlt. Például: `http://contoso.cloudapp.net/videos/example.mp4`. Egy a-oldalának hivatkozása, például egy videolejátszó tartalmazó `http://contoso.cloudapp.net/videos` nem fog működni. A Video Indexer API megköveteli, hogy adja meg a bemeneti fájlt egy URL-címet vagy egy bajtového Pole. Vegye figyelembe, hogy feltölt egy URL-CÍMEN keresztül legfeljebb 10 GB-ot, de nem rendelkezik a határidő időtartamát. További információkért tekintse meg a [gyakorlati útmutató](upload-index-videos.md).

### <a name="how-long-does-it-take-visual-indexer-to-extract-insights-from-media"></a>Mennyi ideig tart Visual indexelő adatokat nyerhet ki az adathordozó?

Mennyi ideig tart a video- vagy fájl, mind a Video Indexer API és a Video Indexer webes felület használatával index attól függ, például a fájl hosszát és a minőség, a fájlban, a számítási száma insights száma több paraméter több egység érhető el, és a streamelési végpontot engedélyezve van-e vagy sem. A legtöbb tartalomtípus feltételezve, hogy 10 S3 szintű fenntartott engedélyezve vannak, hogy elemzésével jelzi előre, hogy indexelő fog 1/3 ½ a videó hossza.  Azonban javasoljuk, hogy néhány tesztfájlok futtatása a saját tartalommal, és a megvilágításához átlagát. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Melyik Azure-régióban érhető el a Video indexer?

Láthatja, hogy mely Azure-régiók a Video Indexer érhető el a [régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) lapot.

### <a name="what-is-the-sla-for-video-indexer"></a>Mit jelent az SLA-t a Video Indexer?

A Video Indexer az ingyenes próbaverzió nem tartozik SLA. Az Azure Media Services az SLA kiterjed a Video Indexer.

Annak az adatokat a a [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_0/) lapot.

## <a name="billing-questions"></a>Számlázással kapcsolatos kérdések

### <a name="how-much-does-video-indexer-cost"></a>A Video Indexer mennyibe?

Video Indexer – egy egyszerű utólagos használt tartalom bemeneti időtartama alapján díjszabási modellt használ. További merülhetnek fel a kódolást, streaming, tárolási, hálózati használati és media szolgáltatás számára fenntartott egységeket. A jelenlegi díjszabás, tekintse meg a [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) lapot.

### <a name="does-video-indexer-offer-a-free-trial"></a>A Video Indexer kínál egy ingyenes próbaverzióra?

Igen, a Video Indexer, amely a keleti velünk a Kapcsolatot Azure-régióban üzemel, és lehetővé teszi a teljes szolgáltatás- és API-funkciók ingyenes próbaidőszakot is kínál. Nincs egy webhely felhasználók esetében, és a 40 óra API-t felhasználók videók érdemes 10 óra kvótáját. 

## <a name="security-questions"></a>Biztonsági kérdések

### <a name="are-audio-and-video-files-indexed-by-video-indexer-stored"></a>Video Indexer, tárolja által indexelt hang- és videofájlok?

Igen, hacsak nem törli a fájlt a Video Indexer vagy a Video Indexer webhely vagy API használatával, a video- és fájlok lesznek tárolva. Az ingyenes próbaverzióra, hang- és, amikor fájlok esetében index tárolva az Azure-régió USA keleti RÉGIÓJA. Ellenkező esetben a storage-fiók Azure-előfizetése a video- és fájlokat tárolja.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Törli a Video Indexer portálon tárolt fájlok is?

Igen, bármikor törölheti a video- és fájlokat, valamint azok insights a Video Indexer. Miután törli a fájlokat, véglegesen eltűnt a Video Indexer, és bárhol is legyenek a Video Indexer tárolja a fájlt (Azure-régiót USA keleti Régiójában próbafiókokon és az egyéb Azure-előfizetés a storage-fiók).

### <a name="who-has-access-to-my-audio-and-video-files-and-that-have-been-indexed-andor-stored-by-video-indexer"></a>A hang- és videofájlok férhet hozzá és, amely indexelt illetve Video Indexer által tárolt?

Igen, bármikor törölheti a video- és fájlokat, valamint azok insights a Video Indexer. Miután törli a fájlokat, véglegesen eltűnt a Video Indexer, és bárhol is legyenek a Video Indexer tárolja a fájlt (Azure-régiót USA keleti Régiójában próbafiókokon és az egyéb Azure-előfizetés a storage-fiók).

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Szabályozhatom felhasználói hozzáférés a Video Indexer-fiókot?

Igen, válassza ki a fiókot a cégbe.

### <a name="who-has-access-to-the-insights-that-were-extracted-from-my-audio-and-video-files-by-video-indexer"></a>Ki férhet hozzá, amelyek a Video Indexer által a saját hang- és videofájlok könyvtárban találhatók az insights?

Bárki, aki a hivatkozásra kattintva a videó és az elemzések alapján a nyilvános, mint saját adatvédelmi beállítással rendelkező videók elérhető lesz. A privát as-annak adatvédelmi beállítással rendelkező videók csak a fiók a videó lettek hívva a felhasználók által érhető el.

### <a name="do-i-still-own-my-content-that-have-been-indexed-and-stored-by-video-indexer"></a>Ehhez továbbra is saját, amely indexeli, illetve a Video Indexer által tárolt saját tartalom?

Igen, egy a [Azure Online szolgáltatások használati feltételeit](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), a saját tartalmait.

### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Azure-régióban, használom a Video Indexer tartani a Video Indexer által indexelt a tartalmat?

Igen, a tartalom és az elemzések folyamatosan az Azure-régión belül, kivéve, ha manuális konfigurációs van az Azure-előfizetés által használt több Azure-régióban. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Mi az az adatvédelmi szabályzatot, a Video Indexer?

A video Indexer hatálya alá tartozik a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement).

## <a name="api-questions"></a>API-kérdések

### <a name="what-are-the-differences-in-capability-on-the-video-indexer-website-versus-the-video-indexer-api"></a>Mik a különbségek a Video Indexer API és a Video Indexer webhelyen funkció?

Az a [Video Indexer](https://www.videoindexer.com) webhelyen keresse a – videótár, elemezheti insights, modellek testreszabása, konfigurálja a fiókot, és szerkessze a videók egyszerű. A robusztus és [Video Indexer – REST API](https://api-portal.videoindexer.ai/), bármilyen infrastruktúra integrálhat vagy ágyazhat widgetek közvetlenül a saját webhelyéhez vagy alkalmazásához.

### <a name="what-apis-does-video-indexer-offer"></a>Milyen API-kat kínál a Video Indexer?

A Video Indexer API-k használatával az információkat a [Video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/)

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hogyan kezdhetem Video Indexer API-val?

Hajtsa végre a [oktatóprogram: Ismerkedés a Video Indexer API](video-indexer-use-apis.md).

### <a name="what-is-an-access-token"></a>Mit jelent a hozzáférési tokent?

A Video Indexer API engedélyezési API-k és műveletek API-kkal tartalmazza. Az engedélyek API-t tartalmaz, amelyek hívásokat hozzáférési jogkivonatot. A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Mi a különbség a fiók hozzáférési jogkivonatának, a felhasználói jogkivonat és a hozzáférési jogkivonatot a videó között?

Ezek képviselik a telefonhívások hitelesítési hatókör.

* Felhasználói szinten - felhasználói hozzáférési tokenek lehetővé teszik a műveletek felhasználói szinten. Lekérhet például társított fiókokat.
* Fiók szintjén – az Objektumszintű hozzáférési jogkivonatainak lehetővé teszik a műveletek az a fiók vagy a videó szintjén. Feltölthet például videókat, listázhatja az összes videót, lekérhet videoelemzéseket stb.
* Videószintű – videó az Objektumszintű hozzáférési tokenek lehetővé teszik a műveletek az adott videó. Lekérhet például videóelemzéseket, letölthet feliratokat, kérhet vezérlőket stb.

Szabályozhatja, hogy ezek a jogkivonatok csak olvasási vagy szerkesztési szerkeszthető megadásával lehetővé teszik = true/false.

### <a name="why-do-i-need-access-tokens-when-using-the-video-indexer-apis"></a>Miért kell meg hozzáférési jogkivonatok, amikor a Video Indexer API-k használatával?

Hozzáférési jogkivonatok biztonsági okokból a Video Indexer API-k használatára van szükség. Ez biztosítja, hogy a beléptetés-visszavonásokat érkeznek-e, vagy azok, akik a fiókokra vonatkozó hozzáférési jogosultságait. 

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Milyen gyakran kell egy új hozzáférési jogkivonat lekérése? Amikor lejár a hozzáférési jogkivonatok?

Hozzáférési jogkivonatok lejár óránként, ezért létre kell hoznia egy új hozzáférési jogkivonat óránként. 

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
