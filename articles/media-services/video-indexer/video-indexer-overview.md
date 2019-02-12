---
title: Mi az a Video Indexer?
titlesuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt a Video Indexer szolgáltatásról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 78b362eb75ab4e0ca17045f8c32761a65897d449
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000182"
---
# <a name="what-is-video-indexer"></a>Mi az a Video Indexer?

Az Azure Video Indexer egy felhőalapú alkalmazás, amelynek alapjait az Azure Media Analytics, az Azure Search, valamint a Cognitive Services (például a Face API, a Microsoft Translator, a Computer Vision API és a Custom Speech Service) adják. Lehetővé teszi, hogy elemzéseket nyerjen ki a videóiból az alább ismertetett Video Indexer-modellekkel:
 
- **Automatikus nyelvfelismerés**: Automatikusan meghatározza a domináns használja a beszélt nyelv. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, kínai (egyszerűsített), japán, orosz és portugál (brazíliai). Ha a nyelv nem ismerhető fel, a Video Indexer angol nyelvűként kezeli a videót.
- **Hanganyag átírása**: 12 nyelvet szöveggé alakít speech, és lehetővé teszi a bővítmények. Támogatott nyelvek angol, spanyol, francia, német, olasz, egyszerűsített kínai, japán, arab, orosz, portugál (brazíliai), Hindi és koreai.
- **Kódolt feliratok**: Kódolt feliratok három formátumban hozza létre: VTT, TTML, SRT.
- **Két feldolgozó channel**: Automatikusan észleli, külön, átirat és összevonása egyetlen idővonalán.
- **A zaj csökkentésére**:  Törli a telefonos hang- vagy zajos felvételeket (a Skype-szűrők alapján).
- **Átirat testreszabási (CRIS)**: Betanítja, és végrehajtja a kiterjesztett egyéni beszéd szöveges modellekhez hozhat létre az átiratok iparágra jellemző.
- **Hangszóró enumerálás**:  Vannak leképezve, és tisztában van azzal, mely speaker küllő melyik szavak és mikor.
- **Hangszóró statisztika**: Előadók speech arányok statisztika biztosít.
- **Vizuális szövegek felismerése (OCR)**: Kiolvassa a videóban vizuálisan megjelenített szöveget.
- **Kulcsképkockák**: Videó stabil kulcsképek észleli.
- **Hangulatelemzés**: A beszéd- és vizuális szöveg pozitív, negatív és semleges hangulati azonosítja.
- **Vizuális tartalom-jóváhagyás**: Észleli a felnőtt és/vagy pikáns vizualizációkat.
- **Kulcsszavak kinyerése**: A kulcsszavak kigyűjti a beszéd- és vizuális szöveget.
- **Szoftverazonosító címkék**: Azonosítja a vizuális objektumok és műveletek jelennek meg.
- **Kibontási védjegyzéssel**: Kiolvassa a márka a beszéd- és vizuális szöveget.
- **Arcfelismerés**: Észleli és csoportokat a videóban megjelenő arcokat.
- **Arcok ("ajánlott face") a miniatűr kinyerési**: Automatikusan azonosítja az egyes csoportokban téglalapot (a minőségét, mérete és elülső pozíció alapján) legmegfelelőbb rögzített face, és csomagolja ki rendszerképet eszközként.
- **Hírességek azonosító**: A video Indexer automatikusan meghatározza a több mint 1 millió hírességek – például a világ vezető, aktorok és színésznők, elnyert érmek számának trendjeit, kutatók, üzleti és a világ vezető technológiai szakértőnk. E hírességekkel kapcsolatban különböző ismert webhelyeken, például a IMDB-n és a Wikipédián is találhatók adatok.
- **Tárfiókalapú arcfelismerés**: A video Indexer betanítja olyan modell, egy meghatározott fiók. Ez aztán a kifejezetten az ebben a fiókban lévő videókhoz betanított modell alapján felismeri az arcokat a videókban.
- **Szöveges tartalom-jóváhagyás**: A hang átiratok explicit szöveg észleli.
- **Elejtett észlelési**: Azt határozza meg, ha egy helyszín módosításokat a videóban.
- **Keret észlelési fekete**: A videóban megjelenő fekete keretek azonosítja.
- **Hang hatások**: Például az aktuális vastaps, beszédfelismerési és csend hang hatások azonosítja.
- **A témakör következtetésekhez**: Lehetővé teszi az átiratok legfontosabb témakörök következtetésekhez. Tartalmazza az 1. szintű [IPTC](https://iptc.org/standards/media-topics/) taxonómiát.
- **Érzelemfelismerő**: Beszéd- és audio jelek alapján érzelmeket azonosít. Az érzelmek a következők lehetnek: öröm, szomorúság, harag vagy félelem.
- **Összetevők**: Kinyeri a "következő részletességi szint" széles skáláját minden, a modellek összetevőket.
- **Fordítási**: A hang átiratok 54 más nyelvekre fordításának hoz létre.

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
