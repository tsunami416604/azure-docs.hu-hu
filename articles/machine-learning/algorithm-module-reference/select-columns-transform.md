---
title: 'Oszlopok átalakítása: Modulhivatkozás kiválasztása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Oszlopok átalakítása modult az Azure Machine Learningben egy olyan átalakítás létrehozásához, amely az oszlopok ugyanazon részhalmazát választja ki, mint az adott adatkészletben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455910"
---
# <a name="select-columns-transform"></a>Oszlopok kiválasztása átalakításhoz

Ez a cikk ismerteti, hogyan használhatja az Oszlopok átalakítása kiválasztása modul t az Azure Machine Learning designer (előzetes verzió). Az Oszlopok átalakítása modul célja annak biztosítása, hogy a későbbi gépi tanulási műveletekben kiszámítható, konzisztens oszlopok at használjanak.

Ez a modul olyan feladatokhoz hasznos, mint például a pontozás, amelyek hez speciális oszlopok igényelnek. A rendelkezésre álló oszlopok módosításai megszakíthatják a folyamatot, vagy módosíthatják az eredményeket.

Az Oszlopok átalakítása kijelölése segítségével oszlopok készletét hozhatja létre és mentheti. Ezután az [Átalakítás alkalmazása](apply-transformation.md) modullal alkalmazza ezeket a beállításokat az új adatokra.

## <a name="how-to-use-select-columns-transform"></a>Az Oszlopok kijelölése átalakítás használata

Ebben a forgatókönyvben azt feltételezi, hogy a szolgáltatás kiválasztásával dinamikus oszlopkészletet szeretne létrehozni, amelyet a modell betanításához használ. Annak érdekében, hogy az oszlopkijelölések megegyezzenek a pontozási folyamatnál, az Oszlopok átalakítása modulsegítségével rögzítse az oszlopkijelöléseket, és alkalmazza őket a folyamat más részeire.

1. Adjon hozzá egy bemeneti adatkészletet a folyamathoz a tervezőben.

2. A [szűrőalapú szolgáltatáskijelölés](filter-based-feature-selection.md)egy példányának hozzáadása .

3. Csatlakoztassa a modulokat, és konfigurálja a szolgáltatásválasztó modult, hogy automatikusan megtalálja a bemeneti adatkészlet számos legjobb szolgáltatását.

4. Adja hozzá a [tanítási modell](train-model.md) egy példányát, és használja a [szűrőalapú szolgáltatáskiválasztása](filter-based-feature-selection.md) kimenetét a betanítás bemeneteként.

    > [!IMPORTANT]
    > Mivel a szolgáltatásfontosság az oszlopban szereplő értékeken alapul, nem tudhatja előre, hogy mely oszlopok érhetők el a [betanítási modell](train-model.md)beviteléhez.  
5. Az Oszlopok átalakítása modul egy példányának csatolása. 

    Ez a lépés egy oszlopkijelölést hoz létre átalakításként, amely menthető vagy más adatkészletekre alkalmazható. Ez a lépés biztosítja, hogy a szolgáltatásválasztásban azonosított oszlopok at más modulok újrafelhasználása érdekében mentse a rendszer.

6. Adja hozzá a [Score Model](score-model.md) modult. 

   *Ne csatlakoztassa a bemeneti adatkészletet.* Ehelyett adja hozzá az [Átalakítás alkalmazása](apply-transformation.md) modult, és csatlakoztassa a szolgáltatáskijelölési átalakítás kimenetét.

   > [!IMPORTANT]
   > Nem számíthat arra, hogy [alkalmazza a szűrésalapú szolgáltatáskijelölést](filter-based-feature-selection.md) a pontozási adatkészletre, és ugyanazt az eredményt kapja. Mivel a szolgáltatás kiválasztása értékeken alapul, előfordulhat, hogy egy másik oszlopkészletet választ, ami a pontozási művelet sikertelensítését eredményezné.
7. Küldje el a folyamatot.

Ez a folyamat a mentés, majd egy oszlop kiválasztása biztosítja, hogy ugyanazokat az adatsémát áll rendelkezésre a betanítás és a pontozás.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
