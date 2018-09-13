---
title: Computer Vision API a Microsoft Cognitive Services |} A Microsoft Docs
description: A Computer Vision API fejlett algoritmusok segítségével dolgozhassa és a Microsoft Cognitive Services információkat ad vissza.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 84d931ad79bf32b39a4d771f6afd1c9a05ad2395
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714819"
---
# <a name="what-is-computer-vision-api-version-10"></a>Mi az számítógép Vision API 1.0-s verzióját?

> [!IMPORTANT]
> Computer Vision API új verziója mostantól elérhető, lásd:
>- [Áttekintés](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Számítógép Vision API 2.0-s verzió](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

A felhőalapú Computer Vision API a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz. Egy kép feltöltésével vagy egy kép URL-címének megadásával a Microsoft Computer Vision algoritmusai különböző módokon elemzik a vizuális tartalmat bemenetek és a felhasználó választásai alapján. A Computer Vision API-val a felhasználók képek is elemezhet:
* [Címkézhet meg képeket a tartalom alapján.](#Tagging)
* [Képek kategorizálása.](#Categorizing)
* [Azonosítsa a típus és minősége lemezképeket.](#Identifying)
* [Emberi arcok észlelése és azok koordinátáit adja vissza. ](#Faces)
* [Ismeri fel a tartomány-specifikus tartalmait.](#Domain-Specific)
* [Hozzon létre a tartalom leírását.](#Descriptions)
* [Optikai karakterfelismerés segítségével azonosíthatja a képeken található nyomtatott szöveg.](#OCR)
* [Kézzel írt szöveg felismerése.](#RecognizeText)
* [Színsémák különbséget.](#Color)
* [Felnőtt tartalom jelző.](#Adult)
* [Körülvágása fényképeket, a miniatűrök használható.](#Thumbnails)

## <a name="requirements"></a>Követelmények
* Támogatott bemeneti módszerek: bináris egy application/octet-stream vagy URL-formájában nyers kép.
* Támogatott képformátum: JPEG, PNG, GIF és BMP.
* Fájl Képméret: legfeljebb 4 MB.
* Lemezkép-dimenzió: nagyobb, mint 50 x 50 képpont.

## <a name="tagging-images"></a>Rendszerképek címkézése
Computer Vision API több mint 2000 felismerhető objektumok, tárgy, táj és műveletek alapján címkéket adja vissza. Ha a címkék olyan nem egyértelmű vagy nem közismert, az API-válasz biztosít "tippek" ismert beállítását a környezetben a címke jelentésének értelmezéséhez. Címkék nem egy besorolás szerint vannak rendezve, és nincs öröklési hierarchiát létezik. Tartalom címkék gyűjteménye a lemezkép teljes mondatokban formázott emberi olvasható nyelveként megjelenik a "leírás" alapját képezi. Vegye figyelembe, hogy ezen a ponton angol Képleírás az egyetlen támogatott nyelv.

Kép feltöltése vagy a kép URL-címének megadása után a Computer Vision API algoritmusok kimeneti címkék az objektumok, a tárgy és a műveletek azonosítja a kép alapján. Címkézés nem korlátozódik a fő tulajdonos, például az előtérben, a személy, de is tartalmaz, a beállítás (belső vagy külső) bútordarab, eszközök, üzemek, például állatokat, Kellékek, kütyük stb.

### <a name="example"></a>Példa
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Képek kategorizálása
Címkézés és a leírások mellett a Computer Vision API a korábbi verziókban meghatározott besorolás-alapú kategóriák adja vissza. Ezekben a kategóriákban egy szülő-gyermek örökletes hierarchiákkal rendelkező besorolás szerint vannak rendszerezve. Az összes kategória angolul jelennek meg. Önállóan vagy az új modell használható.

### <a name="the-86-category-concept"></a>A kategória 86 fogalma
Az alábbi ábrán látható 86 fogalmak listája alapján, vizuális jellemzőket a képen található kategorizálhatók és a széles körű-specifikus. A teljes szöveges formátumú besorolás, lásd: [Kategóriaelnevezési](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Kategóriák elemzése](./Images/analyze_categories.png)

Kép                                                  | Válasz
------------------------------------------------------ | ----------------
![Nőről tető](./Images/woman_roof.png)                 | személyek
![Családi fénykép](./Images/family_photo.png)             | people_crowd
![Cuki kutya](./Images/cute_dog.png)                     | animal_dog
![Öltözet hegyi](./Images/mountain_vista.png)       | outdoor_mountain
![Vizuális Food kenyér elemzése](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Képtípus azonosítása
Többféleképpen is lehet rendszerképeket kategorizálása. Computer Vision API egy logikai jelző, adja meg, hogy egy kép fekete-fehér vagy színes állíthatja be. Azt is beállíthatja azt a jelzőt, hogy jelezze, hogy egy kép Vonalrajz-e. Azt is azt jelzi, hogy kép ClipArt-e, és jelzi a minőségét, például a méretezési csoport 0 – 3.

### <a name="clip-art-type"></a>ClipArt típus
Észleli, hogy kép ClipArt-e.  

Érték | Jelentés
----- | --------------
0     | A nem ClipArt
1     | Nem egyértelmű
2     | Normál ClipArt
3     | Jó ClipArt

Kép|Válasz
----|----
![Vizuális sajtok iránti Szenvedélyének ClipArt elemzése](./Images/cheese_clipart.png)|3 jó--ClipArt
![Vizuális ház Yard elemzése](./Images/house_yard.png)|0 nem--ClipArt

### <a name="line-drawing-type"></a>Vonalrajz típusa
Észleli a lemezkép-e Vonalrajz-e.

Kép|Válasz
----|----
![Vizuális Lion rajz elemzése](./Images/lion_drawing.png)|True (Igaz)
![Vizuális Flower elemzése](./Images/flower.png)|False (Hamis)

### <a name="faces"></a>Arcok
A képen látható emberi arcok észlelése, és a face koordináták, a face, nem és életkor négyszöge generál. Ezek a vizuális szolgáltatások face generált metaadatok a részhalmazát képezik. Szélesebb körű metaadatok arcok (arcfelismerési azonosítása, testtartás észlelési és egyéb) hoz létre a Face API-val.  

Kép|Válasz
----|----
![Vizuális Nőről tető Face elemzése](./Images/woman_roof_face.png) | [{"age": 23, a "nemek": "Női", "faceRectangle": {"left": "felső" 1379: 320, "width": 310, "magasság": 310}}]
![Vizuális Mom Fiamtól Face elemzése](./Images/mom_daughter_face.png) | [{"age": 28 "gender": "Női", "faceRectangle": {"left": "felső" 447: 195, "width": 162, "magasság": 162-es}}, {"age": 10, "gender": "Férfi", "faceRectangle": {"left": 355, a "felső": 87, "width": 143, "magasság": 143}}]
![Vizuális családi Phot Face elemzése](./Images/family_photo_face.png) | [{"age": 11, a "nemek": "Férfi", "faceRectangle": {"left": "felső" 113: 314, "width": 222, "magasság": 222}}, {"age": 11, a "nemek": "Női", "faceRectangle": {"left": 1200-as, "felső": 632, "width": 215, "magasság": 215}}, {"age": 41, "gender": "Férfi", " faceRectangle": {"left": 514-es,"felső": 223,"width": 205,"magasság": 205}}, {"age": 37, a"nemek":"Női","faceRectangle": {"left":"felső"1008: 277,"width": 201,"magasság": 201-es}}]


## <a name="domain-specific-content"></a>Tartomány-specifikus tartalmait

Emellett a címkézési és a legfelső szintű kategorizálási, Computer Vision API is támogatja a speciális (vagy tartomány-specifikus) információt. Speciális információk különálló módként alkalmazott, vagy a magas szintű kategorizálással valósítható meg. Tovább finomíthatja a 86-kategóriaelnevezési tartomány-specifikus modelleket hozzáadásával eszközként működik.

A csak a támogatott speciális információkat jelenleg hírességek felismerése és tereptárgyak felismerése. Azok a személyek és személyek csoportok kategóriáit és a világ különböző pontjain arcrész tartomány-specifikus finomítások.

A tartomány-specifikus modelleket a két lehetőség van:

### <a name="option-one---scoped-analysis"></a>A beállítás egy - hatókörön belüli elemzés
Csak a kiválasztott modell elemzése egy HTTP POST-hívás meghívásával. Ezt a beállítást Ha tudja, hogy melyik modellben is használni szeretné, a modell nevét adja meg, és csak kap információkat megfelelő ehhez a modellhez. Például használhatja ezt a beállítást csak a keresett híresség-felismerés. A válasz lehetséges hírességek, megbízhatósági eredményeiket fűzni megfelelő listáját tartalmazza.

### <a name="option-two---enhanced-analysis"></a>Továbbfejlesztett elemzési két - beállítás
Elemezheti a 86 kategóriaelnevezési kategóriák kapcsolatos további információkat. Ez a beállítás érhető el alkalmazásokban használható ahol felhasználókat szeretné, hogy általános képet elemzési adatok mellett egy vagy több tartomány-specifikus modelleket használva. Ez a metódus meghívásakor 86 kategóriaelnevezési osztályozó először nevezzük. Ismert/megfelelő modellek, amelyek egyeznek kategóriákra, az osztályozó által igénybe vett meghívásához egy második fázishoz a következő. Például ha "Részletek = all" vagy "details" "hírességek" közé tartozik, a metódus meghívja a hírességek osztályozó után a 86-kategória osztályozó nevezzük. Az eredmény "people_" kezdődő címkéket tartalmazza.

## <a name="generating-descriptions"></a>Leírások generálása 
Computer Vision API algoritmusok elemezheti a tartalmat a képet. Ez az elemzés egy "leírás" teljes mondatokban emberek számára olvasható nyelveként megjelenik a alapját képezi. A leírás összefoglalja, hogy mi az a képen található. Computer Vision API algoritmusok alapján azonosítja a kép az objektumok különböző leírások létrehozásához. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza. A robot, amely ezt a technológiát használja, a lemezkép feliratok létrehozása egy példát találhat [Itt](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Példa Leírás létrehozása
![B & W épületek](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Perceiving színsémák
A Látástechnológiai algoritmus kigyűjti kép színeket. A színeket három kontextusban (előtér, háttér és teljes kép) elemzi. 12 domináns kiemelési színcsoportba rendezi vannak csoportosítva. Ezen válogatja ki feketék, kék, brown, szürke, zöld, orange, rózsaszínre, lila, vörös, pávakék, fehér és sárga. Attól függően, a képet a színeket visszaadott a színt hexadecimális kódokkal egyszerű fekete-fehér vagy színcsoportba rendezi.

Kép                                                       | előtér |Háttér| Színek
----------------------------------------------------------- | --------- | ------- | ------
![Öltözet hegyi](./Images/mountain_vista.png)            | Fekete     | Fekete   | Fehér
![Vizuális Flower elemzése](./Images/flower.png)               | Fekete     | Fehér   | Fehér, a fekete, a zöld
![Vizuális elemzése Train állomás](./Images/train_station.png) | Fekete     | Fekete   | Fekete

### <a name="accent-color"></a>Kiemelőszín
Domináns színek és színtelítettség keresztül a felhasználók számára a leginkább figyelemfelkeltő szín képviselik kép kinyert színe.

Kép                                                       | Válasz
----------------------------------------------------------- | ----
![Öltözet hegyi](./Images/mountain_vista.png)            | #BC6F0F
![Vizuális Flower elemzése](./Images/flower.png)               | #CAA501
![Vizuális elemzése Train állomás](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Fekete-fehér
Logikai jelzőt, amely azt jelzi, hogy egy kép fekete-fehér vagy sem.

Kép                                                      | Válasz
---------------------------------------------------------- | ----
![Vizuális elemzése létrehozása](./Images/bw_buildings.png)      | True (Igaz)
![Vizuális ház Yard elemzése](./Images/house_yard.png)      | False (Hamis)

## <a name="flagging-adult-content"></a>Felnőtt tartalom megjelölése
A különféle visual osztályok között van a felnőtt és szexuális csoport, amely lehetővé teszi, hogy felnőtt anyagok észlelését, és a szexuális tartalmú képek megjelenítéséhez korlátozza. A felnőtt és szexuális tartalom észlelési szűrő értékét is beállíthat a felhasználói beállítás szerint befogadásához.

## <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)
Optikai Karakterfelismerés technológia szöveges tartalom észleli a képet, és az azonosított szöveg kibontása karaktersorozattá. Az eredmény a Keresés és számos más orvosi, biztonságot és banki is használhatja. A szöveg nyelvét automatikusan felismeri. Az optikai Karakterfelismeréssel időt takaríthat meg, és kényelmes: a felhasználók által, ezáltal a szöveg átírás helyett fényképet készítsenek.

Optikai Karakterfelismerés 25 nyelveket támogatja. Ezek a nyelvek a következők: arab, egyszerűsített kínai, hagyományos kínai, Cseh, dán, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, spanyol, szerb (cirill betűs és a latin betűs) Szlovák, spanyol, svéd és török.

Ha szükséges, optikai Karakterfelismerés javítja a felismert szöveget fokban kép vízszintes tengely körüli elforgatásának. Optikai Karakterfelismerés biztosít a keret koordináták minden szó az alábbi ábrán látható módon.

![Optikai Karakterfelismerés – áttekintés](./Images/vision-overview-ocr.png) OCR vonatkozó követelmények:
- A bemeneti kép mérete 40 x 40 és 3200 x 3200 képpont között kell lennie.
- A lemezkép nem lehet nagyobb, mint 10 Megapixel.

Bemeneti kép szerint bármilyen több 90 fokos, és a egy kis szög az "40 forgatható fok.

Szövegek felismerése pontossága attól függ, hogy a kép minőségét. Egy pontatlan olvasása a következő helyzetekben okozhatja:
- Határozatlan képek.
- Kézzel írt vagy kurzív szöveg.
- Művészi betűstílusok.
- A kis betűméret.
- Összetett hátterek, árnyékok változása, vagy tükröződést szöveg vagy perspektíva torzulást keresztül.
- Hiányzik vagy túl nagy méretű nagybetűk szavak alapjait:
- Dolní index je, apróságok vagy áthúzott szöveg.

Korlátozások: A fényképekhez, ahol szövege a következő meghatározó, téves származhatnak részlegesen felismerhető szavakat. Néhány fényképek, különösen a fényképek nélkül szöveg, a pontosság sokkal függően változhat a kép típusát.

## <a name="recognize-handwritten-text"></a>Kézzel írt szöveg felismerése
Ez a technológia lehetővé teszi észlelését, és kézzel írt szöveg kinyerése a megjegyzések, levelek, esszék, táblaképek, űrlapok, stb. Különféle felületekkel és hátterekkel használható, például fehér papír, sárga jegyzetlapok vagy fehér tábla.

A kézírásos szövegek felismerése időt és energiát takarít meg, és növeli a hatékonyságot, hiszen lehetővé teszi, hogy szövegek átírása helyett egyszerűen lefényképezze az adott szöveget. Lehetővé teszi, hogy digitalizálhatók a jegyzetek. A digitalizáció gyors és egyszerű keresés megvalósítását teszi lehetővé. Ezen kívül a papírfelhasználást is csökkenti.

A bemeneti követelmények:
- Támogatott képformátum: JPEG, PNG és BMP.
- Kép fájlmérete 4 MB-nál kisebbnek kell lennie.
- Kép mérete legalább 40 x 40, legfeljebb 3200 x 3200 kell lennie.

Megjegyzés: ez a technológia jelenleg előzetes verzióban, és kizárólag angol nyelvre érhető el.

## <a name="generating-thumbnails"></a>Miniatűrök létrehozása
A miniatűr a teljes méretű kép kis reprezentációját. Változatos eszközök, például telefonokon, táblagépeken és számítógépeken hozzon létre egy másik felhasználói felület (UX) elrendezések és a miniatűr méretek van szükség. Intelligens vágás, a Computer Vision API szolgáltatás segítségével a probléma megoldásához.

Kép feltöltése után egy jó minőségű miniatűrt lekérdezi jön létre, és a Computer Vision API algoritmusa elemzi a tárgyakat. Majd körülvágja a képet a követelményeknek, a "lényeges terület" (ROI). A kimenetben megjelenik speciális keretein belül alább ábrán látható módon. A létrehozott miniatűr egy oldalarányát, amely eltér az eredeti képet, a felhasználók igényeinek megfelelően oldalarányának használatával jelenítheti meg.

A miniatűr algoritmus a következőképpen történik:

1. Zavaró elemeket eltávolítja a lemezképet, és felismeri a fő objektum, a "lényeges terület" (ROI).
2. Körülvágja a képet a lényeges azonosított régió alapján.
3. Az arányt, a cél miniatűr dimenziók megfelelően változik.

![Miniatűrök](./Images/thumbnail-demo.png)
