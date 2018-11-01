---
title: Mi az a Computer Vision API?
titlesuffix: Azure Cognitive Services
description: A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.openlocfilehash: 39b3f51693229e75f58c4244089f21c7d3f8677d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250614"
---
# <a name="what-is-computer-vision"></a>Mi a Computer Vision?

A felhőalapú Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz. A Computer Vision olyan gyakori képformátumokat használ, mint a JPEG és a PNG. A kép elemzéséhez feltöltheti a képet, vagy megadhatja a kép URL-címét. A Computer Vision algoritmusai különböző módokon elemzik a képek tartalmát az alapján, hogy milyen vizuális jellemzők érdeklik a felhasználót. Például a Computer Vision által felderíthető, hogy a kép tartalmaz-e felnőtteknek szóló vagy kényes tartalmat, vagy megtalálja az összes arcot a képen.

Az alkalmazáson belül a Computer Visiont az [ügyfélkódtár](quickstarts-sdk/csharp-analyze-sdk.md) vagy közvetlenül a [REST API](vision-api-how-to-topics/howtocallvisionapi.md) hívásával használhatja a következőkre:

- [Képek elemzése](#analyzing-images-for-insight)
- [Szöveg kinyerése képekből](#extracting-text-from-images)
- [Képek tartalmának moderálása](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>Képek elemzése

A Computer Vision használatával képeket elemezhet, így észlelheti és elemzést kaphat a képek vizuális jellemzőiről. Egy helyi kép tartalmának elemzéséhez feltöltheti a képet, vagy távoli képek elemzése esetén megadhatja a képek URL-címét.

A Computer Vision a következő műveleteket képes végrehajtani a képek elemzésekor:

| Műveletek | Leírás |
| ------ | ----------- |
|**[Vizuális jellemzők címkézése](concept-tagging-images.md)**|Felismeri és címkézi a kép vizuális jellemzőit több mint 2000 felismerhető tárgy, élőlény, háttér és tevékenység alapján. Amennyiben a címkék félreérthetőek vagy nem közismertek, a válasz „tippeket” tartalmaz a címke adott környezetben való értelmezésének megkönnyítése érdekében. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.|
|**[Képek kategorizálása](concept-categorizing-images.md)**|Felismeri és kategorizálja a teljes képet egy [kategóriaelnevezési rendszer](Category-Taxonomy.md) segítségével, szülő/gyermek hierarchiák használatával. A kategóriák egyedül vagy az új címkéző modellekkel együtt is használhatóak.<br/>Jelenleg az egyetlen támogatott nyelv a képek címkézéséhez és kategorizálásához az angol.|
|**[Képek leírása](concept-describing-images.md)**|Leírást hoz létre a teljes képhez a felhasználók által is olvasható nyelven, teljes mondatok használatával. A Computer Vision algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza.|
|**[Arcfelismerés](concept-detecting-faces.md)** |Felismeri a képen található arcokat, és információval szolgál minden felismert arccal kapcsolatban. A Computer Vision visszaküldi az arcok koordinátáit, a téglalapot és a képen szereplő személy nemét és életkorát.<br/>A Computer Vision a [Face](/azure/cognitive-services/face/) funkcióinak csak egy részét tartalmazza. A Face szolgáltatást részletesebb elemzések elvégzésére is használhatja, például az arc vagy a testtartás azonosítására.|
|**[Képek típusának észlelése](concept-detecting-image-types.md)**|Észleli a kép jellemzőit, például hogy a kép vonalrajz-e, vagy annak a valószínűségét, hogy a kép ClipArt.|
|**[Tartományspecifikus tartalom észlelése](concept-detecting-domain-content.md)**|Tartománymodellek segítségével észleli és felismeri a kép tartományspecifikus tartalmát, például a hírességeket vagy nevezetességeket. Például, ha egy képen személyek szerepelnek, a Computer Vision egy beépített híresség-tartománymodell használatával megállapítja, hogy a képen felismert személyek megegyeznek-e az ismert hírességek valamelyikével.|
|**[Színséma észlelése](concept-detecting-color-schemes.md)**|A képen használt színek elemzése. A Computer Vision eldönti, hogy a kép fekete-fehér-e vagy színes, illetve színes képek esetében felismeri a domináns és a kiegészítő színeket.|
|**[Miniatűr létrehozása](concept-generating-thumbnails.md)**|Elemzi a kép tartalmát, hogy megfelelő miniatűrt tudjon létrehozni a képhez. A Computer Vision először egy nagy felbontású miniatűrt hoz létre, majd elemzi a képen szereplő objektumokat, hogy meghatározhassa a *releváns tartományt* (ROI). A Computer Vision úgy vágja ki a képet, hogy az megfeleljen a releváns tartomány követelményeinek. Igény szerint a létrehozott miniatűr az eredeti képtől eltérő oldalaránnyal is rendelkezhet.|

## <a name="extracting-text-from-images"></a>Szöveg kinyerése képekből

A Computer Vision használatával a képből [szöveget nyerhet ki OCR használatával](concept-extracting-text-ocr.md), így egy gépek által olvasható karakterfolyamot hozhat létre. Szükség esetén az OCR a felismert szöveg dőlésszögét a kép vízszintes tengelyéhez igazítja, és az összes szó koordinátáit megadja. Az OCR 25 nyelvet támogat, és automatikusan felismeri az észlelt szöveg nyelvét.

Emellett felismeri a [nyomtatott és kézzel írt szövegeket](concept-recognizing-text.md) is a képen. A Computer Vision észleli és kinyeri a nyomtatott és a kézzel írt szövegeket is a különböző tárgyakat, felületeket és háttereket ábrázoló képekről, például nyugtákról, plakátokról, névjegykártyákról, levelekről és táblákról. A nyomtatott és a kézzel írt szövegek felismerése jelenleg előzetes verzióban érhető el, és az egyetlen támogatott nyelv az angol.  

## <a name="moderating-content-in-images"></a>Képek tartalmának moderálása

A Computer Vision [észleli a képen a felnőtteknek szóló és kényes tartalmakat](concept-detecting-adult-content.md), majd egy megbízhatósági pontszámot hoz létre a képen szereplő felnőtt vagy kényes tartalmak valószínűsége alapján. A felnőtteknek szóló vagy kényes tartalom észlelésére vonatkozó szűrő érzékenységét egy csúszka segítségével állíthatja be igényei szerint.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Computer Vision a következő követelményeknek megfelelő képeket képes elemezni:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont  
  Az OCR használatához a kép méretének 50 x 50 és 4200 x 4200 képpont között kell lennie.

## <a name="next-steps"></a>További lépések

Az alábbi rövid útmutatókkal könnyedén elkezdheti a Computer Vision használatát:

- [Kép elemzése](quickstarts-sdk/csharp-analyze-sdk.md)
- [Kézzel írt szöveg kinyerése](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Miniatűr létrehozása](quickstarts-sdk/csharp-thumb-sdk.md)
