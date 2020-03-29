---
title: Modell betanítása - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modell betanítása fontos lépés a fordítási modell létrehozásakor. A képzés az adott képzéshez kiválasztott dokumentumok alapján történik.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595680"
---
# <a name="train-a-model"></a>Modell betanítása

A modell betanítása fontos lépés a fordítási modell létrehozásához, mert betanítás nélkül a modell nem építhető. A képzés a képzéshez kiválasztott dokumentumok alapján történik.

Modell betanítása:

1.  Válassza ki azt a projektet, amelyben modellt szeretne építeni.

2.  A projekt Adatok lapja a projekt nyelvpárjának összes vonatkozó dokumentumát jeleníti meg. Manuálisan jelölje ki a modell betanításához használni kívánt dokumentumokat. Ezen a képernyőn választhatja ki a betanítási, hangolási és tesztelési dokumentumokat. Is csak válassza ki a képzési készlet, és egyéni fordító létre a tuning és a teszt készletek az Ön számára.

    -  Dokumentum neve: A dokumentum neve.

    -  Párosítás: Ha ez a dokumentum párhuzamos vagy egynyelvű dokumentum. Az egynyelvű dokumentumok jelenleg nem támogatottak a képzéshez.

    -  Dokumentum típusa: Lehet betanítás, hangolás, tesztelés vagy szótár.

    -  Nyelvi pár: Ez a projekt forrás- és célnyelvét jeleníti meg.

    -  Forrásmondatok: A forrásfájlból kinyert mondatok számát jeleníti meg.

    -  Célmondatok: A célfájlból kinyert mondatok számát jeleníti meg.

    ![Modell betanítása](media/how-to/how-to-train-model.png)

3.  Kattintson a Vonat gombra.

4.  A párbeszédpanelen adja meg a modell nevét.

5.  Kattintson a Vonat modell elemre.

    ![Betanítási modell párbeszédpanel](media/how-to/how-to-train-model-2.png)

6.  Egyéni fordító benyújtja a betanítást, és megmutatja a képzés állapotát a modellek lapon.

    ![Betanítási modell lap](media/how-to/how-to-train-model-3.png)

>[!Note]
>Az egyéni fordító 10 egyidejű képzést támogat egy munkaterületen belül bármikor.


## <a name="edit-a-model"></a>Modell szerkesztése

A modellt a Modell részletei lapon található Szerkesztés hivatkozással szerkesztheti.

1.  Kattintson a Ceruza ikonra.

    ![Modell szerkesztése](media/how-to/how-to-edit-model.png)

2.  A párbeszédpanel-módosításban

    1.  Modellneve (kötelező): Adjon a modellnek értelmes nevet.

        ![További párbeszédpanelek szerkesztése](media/how-to/how-to-edit-model-dialog.png)

3.  Kattintson a Mentés gombra.


## <a name="next-steps"></a>További lépések

- További információ [a modell részleteinek megtekintéséről.](how-to-view-model-details.md)
