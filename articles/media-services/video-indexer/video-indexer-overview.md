---
title: Mi az a Video Indexer?
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt az Azure Media Services videoindexelő szolgáltatásáról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/02/2020
ms.author: juliako
ms.openlocfilehash: efd8386f464bfdf2ac27d3be07e6572dc27952e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163590"
---
# <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

A Video Indexer (VI) az Azure Media Services AI-megoldása és az Azure Cognitive Services márkarésze. A Video Indexer lehetővé teszi a mély elemzések kinyerését (adatelemzés vagy kódolási készségek nélkül) több csatornán (hang, ének, vizuális) alapuló gépi tanulási modellek használatával. Tovább testreszabhatja és betaníthatja a modelleket. A szolgáltatás lehetővé teszi a mély keresést, csökkenti a működési költségeket, lehetővé teszi az új monetizálási lehetőségeket, és új felhasználói élményt hoz létre a videók nagy archívumain (alacsony belépési korlátokkal).

Az elemzések kinyerésének megkezdéséhez létre kell hoznod egy fiókot, és videókat kell feltöltened. Amikor feltöltöd videóidat a Video Indexerbe, a különböző AI-modellek futtatásával elemzi a vizualizációkat és a hangokat is. Ahogy a Video Indexer elemzi a videót, az AI-modellek által kinyert elemzési adatok.

Az alábbi ábra egy illusztráció, és nem egy technikai magyarázatot, hogyan video indexelő működik a háttérben.

![Az Azure Media Services videoindexelő folyamatábrája](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos emlékeztetőként be kell tartania a Video Indexer használata során érvényes összes vonatkozó jogszabályt, és nem használhatja a Video Indexert vagy bármely Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy amely másokra nézve káros lehet.

Mielőtt bármilyen videót/képet feltöltene a Video Indexerbe, rendelkeznie kell a videó/kép használatához szükséges összes megfelelő joggal, beleértve , amennyiben a törvény előírja, az egyének (ha vannak) összes szükséges beleegyezését a videóban/képen, adataik felhasználásához, feldolgozásához és tárolásához a Video Indexerben és az Azure-ban. Egyes joghatóságok különleges jogi követelményeket írhatnak elő bizonyos adatkategóriák, például biometrikus adatok gyűjtésére, online feldolgozására és tárolására vonatkozóan. Mielőtt a Video Indexert és az Azure-t speciális jogi követelmények hatálya alá tartozó adatok feldolgozásához és tárolásához használná, biztosítania kell az Önre vonatkozó ilyen jogi követelmények betartását.

Ha többet szeretne megtudni a Megfelelőségről, az adatvédelemről és a biztonságról a Video Indexer ben, látogasson el a Microsoft [Adatvédelmi központba.](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) A Microsoft adatvédelmi kötelezettségeivel, adatkezelési és adatmegőrzési gyakorlatával kapcsolatban, beleértve az adatok törlésének módját is, olvassa el a Microsoft [adatvédelmi nyilatkozatát,](https://privacy.microsoft.com/PrivacyStatement)az [online szolgáltatási feltételeket](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") és [az adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). A Video Indexer használatával Ön elfogadja, hogy magára nézve kötelezőnek fogadja el az OST, a DPA és az Adatvédelmi nyilatkozat.

## <a name="what-can-i-do-with-video-indexer"></a>Mire használható a Video Indexer?

A Video Indexer elemzései számos forgatókönyvre alkalmazhatók, köztük a következőkre:

* *Mélykeresés:* A videóból kinyert elemzési adatok segítségével javíthatja a videótárban való keresési élményt. A kimondott szavak és arcok indexelése például lehetővé teszi a keresési élményt, amikor olyan pillanatokat talál egy videóban, ahol egy személy bizonyos szavakat mondott, vagy amikor két embert láttak együtt. A videókból származó ilyen elemzések alapján végzett keresés a hírügynökségek, oktatási intézmények, műsorszolgáltatók, szórakoztatótartalmak tulajdonosai, vállalati üzletági alkalmazások és általában minden olyan iparág számára alkalmazható, amely rendelkezik olyan videokönyvtárral, amely ellen a felhasználóknak keresnie kell.
* *Tartalomkészítés*: Előzeteseket, kiemelési tárcsákat, közösségi média tartalmakat vagy hírklipeket hozhat létre a videóindexelő által a tartalomból kinyert információk alapján. A kulcsképek, a jelenetek jelölői és az időbélyegek sokkal simábbá és egyszerűbbé teszik a létrehozási folyamatot, és lehetővé teszik, hogy eljusson a videó azon részeihez, amelyekre szüksége van a létrehozott tartalomhoz.
* *Kisegítő lehetőségek*: Akár fogyatékkal élők számára szeretné elérhetővé tenni a tartalmat, vagy azt szeretné, hogy a tartalmat különböző régiókban, különböző nyelveken terjesszék, a videóindexelő által több nyelven biztosított átírást és fordítást is használhatja.
* *Monetization*: Video Indexer segíthet növelni a videók értékét. A hirdetési bevételekre támaszkodó iparágak (hírmédia, közösségi média és így tovább) például releváns hirdetéseket jeleníthetnek meg a kinyert elemzési adatok további jelekként való használatával a hirdetésszerver számára.
* *Tartalommoderálás:* Használjon szöveges és vizuális tartalommoderálási modelleket, hogy a felhasználók biztonságban legyenek a nem megfelelő tartalommal szemben, és ellenőrizze, hogy a közzétett tartalom megfelel-e a szervezet értékeinek. Automatikusan letilthatjbizonyos videókat, vagy figyelmeztetheti a felhasználókat a tartalomról.
* *Javaslatok: A*videóelemzésekkel javíthatja a felhasználók elköteleződését azáltal, hogy kiemeli a megfelelő videópillanatokat a felhasználók számára. Ha minden videót további metaadatokkal jelöl meg, javasolhatod a felhasználóknak a legrelevánsabb videókat, és kiemelheted a videó nak az igényeiknek megfelelő részeit.

## <a name="features"></a>Szolgáltatások

Az alábbi lista azokat az elemzési adatokat mutatja be, amelyeket a videóindexelő video- és hangmodelljeivel szerezhet be a videóiból:

### <a name="video-insights"></a>Videóelemzés

* **Arcfelismerés**: Felismeri és csoportosítja a videóban megjelenő arcokat.
* **Hírességek azonosítása**: A Video Indexer automatikusan több mint 1 millió hírességet azonosít – például a világ vezetőit, színészeit, színésznőit, sportolókat, kutatókat, üzleti és technológiai vezetőket szerte a világon. Az adatok ezekről a hírességekről is megtalálható a különböző honlapokon (IMDB, Wikipedia, és így tovább).
* **Fiókalapú arcfelismerés**: A Video Indexer betanít egy modellt a megadott fióknak. Ezután felismeri az arcokat a videó alapján a betanított modell. További információt a [Személy modell testreszabása a Video Indexelő webhelyről](customize-person-model-with-website.md) és [Egy személy modell testreszabása a Video Indexer API-val című témakörben](customize-person-model-with-api.md)talál.
* **Az arcok miniatűrkinyerése** ("legjobb arc"): Automatikusan azonosítja a legjobban rögzített arcokat az arcok minden csoportjában (a minőség, a méret és az elülső pozíció alapján), és képeszközként kinyeri azt.
* **Vizuális szövegfelismerés** (OCR): Kinyeri a videóban vizuálisan megjelenített szöveget.
* **Vizuális tartalom moderálása**: Felismeri a felnőtt és/vagy kényes látványelemeket.
* **Címkeazonosítás**: Azonosítja a megjelenített vizuális objektumokat és műveleteket.
* **Jelenetszegmentálás**: Azt határozza meg, hogy egy jelenet mikor változik meg a videóban a vizuális jelzések alapján. A jelenet egyetlen eseményt ábrázol, és egymást követő felvételek sorozatából áll, amelyek szemantikailag kapcsolódnak egymáshoz.
* **Lövés észlelése**: Azt határozza meg, hogy a felvétel mikor változik a videóban a vizuális jelzések alapján. A felvétel egy sor képkockák vett ugyanabból a mozgókép kamera. További információt a [Jelenetek, felvételek és kulcsképek című témakörben talál.](scenes-shots-keyframes.md)
* **Fekete keret észlelése**: Azonosítja a videóban megjelenő fekete kereteket.
* **Kulcsképkockák kinyerése**: Felismeri a videók stabil kulcsképkockáit.
* **Gördülő hitelek**: Azonosítja az elején és végén a gördülő hitelek végén TV-műsorok és filmek.
* **Animált karakterek észlelése** (előnézet): Az animált tartalom karaktereinek észlelése, csoportosítása és felismerése a [Cognitive Services egyéni látással](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)való integrációja révén. További információ: [Animált karakterfelismerés](animated-characters-recognition.md).
* **Szerkesztői lövés típusú felismerés:** Címkézés felvételek alapján a típus (mint a széles lövés, közepes lövés, közelről, extrém közelről, két lövés, több ember, kültéri és beltéri, és így tovább). További információ: [Szerkesztői felvétel típusának észlelése](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Hangbetekintések

* **Automatikus nyelvmeghatározás**: Automatikusan meghatározza a domináns beszélt nyelvet. A támogatott nyelvek közé tartozik az angol, a spanyol, a francia, a német, az olasz, a kínai (egyszerűsített), a japán, az orosz és a brazil portugál. Ha a nyelv nem azonosítható magabiztosan, a Video Indexer feltételezi, hogy a beszélt nyelv az angol. További információ: [Language identification model](language-identification-model.md).
* **Többnyelvű beszédfelismerés és átírás** (előnézet): Automatikusan azonosítja a beszélt nyelvet a hangkülönböző szegmenseiben. Elküldi a médiafájl minden egyes szegmensét átírandó, majd egyesíti az átírást egy egységes átiratra. További információt a [Többnyelvű tartalom automatikus azonosítása és átírása](multi-language-identification-transcription.md)című témakörben talál.
* **Hangátírás**: A beszédet 12 nyelven alakítja át szöveggé, és engedélyezi a bővítményeket. A támogatott nyelvek közé tartozik az angol, a spanyol, a francia, a német, az olasz, a kínai (egyszerűsített), a japán, az arab, az orosz, a brazil portugál, a hindi és a koreai.
* **Hangfeliratok**: Hangfeliratokat hoz létre három formátumban: VTT, TTML, SRT.
* **Kétcsatornás feldolgozás:** Az automatikus automatikus észleli a külön átiratot, és egyetlen ütemtervbe olvad.
* **Zajcsökkentés**: Törli a telefonos hang- vagy zajos felvételeket (Skype-szűrők alapján).
* **Átirat testreszabása** (CRIS): Egyéni beszéd-szövegmodelleket képez ki, hogy iparág-specifikus átiratokat hozzon létre. További információt a [Nyelvi modell testreszabása a Video Indexelő webhelyről](customize-language-model-with-website.md) és [A nyelvi modell testreszabása a Video Indexer API-kkal című témakörben](customize-language-model-with-api.md)talál.
* **Hangszóró felsorolása**: Térképek és megérti, hogy melyik hangszóró beszélt, amely szavakat és mikor.
* **Előadói statisztika**: A felszólalók beszédarányára vonatkozó statisztikákat tartalmaz.
* **Szöveges tartalom moderálása**: Felismeri az explicit szövegeket a hanganyag alapján készült átiratban.
* **Hanghatások**: Azonosítja a hanghatásokat, például a tapsot, a beszédet és a csendet.
* **Érzelem felismerése:** Azonosítja érzelmek alapján beszéd (mi azt mondta) és a hang tonalitás (hogyan mondják). Az érzelem lehet öröm, szomorúság, harag, vagy a félelem.
* **Fordítás**: Fordítást készít a hanganyag alapján készült átiratról 54 különböző nyelven.

### <a name="audio-and-video-insights-multi-channels"></a>Hang- és videoelemzések (többcsatornás)

Ha egy csatornával indexel, ezeka modellek részleges eredmény érhető el.

* **Kulcsszavak kinyerése:** Kivonatok kulcsszavak beszéd és vizuális szöveget.
* **Elnevezett entitások kibontása:** Kivonja a márkákat, a helyeket és az embereket a beszédből és a vizuális szövegből természetes nyelvi feldolgozással (NLP).
* **Témakör-következtetés**: Kikövetkezteti a fő témaköröket az átiratokból. A 2.
* **Összetevők**: Rendkívül részletes összetevők széles választékát nyeri ki modellhez.
* **Hangulatelemzés**: Azonosítja a pozitív, negatív vagy semleges érzelmeket a beszédben és a vizuális szövegekben.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hogyan kezdhetem el a Video Indexer t?

A Video Indexelő funkcióit háromféleképpen érheti el:

* Video Indexer portál: Könnyen használható megoldás, amely lehetővé teszi a termék kiértékelését, a fiók kezelését és a modellek testreszabását.

    A portálról további információt a [Video Indexer webhely első lépései című témakörben talál.](video-indexer-get-started.md)  

* API-integráció: A Video Indexer összes funkciója elérhető egy REST API-n keresztül, amely lehetővé teszi a megoldás integrálását az alkalmazásokba és az infrastruktúrába.

    Fejlesztőként való kezdéshez olvassa el [a Video Indexer REST API használata](video-indexer-use-apis.md).

* Beágyazható widget: Lehetővé teszi a Video Indexer-elemzési, lejátszó- és szerkesztőélmények beágyazását az alkalmazásba.

    További információt a [Vizuális widgetek beágyazása az alkalmazásban című témakörben talál.](video-indexer-embed-widgets.md)

Ha a webhelyet használja, az elemzési adatok metaadatként kerülnek hozzáadásra, és láthatók a portálon. API-k használata esetén az elemzések JSON-fájlként érhetők el.

## <a name="next-steps"></a>További lépések

Készen áll a Video Indexer használatának megkezdésére. További információkért tekintse át a következő cikkeket:

- [Ismerkedés a Video Indexer webhelyével.](video-indexer-get-started.md)
- [Tartalom feldolgozása videoindexelő REST API-val.](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása az alkalmazásba.](video-indexer-embed-widgets.md)
