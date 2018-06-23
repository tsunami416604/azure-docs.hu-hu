---
title: Számítógép Látástechnológiai API Microsoft kognitív számára |} Microsoft Docs
description: A számítógép Látástechnológiai API speciális algoritmusok használatával alakítsa ki a képek feldolgozni, és a Microsoft kognitív Services adatokat ad vissza.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 86e0441c600162e479c678d3cb1dbeaad423ddb5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349114"
---
# <a name="what-is-computer-vision-api-version-10"></a>Mi az a számítógép Látástechnológiai API 1.0-s verzióját?

> [!IMPORTANT]
> Számítógép Látástechnológiai API új verziója most érhető el, lásd:
>- [Áttekintés](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Számítógép Látástechnológiai API 2.0-s verzió](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

A felhő alapú számítógép Látástechnológiai API-t tartalmaz a fejlesztők számára speciális algoritmusok képek feldolgozása és információt ad vissza a hozzáférést. Egy kép feltöltésével, vagy adja meg egy kép URL-címe, Microsoft számítógép stratégiai algoritmusok elemezheti vizuális tartalom különböző módon a be- és felhasználói lehetőségek alapján. A számítógép Látástechnológiai API-t a felhasználók elemezheti lemezképek:
* [Címke képek tartalom alapján.](#Tagging)
* [Lemezképek kategorizálását.](#Categorizing)
* [A típus és a képek minőségének azonosítása.](#Identifying)
* [Emberi lapok észlelését, és térjen vissza a koordináták. ](#Faces)
* [Ismeri fel a tartomány-specifikus tartalmait.](#Domain-Specific)
* [A tartalom leírását készítése.](#Descriptions)
* [Optikai segítségével azonosíthatja a képek található nyomtatott szöveget.](#OCR)
* [Kézzel írt szöveg felismerése.](#RecognizeText)
* [Színsémák megkülönböztetését.](#Color)
* [Felnőtt tartalom jelző.](#Adult)
* [Körülvágása fényképek miniatűrként használható.](#Thumbnails)

## <a name="requirements"></a>Követelmények
* Támogatott bemeneti módszerek: bináris az application/octet-stream vagy URL-formájában nyers kép.
* Támogatott képformátum: JPEG, PNG, GIF, BMP.
* Fájl Képméret: 4 MB-nál kevesebb.
* Lemezkép-dimenzió: nagyobb, mint 50 x 50 pixelt.

## <a name="tagging-images"></a>Képek címkézése
Számítógép Látástechnológiai API 2000-nél több felismerhető objektumok, élő szervezet, díszletek és műveletek alapján címkék adja vissza. Ha címkék nem egyértelmű, vagy nem közös Tudásbázis, az API-válaszból biztosít "mutatók" környezetben egy ismert beállítás címke szerinti tisztázása. Címkék nem a besorolás szerint vannak rendszerezve, és nem öröklési hierarchia létezik. Tartalom címkék gyűjteménye a lemezkép "description", teljes mondatokat formázott emberi olvasható nyelven jelennek meg alapját képezi. Vegye figyelembe, hogy ezen a ponton angol a lemezkép leírása a támogatott nyelvi.

Után egy kép feltöltésével, vagy adja meg egy kép URL-CÍMÉT, a számítógép Látástechnológiai API-algoritmusok kimeneti címkéket az objektumok, élő szervezet és a lemezkép azonosított műveletek alapján. Címkézés nem korlátozódik a fő tulajdonos, például egy személy az előtérben, de is magában foglalja a beállítás (belső vagy külső) berendezés, eszközök, tőzsdei árfolyamjelző, állatok, Kellékek, kisalkalmazások között stb.

### <a name="example"></a>Példa
![House_Yard](./Images/house_yard.jpg) '

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
## <a name="categorizing-images"></a>Lemezképek kategorizálása
Címkézés és leírások kívül számítógép Látástechnológiai API a korábbi verziókban meghatározott besorolás-alapú kategóriák adja vissza. Ezen kategóriák egy szülő-gyermek örökletes hierarchiás besorolás szerint vannak rendszerezve. Az összes kategória angol nyelvűek Különálló, illetve az új modell is használhatók.

### <a name="the-86-category-concept"></a>A kategória 86 koncepció
Az alábbi ábrán látható 86 fogalmak listája alapján, a kép található vizuális funkciókat csoportosíthatók kezdve széleskörű adott. A teljes szöveges formátumú besorolás, lásd: [kategória besorolás](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Kategóriák elemzése](./Images/analyze_categories.jpg)

Kép                                                  | Válasz
------------------------------------------------------ | ----------------
![Nő tető](./Images/woman_roof.jpg)                 | személyek
![Családbiztonsági fénykép](./Images/family_photo.jpg)             | people_crowd
![Írni kutya](./Images/cute_dog.jpg)                     | animal_dog
![Külső hegyi](./Images/mountain_vista.jpg)       | outdoor_mountain
![Stratégiai étele be elemzése](./Images/bread.jpg)       | food_bread

## <a name="identifying-image-types"></a>Típusú kép azonosítása
Többféleképpen képek csoportosítását. Számítógép Látástechnológiai API-t egy logikai jelző, amely jelzi, hogy a képfájl fekete-fehér vagy szín állíthatja be. Azt a jelzőt, és jelzi, hogy képként sor rajz vagy nem is beállíthat. Azt is jelezheti, hogy lemezkép klip argentin-e, és jelzi a minőségének skálán ilyen 0 – 3.

### <a name="clip-art-type"></a>Klip-kép típusa
Észleli, hogy egy lemezképet-e a klip argentin, vagy nem.  

Érték | Jelentés
----- | --------------
0     | Nem-klip-kép
1     | nem egyértelmű
2     | Normál-klip-kép
3     | jó-klip-kép

Kép|Válasz
----|----
![Stratégiai sajt klip elemek elemzése](./Images/cheese_clipart.jpg)|jó-klip-argentin 3
![Stratégiai House kisebb adagokban is elemzése](./Images/house_yard.jpg)|Nem-klip-argentin 0

### <a name="line-drawing-type"></a>Vonalrajz típusa
Észleli, hogy egy lemezképet-e sor rajz, vagy nem.

Kép|Válasz
----|----
![Stratégiai Lion rajz elemzése](./Images/lion_drawing.jpg)|True (Igaz)
![Stratégiai virág elemzése](./Images/flower.jpg)|False (Hamis)

### <a name="faces"></a>Arcok
Kép emberi lapjaira észleli, és a tapasztalt koordináták, a felület, nem és életkor négyszöge állít elő. Ezek a vizuális szolgáltatások arcfelismerési előállított metaadatok részhalmazai. A lapok (arcfelismerést azonosítása, jelentő észlelési és egyebek) előállított szélesebb körű metaadatok Arcfelismerési API-t használó.  

Kép|Válasz
----|----
![Stratégiai nő tető felület elemzése](./Images/woman_roof_face.png) | [{"élettartama": 23, "nemét": "Nőivarú", "faceRectangle": {"left": "felső" 1379: 320, "szélesség": 310, "magasság": 310}}]
![Stratégiai Mom kiegészítő felület elemzése](./Images/mom_daughter_face.png) | [{"élettartama": 28, "nemét": "Nőivarú", "faceRectangle": {"left": 447, a "top": 195, "szélesség": 162, "magasság": 162}}, {"élettartama": 10, "nemét": "Hímivarú", "faceRectangle": {"left": 355, a "top": 87, "szélesség": 143, "magasság": 143}}]
![Stratégiai termékcsalád Phot felület elemzése](./Images/family_photo_face.png) | [{"élettartama": 11, "nemét": "Hímivarú", "faceRectangle": {"left": 113, a "top": 314, "szélesség": 222, "magasság": 222}}, {"élettartama": 11, "nemét": "Nőivarú", "faceRectangle": {"left": 1200-as, "felső": 632, "szélesség": 215, "magasság": 215}}, {"élettartama": 41, "nemét": "Hímivarú", " faceRectangle": {"left": 514, a"top": 223,"szélesség": 205,"magasság": 205}}, {"élettartama": 37,"nemét":"Nőivarú","faceRectangle": {"left": 1008, a"top": 277,"szélesség": 201,"magasság": 201}}]


## <a name="domain-specific-content"></a>Tartomány-specifikus tartalmait

Továbbá a címkézési és a legfelső szintű kategorizálási, a számítógép Látástechnológiai API is támogatja a speciális (vagy tartományspecifikus) adatait. Speciális adatokat egy önálló metódust vagy a magas szintű kategorizálási valósítható meg. Segítségével tovább pontosíthatja a 86-kategória besorolás tartományspecifikus modellek hozzáadásával funkcionál.

A csak a támogatott speciális információkat jelenleg celebrity felismerési és jellegzetes felismerést. Azok a személyek és a felhasználók csoport kategóriák és a világszerte jellegzetes hely tartományspecifikus finomításokra.

A tartomány-specifikus modellek használatára vonatkozó két lehetőség áll rendelkezésre:

### <a name="option-one---scoped-analysis"></a>A beállítás egy - hatókörrel rendelkező elemző
Csak a kiválasztott modell elemezni figyelőn egy HTTP POST-hívás. Ezt a beállítást Ha tudja, melyik modellt szeretné használni, akkor adja meg a modell neve, és csak kapott információt a modellre vonatkozó. Például használhatja ezt a beállítást csak keres celebrity-felismerés. A válasz lehetőségeket kínál a megfelelő celebrities mellett az vetett bizalmat eredményeiket listáját tartalmazza.

### <a name="option-two---enhanced-analysis"></a>A beállítás két - továbbfejlesztett elemzés
Vizsgálja meg a 86-kategória besorolás a kategóriák kapcsolatos további információkat. Ez a beállítás érhető el használható alkalmazások ahol felhasználók szeretné, hogy általános kép elemzés részletek mellett egy vagy több tartományspecifikus modelleket. Ez a metódus meghívásakor a 86-kategória besorolás osztályozó először nevezzük. Kategóriák egyezik ismert/megfelelő modellek, ha egy második osztályozó indítások menete a következő. Például ha "részletei = all" vagy "Részletek" tartalmaz "celebrities", a metódus meghívja a celebrity osztályozó után a 86-kategória osztályozó nevezik. Az eredmény "people_" kezdetű címkét tartalmaz.

## <a name="generating-descriptions"></a>Leírások generálása 
Számítógép Látástechnológiai API-algoritmusok vizsgálja meg a tartalmat a kép. Az elemzés a "description", teljes mondatokat emberek számára olvasható nyelven jelennek meg a alapját képezi. A leírás összefoglalja, a kép találttal. Számítógép Látástechnológiai API algoritmusok alapján azonosítja a kép objektumok különböző leírások állítanak elő. A leírások minden egyes kiértékeli, és abban, hogy pontszámot jön létre. A lista ezután visszaérkezik rendezett a legmagasabb abban, hogy pontszám legalacsonyabb. Kép feliratok létrehozni ezt a technológiát használó bot példát talál [Itt](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Példa Leírás létrehozása
![B & W épületek](./Images/bw_buildings.jpg) '
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
A számítógép stratégiai algoritmus színek kiolvassa a lemezképre. A színeket három kontextusban (előtér, háttér és teljes kép) elemzi. Ezek tizenkét 12 meghatározó hangsúlyos színek vannak csoportosítva. E hangsúlyos színeket fekete, kék, részekre, szürke, zöld, narancssárga, rózsaszín, lila, piros, pávakék, fehér és sárga. Attól függően, hogy a kép színeit visszaadott hexadecimális szín kód egyszerű fekete-fehér vagy hangsúlyos színeket.

Kép                                                       | Előtérben |Háttér| Színek
----------------------------------------------------------- | --------- | ------- | ------
![Külső hegyi](./Images/mountain_vista.jpg)            | Fekete     | Fekete   | Fehér
![Stratégiai virág elemzése](./Images/flower.jpg)               | Fekete     | Fehér   | Fehér, a fekete, a zöld
![Stratégiai vonat elemzése](./Images/train_station.jpg) | Fekete     | Fekete   | Fekete

### <a name="accent-color"></a>Hangsúlyos szín
Megfelel a legtöbb figyelemfelkeltő színét meghatározó színek és telítettségét keresztül a felhasználók számára a képfájl kinyert színét.

Kép                                                       | Válasz
----------------------------------------------------------- | ----
![Külső hegyi](./Images/mountain_vista.jpg)            | #BC6F0F
![Stratégiai virág elemzése](./Images/flower.jpg)               | #CAA501
![Stratégiai vonat elemzése](./Images/train_station.jpg) | #484B83


### <a name="black--white"></a>Fekete-fehér
Logikai jelző, amely azt jelzi, hogy a képfájl fekete & fehér vagy sem.

Kép                                                      | Válasz
---------------------------------------------------------- | ----
![Stratégiai épület elemzése](./Images/bw_buildings.jpg)      | True (Igaz)
![Stratégiai House kisebb adagokban is elemzése](./Images/house_yard.jpg)      | False (Hamis)

## <a name="flagging-adult-content"></a>Felnőtt tartalom megjelölése
A különféle visual osztályok között van a felnőtt és ellopható csoport, amely lehetővé teszi, hogy felnőtt anyagok észlelését, és korlátozza a szexuális tartalmat tartalmazó képek megjelenését. A felhasználói beállítások megfeleljen értékét állítható be a szűrőt a felnőtt és ellopható tartalom észleléséhez.

## <a name="optical-character-recognition-ocr"></a>Optikai karakter felismerési (OCR)
OCR technológia szöveges tartalom észleli a kép, és az azonosított szöveg kibontja a géppel olvasható karakter adatfolyamba. Az eredmény a Keresés és orvosi rekordokat, a biztonság és a banki például számos más célra használhatja. A szöveg nyelvét automatikusan felismeri. OCR időt takaríthat meg, és lehetővé teheti, hogy a szöveg helyett a szöveg katódsugárcsövön fényképeket biztosít a felhasználók kényelmét szolgálja.

OCR 25 nyelvet támogat. Ezeken a nyelveken vannak: arab, egyszerűsített kínai, hagyományos kínai, Cseh, dán, holland, angol, finn, francia, német, görög, magyar, olasz, japán, koreai, lengyel, lengyel, portugál, román, orosz, szerb (cirill és Latin), Szlovák, spanyol, svéd és török.

Ha szükséges, OCR javítja a felismert szöveg fokban, a kép vízszintes tengely körüli elforgatásának. OCR biztosít a keret koordináták minden szó az alábbi ábrán látható módon.

![OCR áttekintése](./Images/vision-overview-ocr.png) OCR követelményei:
- A bemeneti kép méretének 40 x 40 és 3200 x 3200 képpont között kell lennie.
- A lemezkép nem lehet nagyobb, mint 10 Megapixel.

Bemeneti kép forgatható által 90 fok több plusz egy kis szög, és "40 fokban megadva.

A szöveg felismerés pontosságát attól függ, hogy a kép minőségét. Egy pontatlan olvasási oka lehet az alábbi helyzetekben:
- Határozatlan képek.
- Kézzel vagy kurzív szöveg.
- Művészi betűstílusok.
- Kis méretű szöveges mérete.
- Összetett hátterek, árnyékok vagy tükröződést szöveg vagy perspektíva keresztül.
- Túl nagy méretű vagy hiányzó nagybetűvel szavak alapjait:
- A tömbindex, felső vagy áthúzott szöveg.

Korlátozások: A fényképekhez, ahol szöveget az eseménystreamek, téves származhatnak részben felismerhető szavakat. A néhány fényképekhez, különösen a szöveg nélkül fényképek pontosság sokkal függően változhat a kép típusa.

## <a name="recognize-handwritten-text"></a>Kézzel írt szöveg felismerése
Ez a technológia lehetővé teszi észleli, és kézzel írt szöveg kinyerése megjegyzések betűket, essays, tábla, űrlapok, stb. Különféle felületekkel és hátterekkel használható, például fehér papír, sárga jegyzetlapok vagy fehér tábla.

A kézírásos szövegek felismerése időt és energiát takarít meg, és növeli a hatékonyságot, hiszen lehetővé teszi, hogy szövegek átírása helyett egyszerűen lefényképezze az adott szöveget. Lehetővé teszi, hogy a digitize megjegyzések. A digitization lehetővé teszi a gyors és egyszerű keresés végrehajtásához. Ezen kívül a papírfelhasználást is csökkenti.

Bemeneti követelmények:
- Támogatott képformátum: JPEG, PNG vagy BMP.
- Kép fájlmérete 4 MB-nál kisebbnek kell lennie.
- Kép legalább 40 x 40, legfeljebb 3200 x 3200 kell lennie.

Megjegyzés: ez a technológia jelenleg előzetes verzióban, és kizárólag angol nyelvre érhető el.

## <a name="generating-thumbnails"></a>Miniatűrök létrehozása
A miniatűr a kis méretű kép megjelenítése. Változatos eszközök, például telefonokon, táblagépeken és számítógépeken létrehozásához szükséges különböző felhasználói élmény (UX) elrendezések és a miniatűrök méretét. Intelligens vágás, a számítógép Látástechnológiai API szolgáltatás segítségével tudja megoldani a problémát.

Miután lekérdezi a kiváló minőségű miniatűr létrehozott egy kép feltöltésével, és a számítógép Látástechnológiai API algoritmus elemzi az objektumok a lemezképben. Azt, majd levágja a kép a tartományban, követelményeinek (ROI). A kimenet az alábbi ábrán látható egy különös keretében lekérdezi jelenik meg. A generált miniatűr egy oldalarányát, amely eltér az eredeti oldalarányok alkalmazásához egy felhasználónak rendelkeznie kell az eredeti kép használatával jelenítheti meg.

A miniatűr algoritmus a következőképpen működik:

1. Zavaró elemeket eltávolítja a lemezképet, és ismeri fel a fő objektum, a tartományban, (ROI).
2. A megadott tartományban, a Képalapú levágja.
3. Az eredeti oldalarányok igazodik cél miniatűr változik.

![Miniatűrök](./Images/thumbnail-demo.png)
