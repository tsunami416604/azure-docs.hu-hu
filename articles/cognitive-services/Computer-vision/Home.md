---
title: Mi az a Computer Vision API? – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: db62f003f7eb8dd272305b86712cf1d721797114
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719539"
---
# <a name="what-is-computer-vision"></a>Mi a Computer Vision?

Az Azure Computer Vision szolgáltatása lehetővé teszi a fejlesztőknek, hogy a képeket feldolgozó és a visszaadott adatokat használó fejlett algoritmusokhoz hozzáférjenek. A kép elemzéséhez feltöltheti a képet, vagy megadhatja a kép URL-címét. A képfeldolgozó algoritmusok számos különböző módon elemezheti a tartalmakat, attól függően, hogy milyen vizuális funkciók érdeklik. A Computer Vision például megállapíthatja, hogy egy rendszerkép tartalmaz-e felnőtt tartalmat, vagy megtalálhatja a képen látható összes emberi arcot.

A Computer Vision az alkalmazásban natív SDK használatával vagy közvetlenül a REST API meghívásával használhatja. Ez az oldal széles körben ismerteti, hogy mit tehet a Computer Vision.

## <a name="analyze-images-for-insight"></a>Képek elemzése a betekintéshez

A képeket elemezheti a vizualizáció funkcióinak és jellemzőinek észleléséhez és elemzéséhez. Az alábbi táblázat összes funkcióját a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API tartalmazza.

| Action | Leírás |
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

## <a name="extract-text-from-images"></a>Szöveg kinyerése a képekből

A Computer Vision [READ API](concept-recognizing-text.md#read-api) használatával kinyerheti a nyomtatott és a kézírásos szöveget a képekből egy géppel olvasható karakteres adatfolyamba. Az olvasási API a legújabb modelleket használja, és számos felületen és háttérbeli szöveggel dolgozik, például a nyugták, a plakátok, az üzleti kártyák, a levelek és a táblák. Jelenleg az egyetlen támogatott nyelv az angol.

Az optikai karakterfelismerési [(OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) API használatával több nyelven is kinyerheti a nyomtatott szöveget. Ha szükséges, az OCR kijavította a felismert szöveg forgását, és megadja az egyes szavak keretének koordinátáit. Az OCR 25 nyelvet támogat, és automatikusan észleli a felismert szöveg nyelvét.



## <a name="moderate-content-in-images"></a>Mérsékelt tartalom a képekben

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

- [Rövid útmutató: Computer Vision .NET SDK](quickstarts-sdk/csharp-sdk.md)
- [Rövid útmutató: Python SDK Computer Vision](quickstarts-sdk/python-sdk.md)
- [Rövid útmutató: Java SDK Computer Vision](quickstarts-sdk/java-sdk.md)
