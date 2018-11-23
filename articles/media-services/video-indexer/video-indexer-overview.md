---
title: Mi az a Video Indexer?
titlesuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer szolgáltatásról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ae8634fbfdaa250cbabda6189c6c2eeef8e5e4f1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292377"
---
# <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

Az Azure Video Indexer egy felhőalapú alkalmazás, amelynek alapjait az Azure Media Analytics, az Azure Search, valamint a Cognitive Services (például a Face API, a Microsoft Translator, a Computer Vision API és a Custom Speech Service) adják. Lehetővé teszi, hogy elemzéseket nyerjen ki a videóiból az alább ismertetett Video Indexer-modellekkel:
 
- **Automatikus nyelvmeghatározás**: Automatikusan meghatározza a domináns beszélt nyelvet. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, kínai (egyszerűsített), japán, orosz és portugál (brazíliai). Ha a nyelv nem ismerhető fel, a Video Indexer angol nyelvűként kezeli a videót.
- **Hanganyag átirata**: Szöveggé alakítja a beszédet 10 nyelven, és engedélyezi a bővítményeket. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, kínai (egyszerűsített), japán, arab, orosz és portugál (brazíliai).
- **Hangfeliratok**: Hangfeliratokat hoz létre három formátumban: VTT, TTML, SRT.
- **Kétcsatornás feldolgozás**: Automatikusan észleli a külön átiratokat, és egyetlen idővonalba egyesíti őket.
- **Zajcsökkentés**: Feljavítja a telefonos hangfájlokat és a zajos felvételeket (Skype-szűrők alapján).
- **Átiratok testreszabása (CRIS)**: Kiterjesztett egyéni beszédfelismerési modellek betanításával és végrehajtásával iparág-specifikus átiratokat hoz létre.
- **Beszélők számbavétele**: Felismeri és beazonosítja, hogy mely szavakat mely beszélő mondta, és mikor.
- **Beszélőstatisztikák**: Statisztikai adatokat szolgáltat a beszélők beszédarányáról.
- **Vizuális szövegfelismerés (OCR)**: Kinyeri a videóban vizuálisan megjelenő szövegeket.
- **Kulcsképkockák kinyerése**: Felismeri a videók stabil kulcsképkockáit.
- **Hangulatelemzés**: Azonosítja a pozitív, negatív vagy semleges érzelmeket a beszédben és a vizuális szövegekben.
- **Vizuális tartalom moderálása**: Felismeri a felnőtt és/vagy kényes látványelemeket.
- **Kulcsszavak kinyerése**: Kinyeri a kulcsszavakat a beszédből és a vizuális szövegből.
- **Címkeazonosítás**: Azonosítja a megjelenített vizuális objektumokat és műveleteket.
- **Márkakinyerés**: Kinyeri a márkákat a beszédből és a vizuális szövegből.
- **Arcfelismerés**: Felismeri és csoportosítja a videóban megjelenő arcokat.
- **Miniatűrkinyerés az arcokhoz („legjobb arc”)**: Automatikusan meghatározza a legjobb rögzített arcot minden arccsoportban (minőség, méret és frontális helyzet alapján), és kinyeri őket képobjektumként.
- **Hírességek azonosítása**: A Video Indexer automatikusan azonosít több mint 1 millió hírességet – például vezető politikusokat, színészeket és színésznőket, sportolókat, kutatókat, üzleti és technológiai vezetőket az egész világról. E hírességekkel kapcsolatban különböző ismert webhelyeken, például a IMDB-n és a Wikipédián is találhatók adatok.
- **Fiókalapú arcfelismerés**: A Video Indexer betanít egy modellt a megadott fióknak. Ez aztán a kifejezetten az ebben a fiókban lévő videókhoz betanított modell alapján felismeri az arcokat a videókban.
- **Szöveges tartalom moderálása**: Felismeri az explicit szövegeket a hanganyag alapján készült átiratban.
- **Felvételészlelés**: Megállapítja, hogy mikor történik jelenetváltás a videóban.
- **Fekete keret észlelése**: Azonosítja a videóban megjelenő fekete kereteket.
- **Hangeffektusok**: Azonosítja a hangeffektusokat, például a tapsolást, a beszédet és a csendet.
- **Témakör-következtetés**: Kikövetkezteti a fő témaköröket az átiratokból. Tartalmazza az 1. szintű [IPTC](https://iptc.org/standards/media-topics/) taxonómiát.
- **Érzelemfelismerés**: Azonosítja az érzelmeket beszéd- és audiojelek alapján. Az érzelmek a következők lehetnek: öröm, szomorúság, harag vagy félelem.
- **Összetevők**: Rendkívül részletes összetevők széles választékát nyeri ki modellhez.
- **Fordítás**: Fordítást készít a hanganyag alapján készült átiratról 54 különböző nyelven.

Ha a Video Indexer végzett a feldolgozással és az elemzéssel, utána áttekintheti, összeválogathatja, keresheti és közzéteheti a videós elemzéseket.

A Video Indexer szolgáltatás a tartalomkezelők és a fejlesztők igényeit egyaránt figyelembe veszi. A tartalomkezelők anélkül használhatják a Video Indexer szolgáltatást a webes portálon keresztül, hogy egyetlen sornyi kódot is írniuk kellene. További információért lásd [a Video Indexer webhely használatának első lépéseit](video-indexer-get-started.md) ismertető cikket. A fejlesztőknek az API-kkal lehetőségük nyílik a skálázott tartalomfeldolgozásra. További információért lásd [a Video Indexer REST API használatának](video-indexer-use-apis.md) ismertetését. A szolgáltatás emellett lehetővé teszi a felhasználóknak, hogy widgetekkel tegyenek közzé videós streameket és kinyert elemzéseket a saját alkalmazásaikban. További információért lásd a [vizuális widgetek egy alkalmazásba történő beágyazásának](video-indexer-embed-widgets.md) ismertetését.

A szolgáltatásra meglévő AAD-, LinkedIn-, Facebook-, Google- vagy MSA-fiókkal lehet regisztrálni. További részletekért lásd [az első lépéseket](video-indexer-get-started.md).

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban bemutatunk néhány olyan forgatókönyvet, amelyek során a Video Indexer hasznos eszköznek bizonyulhat.

- Keresés – A videóból kinyert elemzésekkel javítható a keresési élmény a videótárakban. Például az elhangzott szavak és az arcok indexelése lehetőséget teremt arra, hogy a kereső megtaláljon a videókban bizonyos pillanatokat, amikor egy adott személy egy adott dolgot mond, vagy amikor két személy együtt látható. Az ilyen elemzések alapján való keresés hasznos lehet a hírügynökségek, az oktatási intézmények, a műsorszolgáltatók, a szórakoztató tartalmak tulajdonosai és a vállalati üzletági alkalmazások számára, illetve általánosságban bármely iparágban, ahol szükség lehet egy, a felhasználók által kereshető videótárra.

- Értékesítés – A Video Indexer segíthet a videók értékének növelésében. Például a hirdetési bevételekre támaszkodó iparágak (híroldalak, közösségi média stb.) relevánsabb reklámokat jeleníthetnek meg azáltal, hogy a kinyert elemzéseket jelzésként továbbítják a reklámkiszolgálók felé (pl. egy sportcipő reklámját relevánsabb egy futballmérkőzés közepén megjeleníteni, mint egy úszóversenyen).

- Felhasználói érdeklődés – A videóelemzésekkel növelhető a felhasználói érdeklődés oly módon, hogy feltüntetjük a felhasználók számára a videók releváns pillanatait. Például tegyük fel, hogy egy oktatóvideó első 30 perce a gömböket, a következő 30 perce pedig a gúlákat ismerteti. Egy diáknak, aki a gúlákról olvas, hasznosabb, ha a videó a 30. perctől kezdve indul el.

További információt [ebben a blogban](https://aka.ms/videoindexerblog) talál.

## <a name="next-steps"></a>További lépések

Készen áll a Video Indexer használatának megkezdésére. További információkért tekintse át a következő cikkeket:

- [A Video Indexer webhely használatának első lépései](video-indexer-get-started.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)
