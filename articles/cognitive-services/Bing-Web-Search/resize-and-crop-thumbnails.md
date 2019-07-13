---
title: Méretezze át, és vágja körül képminiatűröket – a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan méretezze át és vágja körül, a Bing keresési API-k által biztosított miniatűrök.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868048"
---
# <a name="resize-and-crop-thumbnail-images"></a>Méretezze át és vágja körül, a miniatűrök

Néhány válasz a Bing keresési API-kon keresztül is tartalmazó URL-címek, Bing, átméretezheti és körülvágása által kiszolgált miniatűr képeket, és tartalmazhat lekérdezési paramétereket. Példa:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Ha jelenítjük meg ezeket a miniatűrök egy részét, adja meg a egy beállítást, a fennmaradó képek megtekintését.

> [!NOTE]
> Győződjön meg, hogy vágás és a miniatűr képek átméretezésének figyelembe veszi a harmadik féltől származó jogosultságok, a Bing Search API által kért keresés a forgatókönyv biztosít [használja, és megjelenítési követelményeihez](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Miniatűr átméretezése 

Méretezze át a miniatűr, a Bing javasolja, hogy csak az egyiket adja a `w` (szélesség) vagy `h` (magasság) lekérdezési paraméterek a Miniatűr URL-címben. Csak a magasság és szélesség megadása lehetővé teszi, hogy a Bing, a kép eredeti oldalarány karbantartása. Adja meg a szélességét és magasságát (képpontban). 

Például, ha az eredeti miniatűr 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

És ezt szeretné csökkenteni a méretét, és állítsa a `w` paramétert az új értéket (például `336`), és távolítsa el a `h` paraméter:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Ha csak a szélességének vagy magasságának miniatűr ad meg, a kép eredeti oldalarányának fenn kell tartani. Ha mindkét paramétert is meghatároz, és az eredeti oldalarányok nem kezelt, Bing fehér kitöltés a kép szegélyének hozzáadja.

Például levágás nélküli átméretez egy 200 x 200 480 x 359-lemezképet, ha a teljes szélesség a lemezképet fog tartalmazni, de magasságát fogja tartalmazni, a felső és alsó kép padding fehér 25 képpont. Ha a rendszerkép sikeresen 359 x 480-as, a bal és jobb oldali szegély fehér kitöltési tartalmaz. Ha a kép levágja, fehér margó nem kerül.  

Az alábbi képen az eredeti méretével, egy miniatűrképet (300 x 480-as).  
  
![Eredeti fekvő kép](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Az alábbi képen látható 200 x 200 átméretezte a képet. Az eredeti oldalarányok fenn lesz tartva, és a felső és alsó szegély helyeken fehér (a fekete szegély megtalálható kitöltése megjelenítéséhez).  
  
![Az átméretezett fekvő kép](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Adja meg, amely nagyobb, mint a kép eredeti szélessége és magassága dimenziókat, Bing a fehér kitöltési hozzáadása a bal oldali és a felső szegély.  

## <a name="request-different-thumbnail-sizes"></a>Miniatűr különböző méretű kérelem

Kérjen egy másik miniatűr kép mérete, távolítsa el minden lekérdezési paramétert a Miniatűr URL-CÍMÉT, kivéve a `id` és `pid` paramétereket. Majd adja hozzá vagy a `&w` (szélesség) vagy `&h` lekérdezési paramétert (magasság) képpont, de nem mindkettőt a kívánt lemezkép méretének. A Bing megőrzi a kép eredeti oldalarányának megőrzésével. 

A fenti URL-165 képpont a kép szélessége növelése érdekében használja a következő URL-címe:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Ha olyan lemezképet, amely nagyobb, mint a kép eredeti méretének kér, a Bing hozzáadja a kép körül fehér kitöltési, igény szerint. Például ha a kép eredeti méretének 474 x 316 és beállított `&w` 500-as, a Bing adja vissza egy 500 x 333 rendszerképet. Ez a rendszerkép fog rendelkezni a felső és alsó széle és a bal és jobb oldali kitöltése 13 képpont mentén fehér kitöltési 8,5 képpont.

Fehér kitöltési hozzáadása, ha a kért méret nagyobb, mint a kép eredeti méretének megakadályozni a Bing, állítsa be a `&p` lekérdezési paraméter 0 értékre. Ha például belefoglalja a `&p=0` fenti URL-címét, a Bing paraméter adja vissza egy 474 x 316 kép helyett egy 500 x 333 rendszerképet:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Ha mindkét megadott `&w` és `&h` lekérdezési paraméterek, a Bing megőrzi a kép eredeti oldalarányok, és hozzáadja a fehér kitöltési, igény szerint. Például, ha a kép eredeti mérete 474 x 316 és 200 x 200 beállította a szélességének és magasságának paraméterek (`&w=200&h=200`), a Bing, a felső és alsó fehér kitöltése 33 képpont tartalmazó lemezképek adja vissza. Ha a `&p` lekérdezési paraméter, a Bing 200 x 134 kép adja vissza.

## <a name="crop-a-thumbnail"></a>A miniatűr levágása 

Kép levágása, hogy tartalmazzák a `c` (a vágás) lekérdezési paraméter. A következő értékeket használhatja:
  
- `4` &mdash; Titkos arány  
- `7` &mdash; Az intelligens arány  

### <a name="smart-ratio-cropping"></a>Vágása arány Smart

Ha arány Smart vágása kér (beállításával a `c` paramétert `7`), a Bing fog kép vágása, a tartományban, a központ passzív, a kép eredeti oldalarányok megtartásával. A tartományban, az a terület a képe, amely meghatározza a Bing, a legtöbb importálás részt tartalmaz. Az alábbiakban látható egy példa a régióban a lényeges.  
  
![A lényeges terület](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Ha egy kép átméretezéséhez, és kérelem arány Smart levágás, a Bing a kép a kért méret az eredeti oldalarányok megtartásával csökkenti. A Bing majd körülvágja a képet, az átméretezett dimenziók alapján. Ha átméretezett szélessége kisebb vagy megegyezik magasságának, például a Bing körülvágása lesz a lemezképet, a bal oldali és a régiót a lényeges közepén, a jobb. Ellenkező esetben a Bing fog vágja körül, azt hogy felső és alsó részén a tartományban, közepén.  
  
 
Az alábbiakban látható a lemezkép használatával arány Smart vágása 200 x 200-ra csökken. A Bing méri a bal felső sarokban a lemezképet, mert a rendszerkép alsó részén csonkolva van. 
  
![A 200-as x 200 csonkolva fekvő kép](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Az alábbiakban látható a lemezkép használatával arány Smart vágása 200 x 100-ra csökken. A Bing méri a bal felső sarokban a lemezképet, mert a rendszerkép alsó részén csonkolva van. 
   
![A 200-as x 100 csonkolva fekvő kép](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Az alábbiakban látható a lemezkép használatával arány Smart vágása 100 x 200-ra csökken. Bing méri a lemezképet a központból, mert hiányosak a kép jobb és bal oldali részeit.
  
![100 x 200 csonkolva fekvő kép](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

A Bing a kép tartományban, nem tudja megállapítani, ha a szolgáltatás által használt titkos arány vágása.  

### <a name="blind-ratio-cropping"></a>Titkos vágása arány

Ha vak arány vágása kér (beállításával a `c` paramétert `4`), a Bing a következő szabályokat használ a kép levágása.  
  
- Ha `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, a rendszerkép mért bal felső sarokban, és alsó csonkolva.  
- Ha `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, a rendszerkép mért, és a bal és jobb csonkolva.  

Az alábbiakban látható egy álló rendszerképet, amely 225 x 300.  
  
![Eredeti napraforgó kép](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Az alábbiakban látható a rendszerkép használatával, vak arány vágása 200 x 200-ra csökken. A bal felső sarokban, a kép alatt csonkolva alsó részén eredményez a kép mérése történik.  
  
![A 200-as x 200 csonkolva napraforgó kép](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Az alábbiakban látható a rendszerkép használatával, vak arány vágása 200 x 100-ra csökken. A bal felső sarokban, a kép alatt csonkolva alsó részén eredményez a kép mérése történik.  
  
![A 200-as x 100 csonkolva napraforgó kép](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Az alábbiakban látható a rendszerkép használatával, vak arány vágása 100 x 200-ra csökken. A kép a központ eredményez a kép alatt csonkolva bal és jobb részeit mérése történik.  
  
![100 x 200 csonkolva napraforgó kép](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>További lépések

* [Mik azok a Bing Search APIs?](bing-api-comparison.md)
* [A Bing keresési API-t használja, és megjelenítési követelményeihez](use-display-requirements.md)
