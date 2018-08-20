---
title: Az Azure Video Indexer áttekintése | Microsoft Docs
description: Ez a témakör áttekintést nyújt a Video Indexer szolgáltatásról.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397891"
---
# <a name="what-is-video-indexer-preview"></a>Mi az a Video Indexer? (előzetes verzió)

A Video Indexer egy felhőalapú alkalmazás, amelynek alapjait az Azure Media Analytics, a Cognitive Services (például a Face API, a Microsoft Translator, a Computer Vision API és a Custom Speech Service), valamint az Azure Search adják. A következő elemzéseket nyerheti ki videóiból a mesterséges intelligencián alapuló technológiákkal:

- **Automatikus nyelvmeghatározás**: A Video Indexer képes automatikusan felismerni a videó nyelvét. Az automatikus nyelvfelismerés jelenleg az angol, spanyol, francia, német, olasz, kínai (egyszerűsített), japán és orosz nyelvet támogatja. Ha a nyelv nem ismerhető fel, a Video Indexer angol nyelvűként kezeli a videót.
- **Hanganyag átirata**: A Video Indexer beszédfelismerő funkciója lehetővé teszi a kimondott beszéd írott szöveggé alakítását. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, kínai (egyszerűsített), portugál (brazíliai), japán és orosz (a lista a későbbiekben jelentős mértékben bővülni fog). 
- **Arcok követése és azonosítása**: Az arcfelismerési technológiák lehetővé teszik az arcok felismerését egy videóban. Az észlelt arcokat a rendszer összeveti a hírességek adatbázisával, és kiértékeli, hogy mely hírességek szerepelnek a videóban. Az ügyfelek maguk is megjelölhetik azokat az arcokat, amelyek nem egyeznek meg egy híresség arcával. A Video Indexer a jelölések alapján létrehoz egy arcmodellt, és a későbbiekben képes lesz felismerni a lemodellezett arcokat a videókban.
- **Beszélők indexelése**: A Video Indexer képes felismerni és beazonosítani, hogy mely szavakat mely beszélő mondta, és mikor.
- **Vizuális szövegfelismerés**: E technológiával a Video Indexer szolgáltatás képes kinyerni a videókban megjelenő szövegeket.  
- **Beszédhangok észlelése**: A Video Indexer képes különválasztani a háttérzajt a beszédhangtól. 
- **Jelenetészlelés**: A Video Indexer egy vizuális elemzés alapján képes megállapítani, hogy egy videóban mikor történik jelenetváltás.
- **Kulcsképkockák kinyerése**: A Video Indexer automatikusan észleli a videók kulcsképkockáit. 
- **Hangulatelemzés**: A Video Indexer hangulatelemzést hajt végre a beszédfelismeréssel és az optikai karakterfelismeréssel kinyert szövegeken, és megállapítja, hogy az adott szöveg pozitív, negatív vagy semleges hangulatú, az időkódokkal együtt.
- **Fordítás**: A Video Indexer képes a hanganyag alapján készült átiratot lefordítani az egyik nyelvről a másikra. A támogatott nyelvek a következők: angol, spanyol, francia, német, olasz, kínai (egyszerűsített), portugál (brazíliai), japán és orosz. A fordítást követően a felhasználó a videólejátszóban elérhetővé teheti a feliratot a rendelkezésre álló nyelveken.
- **Vizuális tartalom moderálása**: Ez a technológia lehetővé teszi a felnőtt és/vagy kényes tartalmak észlelését a videóban, és használható a tartalmak szűréséhez. 
- **Kulcsszavak kinyerése**: A Video Indexer a beszédfelismerővel és a vizuális szövegfelismerővel készített átiratok alapján kinyeri a kulcsszavakat.
- **Címkék**: A Video Indexer címkéket biztosít a vizuális objektumokhoz (pl.: kutya, macska, asztal, autó) és tevékenységekhez (pl.: áll, fut, repül).
- **Márkák**: A Video Indexer a beszédfelismerővel és a vizuális szövegfelismerővel készített átiratok alapján kinyeri a kereskedelmi márkajelzéseket.

Ha a Video Indexer végzett a feldolgozással és az elemzéssel, utána áttekintheti, összeválogathatja, keresheti és közzéteheti a videós elemzéseket.

A Video Indexer szolgáltatás a tartalomkezelők és a fejlesztők igényeit egyaránt figyelembe veszi. A tartalomkezelők a Video Indexer szolgáltatást a webes portálon keresztül anélkül használhatják, hogy egyetlen sornyi kódot is írniuk kellene. További információért lásd [a Video Indexer portál használatának első lépéseit](video-indexer-get-started.md). A fejlesztőknek az API-kkal lehetőségük nyílik a skálázott tartalomfeldolgozásra. További információért lásd [a Video Indexer REST API használatának](video-indexer-use-apis.md) ismertetését. A szolgáltatás emellett lehetővé teszi a felhasználóknak, hogy widgetekkel tegyenek közzé videós streameket és kinyert elemzéseket a saját alkalmazásaikban. További információért lásd a [vizuális widgetek egy alkalmazásba történő beágyazásának](video-indexer-embed-widgets.md) ismertetését.

A szolgáltatásra meglévő AAD-, LinkedIn-, Facebook-, Google- vagy MSA-fiókkal lehet regisztrálni. További részletekért lásd [az első lépéseket](video-indexer-get-started.md).

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban bemutatunk néhány olyan forgatókönyvet, amelyek során a Video Indexer hasznos eszköznek bizonyulhat.

- Keresés – A videóból kinyert elemzésekkel javítható a keresési élmény a videótárakban. Például az elhangzott szavak és az arcok indexelése lehetőséget teremt arra, hogy a kereső megtaláljon a videókban bizonyos pillanatokat, amikor egy adott személy egy adott dolgot mond, vagy amikor két személy együtt látható. Az ilyen elemzések alapján való keresés hasznos lehet a hírügynökségek, az oktatási intézmények, a műsorszolgáltatók, a szórakoztató tartalmak tulajdonosai és a vállalati üzletági alkalmazások számára, illetve általánosságban bármely iparágban, ahol szükség lehet egy, a felhasználók által kereshető videótárra.

- Értékesítés – A Video Indexer segíthet a videók értékének növelésében. Például a hirdetési bevételekre támaszkodó iparágak (híroldalak, közösségi média stb.) relevánsabb reklámokat jeleníthetnek meg azáltal, hogy a kinyert elemzéseket jelzésként továbbítják a reklámkiszolgálók felé (pl. egy sportcipő reklámját relevánsabb egy futballmérkőzés közepén megjeleníteni, mint egy úszóversenyen).

- Felhasználói érdeklődés – A videóelemzésekkel növelhető a felhasználói érdeklődés oly módon, hogy feltüntetjük a felhasználók számára a videók releváns pillanatait. Például tegyük fel, hogy egy oktatóvideó első 30 perce a gömböket, a következő 30 perce pedig a gúlákat ismerteti. Egy diáknak, aki a gúlákról olvas, hasznosabb, ha a videó a 30. perctől kezdve indul el.

További információt [ebben a blogban](http://aka.ms/videoindexerblog) talál.

## <a name="next-steps"></a>További lépések

Készen áll a Video Indexer használatának megkezdésére. További információkért tekintse át a következő cikkeket:

- [A Video Indexer portál használatának első lépései](video-indexer-get-started.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)
