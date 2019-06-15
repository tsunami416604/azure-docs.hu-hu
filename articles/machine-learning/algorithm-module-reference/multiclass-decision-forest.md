---
title: 'Döntési erdő osztályú osztályozási műveletet: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: A osztályú döntési erdő modul használata az Azure Machine Learning szolgáltatásban hozhat létre a machine learning-modell alapján a *döntési erdő* algoritmus.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 10364d014431a500e7c38a02d47f432cd464feb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411492"
---
# <a name="multiclass-decision-forest-module"></a>A modul osztályú döntési erdő

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával hozzon létre egy gépi tanulási modell alapján a *döntési erdő* algoritmus. Döntési erdő egy olyan gyorsan döntési fákat, a címkézett adatokból tanulás közben sorozatát ensemble modell.

## <a name="more-about-decision-forests"></a>További információ a döntési erdő

A döntési erdő algoritmus tanulási besorolási módszert a ruhaegyüttes. Az algoritmus működik több döntési fák létrehozásával, majd *szavazási* a legnépszerűbb kimeneti osztályban. Szavazás az egy képernyő, az összesítés, egy besorolási döntési melyik fákban erdő címkéket nem normalizált gyakorisága hisztogram kimenete. Az aggregációs folyamat ezen hisztogramok összegzi, és normalizálja az eredményt pedig a "valószínűségek" minden címke beolvasása. A magas előrejelzés rendelkező fák a végleges döntést a ensemble a nagyobb súlyú rendelkezik.

Döntési fák általában olyan nem számszerű modellek, ami azt jelenti, adatokat a változatos disztribúciók támogatják. Fákban egyszerű tesztek sorozatát futtatja az egyes osztályok faszerkezetben szintjének növelése, amíg el nem levélcsomópont (döntés).

Döntési fák számos előnye van:

+ Nem lineáris döntési határok jelölnek.
+ Azok a számítási és a memóriahasználat hatékony betanítási és Predikciós során.
+ Akkor hajtsa végre az integrált szolgáltatás kiválasztása és besorolása.
+ Azok a rugalmas zajos szolgáltatások folytonosságát.

Az Azure Machine Learning döntési erdő osztályozó döntési fák részének áll. Ensemble modellek általában jobb lefedettség és egyetlen döntési fák pontosságot biztosít. További információkért lásd: [döntési fák algoritmus](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Osztályú döntési erdő konfigurálása



1. Adja hozzá a **osztályú döntési erdő** modult a kísérletvászonra a felületen. Ez a modul alatt található **Machine Learning**, **modell inicializálása**, és **besorolási**.

2. A modul megnyitásához kattintson duplán a **tulajdonságok** ablaktáblán.

3. A **metódus az újraszámítás**, az egyes fák létrehozásához használt módszer kiválasztására szolgál.  Zsákolási vagy replikációs közül választhat.

    + **Zsákoló**: Zsákolási néven is ismert *bootstrap összesítésével*. Ezzel a módszerrel új, véletlenszerűen mintavételi az eredeti adatkészletre a helyettesítő, amíg nincs egy adatkészletet az eredeti méretének által létrehozott minta-fákban van megnövelte. A kimenetek a modellek egyesítése által *szavazási*, ez az összesítés űrlap. További információkért tekintse meg a Wikipédia Bootstrap összesíti.

    + **Replikálása**: Replikáció esetén minden egyes fa be van tanítva, pontosan ugyanazt a bemeneti adatok. A meghatározása, mely split predikátum használt egyes facsomópont véletlenszerű létrehozásával különböző fák marad.

   

4. Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.

    + **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és adja meg az argumentumokkal értékek egy halmazát.


5. **Döntési fák száma**: Írja be a döntési fák algoritmus a ensemble létrehozott maximális számát. További döntési fák létrehozásával lefedettségét esetlegesen kap, de képzési idő növelheti.

    Ez az érték vezérli jelenik meg az eredmények között, amikor megjelenítik a betanított modell fák számát is. Vagy egy egyetlen fa nyomtatása, beállíthatja az érték 1. azonban ez azt jelenti, hogy csak egy fa lehet (a kezdeti paraméterkészlet fa) előállítása, és nincs további ismétléseinek el kell végezni.

6. **A döntési fák maximális mélysége**: Írja be a maximális bármely döntési fa maximális mélységét. A fa mélysége növelése növelheti a pontosság kockázatára overfitting és megnövelt képzési időt.

7. **Csomópontonként véletlen megszakítások száma**: Írja be az egyes csomópontok a fa készítése során használandó elágazást. A *felosztása* azt jelenti, hogy minden egyes szintjét a fához (node) funkcióit véletlenszerűen vannak osztva.

8. **Minták levél csomópontok minimális száma**: Adja meg a minimális száma, amelyekre szükség van egy fa bármely terminál csomópontot (levél) hozhat létre. Ez az érték növelésével növeli az új szabályok létrehozása a küszöbértéket.

    Az alapértelmezett értéke 1, például egyetlen esetet okozhat egy új szabályt létrehozni. Az érték 5-re növeli, ha a betanítási adatok kellene az azonos feltételeket teljesítő legalább öt esetek tartalmaznak.



10. Csatlakozzon a címkézett adatkészlet, és a képzési modulok egyike:

    + Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](./train-model.md) modul.

11. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A fa minden egyes ismétléskor létrehozott megtekintéséhez kattintson a jobb gombbal a kimenetét a [tanítási modell](./train-model.md) modult, majd válassza ki **Visualize**.
+ A szabályokat az egyes csomópontok megtekintéséhez kattintson az elágazást feltárásához-fákban.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 