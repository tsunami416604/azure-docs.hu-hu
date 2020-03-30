---
title: Egy-vs-All többosztályos
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a One-vs-All többosztályos modult az Azure Machine Learningben egy többosztályos besorolási modell létrehozásához bináris besorolási modellek együtteséből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456080"
---
# <a name="one-vs-all-multiclass"></a>Egy-vs-All többosztályos

Ez a cikk ismerteti, hogyan használhatja a One-vs-All többosztályos modul az Azure Machine Learning designer (előzetes verzió). A cél egy besorolási modell létrehozása, amely képes megjósolni több osztály, az *egy az összes* megközelítés használatával.

Ez a modul olyan modellek létrehozásához hasznos, amelyek három vagy több lehetséges kimenetelt jósolnak, ha az eredmény folyamatos vagy kategorikus előrejelző változóktól függ. Ez a módszer azt is lehetővé teszi, hogy bináris osztályozási módszerek et használjon a több kimeneti osztályt igénylő problémákhoz.

### <a name="more-about-one-versus-all-models"></a>További információk az egy az összeshez modellekről

Egyes osztályozási algoritmusok több osztály használatát teszik lehetővé. Mások a lehetséges eredményeket két érték (bináris vagy kétosztályos modell) egyikére korlátozzák. De még a bináris osztályozási algoritmusok is adaptálhatók a többosztályú osztályozási feladatokhoz különböző stratégiákon keresztül. 

Ez a modul megvalósítja az egy az összeshez metódust, amelyben egy bináris modell jön létre a több kimeneti osztály mindegyikéhez. A modul értékeli az egyes bináris modellek az egyes osztályok ellen kiegészíti (az összes többi osztály a modell), mintha ez egy bináris besorolási kérdés. A modul ezután végrehajtja az előrejelzést ezekkel a bináris osztályozók futtatásával, és a legmagasabb megbízhatósági pontszámmal rendelkező előrejelzés kiválasztásával.  

Lényegében a modul létrehoz egy együttest az egyes modellek, majd egyesíti az eredményeket, hogy hozzon létre egy modellt, amely előre jelzi az összes osztály. Bármely bináris osztályozó használható az egy az összes modell alapjaként.  

Tegyük fel például, hogy konfigurál egy [kétosztályos támogatási vektorgép-modellt,](two-class-support-vector-machine.md) és biztosítja azt az Egy és az Összes többosztályos modul bemeneteként. A modul kétosztályos támogatási vektoros gépmodelleket hozna létre a kimeneti osztály minden tagja számára. Ezután az egy az összes hez módszert alkalmazná az eredmények összes osztályra való kombinálására.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Az Egy vs-All többosztályos osztályozó beállítása  

Ez a modul létrehoz egy együttesbináris osztályozási modellek elemzése több osztályban. A modul használatához először konfigurálnia és bekell képeznie egy *bináris osztályozási modellt.* 

A bináris modellt az Egy és az Összes többosztályos modulhoz csatlakoztatja. Ezután betanítja a modellek együttesét a [Train Model](train-model.md) használatával egy címkézett betanítási adatkészlettel.

A modellek egyesítésekor a One-vs-All Multiclass több bináris besorolási modellt hoz létre, optimalizálja az algoritmust az egyes osztályokhoz, majd egyesíti a modelleket. A modul ezeket a feladatokat annak ellenére, hogy a betanítási adatkészlet több osztályértékkel is rendelkezhet.

1. Adja hozzá az Egy vs-All többosztályos modult a tervezőben lévő folyamathoz. Ez a modul a Machine Learning - Initialize ( **Gépi tanulás – inicializálása**) csoportban található a **Besorolás** kategóriában.

   Az Egy vs-All többosztályos osztályozó nem rendelkezik saját konfigurálható paramétereket. Minden testreszabást a bemenetként megadott bináris osztályozási modellben kell elvégezni.

2. Adjon hozzá egy bináris besorolási modellt a folyamathoz, és konfigurálja a modellt. Használhat például [kétosztályos támogatási vektoros gépet](two-class-support-vector-machine.md) vagy [kétosztályos kiemelt döntési fát.](two-class-boosted-decision-tree.md)

3. Adja hozzá a [Train Model](train-model.md) modult a folyamathoz. Csatlakoztassa a nem képzetlen osztályozó, amely a kimeneti One-vs-All Multiclass.

4. A másik bemeneti [modell,](train-model.md)csatlakoztasson egy címkézett betanítási adatkészletet, amely több osztályértékeket tartalmaz.

5. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után használhatja a modellt többosztályos előrejelzések.

Azt is megteheti, hogy a nem képzetlen osztályozó [tetszhet át keresztérvényesítési modellnek](cross-validate-model.md) egy címkézett érvényesítési adatkészlettel szembeni keresztérvényesítéshez.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
