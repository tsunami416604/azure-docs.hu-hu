---
title: Egyéni a fordítót - modell betanítása
titleSuffix: Azure Cognitive Services
description: A modell tanítása egy fontos lépés, létrehozását, a fordítási modellben. Képzési választott ki, hogy betanítások dokumentumok alapján történik.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 2045041a9ee4872e4510e5ee0deba258b0342557
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437288"
---
# <a name="train-a-model"></a>Modell betanítása

A fordítási modellben fejlesztéséhez fontos lépés a modell tanítása azért, mert a nélkül, egy tanítási modell nem hozható létre. Képzési választott ki a betanítások dokumentumok alapján történik.

A modell betanításához:

1.  Válassza ki a projektet, ahová a modell létrehozása.

2.  A projekt az adatok lapon jelennek meg a projekt nyelvi pár vonatkozó összes dokumentumot. Manuálisan válassza ki a dokumentumok, a modell betanításához használandó. Kiválaszthatja a képzés, a beállítás, és a tesztelés dokumentumok ezen a képernyőn. Is, csak a betanítási készletének kiválasztása, és egyéni Translator létrehozása, a hangolási és tesztelése a csoportok az Ön számára.

    -  Dokumentum neve: A dokumentum nevét.

    -  Párosítás: Ha ez a dokumentum egy párhuzamos vagy monolingual dokumentumot. Monolingual dokumentumok jelenleg nem támogatottak a betanításhoz.

    -  A dokumentum típusa: Képzés, hangolása, tesztelési vagy szótár lehet.

    -  Nyelvi párt: Ez mutatja a forrás- és a projekt Célnyelv.

    -  Forrás mondatokat: A forrásfájl kinyert mondatokat számát jeleníti meg.

    -  Cél mondatokat: A célfájl kinyert mondatokat számát jeleníti meg.

    ![Tanítási modell](media/how-to/how-to-train-model.png)

3.  Train gombra.

4.  A párbeszédpanelen adja meg a modell nevét.

5.  Kattintson a tanítási modell.

    ![Train model párbeszédpanel](media/how-to/how-to-train-model-2.png)

6.  Egyéni a fordítót a képzést nyújt, és állapotát jelenítik meg a képzés a modellek lapon.

    ![Train model lap](media/how-to/how-to-train-model-3.png)

>[!Note]
>Egyéni a fordítót belül bármikor munkaterület 10 egyidejű betanítások időben támogatja.


## <a name="edit-a-model"></a>Modell szerkesztése

A Szerkesztés hivatkozásra használó modell részleteit megjelenítő oldalon modell szerkesztheti.

1.  Kattintson a ceruza ikonra.

    ![Modell szerkesztése](media/how-to/how-to-edit-model.png)

2.  A párbeszédpanelen módosítsa a

    1.  Modell neve (kötelező): Adjon meg egy kifejező nevet a modellt.

        ![További párbeszédpanel szerkesztése](media/how-to/how-to-edit-model-dialog.png)

3.  Kattintson a Mentés gombra.


## <a name="next-steps"></a>További lépések

- Ismerje meg, [modell részleteinek megtekintése](how-to-view-model-details.md).
