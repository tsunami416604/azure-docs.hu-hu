---
title: Mi a Computer Vision? - Számítógépes látás
titleSuffix: Azure Cognitive Services
description: A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af49e80766d7ac4bd2d9ed677a2571fb0a5a3189
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80131752"
---
# <a name="what-is-computer-vision"></a>Mi a Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Computer Vision szolgáltatása hozzáférést biztosít a fejlesztők számára a képeket feldolgozó és információkat visszaadó speciális algoritmusokhoz, az Önt érdeklő vizuális funkcióktól függően. A Computer Vision például meg tudja állapítani, hogy egy kép tartalmaz-e felnőtt tartalmat, vagy megtalálhatja a képen lévő összes emberi arcot.

A Computer Vision az alkalmazásban egy natív SDK-n keresztül, vagy a REST API-t közvetlenül hivatkozva használhatja. Ez az oldal széles körben ismerteti, mit lehet csinálni a Computer Vision.

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision digitális eszközkezeléshez

A Computer Vision számos digitális eszközkezelési (DAM) forgatókönyvet képes bekapcsolni. A DAM a multimédiás eszközök szervezésének, tárolásának és visszakeresésének, valamint a digitális jogok és engedélyek kezelésének üzleti folyamata. Előfordulhat például, hogy egy vállalat a látható logók, lapok, objektumok, színek és így tovább alapján szeretné csoportosítani és azonosítani a képeket. Előfordulhat, hogy automatikusan [szeretne feliratokat létrehozni a képekhez,](./Tutorials/storage-lab-tutorial.md) és kulcsszavakat csatolni, hogy azok kereshetővé legyenek. A Cognitive Services, az Azure Cognitive Search és az intelligens jelentések használatával egy all-in-one DAM-megoldásért tekintse meg a [GitHub tudásbányászati megoldásgyorsító útmutatóját.](https://github.com/Azure-Samples/azure-search-knowledge-mining) További DAM-példák: [A Computer Vision Solution Templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) repository.

## <a name="analyze-images-for-insight"></a>Képek elemzése eredmények kinyeréséhez

A képek elemzésével észlelheti és betekintést nyújthat azok vizuális jellemzőibe és jellemzőibe. Az alábbi táblázatösszes szolgáltatását a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API biztosítja.

| Műveletek | Leírás |
| ------ | ----------- |
|**[Vizuális jellemzők címkézése](concept-tagging-images.md)**|Azonosítsa és címkézze a kép vizuális jellemzőit több ezer felismerhető objektumból, élőlényből, tájból és műveletből. Ha a címkék nem egyértelműek vagy nem általános ismeretek, az API-válasz tippeket ad a címke kontextusának tisztázásához. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.|
|**[Objektumok észlelése](concept-object-detection.md)**| Az objektumészlelés hasonló a címkézéshez, de az API minden alkalmazott címkéhez visszaadja a határolókeret koordinátáit. Ha például egy kép egy kutyát, macskát és személyt tartalmaz, a Észlelés művelet felsorolja ezeket az objektumokat a képen lévő koordinátákkal együtt. Ezzel a funkcióval további kapcsolatokat dolgozhat fel a képen lévő objektumok között. Azt is lehetővé teszi, hogy tudja, ha több példánya van ugyanannak a címkének egy képen.|
|**[Márkák észlelése](concept-brand-detection.md)**|Azonosítsa a több ezer globális logót tartalmazó adatbázisból származó képekben vagy videókban szereplő kereskedelmi márkákat. Ezzel a funkcióval például felfedezheti, hogy mely márkák a legnépszerűbbek a közösségi médiában, vagy a legelterjedtebbek a médiatermékek elhelyezésében.|
|**[Képek kategorizálása](concept-categorizing-images.md)**|Felismeri és kategorizálja a teljes képet egy [kategóriaelnevezési rendszer](Category-Taxonomy.md) segítségével, szülő/gyermek hierarchiák használatával. A kategóriák egyedül vagy az új címkéző modellekkel együtt is használhatóak.<br/>Jelenleg az egyetlen támogatott nyelv a képek címkézéséhez és kategorizálásához az angol.|
|**[Képek leírása](concept-describing-images.md)**|Leírást hoz létre a teljes képhez a felhasználók által is olvasható nyelven, teljes mondatok használatával. A Computer Vision algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza.|
|**[Arcfelismerés](concept-detecting-faces.md)** |Felismeri a képen található arcokat, és információval szolgál minden felismert arccal kapcsolatban. A Computer Vision visszaküldi az arcok koordinátáit, a téglalapot és a képen szereplő személy nemét és életkorát.<br/>A Computer Vision a [Face](/azure/cognitive-services/face/) szolgáltatás funkcióegy részét biztosítja. A Face szolgáltatás segítségével részletesebb elemzés, például az arcazonosítás és a póz észlelése.|
|**[Képek típusának észlelése](concept-detecting-image-types.md)**|Észleli a kép jellemzőit, például hogy a kép vonalrajz-e, vagy annak a valószínűségét, hogy a kép ClipArt.|
|**[Tartományspecifikus tartalom észlelése](concept-detecting-domain-content.md)**|Tartománymodellek segítségével észleli és felismeri a kép tartományspecifikus tartalmát, például a hírességeket vagy nevezetességeket. Ha például egy kép személyeket tartalmaz, a Computer Vision tartománymodellt használhat a hírességek számára annak megállapítására, hogy a képen észlelt személyek ismert hírességek-e.|
|**[Színséma észlelése](concept-detecting-color-schemes.md)**|A képen használt színek elemzése. A Computer Vision eldönti, hogy a kép fekete-fehér-e vagy színes, illetve színes képek esetében felismeri a domináns és a kiegészítő színeket.|
|**[Miniatűr létrehozása](concept-generating-thumbnails.md)**|Elemzi a kép tartalmát, hogy megfelelő miniatűrt tudjon létrehozni a képhez. A Computer Vision először kiváló minőségű miniatűrt hoz létre, majd elemzi a képen belüli objektumokat, hogy meghatározza *az érdeklődési területet.* Computer Vision majd növények a képet, hogy illeszkedjen a követelményeknek a terület érdekes. Igény szerint a létrehozott miniatűr az eredeti képtől eltérő oldalaránnyal is rendelkezhet.|
|**[Szerezd meg a terület érdekes](concept-generating-thumbnails.md#area-of-interest)**|Elemezze a kép tartalmát, hogy visszaadja az *érdeklődési terület*koordinátáit. A kép körülvágása és miniatűr létrehozása helyett a Computer Vision visszaadja a terület határolókeret-koordinátáit, így a hívó alkalmazás igény szerint módosíthatja az eredeti képet.|

## <a name="extract-text-from-images"></a>Szöveg kinyerése képekből

A Computer Vision [Read](concept-recognizing-text.md#read-api) API segítségével nyomtatott és kézzel írt szöveget nyerhet ki a képekből egy géppel olvasható karakterfolyamba. A Read API a legújabb modelleket használja, és különböző felületeken és háttereken, például nyugtákon, posztereken, névjegykártyákon, leveleken és táblákon is működik szövegekkel. Jelenleg az angol és a spanyol az egyetlen támogatott nyelv.

Az [optikai karakterfelismerő (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) API-val több nyelven is kinyerheti a nyomtatott szöveget. Szükség esetén az OCR kijavítja a felismert szöveg elforgatását, és megadja az egyes szavak keretkoordinátáit. Az OCR 25 nyelvet támogat, és automatikusan felismeri a felismert szöveg nyelvét.

## <a name="moderate-content-in-images"></a>Képek tartalmának moderálása

A Computer Vision segítségével észlelheti a [felnőtteknek szóló tartalmat](concept-detecting-adult-content.md) egy képen, és megbízhatósági pontszámokat adhat vissza a különböző besorolásokhoz. A tartalom megjelölésének küszöbértéke csúszó skálán állítható be, hogy megfeleljen az Ön igényeinek.

## <a name="use-containers"></a>Tárolók használata

[A Computer Vision tárolók segítségével](computer-vision-how-to-install-containers.md) helyileg felismerheti a nyomtatott és kézzel írt szöveget, ha az adatokhoz közelebb iszlúdizált Docker-tárolót telepít.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Computer Vision a következő követelményeknek megfelelő képeket képes elemezni:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont
  - Az Olvasási API esetében a kép méretének 50 x 50 és 10000 x 10000 képpont között kell lennie.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

A Cognitive Services hez csakúgy, mint a Cognitive Services, a Computer Vision szolgáltatást használó fejlesztőknek is tisztában kell lenniük a Microsoft ügyféladatokra vonatkozó irányelveivel. További információért tekintse meg a [Cognitive Services lapot](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a Microsoft Adatvédelmi központban.

## <a name="next-steps"></a>További lépések

Ismerkedés a Computer Vision-rel egy rövid útmutató val:

- [Rövid útmutató: Computer Vision .NET ügyféltár](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Rövid útmutató: Computer Vision Python ügyfélkönyvtár](./quickstarts-sdk/client-library.md?pivots=programming-language-python)
- [Rövid útmutató: Computer Vision Java ügyfélkönyvtár](./quickstarts-sdk/client-library.md?pivots=programming-language-java)
