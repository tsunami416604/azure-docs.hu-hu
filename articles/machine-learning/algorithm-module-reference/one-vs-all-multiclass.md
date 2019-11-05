---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a One-vs-All Multiclass modult a Azure Machine Learning szolgáltatásban egy többosztályos besorolási modell létrehozásához a bináris besorolási modellek együttes használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518163"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Ez a cikk azt ismerteti, hogyan használható a **One-vs-all Multiclass** modul inazure Machine learning Designer (előzetes verzió), hogy olyan besorolási modellt hozzon létre, amely több osztályt is megjósolhat az "egyetlen és az összes" megközelítés használatával.

Ez a modul olyan modellek létrehozásához hasznos, amelyek három vagy több lehetséges eredményt jeleznek, ha az eredmény a folyamatos vagy a kategorikus prediktív változóktól függ. Ez a módszer lehetővé teszi a bináris besorolási módszerek használatát is olyan problémák esetén, amelyek több kimeneti osztályt igényelnek.

### <a name="more-about-one-vs-all-models"></a>További információ az One-vs-all modellekről

Míg egyes besorolási algoritmusok lehetővé teszik a kettőnél több osztály használatát a kialakítás alapján, mások a lehetséges eredményeket két érték (bináris vagy kétosztályos modell) egyikére korlátozzák. A többosztályos besorolási feladatok esetében azonban még a bináris besorolási algoritmusok is alkalmazhatók különböző stratégiák használatával. 

Ez a modul implementálja az egyetlen és az *összes metódust*, amelyben a több kimeneti osztályhoz létrehoznak egy bináris modellt. Az egyes osztályokhoz tartozó bináris modellek mindegyikét a komplement (a modell minden más osztálya) alapján kell értékelni, mintha bináris besorolási probléma lenne. A rendszer ezeket a bináris osztályozók futtatásával végzi el az előrejelzést, és a legmagasabb szintű megbízhatósági pontszám alapján dönti el az előrejelzést.  

Lényegében létrehozzák az egyes modellek együttesét, és az eredményeket egyesítjük egyetlen olyan modell létrehozásához, amely minden osztályt előre jelez. Így minden bináris osztályozó használható egy-vs-all modell alapjaként.  

 Tegyük fel például, hogy egy [kétosztályos támogatási vektoros](two-class-support-vector-machine.md) modellt konfigurál, és a **One-vs-all Multiclass** modulba bemenetként adja meg. A modul kétosztályos támogatást hoz létre a kimeneti osztály összes tagjához, majd az összes osztály eredményének összevonásához alkalmazza az egy-vs-all metódust.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Az egy-vs-all osztályozó konfigurálása  

Ez a modul a bináris besorolási modellek együttesét hozza létre több osztály elemzéséhez. Ezért a modul használatához először be kell állítania és be kell tanítania egy **bináris besorolási** modellt. 

Ezután összekapcsolhatjuk a bináris modellt **One-vs-all Multiclass** modulhoz, és betanítjuk a modellek együttesét egy címkézett betanítási adatkészlettel a [betanítási modell](train-model.md) használatával.

Ha egyesíti a modelleket, annak ellenére, hogy a betanítási adatkészlet több osztállyal is rendelkezhet, a **One-vs-all Multiclass** több bináris besorolási modellt hoz létre, optimalizálja az egyes osztályok algoritmusát, majd egyesíti a modelleket.

1. Adja hozzá a **One-vs-all Multiclasst** a folyamathoz a tervezőben. Ezt a modult a **besorolási** kategóriában, a Machine learning-inicializálás alatt találja.

    Az **One-vs-all Multiclass** osztályozó nem rendelkezik saját konfigurálható paraméterekkel. A testreszabásokat a bemenetként megadott bináris besorolási modellben kell elvégezni.

2. Adjon hozzá egy bináris besorolási modellt a folyamathoz, és konfigurálja a modellt. Használhat például [kétosztályos támogatású vektoros gépet](two-class-support-vector-machine.md) vagy [kétosztályos, megnövelt döntési fát](two-class-boosted-decision-tree.md).

3. Adja hozzá a [betanítási modell](train-model.md) modult a folyamathoz, és kapcsolja össze a nem betanított osztályozó **One-vs-all Multiclass**kimenetét.

4. A [betanítási modell](train-model.md)másik bemenetén csatlakoztasson egy olyan címkézett betanítási adatkészletet, amelynek több osztályazonosítója van.

5. A folyamat futtatása.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után a modellt használhatja a többosztályos előrejelzések készítéséhez.

Azt is megteheti, hogy átadja a nem betanított osztályozó [modellt a modell](cross-validate-model.md) átállítására egy címkézett érvényesítési adatkészleten keresztül.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
