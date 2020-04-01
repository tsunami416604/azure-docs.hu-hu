---
title: Gyakori kérdések a Video Indexerről – Azure
titleSuffix: Azure Media Services
description: Ez a cikk választ ad az Azure Media Services videoindexerével kapcsolatos gyakori kérdésekre.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/30/2020
ms.author: juliako
ms.openlocfilehash: dd41596b6631bb63e1625325f8bec065b43881cd
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421400"
---
# <a name="video-indexer-frequently-asked-questions"></a>A Video Indexer gyakran tesz fel kérdéseket

Ez a cikk választ ad a Video Indexerről szóló gyakori kérdésekre.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

A Video Indexer egy mesterséges intelligencia szolgáltatás, amely a Microsoft Azure Media Services része. A Video Indexer több gépi tanulási modell vezénylését biztosítja, amelyek lehetővé teszik, hogy egyszerűen kinyerjen egy videóból a mély betekintést. A fejlett és pontos információk biztosítása érdekében a Video Indexer a videó több csatornáját használja: hang, beszéd és vizuális. A Video Indexer elemzési adatait számos módon fel lehet használni, például a tartalom felderíthetőségének és hozzáférhetőségének javítását, új monetizálási lehetőségek létrehozását vagy az elemzéseket használó új élmények létrehozását. A Video Indexer webalapú felületet biztosít a fiókjában lévő modellek teszteléséhez, konfigurálásához és testreszabásához. A fejlesztők rest-alapú API-val integrálhatják a Video Indexert az éles rendszerbe. 

### <a name="what-can-i-do-with-video-indexer"></a>Mire használható a Video Indexer?

A Video Indexer a médiafájlokon a következő műveleteket hajthatja végre:

* Beszéd felismerése és kinyerése, valamint a felszólalók azonosítása.
* A képernyőn megjelenő szöveg azonosítása és kibontása egy videóban.
* Objektumok észlelése egy videofájlban.
* Azonosítsa a márkákat (például: Microsoftot) a hangsávokból és a képernyőn megjelenő szövegből egy videóban.
* Arcok észlelése és felismerése hírességek adatbázisából és az arcok felhasználó által definiált adatbázisából.
* Kivonása témák tárgyalt, de nem feltétlenül említi a hang-és videotartalom.
* A hangsávból feliratvagy felirat létrehozása.

További információt és további videoindexelő funkciókat az [Áttekintés című témakörben talál.](video-indexer-overview.md)

### <a name="how-do-i-get-started-with-video-indexer"></a>Hogyan kezdhetem el a Video Indexer t?

A Video Indexer egy ingyenes próbaverziót tartalmaz, amely 600 percet biztosít a webes felületen és 2400 percet az API-n keresztül. [Bejelentkezhet a Video Indexer webalapú felületére,](https://www.videoindexer.ai/) és kipróbálhatja azt bármilyen webes identitás használatával, azure-előfizetés beállítása nélkül. 

A videók és audiolegyek nagy méretekben való indexeléséhez csatlakoztathatja a Video Indexert egy fizetős Microsoft Azure-előfizetéshez. Az árképzésről további információt az [árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) oldalon talál.

Az első lépésekről az [Első lépések](video-indexer-get-started.md)című részben talál további információt.

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Szükségem van kódolási ismeretekre a Video Indexer használatához?

A Video Indexer webalapú felületen **kódolás nélkül**értékelheti, konfigurálhatja és kezelheti fiókját.  Ha készen áll az összetettebb alkalmazások fejlesztésére, a [Video Indexelő API-val](https://api-portal.videoindexer.ai/) integrálhatja a Video Indexert saját alkalmazásaiba, webhelyeibe vagy [egyéni munkafolyamataiba kiszolgáló nélküli technológiák, például az Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) vagy az Azure Functions használatával.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Gépi tanulási ismeretekre van szükségem a Video Indexer használatához?

Nem, a Video Indexer több gépi tanulási modell egyetlen folyamatba való integrálását biztosítja. A video- vagy hangfájl videoindexelőn keresztüli indexelése egy megosztott idővonalon kinyert elemzési adatok teljes készletét kéri le, anélkül, hogy gépi tanulási ismereteket vagy az ügyfél részéről szükséges algoritmusokról szóló ismereteket szerezne.

### <a name="what-media-formats-does-video-indexer-support"></a>Milyen médiaformátumokat támogat a Video Indexer?

A Video Indexer támogatja a leggyakoribb médiaformátumokat. További részletekért tekintse meg az [Azure Media Kódoló szabványos formátumok](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) listáját.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hogyan tölthetek fel médiafájlokat a Video Indexerbe?

A Video Indexer webalapú portálon a fájlfeltöltési párbeszédpanelen vagy a forrásfájlt közvetlenül tartalmazó URL-címre mutatva tölthet fel médiafájlt (lásd [például](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Az iFrame-vagy beágyazási kód használatával a médiatartalmat tartalmazó URL-címek nem fognak működni (lásd a [példát).](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) A Video Indexer API megköveteli, hogy adja meg a bemeneti fájlt egy URL-cím vagy egy bájt tömb. Az API-t használó URL-en keresztül történő feltöltések legfeljebb 10 GB-ra korlátozódnak, de nincs időtartamuk. További információkért kérjük, olvassa el ezt az [útmutatót](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Mennyi ideig tart a Video Indexer az elemzések kinyerése a médiából?

A video- vagy hangfájlok indexeléséhez szükséges idő , mind a Video Indexer API, mind a Video Indexer webalapú felület használatával, több paramétertől függ, például a fájl hosszától és minőségétől, a fájlban található elemzések számától, a rendelkezésre [álló fenntartott egységek](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) számától, valamint attól, hogy a [streamelési végpont](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) engedélyezve van-e vagy sem. Azt javasoljuk, hogy futtasson néhány tesztfájlt a saját tartalmával, és hozzon egy átlagot, hogy jobb ötlettel járjon el.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Létrehozhatok testre szabott munkafolyamatokat a folyamatok automatizálásához a Video Indexer segítségével?

Igen, integrálhatja a Video Indexer-t olyan kiszolgáló nélküli technológiákba, mint a Logic Apps, a Flow és [az Azure Functions.](https://azure.microsoft.com/services/functions/) A [Logikai alkalmazás](https://azure.microsoft.com/services/logic-apps/) és a [Flow-összekötők](https://flow.microsoft.com/en-us/) a Video Indexerhez [itt](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)talál további részleteket. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Mely Azure-régiókban érhető el a Video indexelő?

Megtekintheti, hogy mely Azure-régiók videoindexelő érhető el a [régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) oldalon.

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Testre szabhatom a Video Indexer modelleket az adott használati esethez? 

Igen. A Video Indexer testreszabhatja néhány rendelkezésre álló modellek jobban megfelel az Ön igényeinek. 

Például a Személy modell támogatja out-of-the-box 1.000.000 arcok híresség felismerés, de azt is betaníthatja felismerni más arcok, amelyek nem szerepelnek az adatbázisban. 

További információt a [Személyek,](customize-person-model-overview.md) [márkák](customize-brands-model-overview.md)és [nyelvi](customize-language-model-overview.md) modellek testreszabásáról szóló cikkekben talál. 

### <a name="what-is-the-sla-for-video-indexer"></a>Mi a videoindexelő SLA-ja?

Az Azure Media Service SLA-ja lefedi a Video Indexelőt, és megtalálható az [SLA-oldalon.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) Az SLA csak a Video Indexer fizetős fiókokra vonatkozik, és nem vonatkozik az ingyenes próbaverzióra.

## <a name="privacy-questions"></a>Adatvédelmi kérdések

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>A video- és hangfájlokat a Video Indexer indexeli tárolja?

Igen, ha nem törli a fájlt a Video Indexer programból, akár a Video Indexer webhely, akár az API használatával, a rendszer tárolja a video- és hangfájlokat. Az ingyenes próbaverzióhoz az indexelt video- és hangfájlok az USA keleti régiójában tárolódnak. Ellenkező esetben a video- és hangfájlok az Azure-előfizetés tárfiókjában tárolódnak.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Törölhetem a Video Indexer Portalon tárolt fájljaimat?

Igen, bármikor törölheti a video- és hangfájlokat, valamint a Video Indexer által kinyert metaadatokat és elemzéseket. Miután törölt egy fájlt a Video Indexerből, a fájl és annak metaadatai és elemzési adatai véglegesen törlődnek a Video Indexer programból. Ha azonban saját biztonsági mentési megoldást valósított meg az Azure storage-ban, a fájl az Azure-tárolóban marad.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Szabályozhatom a felhasználók hozzáférését a Video Indexer-fiókomhoz?

Igen, csak a fiókadminisztrátorok hívhatnak meg és vonhatnak vissza személyeket a fiókjukba, valamint hozzárendelést adhatnak hozzá azokhoz, akik szerkesztési jogosultságokkal rendelkeznek, és akik csak olvasási hozzáféréssel rendelkeznek.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Ki férhet hozzá a Video Indexer által indexelt és/vagy tárolt video- és hangfájljaimhoz, valamint a kinyert metaadatokhoz és elemzésekhez?

A nyilvános adatvédelmi beállítással rendelkező video- vagy hangtartalmakhoz bárki hozzáférhet, aki rendelkezik a video- vagy hangtartalomra és annak elemzési adataira mutató hivatkozással. Az adatvédelmi beállításként privát ként tartalmazó video- vagy hangtartalmakhoz csak azok a felhasználók férhetnek hozzá, akiket meghívtak a video- vagy hangtartalom fiókjába. A tartalom adatvédelmi beállítása a Video Indexer által kinyert metaadatokra és elemzési adatokra is vonatkozik. Az adatvédelmi beállítást a video- vagy hangfájl feltöltésekor adhatja hozzá. Az indexelés után is módosíthatja az adatvédelmi beállítást.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Milyen hozzáféréssel rendelkezik a Microsoft a Video Indexer által indexelt és/vagy tárolt video- vagy hangfájljaimhoz, valamint a kinyert metaadatokhoz és elemzésekhez?

Az [Azure Online Services feltételei](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) értelmében Ön teljes mértékben a tartalom tulajdonában van, és a Microsoft csak az Ön tartalmaihoz, valamint a Video Indexelő által a tartalomból kinyert metaadatokhoz és elemzési adatokhoz fér hozzá az OST és a Microsoft adatvédelmi nyilatkozata szerint.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>A Videó indexelő-fiókomban általam létrehozandó egyéni modellek más fiókok számára is elérhetők?

 Nem, a fiókjában létrehozott egyéni modellek nem érhetők el más fiók számára. A Video Indexer jelenleg lehetővé teszi egyéni [márkák,](customize-brands-model-overview.md) [nyelv](customize-language-model-overview.md)és [személymodellek](customize-person-model-overview.md) készítését a fiókjában. Ezek a modellek csak abban a fiókban érhetők el, amelyben létrehozta a modelleket.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>A tartalom indexelése a Video Indexer az Azure-régióban, ahol én használ video indexelő?

Igen, a tartalom és az elemzések az Azure-régióban maradnak, kivéve, ha rendelkezik egy manuális konfigurációval az Azure-előfizetésben, amely több Azure-régiót használ. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Mi a Video Indexer adatvédelmi irányelvei?

A Video Indexer re is vonatkozik a [Microsoft adatvédelmi nyilatkozata](https://privacy.microsoft.com/privacystatement). Az adatvédelmi nyilatkozat ismerteti a Microsoft által feldolgozott személyes adatokat, a Microsoft által azokat feldolgozott adatokat, valamint azt, hogy a Microsoft milyen célokra dolgozza fel azokat. Ha többet szeretne megtudni az adatvédelemről, látogasson el a [Microsoft Adatvédelmi központba.](https://www.microsoft.com/trustcenter)

### <a name="what-certifications-does-video-indexer-have"></a>Milyen minősítésekkel rendelkezik a Video Indexer?

A Video Indexer jelenleg rendelkezik AZ SOC tanúsítvánnyal. A Video Indexer tanúsítványának áttekintéséhez olvassa el a [Microsoft Adatvédelmi központot.](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure)

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Mi a különbség a privát és a nyilvános videók között? 

Amikor videókat tölt fel a Video Indexerbe, két adatvédelmi beállítás közül választhat: privát és nyilvános. A nyilvános videók bárki számára elérhetők, beleértve a névtelen és azonosítatlan felhasználókat is. A privát a fiók tagokra korlátozódik. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Megpróbáltam feltölteni egy videót nyilvánosként, és nem megfelelő vagy sértő tartalomra jelölték meg, mit jelent ez? 

Amikor feltölt egy videót a Video Indexerbe, algoritmusok és modellek automatikus tartalomelemzést végeznek annak érdekében, hogy ne jelenjenek meg nyilvánosan nem megfelelő tartalmak. Ha egy videó gyanúsnak tűnik, és explicit tartalmat tartalmaz, akkor nem lehet nyilvánosként beállítani. A fióktagjai azonban továbbra is hozzáférhetnek privát videóként (megtekinthetiazt, letöltheti konklúdiés kinyert összetevők, és egyéb műveleteket végezhetnek a fióktagjai számára).   

A videó nyilvános hozzáférésre való beállításához a következőket teheti: 

* Hozzon létre saját felületi réteget (például alkalmazást vagy webhelyet), és használja azt a Video Indexer szolgáltatással való interakcióhoz. Így a videó privát marad a portálunkon, és a felhasználók az Ön felületén keresztül kommunikálhatnak vele. Például továbbra is megkaphatja az elemzéseket, vagy engedélyezheti a videó megtekintését a saját felületén. 
* Kérje a tartalom emberi felülvizsgálatát, ami a korlátozás eltávolítását eredményezné, feltéve, hogy a tartalom nem egyértelmű. 

    Ez a beállítás akkor fedezhető fel, ha a Video Indexer webhelyet közvetlenül a felhasználók használják felületi rétegként, valamint nyilvános (nem hitelesített) megtekintésre. 

## <a name="api-questions"></a>API-val kapcsolatos kérdések

### <a name="what-apis-does-video-indexer-offer"></a>Milyen API-kat kínál a Video Indexer?

A Video Indexer API-jai lehetővé teszik az indexelést, a metaadatok kinyerését, az eszközkezelést, a fordítást, a beágyazást, a modellek testreszabását és még sok mást. A Video Indexer API használatával kapcsolatos részletesebb információkért tekintse meg a [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/)webhelyen.

### <a name="what-client-sdks-does-video-indexer-offer"></a>Milyen ügyfél SDK-kat kínál a Video Indexer?

Jelenleg nincs elérhető ügyfél SDK.There are currently no client SDKs offered. A Video Indexer csapat dolgozik az SDK-k és azt tervezi, hogy hamarosan teljesíteni őket.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hogyan kezdhetem el a Video Indexer API-ját?

Kövesse [bemutató: első lépések a Video Indexer API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Mi a különbség a Video Indexer API és az Azure Media Service v3 API között?

Jelenleg vannak átfedések a Video Indexer API és az Azure Media Service v3 API által kínált funkciók. A két [szolgáltatás](compare-video-indexer-with-media-services-presets.md)összehasonlításáról itt talál további információt.

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Mi az API-hozzáférési jogkivonat, és miért van rá szükségem?

A Video Indexer API egy engedélyezési API-t és egy Operations API-t tartalmaz. Az engedélyezési API olyan hívásokat tartalmaz, amelyek hozzáférési jogkivonatot biztosítanak. A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

Access jogkivonatok szükségesek a videoindexelő API-k biztonsági célokra való használatához. Ez biztosítja, hogy a hívások Öntől vagy a fiókjához hozzáférési engedéllyel rendelkező kedőktől érkezzenek. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Mi a különbség a fiók-hozzáférési jogkivonat, a felhasználói hozzáférési jogkivonat és a video-hozzáférési jogkivonat között?

* Fiókszint – fiókszintű hozzáférési jogkivonatok segítségével fiók- vagy videószinten végezhet műveleteket. Például feltöltheti a videót, felsorolhatja az összes videót, videóelemzéseket kaphat.
* Felhasználói szint – a felhasználói szintű hozzáférési jogkivonatok lehetővé teszik a felhasználói szintű műveletek elvégzését. Lekérhet például társított fiókokat.
* Videószint – videószintű hozzáférési jogkivonatok segítségével egy meghatározott videón végezhet műveleteket. Lekérhet például videóelemzéseket, letölthet feliratokat, kérhet vezérlőket stb.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Milyen gyakran kell új hozzáférési jogkivonatot beszereznem? Mikor járnak le a hozzáférési jogkivonatok?

A hozzáférési jogkivonatok óránként lejárnak, ezért óránként új hozzáférési jogkivonatot kell létrehoznia. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Milyen bejelentkezési lehetőségek vannak a Video Indexer Fejlesztői portálon?

Az Azure AD, a Microsoft-fiók, a Google-fiók vagy a Facebook-fiók használatával is bejelentkezhet. 

Miután regisztrálta az e-mail fiókját egy identitásszolgáltató használatával, nem használhatja ezt az e-mail fiókot egy másik identitásszolgáltatónál.

## <a name="billing-questions"></a>Számlázással kapcsolatos kérdések

### <a name="how-much-does-video-indexer-cost"></a>Mennyibe kerül a Video Indexer?

A Video Indexer egy egyszerű használatalapú díjszabási modellt használ az indexelt tartalombevitel időtartama alapján. További díjak merülhetnek fel a kódolásért, a streamelésért, a tárolásért, a hálózati használatért és a média számára fenntartott egységekért. További információt az [árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) oldalon talál.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Mikor kell fizetnia a Video Indexer használatáért?

Amikor indexelendő videót küld, a felhasználó meghatározza az indexelést videóelemzésnek, hangelemzésnek vagy mindkettőnek. Ez határozza meg, hogy mely sk-eket terhelik meg. Ha a feldolgozás során kritikus szintű hiba lép fel, válaszként egy hibakódot ad vissza. Ebben az esetben nem történik számlázás.  Kritikus hibát okozhat egy hiba a kódunkban, vagy egy kritikus hiba egy belső függőség a szolgáltatás. Hibák, például a hibás azonosítás vagy betekintést kinyerése nem tekinthető kritikusnak, és a válasz ad vissza. Minden olyan esetben, amikor érvényes (nem hibakód) választ ad vissza, számlázás történik.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>A Video Indexer ingyenes próbaverziót kínál?

Igen, a Video Indexer ingyenes próbaverziót kínál, amely teljes körű szolgáltatást és API-funkciókat biztosít. Van egy kvóta 600 perc értékű videók web-alapú felület felhasználók és 2400 perc API-felhasználók számára. 

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
