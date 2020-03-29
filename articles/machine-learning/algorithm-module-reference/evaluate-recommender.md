---
title: 'Ajánló értékelése: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Ajánló kiértékelése modult az Azure Machine Learningben az ajánlómodell-előrejelzések pontosságának kiértékeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312260"
---
# <a name="evaluate-recommender"></a>Ajánló értékelése

Ez a cikk ismerteti, hogyan használhatja az Ajánló kiértékelése modul az Azure Machine Learning designer (előzetes verzió). A cél az, hogy az előrejelzések pontosságának mérése, hogy egy javaslat modell tett. Ezzel a modullal különböző típusú javaslatokat értékelhet ki:  
  
-   Egy felhasználóra és egy elemre előre jelzett minősítések    
-   Felhasználónak ajánlott elemek  
  
Ha egy javaslati modell használatával hoz létre előrejelzéseket, a rendszer kissé eltérő eredményeket ad vissza a támogatott előrejelzési típusok mindegyikéhez. A Kiértékelés ajánló modul a pontozott adatkészlet oszlopformátumából származó előrejelzés típusát következtele. A pontozott adatkészlet például a következőket tartalmazhatja:

- A felhasználó-cikk-értékelés megháromszorozódik
- Felhasználók és az ajánlott elemek

A modul a megfelelő teljesítménymutatókat is alkalmazza, az előrejelzés típusa alapján. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Az ajánló kiértékelése

Az Ajánló kiértékelése modul összehasonlítja az előrejelzési kimenet et egy ajánlási modell használatával a megfelelő "földi igazság" adatokkal. Például a [Score SVD ajánló](score-svd-recommender.md) modul pontozott adatkészleteket hoz létre, amelyeket az Ajánló kiértékelése használatával elemezhet.

### <a name="requirements"></a>Követelmények

Kiértékelése Ajánló szükséges a következő adatkészletek bemenetként. 
  
#### <a name="test-dataset"></a>Teszt adatkészlet

A tesztadatkészlet tartalmazza a "ground truth" adatok formájában felhasználói cikk-értékelés háromszorosára.  

#### <a name="scored-dataset"></a>Pontozott adatkészlet

A pontozott adatkészlet tartalmazza a javaslatmodell által létrehozott előrejelzéseket.  
  
A második adatkészlet oszlopai a pontozási folyamat során végrehajtott előrejelzés típusától függenek. A pontozott adatkészlet például a következők valamelyikét tartalmazhatja:

- Felhasználók, elemek és a minősítések, hogy a felhasználó valószínűleg ad az elem
- A számukra ajánlott felhasználók és elemek listája 

### <a name="metrics"></a>Mérőszámok

A modell teljesítménymutatói a bemenet típusa alapján jönnek létre. A következő szakaszok részletesen ismertetik.

## <a name="evaluate-predicted-ratings"></a>Az előre jelzett minősítések értékelése  

Az előre jelzett minősítések kiértékelésekor a pontozott adatkészletnek (az ajánló kiértékelése második bemenetének) olyan felhasználói cikk-minősítési háromszorosokat kell tartalmaznia, amelyek megfelelnek ezeknek a követelményeknek:
  
-   Az adatkészlet első oszlopa tartalmazza a felhasználói azonosítókat.    
-   A második oszlop a cikkazonosítókat tartalmazza.  
-   A harmadik oszlop a megfelelő felhasználói cikk minősítéseket tartalmazza.  
  
> [!IMPORTANT] 
> A sikeres kiértékeléshez az `User`oszlopneveknek a , `Item`és `Rating`a , a, a, a, a, illetve.  
  
Értékelje az ajánló a "ground truth" adatkészlet minősítéseit a pontozott adatkészlet előre jelzett minősítéseivel. Ezután kiszámítja az átlagos abszolút hibát (MAE) és a gyökerű átlagos négyzetes hibát (RMSE).



## <a name="evaluate-item-recommendations"></a>Elemjavaslatok kiértékelése

Az elemjavaslatok kiértékelésekor használjon pontozott adatkészletet, amely tartalmazza az egyes felhasználók számára ajánlott elemeket:
  
-   Az adatkészlet első oszlopának tartalmaznia kell a felhasználói azonosítót.    
-   Minden további oszlopnak tartalmaznia kell a megfelelő ajánlott cikkazonosítókat, amelyek et az alapján kell megrendelni, hogy egy elem mennyire releváns a felhasználó számára. 

Az adatkészlet csatlakoztatása előtt azt javasoljuk, hogy rendezze az adatkészletet úgy, hogy a legfontosabb elemek az elsők.  

> [!IMPORTANT] 
> A Kiértékelendő ajánló működéséhez `User`az `Item 1` `Item 2`oszlopneveknek , , és `Item 3` így tovább kell lenniük.  
  
Értékelje ki az ajánló kiszámítja az átlagos normalizált diszkontált kumulatív nyereség (NDCG) és adja vissza a kimeneti adatkészletben.  
  
Mivel nem lehet tudni, hogy a tényleges "földi igazság" az ajánlott elemek, Kiértékelés ajánló használja a felhasználói elem minősítések a teszt adatkészletben, mint nyereség a számítás az NDCG. A kiértékeléshez az ajánló pontozási modulcsak ajánlásokat kell készítenie a "földi igazság" minősítéssel rendelkező elemekre (a teszt adatkészletben).  
  

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
