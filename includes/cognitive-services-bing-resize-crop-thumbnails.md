---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753719"
---
Néhány Bing-válaszok a miniatűr képekhez, a Bing által kiszolgált URL-címeket tartalmazza. Előfordulhat, hogy átméretezése és vágja körül, a miniatűr képeket. 

> [!NOTE]
> Győződjön meg, hogy a mérete, és a miniatűr vágása adja meg a Keresés a forgatókönyv és a keresési Bing-API használatával szükség szerint a harmadik fél jogainak tiszteletben és megjelenítési követelményeihez.


Egy kép átméretezéséhez, például a szélesség lekérdezés h paramétert (magasság) lekérdezési paraméter vagy mindkettő a Miniatűr URL-címben. Adja meg a szélességét és magasságát (képpontban). Példa:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Ha csak a szélességét, vagy csak a magasság lekérdezési paramétert ad meg, a Bing a kép eredeti oldalarányok tart fenn. Ha szélességének és magasságának is megadhat, és nem megmaradjanak a kép eredeti oldalarányának megőrzésével, a Bing fehér kitöltési hozzáadása a kép szegélyének. Például levágás nélküli átméretez egy 200 x 200 480 x 359-lemezképet, ha a teljes szélesség lemezképét tartalmazza, de magasságát tartalmaz, a felső és alsó kép padding fehér 25 képpont. Ugyanez igaz lenne, ha a rendszerkép lett 359 x 480-as kivételével a bal és jobb oldali szegély tartalmazná fehér kitöltési. Ha a kép levágja, fehér margó nem kerül.  

 
Az alábbi képen az eredeti méretével, egy miniatűrképet (300 x 480-as).  
  
![Eredeti fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Az alábbi képen látható 200 x 200 átméretezte a képet. Az eredeti oldalarányok fenn lesz tartva, és a felső és alsó szegély helyeken fehér (a fekete szegély megtalálható kitöltése megjelenítéséhez).  
  
![Az átméretezett fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Adja meg, amely nagyobb, mint a kép eredeti szélessége és magassága dimenziókat, ha a kép a bal oldali és a felső szegély fehér számokhoz.  
  
Kép levágása, adjon meg a c (a vágás) lekérdezési paraméter. Az alábbiakban megadhatja azt a lehetséges értékeket.  
  
- 4&mdash;vakok arány  
- 7&mdash;arány smart  
  
Ha arány Smart vágása kér (c = 7), a lemezkép van csonkolva a kép régió a lényeges a központból passzív a kép eredeti oldalarányok megtartásával. A tartományban, az a terület a képe, amely meghatározza a Bing, a legtöbb importálás részt tartalmaz. Az alábbiakban látható egy példa a régióban a lényeges.  
  
![A lényeges terület](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Ha egy kép átméretezéséhez, és arány Smart vágása kérelem, az eredeti oldalarányok megtartásával a kép csökkenteni a kért méret. A lemezkép van majd csonkolva átméretezett dimenziók alapján. Például ha átméretezett szélessége kisebb vagy megegyezik magasságának, a lemezkép csonkolva bal és a régiót a lényeges közepén, jobb. Ellenkező esetben a kép csonkolva van a felső és alsó részén a tartományban, közepén.  
  
 
Az alábbiakban látható a lemezkép használatával arány Smart vágása 200 x 200-ra csökken.  
  
![A 200-as x 200 csonkolva fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Az alábbiakban látható a lemezkép használatával arány Smart vágása 200 x 100-ra csökken.  
   
![A 200-as x 100 csonkolva fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Az alábbiakban látható a lemezkép használatával arány Smart vágása 100 x 200-ra csökken.  
  
![100 x 200 csonkolva fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Bing a kép tartományban, nem tudja megállapítani, ha a Bing vak arány vágása használ.  
  
Ha vak arány vágása kér (c = 4), a Bing a következő szabályokat használ a kép levágása.  
  
- Ha (kép eredeti szélessége / kép eredeti magassága) < (kért Képszélességet / kép magassága a kért), a lemezkép felső sarokban marad, és középen körbevágjuk alsó mérése történik.  
- Ha (kép eredeti szélessége / kép eredeti magassága) > (a kért Képszélességet / kép magassága a kért), a rendszerkép mért, és a bal és jobb csonkolva.  



Az alábbiakban látható egy álló rendszerképet, amely 225 x 300.  
  
![Eredeti napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Az alábbiakban látható a rendszerkép használatával, vak arány vágása 200 x 200-ra csökken. A bal felső sarokban a kép alatt csonkolva alsó részén eredményez a kép mérése történik.  
  
![A 200-as x 200 csonkolva napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Az alábbiakban látható a rendszerkép használatával, vak arány vágása 200 x 100-ra csökken. A bal felső sarokban a kép alatt csonkolva alsó részén eredményez a kép mérése történik.  
  
![A 200-as x 100 csonkolva napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Az alábbiakban látható a rendszerkép használatával, vak arány vágása 100 x 200-ra csökken. A kép a központ eredményez a kép alatt csonkolva bal és jobb részeit mérése történik.  
  
![100 x 200 csonkolva napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

