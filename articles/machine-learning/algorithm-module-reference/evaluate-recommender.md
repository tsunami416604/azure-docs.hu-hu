---
title: 'Az ajánló értékelése: modulok leírása'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Service kiértékelése az Ajánlói modult az Ajánlói modellek előrejelzési pontosságának kiértékeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517994"
---
# <a name="evaluate-recommender"></a>Az ajánló kiértékelése

Ez a cikk azt ismerteti, hogyan használható a **kiértékelési ajánló** modul a Azure Machine learning Designerben (előzetes verzió) a javaslati modell által készített előrejelzések pontosságának méréséhez. A modul használatával négy különböző típusú javaslatot lehet kiértékelni:  
  
-   Az adott felhasználóhoz és tételhez előrejelzett minősítések  
  
-   Egy adott felhasználó számára ajánlott elemek  
  
Ha egy javaslati modellel hoz létre előrejelzéseket, a rendszer némileg eltérő eredményt ad vissza az egyes támogatott előrejelzési típusoknál. Az **értékelést ajánló** modul a pontozásos adathalmaz oszlopainak formátumát határozza meg. A **pontszámmal rendelkező adatkészlet** például a következőket tartalmazhatja:

- felhasználói elemek minősítésének háromszorosa
- felhasználók és az ajánlott elemek

A modul a megfelelő teljesítmény-mérőszámokat is alkalmazza az előrejelzés típusa alapján. 

  
## <a name="how-to-configure-evaluate-recommender"></a>A kiértékelési ajánló konfigurálása

A **kiértékelést ajánló** modul összehasonlítja a javaslati modellben szereplő előrejelzések kimenetét a megfelelő "alapvető igazság" adattal. A [score SVD ajánló](score-svd-recommender.md) modul például a **kiértékelési javaslattal**elemezhető, pontszámmal ellátott adatkészleteket hoz létre.

### <a name="requirements"></a>Követelmények

Az **ajánlás kiértékeléséhez** a következő adatkészleteket kell bemenetként megadni. 
  
#### <a name="test-dataset"></a>Adatkészlet tesztelése

A **tesztelési adatkészlet** tartalmazza a "alapvető igazság" adatokat a **felhasználó-elem minősítési háromszorosa**formájában.  

#### <a name="scored-dataset"></a>Pontozásos adatkészlet

A **pontozásos adatkészlet** tartalmazza a javaslati modell által generált előrejelzéseket.  
  
Az ebben a második adatkészletben lévő oszlopok attól függnek, hogy milyen előrejelzést végeztek a pontozás során. A pontozásos adatkészlet például a következők bármelyikét tartalmazhatja:

- Azok a felhasználók, elemek és minősítések, amelyeket a felhasználó valószínűleg az elemhez adna
- A felhasználók és az azokhoz ajánlott elemek listája 

### <a name="metrics"></a>Mérőszámok

A modell teljesítmény-metrikái a bemenet típusa alapján jönnek létre. Részletekért tekintse meg a következő részeket:

+ [Előre jelzett minősítések kiértékelése](#evaluate-predicted-ratings)
+ [Elemek kiértékelése javaslatok](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Előre jelzett minősítések kiértékelése  

A prediktív minősítések kiértékelése során a **kiértékelt**adatkészletek (az ajánló második bemenete) a **felhasználó-elem minősítési háromszorosát**kell tartalmazniuk, amely megfelel a következő követelményeknek:
  
-   Az adatkészlet első oszlopa felhasználói azonosítókat tartalmaz.  
  
-   A második oszlop az elemek azonosítóit tartalmazza.  
  
-   A harmadik oszlop a megfelelő felhasználói elemek minősítéseit tartalmazza.  
  
> [!IMPORTANT] 
> A sikeres értékeléshez az oszlopnevek `User`, `Item`és `Rating`nak kell lenniük.  
  
A **kiértékeléshez az ajánló** összehasonlítja a fő igazság adatkészletben szereplő minősítéseket a pontozásos adatkészlet előre jelzett minősítésével, és kiszámítja az **átlagos abszolút hibát** (Mae) és a **legfelső szintű, négyzetes hibát** (gyökátlagos).



## <a name="evaluate-item-recommendations"></a>Elemek kiértékelése javaslatok

Az elemre vonatkozó javaslat kiértékelése során használjon egy pontozásos adatkészletet, amely tartalmazza az egyes felhasználók ajánlott elemeit:
  
-   Az adatkészlet első oszlopának tartalmaznia kell a felhasználói azonosítót.  
  
-   Az összes további oszlopnak tartalmaznia kell a megfelelő ajánlott elem-azonosítókat, amelyeket az adott elem a felhasználónak kell megrendelnie. 

    Az adatkészlet csatlakoztatása előtt azt javasoljuk, hogy rendezze az adatkészletet úgy, hogy a legfontosabb elemek legyenek elsőként.  



> [!IMPORTANT] 
> Az **ajánló működésének kiértékeléséhez** az oszlopnevek `User`, `Item 1`, `Item 2`, `Item 3` stb.  
  
Az **ajánló kiértékelésével** kiszámítja a normalizált diszkontált összesített nyereség (**NDCG**) átlagát, és visszaadja a kimeneti adatkészletben.  
  
Mivel az ajánlott elemek esetében nem lehetséges a tényleges "alapvető igazság", **értékelje** ki a felhasználó-elem minősítéseket a tesztelési adatkészletben a NDCG kiszámítása során. A kiértékeléshez az Ajánlói pontozási modulnak csak a (tesztelési adatkészletben lévő), az alapvető igazság minősítéssel rendelkező elemekre vonatkozó javaslatokat kell előkészítenie.  
  

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
