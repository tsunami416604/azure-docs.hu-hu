---
title: 'Többosztályos növelt döntési fa: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használható a többosztályos növelt döntési famodul a Azure Machine Learning szolgáltatásban a címkézett információkkal rendelkező osztályozó létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 08/22/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4a9cf134a3db5b9bc62cd0f9054aefa6092954d9
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129912"
---
# <a name="multiclass-boosted-decision-tree"></a>Többosztályos növelt döntési fa

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy gépi tanulási modellt, amely a megnövelt döntési fák algoritmusán alapul.

A megnövelt döntési fa egy olyan együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább. Az előrejelzések együttesen a fák együttesén alapulnak.

## <a name="how-to-configure"></a>Konfigurálás 

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás egy felügyelt tanulási módszer, szüksége van egy *címkével ellátott* adatkészletre, amely tartalmazza az összes sor értékét tartalmazó címke oszlopot.

Az ilyen típusú modellt a [Train modellel](././train-model.md)is betaníthatja. 

1.  Adja hozzá a többosztályos **növelt döntési fa** modult a kísérlethez.

1.  Válassza ki, hogyan szeretné képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek egy adott halmazát argumentumként.


    *  A kihagyott **levelek maximális száma a fa** korlátaiban a bármely fában létrehozható terminál-csomópontok (levelek) maximális száma.
    
        Ennek az értéknek a növelésével növelheti a fa méretét, és nagyobb pontosságot érhet el, ha a túlilleszkedés és a hosszabb betanítási idő fennáll.
  
    * A **mintavételek minimális száma a levél csomópontjaiban** azt jelzi, hogy hány esetben szükséges a terminál csomópont (levél) létrehozása a fában.  

         Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.

    * A **tanulási ráta** meghatározza a lépés méretét a tanulás során. Adjon meg egy 0 és 1 közötti számot.

         A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassabban konvergál az optimális megoldáson. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik.

    * A felépített **fák száma** azt jelzi, hogy hány döntési fát kell létrehozni az Ensemble-ban. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.

    *  A **véletlenszerű számú magok** opcionálisan nem negatív egész számot állítanak be a véletlenszerű mag értékeként való használatra. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.  

         A véletlenszerű mag beállítása alapértelmezés szerint 42. A különböző véletlenszerű magok használatával végzett egymást követő futtatások eltérő eredménnyel rendelkezhetnek.

> [!Note]
> Ha az **oktatói módot** **egyetlen paraméterként**állítja be, csatlakoztasson egy címkézett adatkészletet és a betanítási [modell](./train-model.md) modult.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
