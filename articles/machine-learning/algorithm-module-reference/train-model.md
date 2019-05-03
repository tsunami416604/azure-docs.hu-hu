---
title: 'Tanítási modell: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a **Train Model** modul az Azure Machine Learning szolgáltatás egy osztályozási vagy regressziós modell betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028110"
---
# <a name="train-model-module"></a>Tanítási modell modulhoz

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul egy osztályozási vagy regressziós modellt használja. Képzési definiált egy modellt és a paraméterek beállítása után történik, és a címkézett adatokra van szüksége. Is **tanítási modell** való szoftveres átképezése egy meglévő modell új adatokkal. 

## <a name="how-the-training-process-works"></a>A betanítási folyamat működése

Az Azure Machine Learning létrehozása és a egy machine learning-modell használatával általában egy három lépéses folyamat. 

1. A modell egy adott típusú algoritmus kiválasztása és a paraméterek vagy a hiperparaméterek definiálása konfigurálásához. Válasszon a következő modell közül: 

    + **Besorolási** modelleket, a Neurális hálózatokat, döntési fák és döntési erdő és egyéb algoritmusokkal alapján.
    + **Regresszió** , ilyenek például a standard szintű lineáris regresszió, vagy amely használja, más algoritmusok, beleértve a Neurális hálózatokat és Bayes regressziós modelleket.  

2. Adjon meg egy adatkészletet, amely a címkéje, és kompatibilis az algoritmus az adatokat. Az adatok és a modell **tanítási modell**.

    Milyen képzési hoz létre egy olyan adott bináris formátum, a iLearner, a statisztikai minták származó adatokat beágyazó. Közvetlenül nem lehet módosítani, vagy olvassa el ezt a formátumot; azonban más modulok tesznek a betanított modell is használja. 
    
    A modell tulajdonságait is megtekintheti. További információkért tekintse meg az eredmények szakaszt.

3. Képzési befejezése után a betanított modell használja az egyik a [pontozási modulok](./score-model.md), hogy az új adatok előrejelzéseket.

## <a name="how-to-use-train-model"></a>Hogyan használható **tanítási modell**  
  
1.  Az Azure Machine Learning konfigurálja a besorolási modell vagy regressziós modell.
    
2. Adja hozzá a **tanítási modell** modult a kísérletvászonra.  Ez a modul alatt található a **Machine Learning** kategória. Bontsa ki a **Train**, majd húzza a **tanítási modell** modult is futtathatja a kísérletet.
  
3.  A bal oldali bemeneti csatolása a kellő módot. A betanítási adatkészletet csatolása a jobb oldali bemeneti portjával **tanítási modell**.

    A betanítási adatkészletet egy címke oszlopot kell tartalmaznia. Címkék nélkül a sorokat a rendszer figyelmen kívül hagyja.
  
4.  A **címke oszlop**, kattintson a **Oszlopválasztás indítása**, csak egy oszlop, amely tartalmazza a modell betanítási használható eredményeket, majd.
  
    - Besorolási kapcsolatos problémák esetén a címke oszlop musí obsahovat Element **kategorikus** értékek vagy **diszkrét** értékeket. Néhány példa lehet egy igen/nem minősítés, a betegségek besorolási kódot vagy a neve, vagy egy jövedelem csoporthoz.  Ha noncategorical oszlop kiválasztása, a modul hibát adnak vissza betanítás során.
  
    -   Regressziós kapcsolatos problémák esetén a címke oszlopának tartalmaznia kell **numerikus** adatokat, amely a válasz változót jelöli. A numerikus adatokat ideális esetben a folyamatos méretezési csoport jelöli. 
    
    Példák a kredit kockázati pontszám, a tervezett eltelt idő a merevlemezt, vagy a telefonos előre jelzett száma egy adott nap vagy idő lehet.  Ha nem választ egy numerikus oszlopot, előfordulhat, hogy hibaüzenetet kap.
  
    -   Ha nem adja meg a használandó oszlop címkét, az Azure Machine Learning megpróbálja célszámítógéppel a megfelelő címkét oszlopban, azaz a metaadatok az adatkészlet alapján. Ha azt választja ki a megfelelő oszlopban, javítsa ki az Oszlopválasztó használatával.
  
    > [!TIP] 
    > Ha problémája van az Oszlopválasztó használatával, tekintse meg a cikket [Select Columns in Dataset](./select-columns-in-dataset.md) tippeket. Néhány gyakori forgatókönyvek és tippek a ismerteti a **a szabályok** és **név szerint** beállítások.
  
5.  Futtassa a kísérletet. Ha sok adatot, ez eltarthat egy ideig.

## <a name="bkmk_results"></a> Eredmények

Miután a modell tanítása:

+ A modell paramétereket és a szolgáltatás súlyok megtekintéséhez kattintson a jobb gombbal a kimenetet, és válasszon **Visualize**.
+ A modell használatának egyéb kísérletekben, kattintson a jobb gombbal a modell, és válassza ki **modell mentése**. Adjon meg egy nevet a modellhez. 

    Ez menti a modell pillanatképet készít, amely a kísérlet ismételt futtatása nem frissül.
+ A modell előrejelzésére, új értékeket használatához kösse össze a [Score Model](./score-model.md) modul új bemeneti adatokkal együtt.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 