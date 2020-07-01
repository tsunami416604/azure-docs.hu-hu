---
title: Mi a Computer Vision? – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cfd8dd7474cf7926ef2230e6be53a967a97645db
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85554401"
---
# <a name="what-is-computer-vision"></a>Mi a Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Computer Vision szolgáltatása lehetővé teszi a fejlesztők számára, hogy olyan fejlett algoritmusokhoz férhessenek hozzá, amelyek képeket dolgoznak fel, és az Önt érdeklő vizuális funkciók alapján adnak vissza adatokat. A Computer Vision például megállapíthatja, hogy egy rendszerkép tartalmaz-e felnőtt tartalmat, megkeresheti-e az adott márkákat vagy objektumokat, vagy megkeresheti az emberi arcokat.

Az alkalmazásban Computer Vision az ügyféloldali kódtár SDK-n keresztül, vagy közvetlenül a REST API meghívásával is használhatja. Ez az oldal széles körben ismerteti, hogy mit tehet a Computer Vision.

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision a digitális eszközök kezeléséhez

A Computer Vision számos digitális Asset Management-(DAM-) forgatókönyvet képes kihasználni. A DAM a gazdag média-eszközök rendszerezésének, tárolásának és lekérésének üzleti folyamata, valamint a digitális jogok és engedélyek kezelése. Előfordulhat például, hogy egy vállalat szeretné csoportosítani és azonosítani a képeket a látható emblémák, arcok, objektumok, színek és egyebek alapján. Vagy előfordulhat, hogy automatikusan kívánja [létrehozni a képekhez tartozó feliratokat](./Tutorials/storage-lab-tutorial.md) és kulcsszavakat csatolni, hogy azok kereshetőek legyenek. Az Cognitive Services, az Azure Cognitive Search és az intelligens jelentéskészítés szolgáltatást használó teljes körű megoldásért tekintse meg a Knowledge Reporting [Solution-gyorsító útmutatót](https://github.com/Azure-Samples/azure-search-knowledge-mining) a githubon. Más DAM-példákért tekintse meg a [Computer Vision megoldási sablonok](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) tárházát.

## <a name="analyze-images-for-insight"></a>Képek elemzése eredmények kinyeréséhez

Elemezheti a képeket, hogy információkat szolgáltasson a vizualizáció szolgáltatásairól és jellemzőiről. Az alábbi táblázat összes funkcióját a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API tartalmazza.

| Műveletek | Leírás |
| ------ | ----------- |
|**[Vizuális jellemzők címkézése](concept-tagging-images.md)**|Azonosíthatja és címkézheti egy kép vizualizációs funkcióit több ezer felismerhető objektumból, az élő dolgokból, a díszletből és a tevékenységekből álló készletből. Ha a címkék nem egyértelműek vagy nem általános ismeretek, az API-válasz a címke kontextusának tisztázására szolgáló tippeket tartalmaz. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.|
|**[Objektumok észlelése](concept-object-detection.md)**| Az objektum-észlelés hasonlít a címkézéshez, de az API az egyes alkalmazott címkékhez tartozó határoló mezők koordinátáit adja vissza. Ha például egy rendszerkép kutyát, macskát és személyt tartalmaz, az észlelési művelet a képen látható koordinátákkal együtt listázza ezeket az objektumokat. Ezt a funkciót használhatja a képek objektumai közötti további kapcsolatok feldolgozásához. Azt is megtudhatja, hogy mikor van több példánya ugyanazzal a címkével egy képen.|
|**[Márkák észlelése](concept-brand-detection.md)**|A képeken vagy videókban található kereskedelmi márkákat több ezer globális emblémából álló adatbázisból azonosíthatja. Ezt a funkciót használhatja például arra, hogy felderítse, mely márkák a legnépszerűbbek a közösségi médiában, vagy a média termékeinek legelterjedtebb eleme.|
|**[Képek kategorizálása](concept-categorizing-images.md)**|Felismeri és kategorizálja a teljes képet egy [kategóriaelnevezési rendszer](Category-Taxonomy.md) segítségével, szülő/gyermek hierarchiák használatával. A kategóriák egyedül vagy az új címkéző modellekkel együtt is használhatóak.<br/>Jelenleg az egyetlen támogatott nyelv a képek címkézéséhez és kategorizálásához az angol.|
|**[Képek leírása](concept-describing-images.md)**|Leírást hoz létre a teljes képhez a felhasználók által is olvasható nyelven, teljes mondatok használatával. A Computer Vision algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza.|
|**[Arcfelismerés](concept-detecting-faces.md)** |Felismeri a képen található arcokat, és információval szolgál minden felismert arccal kapcsolatban. A Computer Vision visszaküldi az arcok koordinátáit, a téglalapot és a képen szereplő személy nemét és életkorát.<br/>Computer Vision a [Face](/azure/cognitive-services/face/) szolgáltatás funkcióinak egy részét biztosítja. A Face szolgáltatást részletesebb elemzésre is használhatja, például az arc azonosítására és az észlelésre.|
|**[Képek típusának észlelése](concept-detecting-image-types.md)**|Észleli a kép jellemzőit, például hogy a kép vonalrajz-e, vagy annak a valószínűségét, hogy a kép ClipArt.|
|**[Tartományspecifikus tartalom észlelése](concept-detecting-domain-content.md)**|Tartománymodellek segítségével észleli és felismeri a kép tartományspecifikus tartalmát, például a hírességeket vagy nevezetességeket. Ha például egy rendszerkép személyeket tartalmaz, Computer Vision a hírességek egy tartományi modelljével határozzák meg, hogy a rendszerképben észlelt személyek ismert hírességek-e.|
|**[Színséma észlelése](concept-detecting-color-schemes.md)**|A képen használt színek elemzése. A Computer Vision eldönti, hogy a kép fekete-fehér-e vagy színes, illetve színes képek esetében felismeri a domináns és a kiegészítő színeket.|
|**[Miniatűr létrehozása](concept-generating-thumbnails.md)**|Elemzi a kép tartalmát, hogy megfelelő miniatűrt tudjon létrehozni a képhez. Computer Vision először egy kiváló minőségű miniatűrt hoz létre, majd a képen található objektumokat elemzi a *fontos terület*meghatározásához. Computer Vision ezután megvágja a képet, hogy megfeleljen a fontos terület követelményeinek. Igény szerint a létrehozott miniatűr az eredeti képtől eltérő oldalaránnyal is rendelkezhet.|
|**[A fontos terület beszerzése](concept-generating-thumbnails.md#area-of-interest)**|Egy rendszerkép tartalmának elemzése a *fontos terület*koordinátáinak visszaadásához. A rendszerkép kivágása és a miniatűr létrehozása helyett a Computer Vision a régió határolókeret koordinátáit adja vissza, így a hívó alkalmazás igény szerint módosíthatja az eredeti rendszerképet.|

## <a name="detect-text-from-images"></a>Képekből származó szöveg észlelése

A Computer Vision [text észlelési](concept-recognizing-text.md) API-k API-val kinyerheti a nyomtatott és a kézírásos szöveget a képekből egy géppel olvasható karakteres adatfolyamba. Az olvasási API a legújabb modelleket használja, és számos felületen és háttéren, például nyugtán, plakáton, névjegykártyán, levélen és táblán is működik szöveggel. A két szöveges észlelési API támogatja a nyomtatott szövegek [több nyelven](./language-support.md)való kinyerését.

## <a name="moderate-content-in-images"></a>Képek tartalmának moderálása

A Computer Vision használatával [felderítheti a felnőtt tartalmakat](concept-detecting-adult-content.md) egy rendszerképben, és a különböző besorolások megbízhatósági pontszámait is visszaküldheti. A tartalom megjelölésére vonatkozó küszöbérték beállítható egy csúszó skálán, hogy megfeleljenek a beállításoknak.

## <a name="use-containers"></a>Tárolók használata

[Computer Vision tárolók használatával](computer-vision-how-to-install-containers.md) helyileg ismeri fel a nyomtatott és a kézírásos szövegeket, ha a szabványos Docker-tárolót az adataihoz közelebb telepíti.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Computer Vision a következő követelményeknek megfelelő képeket képes elemezni:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont
  - Az olvasási API esetében a rendszerkép méretei 50 x 50 és 10000 x 10000 képpont közé kell, hogy legyenek.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Computer Vision szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>További lépések

A Computer Vision használatának első lépései a gyors üzembe helyezési útmutatót követve:

- [Gyors útmutató: Computer Vision .NET ügyféloldali kódtár](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Gyors útmutató: Computer Vision Python ügyféloldali kódtár](./quickstarts-sdk/client-library.md?pivots=programming-language-python)
- [Rövid útmutató: Computer Vision Java ügyféloldali kódtár](./quickstarts-sdk/client-library.md?pivots=programming-language-java)
