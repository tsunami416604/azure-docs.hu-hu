---
title: 'Kétosztályos döntési erdő: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a Kétosztályos döntési erdő modul az Azure Machine Learning szolgáltatásban hozzon létre egy gépi tanulási modellt az döntési erdő algoritmus alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 73b7822c56e2b07eeefdedce1bce6d410d110ebc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411452"
---
# <a name="two-class-decision-forest-module"></a>Kétosztályos döntési erdő modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával hozzon létre egy gépi tanulási modellt az döntési erdő algoritmus alapján.  

Döntési erdő gyors, felügyelt ensemble modellek. Ez a modul használata jó választás, ha azt szeretné, a cél, amely legfeljebb két eredmények előrejelzésére. 

## <a name="understanding-decision-forests"></a>Döntési erdő ismertetése

A döntési erdő algoritmus fájlosztályozási feladatokhoz szánt metódus tanulási ruhaegyüttes. Ensemble módszerek az általános elvet alapulnak, amely ahelyett, hogy egy adott modellt hagyatkoznia, megkaphassa jobb eredményeket és a egy több általánosított modell több kapcsolódó modellek létrehozásához, és valamilyen módon kombinálva. Ensemble modellek általában jobb lefedettség és egyetlen döntési fák pontosságot biztosít. 

Számos módon egyéni modelleket hozhat létre, és ötvözze őket a ruhaegyüttes. Ez egy döntési erdő adott megvalósítását működik több döntési fák létrehozásával, majd **szavazási** a legnépszerűbb kimeneti osztályban. Szavazás: eredmények létrehozása egy ensemble modell a analógiájára módszerek egyike. 

+ Számos egyéni besorolás fák használatával jön létre, az egész adatkészletre, de másik (általában kiválasztással) kiindulási pontként. Ez eltér a véletlenszerű erdő módszer, amelyben az egyes döntési fák előfordulhat, hogy csak néhány véletlenszerű része használható az adatok és funkciók.
+ A döntési erdő fában fákban címkéket nem normalizált gyakorisága hisztogram jelenít meg. 
+ Az aggregációs folyamat ezen hisztogramok összegzi, és normalizálja az eredményt pedig a "valószínűségek" minden címke beolvasása. 
+ A magas előrejelzés rendelkező fák nagyobb súlyú lesz a ensemble, a végleges döntést.

Döntési fák általában besorolási feladatok számos előnye van:
  
- Nem lineáris döntési határok rögzítheti azokat.
- Betanítása, és a nagy mennyiségű adatot, előre jelezni, mivel ezek a számítási és a memória kihasználtsági hatékony.
- Szolgáltatás kiválasztása a képzés és a besorolási folyamatok van integrálva.  
- Fák zajos adatokat, és számos funkciója képes kezelni.  
- Nem számszerű modellek, ami azt jelenti, kezelheti a változatos disztribúciók adatok. 

Azonban egyszerű döntési fák is overfit adatokon, és kevesebb általánosítható, mint a fa együttesek.

További információkért lásd: [döntési erdő](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Konfigurálása
  
1.  Adja hozzá a **Kétosztályos döntési erdő** modult a kísérletvászonra az Azure Machine Learning, és nyissa meg a **tulajdonságok** modul ablaktáblán. 

    A modul alatt találja **Machine Learning**. Bontsa ki a **inicializálása**, majd **besorolási**.  
  
2.  A **metódus az újraszámítás**, az egyes fák létrehozásához használt módszer kiválasztására szolgál.  Választhat **zsákoló** vagy **replikálása**.  
  
    -   **Zsákoló**: Zsákolási néven is ismert *bootstrap összesítésével*. Ezzel a módszerrel új, véletlenszerűen mintavételi az eredeti adatkészletre a helyettesítő, amíg nincs egy adatkészletet az eredeti méretének által létrehozott minta-fákban van megnövelte.  
  
         A kimenetek a modellek egyesítése által *szavazási*, ez az összesítés űrlap. Besorolási döntési erdő fákban jelenít meg egy normalizálás előtt gyakorisága hisztogram címkék. Az összesítés a sum ezek hisztogramok és normalizálása "valószínűségek" minden címke lekéréséhez. Ily módon a magas előrejelzés rendelkező fák a végleges döntést a ensemble a nagyobb súlyú van.  
  
         További információkért tekintse meg a Wikipédia Bootstrap összesíti.  
  
    -   **Replikálása**: Replikáció esetén minden egyes fa be van tanítva, pontosan ugyanazt a bemeneti adatok. A meghatározása, mely split predikátum használt egyes facsomópont véletlenszerű marad, és a fák eltérő lesz.   
  
3.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhat egy adott értékhalmazt argumentumként.
  
4.  A **döntési fák száma**, írja be a döntési fák algoritmus a ensemble létrehozott maximális számát. További döntési fák létrehozásával lefedettségét esetlegesen kap, de képzési idő növeli.  
  
    > [!NOTE]
    >  Ez az érték vezérli jelenik meg, amikor megjelenítik a betanított modell fák számát is. Ha azt szeretné, vagy egy egyetlen fa nyomtatása, beállíthatja az érték 1-re. Azonban csak egy fa lehet (a kezdeti paraméterkészlet fa) előállított és nincs további ismétléseinek lesz végrehajtva.
  
5.  A **a döntési fák maximális mélysége**, írja be a maximális bármely döntési fa maximális mélységét. A fa mélysége növelése növelheti a pontosság kockázatára overfitting és megnövelt képzési időt.
  
6.  A **csomópontonként véletlen megszakítások száma**, írja be a minden csomópont a fa készítése során használandó megszakítások számát. A *felosztása* azt jelenti, hogy minden egyes szintjét a fához (node) funkcióit véletlenszerűen vannak osztva.
  
7.  A **minták levél csomópontonként minimális száma**, jelezze a minimális száma, amelyekre szükség van egy fa bármely terminál csomópontot (levél) hozhat létre.
  
     Ez az érték növelésével növeli az új szabályok létrehozása a küszöbértéket. Az alapértelmezett értéke 1, például egyetlen esetet okozhat egy új szabályt létrehozni. Az érték 5-re növeli, ha a betanítási adatok kellene az azonos feltételeket teljesítő legalább öt esetek tartalmaznak.  
  
8.  Válassza ki a **ismeretlen érték kategorikus funkciók engedélyezése** ismeretlen értékeket egy új csoportot a képzés és érvényesítési csoportok létrehozásának lehetősége. A modell ismert értékek kevésbé pontos lehet, de azt is lehetővé teszi új (ismeretlen) értékek jobb javaslatok érdekében. 

     Törölje ezt a beállítást, ha a modell elfogadhatja csak az abban található a betanítási adatok értékeket.
  
9. Címkézett adatkészletet, és az egyik csatolása a [képzési modulok](module-reference.md):  
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](./train-model.md) modul.  
  
    
## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A fa minden egyes ismétléskor létrehozott megtekintéséhez kattintson a jobb gombbal a kimenetét a [tanítási modell](./train-model.md) modult, majd válassza ki **Visualize**.
  
    Kattintson az elágazást feltárásához és a szabályokat az egyes csomópontok-fákban.

+ A modell pillanatkép menteni, kattintson a jobb gombbal a **betanított modell** kimenetét, és válassza a **modell mentése**. A mentett modell egymást követő kísérlet futtatásának nem frissül.

+ A modell pontozása használ, adja hozzá a **Score Model** egy modult.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 