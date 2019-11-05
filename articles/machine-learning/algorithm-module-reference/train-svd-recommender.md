---
title: 'A Train SVD ajánló: modul referenciája'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Service-ben a SVD ajánló modult a Bayes-ajánlásoknak a SVD algoritmus használatával történő betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515667"
---
# <a name="train-svd-recommender"></a>SVD-ajánló

Ez a cikk azt ismerteti, hogyan használható a **SVD ajánló** modul a Azure Machine learning Designerben (előzetes verzió). Ezzel a modullal a **SVD** (Single Value dekompozíció) algoritmus alapján betaníthatja a javaslatok modelljét.  

A **Train SVD ajánló** modul beolvassa a felhasználói elemek minősítésének háromszorosát tartalmazó adatkészletet. Egy betanított SVD-ajánlót ad vissza. Ezután használhatja a betanított modellt a minősítések előrejelzéséhez vagy javaslatok létrehozásához a [score SVD ajánló](score-svd-recommender.md) modul használatával.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>További tudnivalók a javaslatok modelljeiről és a SVD ajánlóról  

Egy ajánlási rendszer fő célja, hogy a rendszer egy vagy több *elemét* javasolja a *felhasználóknak* . Egy elem például film, étterem, könyv vagy zeneszám lehet. A felhasználó lehet személy, csoport vagy más entitás, amely elem-beállításokkal rendelkezik.  

Az ajánlott rendszerek két fő megközelítéssel rendelkeznek. 

+ Az első a **Content-based** megközelítés, amely lehetővé teszi a felhasználók és elemek funkcióinak használatát. A felhasználókat olyan tulajdonságok írják le, mint például az életkor és a nemek, és az elemeket a szerző és a gyártó tulajdonságai is meghatározhatják. A közösségi partnerkereső webhelyeken tipikusan példákat talál a Content-based ajánlási rendszerekre. 
+ A második módszer az **együttműködésen**alapuló szűrés, amely kizárólag a felhasználók és az elemek azonosítóit használja, és a felhasználók által az elemekhez megadott minősítési (ritka) mátrixból beolvassa az ezen entitásokra vonatkozó implicit információkat. Megtudhatja, hogy egy felhasználó a minősítéssel rendelkező elemekről, illetve azokról a felhasználókról, akik azonos elemeket értékeltek.  

A SVD ajánló a felhasználók és az elemek azonosítóit, valamint a felhasználók által az elemeknek megadott minősítési mátrixot használja. **Együttműködésen alapuló ajánló**. 

A SVD ajánlóval kapcsolatos további információkért tekintse meg a kapcsolódó kutatási papírt: [Matrix faktorizációs Techniques for ajánló Systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>A Train SVD ajánló konfigurálása  

+ [A betanítási adatfeldolgozás előkészítése](#prepare-data)
+ [A modell betanítása](#train-the-model)

### <a name="prepare-data"></a>Adatok előkészítése

A modul használatának megkísérlése előtt elengedhetetlen, hogy az adatai a javaslati modell által várt formátumban legyenek. A **felhasználó-tétel minősítéssel rendelkező triplák** betanítási adatkészlete kötelező.

#### <a name="required-dataset-of-user-item-ratings"></a>A felhasználói elemek minősítéséhez szükséges adatkészlet

Fontos, hogy a betanításhoz használt bemeneti adatok a megfelelő formátumban tartalmazzák a megfelelő típusú adatokat: 

+ Az első oszlopnak felhasználói azonosítókat kell tartalmaznia.
+ A második oszlopnak tartalmaznia kell az elemek azonosítóit.
+ A harmadik oszlop tartalmazza a felhasználó-tétel párok minősítését. A minősítési értékeknek numerikus típusúnak kell lenniük.  

                                                                                                                                                                                                           

Az **éttermi minősítési** adatkészlet Azure Machine learning Designerben (kattintson a **mentett adatkészletek** , majd a **minták**) a várt formátumot mutatja:

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Ebből a mintából láthatja, hogy egyetlen felhasználó minősítése két külön étterem. 

### <a name="train-the-model"></a>A modell tanítása

1.  Adja hozzá a **Train SVD Ajánlói** modult a folyamathoz a tervezőben, és kapcsolja össze a betanítási adataival.  
   
2.  A **faktorok számának**megadásához írja be az ajánlóval használandó tényezők számát megadó számot.  
    
    Minden tényező méri, hogy a felhasználó mennyit kapcsolódott az elemhez. A tényezők száma egyben a látens dimenzióját is. A felhasználók és az elemek számának növelésével jobb, ha nagyobb számú tényezőt állít be. Ha azonban a szám túl nagy, csökkenhet a teljesítmény.
    
3.  Az **ajánlási algoritmusok iterációinak száma**azt jelzi, hogy az algoritmus hányszor dolgozza fel a bemeneti adatokat. Minél nagyobb ez a szám, annál pontosabbak a jóslatok; a képzés azonban lassabb. Az alapértelmezett érték 30.

4.  A **tanulási arány**mezőbe írjon be egy 0,0 és 2,0 közötti számot, amely meghatározza a lépés méretét a tanulás során.

    A tanulási arány meghatározza az egyes iterációkban végrehajtott lépés méretét. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik. 
  
5.  A folyamat futtatása.  


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
