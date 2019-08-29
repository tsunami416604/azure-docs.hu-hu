---
title: 'Kétosztályos támogatású vektoros gép: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a kétosztályos **támogatású vektoros gépi** modul a Azure Machine learning szolgáltatásban olyan modell létrehozásához, amely a támogatási vektoros gép algoritmusán alapul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 23f5c638146472b72078e76745e557b6babe7a49
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128309"
---
# <a name="two-class-support-vector-machine-module"></a>Kétosztályos támogatású vektoros gépi modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal olyan modellt hozhat létre, amely a támogatási vektoros gép algoritmusán alapul. 

A support Vector Machines (SVMs) a felügyelt tanulási módszerek jól kutatott osztálya. Ez az adott implementáció a két lehetséges eredmény előrejelzésére alkalmas, akár folyamatos, akár kategorikus változók alapján.

A modell paramétereinek meghatározása után betanítja a modellt a betanítási modulok használatával, és olyan címkézett adatkészletet biztosít, amely címkét vagy eredmény oszlopot tartalmaz.

## <a name="about-support-vector-machines"></a>Tudnivalók a támogatási vektoros gépekről

A támogatott vektoros gépek a legkorábbi a gépi tanulási algoritmusok közé tartoznak, a SVM-modelleket pedig számos alkalmazásban használták, a szöveg-és képbesorolási adatok lekérése alapján. A SVMs a besorolási és a regressziós feladatokhoz is használható.

Ez a SVM-modell egy felügyelt tanulási modell, amelyhez címkézett adat szükséges. A betanítási folyamat során az algoritmus elemzi a bemeneti adatokat, és felismeri a mintákat a *hyperplane*nevű többdimenziós szolgáltatásban.  Az összes bemeneti példa erre a területre mutat, és a kimeneti kategóriákra van leképezve oly módon, hogy a kategóriák a lehető legszélesebb és egyértelműek legyenek.

Az előrejelzéshez a SVM algoritmus új példákat rendel egy kategóriához vagy a másikhoz, és hozzárendeli őket ugyanahhoz a helyhez. 

## <a name="how-to-configure"></a>Konfigurálás 

Ennél a modellnél javasolt az adatkészlet normalizálása, mielőtt az osztályozó betanítására használhatná.
  
1.  Adja hozzá a kétosztályos **támogatás Vector Machine** modult a kísérlethez.  
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek egy adott halmazát argumentumként.  

3.  Az **Ismétlések számának**megadásához írjon be egy számot, amely a modell létrehozásakor használt ismétlések számát jelöli.  
  
     Ezzel a paraméterrel szabályozhatja a kompromisszumot a betanítási sebesség és a pontosság között.  
  
4.  A **lambda**mezőben adja meg az L1 regularizációs súlyozásához használandó értéket.  
  
     Ez a regularizációs együttható a modell finomhangolására használható. Nagyobb értékek szankcionálják az összetettebb modelleket.  
  
5.  Válassza ki a lehetőséget, normalizálja a **szolgáltatásokat**, ha a betanítás előtt szeretné normalizálni a szolgáltatásokat.
  
     Ha a normalizálás alkalmazása előtt betanítást alkalmaz, az adatpontok középpontba kerülnek és méretezhetők, hogy a szórás egy egysége legyen.
  
6.  Válassza ki a lehetőséget, a **projekt az egység szférában**lehetőségre az együtthatók normalizálása érdekében.
  
     Az adategységek térközének kivetítése azt jelenti, hogy a betanítás előtt az adatpontok 0 és méretezhetők, hogy a szórás egy egysége legyen.
  
7.  A **véletlenszerű számú magok**mezőben adjon meg egy egész számot, amelyet magként kíván használni, ha biztosítani kívánja az egyes futtatások reprodukálhatóságát.  Ellenkező esetben a rendszer egy rendszeróra-értéket használ a magokként, ami némileg eltérő eredményeket eredményezhet a futtatások között.
  
9. Egy címkézett adatkészlet és egy betanítási [modul](module-reference.md)csatlakoztatása:
  
    -   Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](train-model.md) modult.
  

10. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha meg szeretné tekinteni a modell paramétereinek összegzését, valamint a képzésből beszerzett funkciók súlyozását, kattintson a jobb gombbal a [vonat modell](./train-model.md)kimenetére, majd válassza a **Megjelenítés**lehetőséget.

+ Ha a betanított modelleket szeretné használni az előrejelzések készítéséhez, csatlakoztassuk a betanított modellt a [pontszám modell](score-model.md) modulhoz.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 