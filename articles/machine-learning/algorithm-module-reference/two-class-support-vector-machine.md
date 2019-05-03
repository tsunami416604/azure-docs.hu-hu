---
title: 'Kétosztályos támogató Vektorgép: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a **Kétosztályos támogató Vektorgép** modul az Azure Machine Learning szolgáltatás egy a támogató vektorgép-algoritmust alapuló modell létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027930"
---
# <a name="two-class-support-vector-machine-module"></a>Kétosztályos támogató Vektorgép-modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával létrehoz egy modellt, amely a támogató vektorgép-algoritmust alapul. 

Támogatási vektor gépek (SVMs), egy felügyelt tanítás módszerek jól alapos tudományos kutatásnak osztályát. Ez a megvalósítás a megfelelő a előrejelzési a két lehetséges kimenetek, folyamatos vagy kategorikus változók alapján.

A modell paramétereket meghatározó, miután a modell betanítását képzési modulok használatával, és így egy *adatkészlet címkézett* , amely tartalmaz egy címkét vagy serkenti az eredményt.

## <a name="about-support-vector-machines"></a>Támogatási vektor gépekről

Támogatási vektor gépek között a legkorábbi gépi tanulási algoritmusok, és SVM modellek használt számos alkalmazás, a szöveges és bináris osztályozás az információk lekéréséhez. SVMs besorolási és regressziós folyamatokat is használható.

Ez a modell SVM felügyelt tanítási modell címkézett adatokkal igénylő. A betanítási folyamat algoritmus elemzi a bemeneti adatokat, és minták nevű többdimenziós funkció szóközzel felismeri a *hyperplane*.  Összes bemeneti példákat jelentésekként jelennek meg ezen a helyen pontok és a kimeneti oly módon, hogy a kategóriák szerint vannak csoportosítva, szélesebb, és törölje a lehető eseményáramlási kimaradást kategóriák vannak leképezve.

Előrejelzési a SVM algoritmust rendeli hozzá az új példák egy kategóriát, vagy a másik hozzárendeli őket, hogy ugyanazon terén. 

## <a name="how-to-configure"></a>Konfigurálása 

A modell típus javasoljuk, hogy az osztályozó által igénybe vett betanítása és normalizálása az adatkészlet.
  
1.  Adja hozzá a **Kétosztályos támogató Vektorgép** modult a kísérletvászonra.  
  
2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhat egy adott értékhalmazt argumentumként.  

3.  A **tevékenységsort**, adjon meg egy számot, amely azt jelzi, hogy a modell létrehozását, használja az ismétlések száma.  
  
     Ez a paraméter képzési sebesség és a pontosság közötti kompromisszum vezérlésére használható.  
  
4.  A **Lambda**, adjon meg egy értéket a súly L1 regularizációs használja.  
  
     A modell finomhangolása a regularizációs együttható használható. A nagyobb értékek megbüntetésére összetettebb modelleket.  
  
5.  A beállítást, **funkciók normalizálása**, ha azt szeretné, mielőtt képzési szolgáltatásai optimalizálására.
  
     Ha normalizálási képzés, mielőtt alkalmaz adatpontok az átlag, középre és méretezve, hogy rendelkezik a szórást egy egysége.
  
6.  A beállítást, **projekthez, hogy az egység a sphere**, együttható optimalizálására.
  
     Értékek területre egység előrejelzése azt jelenti, hogy képzés, mielőtt adatpontok középre 0 és méretezve, hogy rendelkezik a szórást egy egysége.
  
7.  A **véletlenszerű szám kezdőérték**, írja be az egész szám az egyik kezdőérték használható, ha azt szeretné, megismételhetőség frissítési kísérletei során.  Ellenkező esetben a rendszer órája érték lesz az egyik kezdőérték, ami kis mértékben eltérő eredményt eredményezhet frissítési kísérletei során.
  
9. Címkézett adatkészlet és a egy a [képzési modulok](module-reference.md):
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](train-model.md) modul.
  

10. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A modell paraméterek együtt a szolgáltatás súlyok származó képzési, összefoglaló kattintson a jobb gombbal a kimenetét [tanítási modell](./train-model.md), és válassza ki **Visualize**.

+ A betanított modellek használatával előrejelzéseket, csatlakozzon a betanított modellt a [Score Model](score-model.md) modul.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 