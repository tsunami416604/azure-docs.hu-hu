---
title: Mi az az Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Azure Media Services Video Indexer szolgáltatásról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/17/2019
ms.author: juliako
ms.openlocfilehash: a30fa858ea7befa2363e0ac2742e16d46037d0e0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123510"
---
# <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

A Azure Media Services Video Indexer az Azure Cognitive Services-ra épülő AI-megoldás. A Video Indexer lehetővé teszi a részletes elemzések kinyerését (az adatok elemzése és a kódolási képességek nélkül) a gépi tanulási modellek használatával több csatornán (hang, vokál, vizualizáció). Tovább testreszabhatja és betaníthatja a modelleket. A szolgáltatás lehetővé teszi a mélyreható keresést, csökkenti a működési költségeket, lehetővé teszi az új bevételi lehetőségeket, új felhasználói élményt nyújtva a videók nagy archívumában (alacsony belépési korlátokkal). 

Az Video Indexerekkel való kinyerésének megkezdéséhez létre kell hoznia egy fiókot, és fel kell töltenie a videókat. A videók Video Indexer való feltöltésekor a vizualizációkat és a hanganyagokat különböző AI-modellek futtatásával elemezze. Ahogy Video Indexer elemzi a videót, a modellek által kinyert elemzéseket.

![Folyamatábra](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Mire használhatom a Video Indexer?

Video Indexer az adatok számos forgatókönyvre alkalmazhatók, többek között a következők:

* Részletes *Keresés* – a videóból kinyert adatok segítségével javíthatja a keresési élményt a videó-tárak között. Például a kimondott szavak és arcok indexelése lehetővé teszi a keresési élmény megkeresését egy videóban, ahol egy személy bizonyos szavakat beszélt, vagy ha két személy együtt volt látható. Az ilyen elemzések alapján való keresés hasznos lehet a hírügynökségek, az oktatási intézmények, a műsorszolgáltatók, a szórakoztató tartalmak tulajdonosai és a vállalati üzletági alkalmazások számára, illetve általánosságban bármely iparágban, ahol szükség lehet egy, a felhasználók által kereshető videótárra.
* *Tartalom létrehozása*: hozzon létre pótkocsikat, jelöljön ki orsókat, közösségi médiatartalmakat vagy híreket a tartalom alapján, video Indexer kivonatokat. A kulcsképek, a jelenetek jelölői és az időbélyegek a személyek és a címkék megjelenéséhez a létrehozási folyamat sokkal simább és egyszerűbb, és lehetővé teszi, hogy közvetlenül a létrehozott tartalom érdekes részeit kapja meg.
* *Kisegítő lehetőségek*: azt, hogy szeretné-e elérhetővé tenni a tartalmat a fogyatékkal élők számára, vagy ha azt szeretné, hogy a tartalmak különböző nyelveken keresztül legyenek kiosztva különböző régiókban, használhatja a videó által biztosított átírást és fordítást. indexelő több nyelven.
* *Bevétel*: A Video Indexer segítségével növelheti a videók értékét. Például az ad-bevételre támaszkodó iparágak (például a média, a közösségi média stb.) a releváns hirdetéseket a kinyert elemzések segítségével, az ad-kiszolgáló további jeleiként használva érhetik el.
* *Tartalom moderálása*: a szöveges és a vizuális tartalom moderálási modelljeinek használatával a felhasználók biztonságban maradhatnak a nem megfelelő tartalomtól, és ellenőrizhetik, hogy a közzétett tartalom megfelel-e a szervezet értékeinek. Automatikusan blokkolhat bizonyos videókat, vagy riasztást kaphat a felhasználóknak a tartalommal kapcsolatban. 
* *Javaslatok*: A videóval kapcsolatos bepillantást a felhasználók bevonásának javítására lehet használni. Ha az egyes videókat további metaadatokkal címkézi, a felhasználók a legfontosabb videókra is rámutatnak, és kiemelik a videónak az igényeiknek megfelelő részét. 

## <a name="features"></a>Szolgáltatások

Az alábbi lista azokat az elemzéseket tartalmazza, amelyeket Video Indexer videó és hangmodell használatával kereshet a videókból:

### <a name="video-insights"></a>Videó-felismerések

* **Arcfelismerés**: Észleli és csoportosítja a videóban szereplő arcokat.
* **Híresség azonosítása**: Video Indexer automatikusan azonosítja a több mint 1 000 000 hírességet – többek között a világ vezetőit, színészeit, színésznőit, sportolóit, kutatóit, üzleti és műszaki vezetőit világszerte. E hírességekkel kapcsolatban különböző ismert webhelyeken, például a IMDB-n és a Wikipédián is találhatók adatok.
* **Fiók alapú Arcfelismerés**: Video Indexer egy adott fiókhoz tartozó modellt. Ezután a betanított modell alapján felismeri a videóban található arcokat. További információ: [személyre szabott modell testreszabása a video Indexer webhelyről](customize-person-model-with-website.md) és [személyre szabott modell testreszabása a video Indexer API-val](customize-person-model-with-api.md).
* **Bélyegképek kinyerése arcok számára** ("legjobb arc"): A automatikusan azonosítja a legjobban rögzített arcot az arcok egyes csoportjaiban (a minőség, a méret és az elülső pozíció alapján), és képobjektumként kinyeri.
* **Vizuális szöveg felismerése** (OCR): A videóban vizuálisan megjelenő szöveg kibontása.
* **Vizuális tartalom moderálása**: Észleli a felnőtt és/vagy a zamatos vizualizációkat.
* **Címkék azonosítása**: A megjelenített vizuális objektumokat és műveleteket azonosítja.
* **Jelenet szegmentálása**: meghatározza, hogy a jelenet Mikor változik a videóban a vizuális célzások alapján. Egy jelenet egyetlen eseményt ábrázol, és egy sor egymást követő felvételből áll, amelyek szemantikai kapcsolatban állnak egymással.
* **Shot észlelés**: meghatározza, hogy mikor változik a videó a vizualizációs útmutatók alapján. A shot olyan keretek sorozata, amelyek ugyanabba a mozgóképes kamerából származnak. További információ: jelenetek, felvételek és kulcsképek.
* **Fekete keret észlelése**: A videóban bemutatott fekete képkockákat azonosítja.
* **Kulcsképek kibontása**: Egy videóban található stabil kulcsképek észlelése.
* **Működés közbeni kreditek**: a TV-műsorok és-filmek végén található működés közbeni kreditek kezdetét és végét határozza meg.

### <a name="audio-insights"></a>Audio-elemzések

* **Automatikus nyelvfelismerés**: Automatikusan azonosítja a domináns beszéd nyelvét. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, orosz és brazíliai portugál. Ha a nyelv nem azonosítható bizalommal, Video Indexer feltételezi, hogy a beszélt nyelv angol. További információ: [Language Identification Model](language-identification-model.md).
* **Hang átírása**: 12 nyelven alakítja át a beszédet szöveggé, és lehetővé teszi a bővítmények használatát. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, Arab, Orosz, brazíliai portugál, hindi és koreai.
* **Kódolt feliratok**: Három formátumban hozza létre a kódolt feliratozást: VTT, TTML, SRT.
* **Két csatorna feldolgozása**: Automatikus észlelés, külön átirat és egyesítés egyetlen idővonalra.
* **Zaj csökkentése**: Törli a telefonos hang-vagy zajos felvételeket (a Skype-szűrők alapján).
* **Átirat testreszabása** (CRIS): Az egyéni beszédeket a szöveges modellekhez az iparági specifikus átiratok létrehozásához. További információkért lásd: [nyelvi modell testreszabása a video Indexer webhelyről](customize-language-model-with-website.md) és a [nyelvi modell testreszabása a video Indexer API](customize-language-model-with-api.md)-kkal.
* **Beszélő enumerálása**: Leképezi és megérti, hogy a beszélő mely szavakat és mikor szólalt meg.
* A **beszélő statisztikái**: Statisztikákat biztosít a beszélők beszéd arányáról.
* **Szöveges tartalom moderálása**: A hangátiratban szereplő explicit szöveget észleli.
* Hangeffektusok: Meghatározza a hangeffektusokat, például a tapsot, a beszédet és a csendet.
* **Érzelem észlelése**: A beszédfelismerésen alapuló érzelmeket azonosítja (mit mondott) és a hangtónust (ahogy azt mondják). Az érzelem lehet a Joy, a szomorúság, a düh vagy a félelem.
* **Fordítás**: A hangátirat fordításait 54 különböző nyelvekre hozza létre.

### <a name="audio-and-video-insights-multi-channels"></a>Hang-és video-elemzések (több csatorna)

Ha az egyik csatorna részleges eredménye alapján indexeli az adott modelleket, elérhető lesz

* **Kulcsszavak kinyerése**: Kulcsszavak kinyerése a beszéd és a vizualizáció szövege alapján.
* **Márkák kinyerése**: Kinyeri a márkákat a beszéd és a vizualizáció szövege alapján.
* **Témakör következtetése**: A fő témakörök következtetéseit az átiratokból hozza. A rendszer az első szintű IPTC-besorolást is tartalmazza.
* Összetevők: Az egyes modellekhez tartozó "következő részletességű" összetevők részletes készletének kibontása.
* **Hangulat elemzése**: Pozitív, negatív és semleges érzelmeket azonosít a beszéd és a vizualizáció szövege alapján.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hogyan szerezhetem be a Video Indexer?

A Video Indexer képességek elérését háromféle módon érheti el: 

* Video Indexer portál – egy könnyen használható megoldás, amely lehetővé teszi a termék kiértékelését, a fiók kezelését és a modellek testreszabását. 

    További információ a portálról: Ismerkedés [a video Indexer webhellyel](video-indexer-get-started.md).  
* API-integráció – a Video Indexer összes funkciója elérhető egy REST API, hogy lehetővé váljon a megoldás integrálása az alkalmazásokba és az infrastruktúrába. 

    A fejlesztőknek szóló első lépésekhez tekintse meg a [Video Indexer REST API használata](video-indexer-use-apis.md)című témakört. 
* Emendable widget – lehetővé teszi, hogy beágyazza a video Indexer-betekintést, a lejátszót és a szerkesztői élményt az alkalmazásba. 

    További információ: [Visual widgetek beágyazása az alkalmazásba](video-indexer-embed-widgets.md). 

Ha a webhelyet használja, az adatok metaadatokként lesznek hozzáadva, és láthatók a portálon. Ha API-kat használ, az eredmények JSON-fájlként érhetők el. 

## <a name="next-steps"></a>További lépések

Készen áll a Video Indexer használatának megkezdésére. További információkért tekintse át a következő cikkeket:

- [A Video Indexer webhely használatának első lépései](video-indexer-get-started.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)
