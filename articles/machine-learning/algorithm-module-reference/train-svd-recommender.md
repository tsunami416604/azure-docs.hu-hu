---
title: 'Vonat SVD ajánlója: Modul referencia'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Betanítás SVD ajánló modul az Azure Machine Learning betanításához egy Bayes-i an recommender az SVD algoritmus használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477408"
---
# <a name="train-svd-recommender"></a>SVD-ajánló betanítása

Ez a cikk ismerteti, hogyan használhatja a Train SVD ajánló modul az Azure Machine Learning designer (előzetes verzió). Ezzel a modullal az egyértékű bomlás (SVD) algoritmuson alapuló ajánlási modell betanítása.  

A Train SVD Recommender modul beolvassa a felhasználói cikk-minősítési háromszorosai adatkészletét. Egy képzett SVD-ajánlót ad vissza. Ezután a betanított modell segítségével előre jelezheti a minősítések, vagy javaslatokat generál, a [Score SVD ajánló](score-svd-recommender.md) modul használatával.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>További információk az ajánlási modellekről és az SVD ajánlóról  

Az ajánlási rendszer fő célja, hogy egy vagy több *elemet* ajánljon a rendszer *felhasználóinak.* Egy elemre lehet például film, étterem, könyv vagy dal. A felhasználó lehet személy, személyek csoportja vagy más, elembeállításokkal rendelkező entitás.  

Az ajánlórendszereknek két fő megközelítése van: 

+ A **tartalomalapú** megközelítés mind a felhasználók, mind az elemek számára használja a funkciókat. A felhasználók olyan tulajdonságokkal írhatók le, mint az életkor és a nem. Az elemeket olyan tulajdonságokkal lehet leírni, mint a szerző és a gyártó. A közösségi partnerkereső webhelyeken a tartalomalapú ajánlási rendszerek tipikus példáit találja. 
+ **Az együttműködésen alapuló szűrés** csak a felhasználók és az elemek azonosítóit használja. Ezeket az entitásokat implicit információkat kap a felhasználók által az elemeknek adott minősítések (ritka) mátrixából. A felhasználóról az általa értékelt elemekből és más olyan felhasználóktól tudhatunk meg többet, akik ugyanazokat az elemeket értékelték.  

Az SVD-ajánló a felhasználók és az elemek azonosítóit, valamint a felhasználók által az elemekhez adott minősítések mátrixát használja. Ez egy *együttműködésen alapuló ajánló*. 

Az SVD ajánlójáról további információt a megfelelő kutatási dokumentumban talál: [Mátrixfaktorizációs technikák az ajánlórendszerekhez](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>A Train SVD recommender beállítása  

### <a name="prepare-data"></a>Adatok előkészítése

A modul használata előtt a bemeneti adatokat kell a formátumot, hogy a javaslat modell elvár. A felhasználói cikk-minősítési háromszoros betanítási adatkészlet szükséges.

+ Az első oszlop felhasználói azonosítókat tartalmaz.
+ A második oszlop cikkazonosítókat tartalmaz.
+ A harmadik oszlop a felhasználói elem pár minősítését tartalmazza. A minősítési értékeknek numerikus típusúaknak kell lenniük.  

Az Azure Machine Learning tervezőjének **filmminősítési** adatkészlete (válassza **az Adatkészletek,** majd **a Minták**lehetőséget) bemutatja a várt formátumot:

![Film értékelések](media/module/movie-ratings-dataset.png)

Ebből a mintából láthatja, hogy egyetlen felhasználó több filmet értékelt. 

### <a name="train-the-model"></a>A modell betanítása

1.  Adja hozzá a Train SVD recommender modult a folyamathoz a tervezőben, és csatlakoztassa a betanítási adatokhoz.  
   
2.  A **Tényezők száma területen**adja meg az ajánlóval használandó tényezők számát.  
    
    Minden tényező azt méri, hogy a felhasználó mennyire kapcsolódik az elemhez. A tényezők száma is a dimenziós látens tényező térben. A felhasználók és elemek számának növekedésével jobb, ha nagyobb számú tényezőt állít be. De ha a szám túl nagy, a teljesítmény csökkenhet.
    
3.  **A javaslati algoritmus iterációinak száma** azt jelzi, hogy az algoritmusnak hányszor kell feldolgoznia a bemeneti adatokat. Minél magasabb ez a szám, annál pontosabbak az előrejelzések. A nagyobb szám azonban lassabb képzést jelent. Az alapértelmezett érték 30.

4.  A **tanulási sebesség**hez adjon meg egy 0.0 és 2.0 közötti számot, amely meghatározza a tanulás lépésméretét.

    A tanulási arány határozza meg a lépés méretét az egyes iterációk. Ha a lépés mérete túl nagy, előfordulhat, hogy túllépi az optimális megoldást. Ha a lépés mérete túl kicsi, a képzés hosszabb időt vesz igénybe, hogy megtalálja a legjobb megoldást. 
  
5.  Küldje el a folyamatot.  


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
