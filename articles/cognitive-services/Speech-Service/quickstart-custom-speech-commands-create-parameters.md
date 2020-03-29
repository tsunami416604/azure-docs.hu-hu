---
title: 'Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben paramétereket adhat hozzá egy egyéni parancsok alkalmazáshoz.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348534"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Gyorsútmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)

Az [előző cikkben](./quickstart-custom-speech-commands-create-new.md)létrehoztunk egy új egyéni parancsok projektet, hogy paraméterek nélkül válaszoljon a parancsokra.

Ebben a cikkben kiterjesztjük ezt az alkalmazást paraméterekkel, hogy képes legyen kezelni több eszköz be- és kikapcsolását.

## <a name="create-parameters"></a>Paraméterek létrehozása

1. A [korábban létrehozott](./quickstart-custom-speech-commands-create-new.md) projekt megnyitása
1. Mivel a parancs most antól be- és kikapcsol, nevezze át a parancsot "TurnOnOff" névre.
   - Mutasson a Parancs nevére, és válassza a szerkesztés ikont a név módosításához
1. Új paraméter létrehozása annak ábrázolására, hogy a felhasználó be- vagy ki kívánja-e kapcsolni az eszközt
   - A `+` Paraméterek szakasz melletti ikon kijelölése

   > [!div class="mx-imgBorder"]
   > ![Paraméter létrehozása](media/custom-speech-commands/create-on-off-parameter.png)

   | Beállítás            | Ajánlott érték     | Leírás                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Név               | Be-ki               | A paraméter leíró neve                                                                     |
   | Globális          | Ellenőrizetlen           | Jelölőnégyzet, amely jelzi, hogy a paraméter értéke globálisan alkalmazva van-e a projekt összes parancsára |
   | Kötelező           | Ellenőrizni             | Jelölőnégyzet, amely jelzi, hogy szükség van-e erre a paraméterre a parancs végrehajtása előtt          |
   | Válaszsablon  | "- Be vagy ki?"      | A kérdés, hogy kérje az értékét ezt a paramétert, ha nem ismert                                       |
   | Típus               | Sztring              | A paraméter típusa, például Szám, Karakterlánc vagy Dátumidő                                               |
   | Konfiguráció      | Karakterláncok listája         | Karakterláncok esetén a karakterlánclista a bemeneteket a lehetséges értékek egy készletére korlátozza                                      |
   | Karakterlánclista-értékek | be, ki             | Karakterlánclista paraméter esetén a lehetséges értékek és szinonimáik halmaza                                |

   - Ezután ismét `+` válassza az ikont egy második paraméter hozzáadásához, amely az eszközök nevét jelöli. Ebben a példában egy tv és egy

   | Beállítás            | Ajánlott érték       | Leírás                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Név               | SubjectDevice (Tárgyeszköz)         | A paraméter leíró neve                                                                     |
   | Globális          | Ellenőrizetlen             | Jelölőnégyzet, amely jelzi, hogy a paraméter értéke globálisan alkalmazva van-e a projekt összes parancsára |
   | Kötelező           | Ellenőrizni               | Jelölőnégyzet, amely jelzi, hogy szükség van-e erre a paraméterre a parancs végrehajtása előtt          |
   | Válaszsablon  | "- Melyik eszköz?"     | A kérdés, hogy kérje az értékét ezt a paramétert, ha nem ismert                                       |
   | Típus               | Sztring                | A paraméter típusa, például Szám, Karakterlánc vagy Dátumidő                                               |
   | Konfiguráció      | Karakterláncok listája           | Karakterláncok esetén a karakterlánclista a bemeneteket a lehetséges értékek egy készletére korlátozza                                      |
   | Karakterlánclista-értékek | tv, ventilátor               | Karakterlánclista paraméter esetén a lehetséges értékek és szinonimáik halmaza                                |
   | Szinonimák (tv)      | televízió, tv     | Választható szinonimák a karakterlánclista-paraméter minden lehetséges értékéhez                                      |

## <a name="add-sample-sentences"></a>Mintamondatok hozzáadása

A paraméterekkel hasznos az összes lehetséges kombinációt lefedő mintamondatok hozzáadása. Példa:

1. Teljes paraméterinformáció -`"turn {OnOff} the {SubjectDevice}"`
1. Részleges paraméterinformáció -`"turn it {OnOff}"`
1. Nincs paraméterinformáció -`"turn something"`

A különböző mennyiségű információt tartalmazó mintamondatok lehetővé teszik, hogy az Egyéni parancsok alkalmazás egylépéses felbontásokat és részleges információkat tartalmazó többfordulatos felbontásokat is feloldjon.

Ezt szem előtt tartva, szerkesztsd a mintamondatokat, hogy a paramétereket az alábbiak szerint használd.

> [!TIP]
> A Mintamondatok szerkesztőben a kapcsos zárójelek segítségével hivatkozhat a paraméterekre. - `turn {OnOff} the {SubjectDevice}`A tabulátor kiegészítésével hivatkozhat a korábban létrehozott paraméterekre.

> [!div class="mx-imgBorder"]
> ![Mintamondatok paraméterekkel](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Paraméterek hozzáadása a Befejezés szabályhoz

Az [előző rövid útmutatóban](./quickstart-custom-speech-commands-create-new.md)létrehozott Befejezés szabály módosítása:

1. Adjon hozzá egy új feltételt, és válassza a Kötelező paraméter lehetőséget. Válassza `OnOff` ki a két és`SubjectDevice`
1. A beszédfelismerési válasz `OnOff` művelet `SubjectDevice`szerkesztése a következőként:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Próba

Nyissa meg a Csevegés teszt panelt, és próbáljon ki néhány interakciót.

- Bevitel: kapcsolja ki a tv
- Kimenet: Ok, a tv kikapcsolása

- Bemenet: kapcsolja ki a televíziót
- Kimenet: Ok, a tv kikapcsolása

- Bemenet: kapcsolja ki
- Kimenet: Melyik eszköz?
- Bemenet: a tv
- Kimenet: Ok, a tv kikapcsolása

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Rövid útmutató: Egyéni parancsok használata egyéni hanggal (előzetes verzió)](./quickstart-custom-speech-commands-select-custom-voice.md)
