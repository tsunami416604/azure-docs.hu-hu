---
title: 'Oszlopok átalakítása: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan hozhat létre olyan átalakítást, amely az oszlopok kijelölése a Azure Machine Learning szolgáltatásban lehetőséggel az oszlopok azonos részhalmazát választja, mint a megadott adatkészletben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: def15b3c28b2278f884b36acc39fa75982fcf8c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516200"
---
# <a name="select-columns-transform"></a>Oszlopok átalakításának kiválasztása

Ez a cikk azt ismerteti, hogyan használható az **Oszlopok kiválasztása átalakító** modul a Azure Machine learning Designerben (előzetes verzió). Az **Oszlopok kiválasztása átalakító** modul célja annak biztosítása, hogy a rendszer mindig kiszámítható, konzisztens oszlopokat használ az alárendelt gépi tanulási műveletekben.

Ez a modul hasznos lehet olyan feladatokhoz, mint a pontozás, amelyeknek konkrét oszlopokra van szükségük. A rendelkezésre álló oszlopok változásai megváltoztathatják a folyamatot, vagy módosíthatják az eredményeket.

Az **Oszlopok kiválasztása átalakító** használatával hozhat létre és menthet oszlopokat. Ezután használja az [átalakítási modul alkalmazása](apply-transformation.md) lehetőséget, hogy ezeket a beállításokat az új adatértékekre alkalmazza.

## <a name="how-to-use-select-columns-transform"></a>Az oszlopok kiválasztásának átalakítása

Ez a forgatókönyv feltételezi, hogy a szolgáltatás kiválasztásával kívánja létrehozni a modell betanításához használni kívánt oszlopok dinamikus készletét. Annak biztosítása érdekében, hogy az Oszlopok kiválasztása azonos legyen a pontozási folyamat esetében, az **Oszlopok kiválasztása átalakítási** modullal rögzítheti az oszlopok kiválasztását, és alkalmazhatja őket a folyamatban.

1. Adjon hozzá egy bemeneti adatkészletet a folyamathoz a tervezőben.

2. Adja hozzá a [Filter-alapú szolgáltatás kijelölésének](filter-based-feature-selection.md)egy példányát.

3. Kapcsolja össze a modulokat, és konfigurálja a funkció-kiválasztási modult, hogy automatikusan megkeresse a bemeneti adatkészletben szereplő legjobb funkciókat.

4. Adja hozzá a [betanítási modell](train-model.md) egy példányát, és használja a betanítás bemenetként a [szűrésen alapuló funkció kiválasztását](filter-based-feature-selection.md) .

    > [!IMPORTANT]
    > Mivel a funkció fontossága az oszlopban szereplő értékek alapján van eldöntve, nem tudja előre, hogy mely oszlopok lehetnek elérhetők a [modell betanításához](train-model.md).  
5. Most csatolja a **Select Columns átalakító** modul egy példányát. 

    Ez egy olyan oszlop kijelölését eredményezi, amely menthető vagy alkalmazható más adatkészletekbe. Ez a lépés biztosítja, hogy a szolgáltatások kijelölésével azonosított oszlopokat a rendszer a más modulok általi újrafelhasználás céljából menti.

6. Adja hozzá a [pontszám modell](score-model.md) modult. 

    **Ne kapcsolja össze a bemeneti adatkészletet.**

    Ehelyett adja hozzá a [transzformáció alkalmazása](apply-transformation.md) modult, és kapcsolja össze a funkció-kiválasztási átalakítás kimenetét.

   > [!IMPORTANT]
   > Nem számíthat arra, hogy a kiértékelési adatkészletre alkalmazza a [szűrő alapú funkciót](filter-based-feature-selection.md) , és ugyanazokat az eredményeket kapja. Mivel a funkció kiválasztása az értékek alapján történik, más oszlopokat is választhat, ami miatt a pontozási művelet sikertelen lesz.
7. A folyamat futtatása.

A Mentés és az oszlopok kijelölésének folyamata biztosítja, hogy ugyanaz az Adatséma elérhető legyen a képzéshez és a pontozáshoz.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
