---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "67868982"
---
Néhány Bing-válasz tartalmaz URL-címeket a Bing által szolgáltatott miniatűr lemezképekhez. Átméretezheti és levágja a miniatűr rendszerképeit. 

> [!NOTE]
> Győződjön meg arról, hogy a miniatűr mérete és levágása egy keresési forgatókönyvet és a harmadik fél jogainak tiszteletben tartását biztosítja Bing Search API-használat és a megjelenítési követelmények alapján.


A képek átméretezéséhez adja meg a p (width) lekérdezési paramétert, a h (magasság) lekérdezési paramétert vagy mindkettőt a miniatűr URL-címében. Itt adhatja meg a szélességet és a magasságot képpontban megadva. Példa:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Ha csak a Width vagy csak a Height lekérdezési paramétert adja meg, a Bing karbantartja a kép oldalarányát. Ha a szélességet és a magasságot is megadja, és nem tartja karban a kép eredeti oldalarányát, a Bing fehér betöltést ad a kép szegélyéhez. Ha például egy 480x359-rendszerképet a 200x200-re való méretezés nélkül lakik, a teljes szélesség tartalmazza a képet, de a magasság 25 képpontot tartalmaz a képen látható tetején és alján. Ugyanez igaz, ha a képet 359x480, kivéve a bal és a jobb oldali szegély fehér kitöltést tartalmaz. Ha levágja a képet, a fehér kitöltés nincs hozzáadva.  

 
Az alábbi képen a miniatűr kép eredeti mérete (480x300) látható.  
  
![Eredeti tájképi rendszerkép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Az alábbi ábrán a 200x200-re átméretezett kép látható. A rendszer karbantartja a méretarányt, és a felső és az alsó szegély fehér színű (a fekete szegély beletartozik a kitöltés megjelenítéséhez).  
  
![Átméretezett tájképi rendszerkép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Ha olyan dimenziókat ad meg, amelyek nagyobbak, mint a rendszerkép eredeti szélessége és magassága, a képet a bal és a felső szegélyen fehér színűre kell kijelölnie.  
  
Egy kép körülvágásához adja meg a c (Crop) lekérdezési paramétert. A lehetséges értékek a következők lehetnek.  
  
- 4&mdash;vak arány  
- 7&mdash;intelligens arány  
  
Ha intelligens oldalarány-levágást kér (c = 7), a kép a kép területének közepéről lesz kiválasztva a kép méretarányának megtartása mellett. Az érdeklődési régió a Bing által a legtöbb importálási rész részét képező rendszerkép területe. A következő példa egy érdekes régiót mutat be.  
  
![Érdekes régió](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Ha átméretezi a rendszerképet, és intelligens oldalarányt szeretne leképezni, a rendszer a képet a kívánt méretre csökkenti a Méretarány megtartása mellett. Ekkor a rendszer levágja a képet az átméretezett méretek alapján. Ha például az átméretezett szélesség kisebb vagy egyenlő, mint a magasság, a rendszer a képet az érdeklődési terület középpontjának bal és jobb oldalán vágja le. Ellenkező esetben a rendszer levágja a képet az adott régió középpontjának tetején és alján.  
  
 
Az alábbi ábrán a 200x200-re csökkenthető az intelligens oldalarány levágása.  
  
![Fekvő kép kivágása a 200x200-be](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Az alábbi ábrán látható, hogy a képet a 200x100 az intelligens oldalarányok levágása segítségével csökkenti.  
   
![A fekvő kép bevágása a 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Az alábbi ábrán látható, hogy a képet a 100x200 az intelligens oldalarányok levágása segítségével csökkenti.  
  
![A fekvő kép bevágása a 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Ha a Bing nem tudja meghatározni a rendszerkép régióját, a Bing a vak arányt használja.  
  
Ha a vak arányt (c = 4) kéri, a Bing a következő szabályok használatával vágja le a rendszerképet.  
  
- If (az eredeti képszélesség/eredeti képmagasság) < (kép szélessége/a kért képmagasság), a kép a bal felső sarokban és a levágás alul látható.  
- Ha (az eredeti képszélesség/eredeti képmagasság) > (kép szélessége/a kért kép magassága), a rendszer a képet a középpontból méri, és a bal és a jobb oldalon levágja.  



Az alábbi képen egy 225x300 álló portré látható.  
  
![Eredeti napraforgó képe](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
A következő ábrán látható, hogy a képet a "a" vak arányok levágásával csökkentettük. A képet a bal felső sarokban kell mérni, ami a bevágási kép alsó részét eredményezi.  
  
![A napraforgó képének bevágása a 200x200-be](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Az alábbi ábrán látható, hogy a 200x100 a vak arányok levágásával csökkenthető. A képet a bal felső sarokban kell mérni, ami a bevágási kép alsó részét eredményezi.  
  
![A napraforgó-kép bevágása a 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Az alábbi ábrán látható, hogy a 100x200 a vak arányok levágásával csökkenthető. A képet a középpontból kell mérni, ami a levágott kép bal és jobb oldali részét eredményezi.  
  
![A napraforgó-kép bevágása a 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

