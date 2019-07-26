---
title: Mi az a Video Indexer?
titlesuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer szolgáltatásról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: b0d0df5d113b1d75602022085b8bb17133f07333
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422984"
---
# <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

Az Azure Video Indexer egy felhőalapú alkalmazás, amelynek alapjait az Azure Media Analytics, az Azure Search, valamint a Cognitive Services (például a Face API, a Microsoft Translator, a Computer Vision API és a Custom Speech Service) adják. Lehetővé teszi, hogy a videókból kinyerje az elemzéseket Video Indexer videó-és hangmodellek használatával, az alábbiakban ismertetett módon:
  
## <a name="video-insights"></a>Videó-felismerések

- **Arcfelismerés**: Észleli és csoportosítja a videóban szereplő arcokat.
- **Híresség azonosítása**: Video Indexer automatikusan azonosítja a több mint 1 000 000 hírességet – többek között a világ vezetőit, színészeit, színésznőit, sportolóit, kutatóit, üzleti és műszaki vezetőit világszerte. E hírességekkel kapcsolatban különböző ismert webhelyeken, például a IMDB-n és a Wikipédián is találhatók adatok.
- **Fiók alapú Arcfelismerés**: Video Indexer egy adott fiókhoz tartozó modellt. Ezután a betanított modell alapján felismeri a videóban található arcokat. További információ: személyre szabott [modell testreszabása a video Indexer](customize-person-model-with-website.md) webhelyről és [személyre szabott modell testreszabása a video Indexer API-val](customize-person-model-with-api.md).
- **Bélyegképek kinyerése arcok számára** ("legjobb arc"): A automatikusan azonosítja a legjobban rögzített arcot az arcok egyes csoportjaiban (a minőség, a méret és az elülső pozíció alapján), és képobjektumként kinyeri.
- **Vizuális szöveg felismerése** (OCR): A videóban vizuálisan megjelenő szöveg kibontása.
- **Vizuális tartalom moderálása**: Észleli a felnőtt és/vagy a zamatos vizualizációkat.
- **Címkék azonosítása**: A megjelenített vizuális objektumokat és műveleteket azonosítja.
- **Jelenet szegmentálása**: meghatározza, hogy a jelenet Mikor változik a videóban a vizuális célzások alapján. Egy jelenet egyetlen eseményt ábrázol, és egy sor egymást követő felvételből áll, amelyek szemantikai kapcsolatban állnak egymással. 
- **Shot észlelés**: meghatározza, hogy mikor változik a videó a vizualizációs útmutatók alapján. A shot olyan keretek sorozata, amelyek ugyanabba a mozgóképes kamerából származnak. További információ: [jelenetek, felvételek és kulcsképek](scenes-shots-keyframes.md).
- **Fekete keret észlelése**: A videóban bemutatott fekete képkockákat azonosítja.
- **Kulcsképek**kibontása: Egy videóban található stabil kulcsképek észlelése.
- **Működés közbeni kreditek**: a TV-műsorok és-filmek végén található működés közbeni kreditek kezdetét és végét határozza meg.

## <a name="audio-insights"></a>Audio-elemzések

- **Automatikus nyelvfelismerés**: Automatikusan azonosítja a domináns beszéd nyelvét. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, orosz és brazíliai portugál. Ha a nyelv nem azonosítható bizalommal, Video Indexer feltételezi, hogy a beszélt nyelv angol. További információ: [Language Identification Model](language-identification-model.md).
- **Hang**átírása: 12 nyelven alakítja át a beszédet szöveggé, és lehetővé teszi a bővítmények használatát. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, Arab, Orosz, brazíliai portugál, hindi és koreai.
- **Kódolt feliratok**: Három formátumban hozza létre a kódolt feliratozást: VTT, TTML, SRT.
- **Két csatorna feldolgozása**: Automatikus észlelés, külön átirat és egyesítés egyetlen idővonalra.
- **Zaj csökkentése**: Törli a telefonos hang-vagy zajos felvételeket (a Skype-szűrők alapján).
- **Átirat testreszabása** (CRIS): Az egyéni beszédeket a szöveges modellekhez az iparági specifikus átiratok létrehozásához. További információkért lásd: [nyelvi modell testreszabása a video Indexer](customize-language-model-with-website.md) webhelyről és a [nyelvi modell testreszabása a video Indexer API](customize-language-model-with-api.md)-kkal.
- **Beszélő enumerálása**: Leképezi és megérti, hogy a beszélő mely szavakat és mikor szólalt meg.
- A **beszélő statisztikái**: Statisztikákat biztosít a beszélők beszédének arányáról.
- **Szöveges tartalom moderálása**: A hangátiratban szereplő explicit szöveget észleli.
- Hangeffektusok: Meghatározza a hangeffektusokat, például a tapsot, a beszédet és a csendet.
- **Érzelem észlelése**: A beszédfelismerésen alapuló érzelmeket azonosítja (mit mondott) és a hangtónust (ahogy azt mondják).  Az érzelmek a következők lehetnek: öröm, szomorúság, harag vagy félelem.
- **Fordítás**: A hangátirat fordításait 54 különböző nyelvekre hozza létre.

## <a name="audio-and-video-insights-multi-channels"></a>Hang-és video-elemzések (több csatorna)

Ha az egyik csatorna részleges eredménye alapján indexeli az adott modelleket, elérhető lesz

- **Kulcsszavak**kinyerése: Kulcsszavak kinyerése a beszéd és a vizualizáció szövege alapján.
- **Márkák**kinyerése: Kinyeri a márkákat a beszéd és a vizualizáció szövege alapján.
- **Témakör következtetése**: A fő témakörök következtetéseit az átiratokból hozza. A rendszer az első szintű IPTC-besorolást is tartalmazza.
- Összetevők: Az egyes modellekhez tartozó "következő részletességű" összetevők részletes készletének kibontása.
- **Hangulat elemzése**: Pozitív, negatív és semleges érzelmeket azonosít a beszéd és a vizualizáció szövege alapján.
 
Ha a Video Indexer végzett a feldolgozással és az elemzéssel, utána áttekintheti, összeválogathatja, keresheti és közzéteheti a videós elemzéseket.

A Video Indexer szolgáltatás a tartalomkezelők és a fejlesztők igényeit egyaránt figyelembe veszi. A tartalomkezelők anélkül használhatják a Video Indexer szolgáltatást a webes portálon keresztül, hogy egyetlen sornyi kódot is írniuk kellene. További információért lásd [a Video Indexer webhely használatának első lépéseit](video-indexer-get-started.md) ismertető cikket. A fejlesztőknek az API-kkal lehetőségük nyílik a skálázott tartalomfeldolgozásra. További információért lásd [a Video Indexer REST API használatának](video-indexer-use-apis.md) ismertetését. A szolgáltatás emellett lehetővé teszi a felhasználóknak, hogy widgetekkel tegyenek közzé videós streameket és kinyert elemzéseket a saját alkalmazásaikban. További információért lásd a [vizuális widgetek egy alkalmazásba történő beágyazásának](video-indexer-embed-widgets.md) ismertetését.

A szolgáltatásra meglévő AAD-, LinkedIn-, Facebook-, Google- vagy MSA-fiókkal lehet regisztrálni. További részletekért lásd [az első lépéseket](video-indexer-get-started.md).

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban bemutatunk néhány olyan forgatókönyvet, amelyek során a Video Indexer hasznos eszköznek bizonyulhat.

- Keresés – A videóból kinyert elemzésekkel javítható a keresési élmény a videótárakban. Például az elhangzott szavak és az arcok indexelése lehetőséget teremt arra, hogy a kereső megtaláljon a videókban bizonyos pillanatokat, amikor egy adott személy egy adott dolgot mond, vagy amikor két személy együtt látható. Az ilyen elemzések alapján való keresés hasznos lehet a hírügynökségek, az oktatási intézmények, a műsorszolgáltatók, a szórakoztató tartalmak tulajdonosai és a vállalati üzletági alkalmazások számára, illetve általánosságban bármely iparágban, ahol szükség lehet egy, a felhasználók által kereshető videótárra.
- Tartalom létrehozása – a videókból kinyert elemzések és az olyan tartalmak hatékony létrehozása, mint például a pótkocsik, a közösségi médiatartalmak, a hírforrások stb. a szervezet archívumában meglévő tartalomból 
- Értékesítés – A Video Indexer segíthet a videók értékének növelésében. Például a hirdetési bevételekre támaszkodó iparágak (híroldalak, közösségi média stb.) relevánsabb reklámokat jeleníthetnek meg azáltal, hogy a kinyert elemzéseket jelzésként továbbítják a reklámkiszolgálók felé (pl. egy sportcipő reklámját relevánsabb egy futballmérkőzés közepén megjeleníteni, mint egy úszóversenyen).
- Felhasználói érdeklődés – A videóelemzésekkel növelhető a felhasználói érdeklődés oly módon, hogy feltüntetjük a felhasználók számára a videók releváns pillanatait. Például tegyük fel, hogy egy oktatóvideó első 30 perce a gömböket, a következő 30 perce pedig a gúlákat ismerteti. Egy diáknak, aki a gúlákról olvas, hasznosabb, ha a videó a 30. perctől kezdve indul el.

## <a name="next-steps"></a>További lépések

Készen áll a Video Indexer használatának megkezdésére. További információkért tekintse át a következő cikkeket:

- [A Video Indexer webhely használatának első lépései](video-indexer-get-started.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)
