---
title: Modell betanítása – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modell betanítása fontos lépés a fordítási modellek létrehozásakor. Betanítás az adott képzésekhez kiválasztott dokumentumok alapján történik.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: c29a0b8b429705bb0315c37fc6fe63eb8d77511f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996703"
---
# <a name="train-a-model"></a>Modell betanítása

A modell betanítása fontos lépés a fordítási modellek létrehozásához, mert képzés nélkül nem lehet létrehozni a modellt. Betanítás a képzésekhez kiválasztott dokumentumok alapján történik.

Modell betanítása:

1.  Válassza ki azt a projektet, amelyben modellt kíván létrehozni.

2.  A projekthez tartozó adatlapon megjelenik a Project Language pár releváns dokumentuma. Manuálisan válassza ki a modell betanításához használni kívánt dokumentumokat. Kiválaszthatja a képernyőn megjelenő dokumentumok tanítását, finomhangolását és tesztelését. Azt is megteheti, hogy kiválasztja a betanítási készletet, és egyéni fordítóval létrehozta a hangolási és tesztelési készleteket.

    -  Dokumentum neve: a dokumentum neve.

    -  Párosítás: Ha ez a dokumentum egy párhuzamos vagy egynyelvű dokumentum. Az egynyelvű dokumentumok jelenleg nem támogatottak a betanításhoz.

    -  Dokumentum típusa: képzés, hangolás, tesztelés vagy szótár lehet.

    -  Nyelvi pár: a projekt forrás-és célként megadott nyelvét jeleníti meg.

    -  Forrás mondatok: a forrásfájlban kinyert mondatok számát jeleníti meg.

    -  Cél mondatok: a célfájlből kinyert mondatok számát jeleníti meg.

    ![Modell betanítása](media/how-to/how-to-train-model.png)

3.  Kattintson a betanítás gombra.

4.  A párbeszédpanelen adja meg a modell nevét.

5.  Kattintson a tanítási modell elemre.

    ![A tanítási modell párbeszédablaka](media/how-to/how-to-train-model-2.png)

6.  Az egyéni fordító beküldi a képzést, és megjeleníti a képzés állapotát a modellek lapon.

    ![Modell betanítása lap](media/how-to/how-to-train-model-3.png)

>[!Note]
>Az egyéni fordító egyszerre 10 egyidejű betanítást támogat egy adott munkaterületen belül.


## <a name="edit-a-model"></a>Modell szerkesztése

A modelleket a modell részletei oldalon található szerkesztés hivatkozás használatával szerkesztheti.

1.  Kattintson a ceruza ikonra.

    ![Modell szerkesztése](media/how-to/how-to-edit-model.png)

2.  A párbeszédpanelen módosítsa a következőt:

    1.  Modell neve (kötelező): adjon egy értelmes nevet a modellnek.

        ![További párbeszédpanel szerkesztése](media/how-to/how-to-edit-model-dialog.png)

3.  Kattintson a Mentés gombra.


## <a name="next-steps"></a>További lépések

- Megtudhatja [, hogyan tekintheti meg a modell részleteit](how-to-view-model-details.md).
