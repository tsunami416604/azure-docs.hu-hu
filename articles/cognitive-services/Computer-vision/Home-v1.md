---
title: Mi az a Computer Vision API?
titlesuffix: Azure Cognitive Services
description: A Computer Vision API speciális képfeldolgozó és információt visszaadó algoritmusokhoz biztosít hozzáférést a fejlesztők számára.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 57388b4e2f74a1e9e8d9c322020077dfd67e89c1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198146"
---
# <a name="what-is-computer-vision-api-version-10"></a>Mi a Computer Vision API 1.0-s verziója?

> [!IMPORTANT]
> A Computer Vision API új verziója érhető el, lásd:
>- [Áttekintés](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Computer Vision API 2.0-s verzió](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

A felhőalapú Computer Vision API a fejlesztők számára hozzáférést biztosít speciális képfeldolgozó és információt visszaadó algoritmusokhoz. Egy kép feltöltésével vagy egy kép URL-címének megadásával a Microsoft Computer Vision algoritmusai különböző módokon elemzik a vizuális tartalmat bemenetek és a felhasználó választásai alapján. A Computer Vision API használatával a felhasználók a következő célokból elemezhetnek képeket:
* [Képek címkézése tartalom alapján.](#Tagging)
* [Képek kategorizálása.](#Categorizing)
* [Képek típusának és minőségének azonosítása.](#Identifying)
* [Emberi arcok észlelése és azok koordinátáinak visszaadása.](#Faces)
* [Fogalomkör-specifikus tartalom felismerése.](#Domain-Specific)
* [Tartalomleírások generálása.](#Descriptions)
* [A képeken található nyomtatott szöveg azonosítása optikai karakterfelismerés használatával.](#OCR)
* [Kézzel írt szöveg felismerése.](#RecognizeText)
* [Színsémák megkülönböztetése.](#Color)
* [Felnőtt tartalom megjelölése.](#Adult)
* [Képek körülvágása miniatűrként való felhasználáshoz.](#Thumbnails)

## <a name="requirements"></a>Követelmények
* Támogatott bemeneti módszerek: Egy application/octet-stream vagy URL-formájában bináris nyers kép.
* Képformátum támogatja: JPEG, PNG, GIF, BMP.
* Kép mérete: 4 MB-nál kevesebb.
* Lemezkép-dimenzió: Nagyobb, mint 50 x 50 képpont.

## <a name="tagging-images"></a>Képek címkézése
Computer Vision API alapján ad vissza címkéket ezer felismerhető objektumok, tárgy, táj és műveleteket. Amennyiben a címkék félreérthetőek vagy nem közismertek, az API válasza „tippeket” tartalmaz a címke adott környezetben való értelmezésének megkönnyítése érdekében. A címkékhez nincs besorolási rendszer és öröklési hierarchia. A tartalomcímkék gyűjteménye képezi a kép ember által olvasható nyelven, teljes mondatokban megformált „leírásának” alapját. Fontos tudni, hogy a képleírásokhoz jelenleg csak az angol nyelv támogatott.

Egy kép feltöltése vagy egy kép URL-címének megadása után a Computer Vision API algoritmusai címkéket adnak vissza a képen felismert tárgyak, élőlények és cselekvések alapján. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.

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
A címkézésen és leírásokon kívül a Computer Vision API a korábbi verziókban definiált, besorolás-alapú kategóriákat is visszaad. Ezek a kategóriák öröklődő szülő/gyermek hierarchián alapuló elnevezésekkel vannak besorolva. Minden kategórianév angol nyelvű. Önállóan, vagy új modelljeinkkel is használhatók.

### <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom
A képek elemei az alábbi ábrán látható 86 fogalom szerint sorolhatók be általános vagy szűkebb kategóriákba. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) oldalon találja meg.

![Kategóriák elemzése](./Images/analyze_categories.png)

Kép                                                  | Válasz
------------------------------------------------------ | ----------------
![Nő a tetőn](./Images/woman_roof.png)                 | people
![Családi fénykép](./Images/family_photo.png)             | people_crowd
![Aranyos kutya](./Images/cute_dog.png)                     | animal_dog
![Hegyvidéki tájkép](./Images/mountain_vista.png)       | outdoor_mountain
![Elemzési célra készült, kenyeret ábrázoló kép](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Képtípusok azonosítása
A képek több módon kategorizálhatók. A Computer Vision API egy logikai jelző beállításával jelezheti, hogy egy kép fekete-fehér-e vagy színes. Azt is ugyanígy tudja jelezni, hogy a kép vonalrajz vagy sem. Jelezni tudja, hogy a kép ClipArt-e, és 0-tól 3-ig terjedő skálán annak minőségét is.

### <a name="clip-art-type"></a>ClipArt típus
Észleli, hogy a kép ClipArt, vagy nem.  

Value | Jelentés
----- | --------------
0     | Nem ClipArt
1     | nem egyértelmű
2     | Normál ClipArt
3     | Jó ClipArt

Kép|Válasz
----|----
![Sajtot ábrázoló ClipArt vizuális elemzéshez](./Images/cheese_clipart.png)|3 – jó ClipArt
![Ház és udvar képe vizuális elemzéshez](./Images/house_yard.png)|0 – Nem ClipArt

### <a name="line-drawing-type"></a>Vonalrajz típus
Észleli, hogy a kép vonalrajz, vagy nem.

Kép|Válasz
----|----
![Oroszlánt ábrázoló rajz vizuális elemzéshez](./Images/lion_drawing.png)|True (Igaz)
![Virág képe vizuális elemzéshez](./Images/flower.png)|False (Hamis)

### <a name="faces"></a>Arcok
Észleli a képen látható emberi arcokat, és megadja az arcok koordinátáit, befoglaló téglalapját, nemét és életkorát. Ezek a vizuális jellemzők az archoz generált metaadatok részhalmazát képezik. Az arcokhoz generált többi metaadat (azonosítás arc alapján, testtartás-felismerés és sok más) a Face API használatával érhető el.  

Kép|Válasz
----|----
![Tetőn álló nő arcának vizuális elemzése](./Images/woman_roof_face.png) | [{"age": 23., "gender": "Female", "faceRectangle": { "left": "felső" 1379: 320, "width": 310, "height": 310 } } ]
![Anyát és lányát ábrázoló kép vizuális elemzése](./Images/mom_daughter_face.png) | [{"age": 28., "gender": "Female", "faceRectangle": { "left": "felső" 447: 195, "width": 162-es, "magasság": 162-es}}, {"age": 10., "gender": "Male", "faceRectangle": { "left": "felső" 355: 87, "width": 143, "magasság": 143 } } ]
![Családi fotó vizuális elemzése](./Images/family_photo_face.png) | [{"age": 11., "gender": "Male", "faceRectangle": { "left": "felső" 113: 314, "width": 222, "magasság": 222}}, {"age": 11., "gender": "Female", "faceRectangle": { "left": 1200-as, "felső": 632, "width": 215, "magasság": 215}}, {"age": 41-es, "gender": "Male", "faceRectangle": { "left": 514-es, "felső": 223, "width": 205, "magasság": 205}}, {"age": 37, a "gender": "Female", "faceRectangle": { "left": 1008: "felső": 277, "width": 201-es, "magasság": 201 } } ]


## <a name="domain-specific-content"></a>Fogalomkör-specifikus tartalom

Címkézésen és felső szintű kategorizáláson kívül a Computer Vision API speciális (fogalomkör-specifikus) információk kinyerését is támogatja. Speciális információk kinyerése önálló metódusként, vagy magas szintű kategorizálással is megvalósítható. A 86 kategóriára épülő besorolás további finomítására szolgál fogalomkör-specifikus modellek hozzáadásával.

Jelenleg az egyetlen támogatott speciális információ a hírességek és tereptárgyak felismerése. Ezek az people_ (emberek) és people_group (embercsoport) kategóriák fogalomkör-specifikus finomításai, és közismert tereptárgyak a világ minden táján.

A fogalomkör-specifikus modellek két módon használhatók:

### <a name="option-one---scoped-analysis"></a>Első lehetőség – hatókörön belüli elemzés
Csak a kiválasztott modell elemzése egy HTTP POST hívás kezdeményezésével. Ezzel a lehetőséggel, ha tudja, melyik modellt kívánja használni, megadhatja a modell nevét, és csak az arra vonatkozó információkat kapja vissza. Ezt a lehetőséget használhatja például akkor, ha a cél csak a hírességek felismerése. A válasz az esetleg felismert hírességek listáját adja vissza, a felismerésük megbízhatósági értékével együtt.

### <a name="option-two---enhanced-analysis"></a>Második lehetőség – fejlett elemzés
Az elemzés további, a 86 besorolási kategóriához kapcsolódó részleteket szolgáltat. Ez a lehetőség olyan alkalmazásokban használható, amelyekben a felhasználó az egy vagy több fogalomkör-specifikus modellből származó részleteken kívül általános képelemzést is szeretne. Ennek a metódusnak a hívásakor először a 86 kategóriaelnevezést használó besorolás lesz meghíva. Ha a kategóriák bármelyike egyezik egy ismert/megfelelő modellével, akkor újabb besorolási hívások következnek. Ha például a „details=all” vagy a „details” tartalmazza a „celebrities” (hírességek) szót, akkor a metódus a 86 kategóriaelnevezést használó besorolás után meghívja hírességek besorolását. Az eredményben „people_” előtagú megnevezések szerepelnek.

## <a name="generating-descriptions"></a>Leírások generálása 
A Computer Vision API algoritmusai a képek tartalmát elemzik. Ez az elemzés képezi a kép ember által olvasható, teljes mondatokban megfogalmazott „leírásának” alapját. A leírás összegzi mindazt, ami a képen látható. A Computer Vision API algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza. Ezt a technológiát használja a képek leírásának létrehozására például az [itt](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption) megtalálható robot is.  

### <a name="example-description-generation"></a>Példa leírás generálására
![Épületek fekete-fehér képe](./Images/bw_buildings.png) '
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

## <a name="perceiving-color-schemes"></a>Színsémák észlelése
A Computer Vision algoritmusa kigyűjti a képen található színeket. A színeket három kontextusban (előtér, háttér és teljes kép) elemzi. 12 fő szín szerint válogatja szét őket. Ezek a színek a következők: fekete, kék, barna, szürke, zöld, narancsszín, rózsaszín, lila, vörös, pávakék, fehér és sárga. Egy kép színösszeállításától függően az egyszerű fekete és fehér vagy más színek hexadecimális színkódokként lehetnek visszaadva.

Kép                                                       | Előtér |Háttér| Színek
----------------------------------------------------------- | --------- | ------- | ------
![Hegyvidéki tájkép](./Images/mountain_vista.png)            | Fekete     | Fekete   | Fehér
![Virág képe vizuális elemzéshez](./Images/flower.png)               | Fekete     | Fehér   | Fehér, fekete, zöld
![Vasútállomás képe vizuális elemzéshez](./Images/train_station.png) | Fekete     | Fekete   | Fekete

### <a name="accent-color"></a>Kiegészítő szín
A képről kinyert szín a felhasználó számára az uralkodó színek és a telítettség által legszembetűnőbbnek szánt színnek felel meg.

Kép                                                       | Válasz
----------------------------------------------------------- | ----
![Hegyvidéki tájkép](./Images/mountain_vista.png)            | #BC6F0F
![Virág képe vizuális elemzéshez](./Images/flower.png)               | #CAA501
![Vasútállomás képe vizuális elemzéshez](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Fekete-fehér
Logikai jelző, amely azt jelzi, hogy a kép fekete-fehér-e vagy sem.

Kép                                                      | Válasz
---------------------------------------------------------- | ----
![Épület képe vizuális elemzéshez](./Images/bw_buildings.png)      | True (Igaz)
![Ház és udvar képe vizuális elemzéshez](./Images/house_yard.png)      | False (Hamis)

## <a name="flagging-adult-content"></a>Felnőtt tartalom megjelölése
A kategóriák között a felnőtt és kényes tartalom is szerepel, ezáltal észlelhető a felnőtt tartalom, és korlátozható a szexuális tartalmú képek megjelenítése. A felnőtteknek szóló vagy kényes tartalom észlelésére vonatkozó szűrő érzékenységét a felhasználó egy csúszka segítségével állíthatja be igényei szerint.

## <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)
Az ORC-technológia észleli a képek szöveges tartalmát, és számítógép által olvasható karaktersorozattá alakítja az észlelt szöveget. Az eredmény kereséshez és sok más célra, például egészségügyi, biztonsági vagy banki adatként hasznosítható. A szöveg nyelvét automatikusan felismeri. Az optikai karakterfelismeréssel időt takaríthat meg, és használata kényelmes: a szövegek átírása helyett lefényképezheti azt.

Az optikai karakterfelismerés 25 nyelvet támogat. Ezeken a nyelveken a következők: Arab, kínai (egyszerűsített), kínai (hagyományos), Cseh, dán, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, spanyol, szerb (cirill betűs és Latin), szlovák, spanyol, Svéd és török.

Szükség esetén az OCR a felismert szöveg dőlésszögét a kép vízszintes tengelyéhez igazítja. Az OCR az alábbi ábrán látható módon adja meg az egyes szavak koordinátáit.

![Az optikai karakterfelismerés áttekintése](./Images/vision-overview-ocr.png) – Az OCR használatára vonatkozó követelmények:
- A bemeneti kép méretének 40 × 40 és 3200 × 3200 képpont között kell lennie.
- A kép nem lehet 10 megapixelnél nagyobb.

A bemeneti kép a 90 fok bármely többszörösével, plusz egy kis, legfeljebb 40 fokos szöggel lehet elforgatva.

A szövegfelismerés pontossága függ a kép minőségétől. Felismerési hibákat okozhatnak az alábbi körülmények:
- Elmosódott képek.
- Kézzel írt, folyóírásos szöveg.
- Művészi betűstílusok.
- Kis méretű szöveg.
- Bonyolult háttér, árnyékok, a szöveget átfedő fényhatás vagy perspektivikus torzítás.
- Túl nagy méretű vagy hiányzó nagybetűk a szavak elején.
- Felső vagy alsó indexben lévő, vagy áthúzott szöveg.

Korlátozások: A fényképekhez, ahol szövege a következő meghatározó téves részlegesen felismert származhatnak. Bizonyos képeken, elsősorban szöveg nélküli fényképeken a pontosság jelentősen változhat a kép típusától függően.

## <a name="recognize-handwritten-text"></a>Kézzel írt szöveg felismerése
Ez a technológia lehetővé teszi kézzel írt jegyzetek, levelek, esszék, táblaképek, űrlapok vagy más elemek szövegének felismerését és kinyerését. Különféle felületekkel és hátterekkel használható, például fehér papír, sárga jegyzetlapok vagy fehér tábla.

A kézírásos szövegek felismerése időt és energiát takarít meg, és növeli a hatékonyságot, hiszen lehetővé teszi, hogy szövegek átírása helyett egyszerűen lefényképezze az adott szöveget. Lehetővé teszi jegyzetek digitalizálását. A digitalizálás által gyors és egyszerű keresések végezhetők. Ezen kívül a papírfelhasználást is csökkenti.

A bemenetre vonatkozó követelmények:
- Képformátum támogatja: JPEG, PNG és BMP.
- A képfájl méretének 4 MB-nál kisebbnek kell lennie.
- A képméret legalább 40 × 40, legfeljebb 3200 × 3200 lehet.

Megjegyzés: ez a technológia jelenleg előzetes verzióban, és kizárólag angol nyelvre érhető el.

## <a name="generating-thumbnails"></a>Miniatűrök létrehozása
A miniatűrök a teljes képek kis méretű helyettesítői. A különböző eszközök, például telefonok, táblagépek és asztali számítógépek felhasználói felülete különböző elrendezéseket és miniatűrméreteket követel. A Computer Vision API okos levágás funkciója megoldja ezt a problémát.

Egy kép feltöltése után abból jó minőségű miniatűr lesz generálva, a Computer Vision API algoritmusa pedig elemzi a képen lévő tárgyakat. Majd körülvágja a képet a "lényeges terület" követelményeinek. A kimenet egy speciális keretben jelenik meg, az alábbi ábrán látható módon. A létrehozott miniatűr a felhasználó igényei szerint az eredeti képétől eltérő oldalaránnyal is rendelkezhet.

A miniatűrt előállító algoritmus működése a következő:

1. Zavaró elemet távolít el a lemezképet, és felismeri a fő objektum, a "lényeges terület".
2. Körülvágja a képet a azonosított terület hasznos hely alapján.
3. A miniatűrhöz megadott méreteknek megfelelően módosítja az oldalarányt.

![Miniatűrök](./Images/thumbnail-demo.png)
