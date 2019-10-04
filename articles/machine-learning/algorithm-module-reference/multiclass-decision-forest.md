---
title: 'Többosztályos döntési erdő: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a többosztályos döntési erdő modul a Azure Machine Learning szolgáltatásban egy gépi tanulási modell létrehozásához a *döntési erdő* algoritmusa alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b56f08dfd1a14ffedffb612bb8974086ee08ede7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128611"
---
# <a name="multiclass-decision-forest-module"></a>Többosztályos döntési erdő modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal gépi tanulási modellt hozhat létre a *döntési erdő* algoritmusa alapján. A döntési erdő egy olyan Ensemble-modell, amely gyorsan kiépíti a döntési fák egy sorozatát, miközben a címkézett adatokból tanul.

## <a name="more-about-decision-forests"></a>További információ a döntési erdőkről

A döntési erdő algoritmusa egy együttes tanulási módszer a besoroláshoz. Az algoritmus több döntési fa létrehozásával, majd a legnépszerűbb kimeneti osztályban való szavazással működik. A szavazás egy aggregációs űrlap, amelyben a besorolási döntési erdőben lévő összes fa a címkék nem normalizált gyakorisági hisztogramját adja vissza. Az összesítési folyamat összegzi ezeket a hisztogramokat, és normalizálja az eredményt az egyes címkék "valószínűségek" lekéréséhez. A magas előrejelző megbízhatósággal rendelkező fák nagyobb súlyt kapnak az Ensemble végső döntésében.

Az általános döntési fák nem számszerű modellek, ami azt jelenti, hogy különböző eloszlásokkal rendelkező adattípusokat támogatnak. Minden fában egy egyszerű teszt sorozata fut minden egyes osztályhoz, így a faszerkezetek szintjei addig növekednek, amíg el nem éri a levél csomópontot (döntés).

A döntési fák számos előnnyel rendelkeznek:

+ Nem lineáris döntési határokat is jelenthetnek.
+ A képzés és az előrejelzés során a számítási és a memóriahasználat hatékonyak.
+ Integrált funkciók kiválasztását és besorolását végzik.
+ Ezek rugalmasan működnek a zajos funkciók jelenlétében.

Azure Machine Learning a döntési erdő besorolása a döntési fák együttesét tartalmazza. Az Ensemble-modellek általában jobb lefedettséget és pontosságot biztosítanak, mint az önálló döntési fák. További információ: döntési [fák](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Többosztályos döntési erdő konfigurálása



1. Adja hozzá a többosztályos **döntési erdő** modult a kísérlethez az illesztőfelületen. Ez a modul a **Machine learning**, a **modell inicializálása**és a **besorolás**területen található.

2. A **Tulajdonságok** ablaktábla megnyitásához kattintson duplán a modulra.

3. Azújramintavételezési módszernél válassza ki az egyes fák létrehozásához használt módszert.  A csomagok és a replikálás közül választhat.

    + **Poggyász**: A poggyászt rendszerindítási *összesítésnek*is nevezik. Ebben a módszerben minden fát egy új mintán termesztenek, és az eredeti adatkészlet véletlenszerűen történő mintavételezésével jön létre, és csak akkor történik meg, ha nem rendelkezik az eredeti méretű adatkészlettel. A modellek kimeneteit a rendszer *szavazással*kombinálja, amely az Összesítés formáját képezi. További információ: a rendszerindítási összesítések Wikipedia-bejegyzése.

    + **Replikálás**: A replikáció során minden fát pontosan ugyanazok a bemeneti adatok képeznek. Annak meghatározása, hogy az egyes facsomópontok melyik felosztó predikátumot használják, a különböző fák létrehozásakor is véletlenszerűek maradnak.

   

4. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és adjon meg egy értéket argumentumként.


5. **Döntési fák száma**: Adja meg az Ensemble-ban létrehozható döntési fák maximális számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzés hosszabb ideig is növelheti.

    Ez az érték határozza meg az eredményekben megjelenített fák számát is a betanított modell megjelenítésekor. Egyetlen fa megjelenítéséhez vagy kinyomtatásához beállíthatja az értéket 1-re; azonban ez azt jelenti, hogy csak egy fát lehet előállítani (a kezdeti paraméterekkel rendelkező fát), és a rendszer nem végez további iterációkat.

6. **A döntési fák maximális mélysége**: Írjon be egy számot a döntési fa maximális mélységének korlátozásához. A fa mélységének növelésével növelheti a pontosságot, és megnövelheti a beilleszkedő és a megnövekedett betanítási időt is.

7. **Véletlenszerű felosztások száma/csomópont**: Adja meg a fa egyes csomópontjainak kiépítésekor használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) egyes szintjeinek funkciói véletlenszerűen vannak osztva.

8. **Minták minimális száma a levél csomópontjain**: Itt adhatja meg, hogy az esetek minimális száma alapján kell-e létrehozni bármely terminál-csomópontot (levél) a fában. Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét.

    Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.



10. Egy címkézett adatkészlet és egy betanítási modul csatlakoztatása:

    + Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](./train-model.md) modult.

11. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha meg szeretné tekinteni az egyes iterációkban létrehozott fát, kattintson a jobb gombbal a [Train Model](./train-model.md) modul kimenetére, és válassza a **Megjelenítés**lehetőséget.
+ Ha meg szeretné tekinteni az egyes csomópontok szabályait, kattintson az egyes faszerkezetekre a felosztások részletezéséhez.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 