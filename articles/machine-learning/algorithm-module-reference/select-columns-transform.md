---
title: 'Oszlopok átalakítása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre olyan átalakítást, amely az oszlopok kijelölése átalakító modullal Azure Machine Learning egy olyan transzformációt, amely a megadott adatkészletben lévő oszlopok azonos részhalmazát választja.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79455910"
---
# <a name="select-columns-transform"></a>Oszlopok kiválasztása átalakításhoz

Ez a cikk azt ismerteti, hogyan használható az Oszlopok kiválasztása átalakító modul a Azure Machine Learning Designerben (előzetes verzió). Az Oszlopok kiválasztása átalakító modul célja annak biztosítása, hogy kiszámítható, konzisztens oszlopok legyenek használatban az alárendelt gépi tanulási műveletekben.

Ez a modul hasznos lehet olyan feladatokhoz, mint a pontozás, amelyeknek konkrét oszlopokra van szükségük. A rendelkezésre álló oszlopok változásai megváltoztathatják a folyamatot, vagy módosíthatják az eredményeket.

Az oszlopok kiválasztásával létrehozhatja és mentheti az oszlopok készletét. Ezután használja az [átalakítási modul alkalmazása](apply-transformation.md) lehetőséget, hogy ezeket a beállításokat az új adatértékekre alkalmazza.

## <a name="how-to-use-select-columns-transform"></a>Az oszlopok kiválasztásának átalakítása

Ez a forgatókönyv feltételezi, hogy a szolgáltatás kiválasztásával kívánja létrehozni a modell betanításához használni kívánt oszlopok dinamikus készletét. Annak biztosítása érdekében, hogy az Oszlopok kiválasztása azonos legyen a pontozási folyamat esetében, az Oszlopok kiválasztása átalakítási modullal rögzítheti az oszlopok kiválasztását, és alkalmazhatja őket a folyamatban.

1. Adjon hozzá egy bemeneti adatkészletet a folyamathoz a tervezőben.

2. Adja hozzá a [Filter-alapú szolgáltatás kijelölésének](filter-based-feature-selection.md)egy példányát.

3. A modulok összekapcsolásával és a szolgáltatás kiválasztási moduljának konfigurálásával automatikusan megtalálhatja a bemeneti adatkészletben található legjobb funkciókat.

4. Adja hozzá a [betanítási modell](train-model.md) egy példányát, és használja a betanítás bemenetként a [szűrésen alapuló funkció kiválasztását](filter-based-feature-selection.md) .

    > [!IMPORTANT]
    > Mivel a funkció fontossága az oszlopban szereplő értékek alapján történik, nem tudja előre, hogy mely oszlopok lehetnek elérhetők a [modell betanításához](train-model.md).  
5. Csatolja az Oszlopok kiválasztása átalakító modul egy példányát. 

    Ez a lépés egy oszlop kijelölését állítja elő átalakításként, amelyet más adatkészletekre is menthet vagy alkalmazhat. Ez a lépés biztosítja, hogy a szolgáltatás kijelölésében azonosított oszlopokat más modulok újbóli felhasználása céljából menti a rendszer.

6. Adja hozzá a [pontszám modell](score-model.md) modult. 

   *Ne kapcsolja össze a bemeneti adatkészletet.* Ehelyett adja hozzá a [transzformáció alkalmazása](apply-transformation.md) modult, és kapcsolja össze a funkció-kiválasztási átalakítás kimenetét.

   > [!IMPORTANT]
   > Nem számíthat arra, hogy a kiértékelési adatkészletre alkalmazza a [szűrő alapú funkciót](filter-based-feature-selection.md) , és ugyanazokat az eredményeket kapja. Mivel a funkció kiválasztása az értékek alapján történik, más oszlopokat is választhat, ami miatt a pontozási művelet sikertelen lesz.
7. A folyamat elküldése.

A Mentés és az oszlopok kijelölésének folyamata biztosítja, hogy ugyanaz az Adatséma elérhető legyen a képzéshez és a pontozáshoz.


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
