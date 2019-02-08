---
title: Mi az a Computer Vision API? – A computer Vision
titlesuffix: Azure Cognitive Services
description: A Computer Vision szolgáltatás a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4322b118268ee88235190f5672585d17ea365495
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872644"
---
# <a name="what-is-computer-vision"></a>Mi a Computer Vision?

Azure Computer Vision service a, hogy képeket dolgozzon és vracející információkat nyújt a fejlesztőknek korszerű algoritmusokat. A kép elemzéséhez feltöltheti a képet, vagy megadhatja a kép URL-címét. A képek algoritmusok elemezheti a tartalmat a különféle módokon, érdekli visual funkcióktól függően. Computer Vision meghatározhatja például, ha kép felnőtt vagy pikáns tartalom található, vagy azt találhatja mind az emberi arcok a képen.

Használhatja Computer Vision az alkalmazás használatával egy natív SDK-t vagy a REST API közvetlen meghívása. Ezen a lapon széles körben ismerteti, mire képes a Computer Vision.

## <a name="analyze-images-for-insight"></a>Az insight képeket elemezhet

Elemezheti a képek észlelése és azok vizuális jellemzőinek és jellemzők vonatkozó adatokat tartalmaznak. Az a Funkciók, az alábbi táblázat által biztosított a [kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-t.

| Műveletek | Leírás |
| ------ | ----------- |
|**[Vizuális jellemzők címkézése](concept-tagging-images.md)**|Valamint a vizuális jellemzőket a képet, az ezer felismerhető objektumok, élő dolog, táj és műveletek közül. Ha a címkék nem egyértelmű vagy nem közismert, az API-válasz biztosít "tippek" egy ismert beállítás kontextusában a címke jelentésének értelmezéséhez. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.|
|**[Objektumok észlelése](concept-object-detection.md)**| Objektumfelismerés hasonlít a címkézés, de az API-t adja vissza a határolókeret koordinátái minden alkalmazott címke esetében. Például képet tartalmaz egy kutya, cat és személy, ha a hibakeresés művelet felsorolja azokat az objektumokat a a koordináták a képen. Ez a funkció segítségével a dolgozhatók további a képet az objektumok közötti kapcsolatok. Azt jelzi, ha több példánya ugyanazt a címkét a képet.|
|**[Márkái észlelése](concept-brand-detection.md)**|A képek és videók globális emblémák ezer adatbázisból kereskedelmi márkái azonosításához. Használhatja a szolgáltatás, például felderítheti, mely márkái: a közösségi oldalakon legnépszerűbb vagy a media termékelhelyezésről legelterjedtebb.|
|**[Képek kategorizálása](concept-categorizing-images.md)**|Felismeri és kategorizálja a teljes képet egy [kategóriaelnevezési rendszer](Category-Taxonomy.md) segítségével, szülő/gyermek hierarchiák használatával. A kategóriák egyedül vagy az új címkéző modellekkel együtt is használhatóak.<br/>Jelenleg az egyetlen támogatott nyelv a képek címkézéséhez és kategorizálásához az angol.|
|**[Képek leírása](concept-describing-images.md)**|Leírást hoz létre a teljes képhez a felhasználók által is olvasható nyelven, teljes mondatok használatával. A Computer Vision algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza.|
|**[Arcfelismerés](concept-detecting-faces.md)** |Felismeri a képen található arcokat, és információval szolgál minden felismert arccal kapcsolatban. A Computer Vision visszaküldi az arcok koordinátáit, a téglalapot és a képen szereplő személy nemét és életkorát.<br/>A Computer Vision a [Face](/azure/cognitive-services/face/) funkcióinak csak egy részét tartalmazza. A Face szolgáltatást részletesebb elemzések elvégzésére is használhatja, például az arc vagy a testtartás azonosítására.|
|**[Képek típusának észlelése](concept-detecting-image-types.md)**|Észleli a kép jellemzőit, például hogy a kép vonalrajz-e, vagy annak a valószínűségét, hogy a kép ClipArt.|
|**[Tartományspecifikus tartalom észlelése](concept-detecting-domain-content.md)**|Tartománymodellek segítségével észleli és felismeri a kép tartományspecifikus tartalmát, például a hírességeket vagy nevezetességeket. Például, ha egy képen személyek szerepelnek, a Computer Vision egy beépített híresség-tartománymodell használatával megállapítja, hogy a képen felismert személyek megegyeznek-e az ismert hírességek valamelyikével.|
|**[Színséma észlelése](concept-detecting-color-schemes.md)**|A képen használt színek elemzése. A Computer Vision eldönti, hogy a kép fekete-fehér-e vagy színes, illetve színes képek esetében felismeri a domináns és a kiegészítő színeket.|
|**[Miniatűr létrehozása](concept-generating-thumbnails.md)**|Elemzi a kép tartalmát, hogy megfelelő miniatűrt tudjon létrehozni a képhez. Számítógépes Látástechnológia először létrehoz egy jó minőségű miniatűrt, és ezután elemzi a tárgyakat meghatározni a *érdeklődési körét*. Számítógépes Látástechnológia majd körülvágja a képet a terület a lényeges követelményeinek. Igény szerint a létrehozott miniatűr az eredeti képtől eltérő oldalaránnyal is rendelkezhet.|
|**[A terület a lényeges beolvasása](concept-generating-thumbnails.md#area-of-interest)**|Elemezheti a koordinátáit adja vissza a kép tartalmát a *érdeklődési körét*. Ugyanezt a funkciót, amellyel miniatűrkép generálása, de ahelyett, hogy a kép levágás, Computer Vision adja vissza a határolókeret koordinátái a régió, így a hívó alkalmazás az eredeti képet igény szerint módosíthatja.|


## <a name="extract-text-from-images"></a>Képek szöveg kinyerése

A Computer Vision használatával a képből [szöveget nyerhet ki OCR használatával](concept-extracting-text-ocr.md), így egy gépek által olvasható karakterfolyamot hozhat létre. Szükség esetén az OCR a felismert szöveg dőlésszögét a kép vízszintes tengelyéhez igazítja, és az összes szó koordinátáit megadja. Az OCR 25 nyelvet támogat, és automatikusan felismeri az észlelt szöveg nyelvét.

Emellett felismeri a [nyomtatott és kézzel írt szövegeket](concept-recognizing-text.md) is a képen. A Computer Vision észleli és kinyeri a nyomtatott és a kézzel írt szövegeket is a különböző tárgyakat, felületeket és háttereket ábrázoló képekről, például nyugtákról, plakátokról, névjegykártyákról, levelekről és táblákról. A nyomtatott és a kézzel írt szövegek felismerése jelenleg előzetes verzióban érhető el, és az egyetlen támogatott nyelv az angol.  

## <a name="moderate-content-in-images"></a>Mérsékelt tartalmú képeket

A Computer Vision [észleli a képen a felnőtteknek szóló és kényes tartalmakat](concept-detecting-adult-content.md), majd egy megbízhatósági pontszámot hoz létre a képen szereplő felnőtt vagy kényes tartalmak valószínűsége alapján. A felnőtteknek szóló vagy kényes tartalom észlelésére vonatkozó szűrő érzékenységét egy csúszka segítségével állíthatja be igényei szerint.

## <a name="use-containers"></a>Tárolók használata

[Számítógépes Látástechnológia tárolókkal](computer-vision-how-to-install-containers.md) , helyileg, nyomtatott és kézzel írt szöveg felismerése szabványos Docker-tároló közelebb telepítse az adatokhoz.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Computer Vision a következő követelményeknek megfelelő képeket képes elemezni:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont
  - Az OCR használatához a kép méretének 50 x 50 és 4200 x 4200 képpont között kell lennie.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Mint az összes, a Cognitive Services a Computer Vision service segítségével a fejlesztők célszerű tisztában lennie a házirendek a Microsoft-vásárlói adatokat. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Első lépések a Látástechnológiai a következő rövid útmutató:

- [Rövid útmutató: Kép elemzése](quickstarts-sdk/csharp-analyze-sdk.md)
- [Rövid útmutató: Kézzel írt szöveg kinyerése](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Rövid útmutató: Miniatűrkép generálása](quickstarts-sdk/csharp-thumb-sdk.md)
