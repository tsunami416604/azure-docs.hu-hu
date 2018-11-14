---
title: Egyéni a fordítót - modell betanítása
titleSuffix: Azure Cognitive Services
description: A modell tanítása egy fontos lépés, létrehozását, a fordítási modellben. Képzési választott ki, hogy betanítások dokumentumok alapján történik.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 9ec8cbe3d2467714a4b2586db79566aaef30d6d7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627423"
---
# <a name="train-a-model"></a>Modell betanítása

A fordítási modellben fejlesztéséhez fontos lépés a modell tanítása azért, mert a nélkül, egy tanítási modell nem hozható létre. Képzési választott ki a betanítások dokumentumok alapján történik.

A modell betanításához:

1.  Válassza ki a projektet, ahová a modell létrehozása.

2.  A projekt az adatok lapon jelennek meg a projekt nyelvi pár vonatkozó összes dokumentumot. Manuálisan válassza ki a dokumentumok, a modell betanításához használandó. Kiválaszthatja a képzés, a beállítás, és a tesztelés dokumentumok ezen a képernyőn. Is, csak a betanítási készletének kiválasztása, és egyéni Translator létrehozása, a hangolási és tesztelése a csoportok az Ön számára.

    -  Dokumentum neve: a dokumentum nevét.

    -  Párosítás: Ha ez a dokumentum egy párhuzamos vagy monolingual dokumentum.

    - Monolingual dokumentumok jelenleg nem támogatottak a betanításhoz.

    -  A dokumentum típusa: képzési, hangolása, tesztelési vagy szótár is lehet.

    -  Nyelvi pár: Ez a forrás- és a projekt Célnyelv mutatja.

    -  Mondatok forrás: kinyert mondatokat számát jeleníti meg a
    - forrásfájl.

    -  Mondatok cél: kinyert mondatokat számát jeleníti meg a
    - célfájl.

    ![Tanítási modell](media/how-to/how-to-train-model.png)

3.  Train gombra.

4.  A párbeszédpanelen adja meg a modell nevét.

5.  Kattintson a tanítási modell.

    ![Train model párbeszédpanel](media/how-to/how-to-train-model-2.png)

6.  Egyéni a fordítót a képzést nyújt, és állapotát jelenítik meg a képzés a modellek lapon.

    ![Train model lap](media/how-to/how-to-train-model-3.png)


## <a name="edit-a-model"></a>Modell szerkesztése

A Szerkesztés hivatkozásra használó modell részleteit megjelenítő oldalon modell szerkesztheti.

1.  Kattintson a ceruza ikonra.

    ![Modell szerkesztése](media/how-to/how-to-edit-model.png)

2.  A párbeszédpanelen módosítsa a

    1.  Minta (kötelező): a modell egy kifejező nevet adni.

        ![További párbeszédpanel szerkesztése](media/how-to/how-to-edit-model-dialog.png)

3.  Kattintson a Mentés gombra.


## <a name="next-steps"></a>További lépések

- Ismerje meg, [modell részleteinek megtekintése](how-to-view-model-details.md).
