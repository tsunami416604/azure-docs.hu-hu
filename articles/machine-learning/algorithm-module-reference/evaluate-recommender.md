---
title: 'Az ajánló értékelése: modulok leírása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Azure Machine Learning kiértékelése modult az Ajánlói modellre vonatkozó előrejelzések pontosságának kiértékeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428537"
---
# <a name="evaluate-recommender"></a>Ajánló értékelése

Ez a cikk azt ismerteti, hogyan használható a kiértékelési ajánló modul a Azure Machine Learning Designerben (előzetes verzió). A cél az, hogy megmérjük a javaslati modell által készített előrejelzések pontosságát. A modul használatával különböző típusú ajánlásokat lehet kiértékelni:  
  
-   A felhasználóra és egy tételre előrejelzett minősítések    
-   Felhasználó számára ajánlott elemek  
  
Ha egy javaslati modell használatával hoz létre előrejelzéseket, a rendszer némileg eltérő eredményt ad vissza minden egyes támogatott előrejelzési típushoz. Az értékelést ajánló modul a pontozásos adathalmaz oszlopainak formátumát határozza meg. A pontszámmal rendelkező adatkészlet például a következőket tartalmazhatja:

- Felhasználói elemek minősítésének háromszorosa
- Felhasználók és az ajánlott elemek

A modul a megfelelő teljesítmény-mérőszámokat is alkalmazza az előrejelzés típusa alapján. 

  
## <a name="how-to-configure-evaluate-recommender"></a>A kiértékelési ajánló konfigurálása

A kiértékelést ajánló modul összehasonlítja az előrejelzési kimenetet egy, a megfelelő "alapvető igazság" adatokat tartalmazó javaslati modellel. A [score SVD ajánló](score-svd-recommender.md) modul például a kiértékeléssel elemezhető, kiértékelt adatkészleteket hoz létre.

### <a name="requirements"></a>Követelmények

Az ajánlás kiértékeléséhez a következő adatkészleteket kell bemenetként megadni. 
  
#### <a name="test-dataset"></a>Adatkészlet tesztelése

A tesztelési adatkészlet tartalmazza a "alapvető igazság" adatokat a felhasználó-elem minősítési háromszorosa formájában.  

#### <a name="scored-dataset"></a>Pontozásos adatkészlet

A pontozásos adatkészlet tartalmazza azokat az előrejelzéseket, amelyeket az ajánlási modell generált.  
  
A második adatkészlet oszlopai a pontozási folyamat során végrehajtott előrejelzéstől függenek. A pontozásos adatkészlet például a következők egyikét tartalmazhatja:

- Azok a felhasználók, elemek és minősítések, amelyeket a felhasználó valószínűleg az elemhez adna
- A felhasználók és az azokhoz ajánlott elemek listája 

### <a name="metrics"></a>Metrikák

A modell teljesítmény-metrikái a bemenet típusa alapján jönnek létre. A következő fejezetekben részletes információkat talál.

## <a name="evaluate-predicted-ratings"></a>Előre jelzett minősítések kiértékelése  

A prediktív minősítések kiértékelése során a pontszámmal rendelkező adatkészlet (az ajánló második bemenete) a következő igényeknek megfelelő, felhasználó által értékelt hármasokat kell tartalmaznia:
  
-   Az adatkészlet első oszlopa tartalmazza a felhasználói azonosítókat.    
-   A második oszlop az elemek azonosítóit tartalmazza.  
-   A harmadik oszlop a megfelelő felhasználói elemek minősítéseit tartalmazza.  
  
> [!IMPORTANT] 
> A sikeres értékeléshez az oszlopnevek `User`, `Item`és `Rating`nak kell lenniük.  
  
A kiértékeléshez az ajánló összehasonlítja a "alapvető igazság" adatkészletben szereplő minősítéseket a pontozásos adatkészlet előrejelzett minősítésével. Ezután kiszámítja az átlagos abszolút hibát (MAE) és a legfelső szintű, négyzetes hibát (GYÖKÁTLAGOS).



## <a name="evaluate-item-recommendations"></a>Elemek kiértékelése javaslatok

Amikor kiértékeli az elemek javaslatait, használjon egy pontozásos adatkészletet, amely tartalmazza az egyes felhasználók ajánlott elemeit:
  
-   Az adatkészlet első oszlopának tartalmaznia kell a felhasználói azonosítót.    
-   Az összes további oszlopnak tartalmaznia kell a megfelelő ajánlott elem-azonosítókat, amelyeket az adott elem a felhasználónak kell megrendelnie. 

Az adatkészlet csatlakoztatása előtt azt javasoljuk, hogy rendezze az adatkészletet úgy, hogy a legfontosabb elemek először is megtörténjenek.  

> [!IMPORTANT] 
> Az ajánló működésének kiértékeléséhez az oszlopnevek `User`, `Item 1`, `Item 2`, `Item 3` stb.  
  
Az ajánló kiértékelésével kiszámítja a normalizált diszkontált összesített nyereség (NDCG) átlagát, és visszaadja a kimeneti adatkészletben.  
  
Mivel az ajánlott elemek esetében nem lehetséges a tényleges "alapvető igazság", értékelje ki a felhasználó-elem minősítéseket a tesztelési adatkészletben a NDCG kiszámítása során. A kiértékeléshez az Ajánlói pontozási modulnak csak a "alapvető igazság" minősítéssel rendelkező elemekre vonatkozó javaslatokat kell létrehoznia (a tesztelési adatkészletben).  
  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
