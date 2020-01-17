---
title: 'Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió) – Speech Service'
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
ms.openlocfilehash: 994ac88f78dfe5a5b0ee6fef3fa97d66d53c911b
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156692"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)

Az [előző cikkben](./quickstart-custom-speech-commands-create-new.md)egy új egyéni parancs-projektet hoztunk létre, amely paraméterek nélkül válaszol a parancsokra.

Ebben a cikkben a paraméterekkel bővítjük ezt az alkalmazást, hogy kezelni tudja a több eszköz bekapcsolását és kikapcsolását.

## <a name="create-parameters"></a>Paraméterek létrehozása

1. Nyissa meg a [korábban létrehozott](./quickstart-custom-speech-commands-create-new.md) projektet
1. Mivel a parancs most már be-és kikapcsolja a-t, nevezze át a parancsot "TurnOnOff"-re.
   - Vigye a kurzort a parancs nevére, és válassza a Szerkesztés ikont a név módosításához.
1. Új paraméter létrehozása, amely azt jelzi, hogy a felhasználó be-vagy kikapcsolja az eszközt
   - Válassza a paraméterek szakasz melletti `+` ikont.

   > [!div class="mx-imgBorder"]
   > ![paraméter létrehozása](media/custom-speech-commands/create-on-off-parameter.png)

   | Beállítás            | Ajánlott érték     | Leírás                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name (Név)               | Javítás OnOff               | A paraméter leíró neve                                                                     |
   | Globális          | nincs bejelölve           | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan a projekt összes parancsára vonatkozik-e |
   | Szükséges           | ellenőrizni             | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt          |
   | Válasz sablonja  | "-Be vagy ki?"      | A paraméter értékének megadására vonatkozó kérés, ha nem ismert                                       |
   | Type (Típus)               | Sztring              | A paraméter típusa, például szám, karakterlánc vagy dátum/idő                                               |
   | Konfiguráció      | Karakterlánc-lista         | Karakterláncok esetén a sztringek listája a lehetséges értékek egy halmazára korlátozza a bemeneteket                                      |
   | Karakterlánc-lista értékei | be, ki             | Karakterlánc-lista paraméter esetén a lehetséges értékek és azok szinonimáik halmaza                                |

   - Ezután válassza a `+` ikont egy második paraméter hozzáadásához, amely az eszközök nevét jelöli. Ebben a példában egy TV és egy ventilátor

   | Beállítás            | Ajánlott érték       | Leírás                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name (Név)               | SubjectDevice         | A paraméter leíró neve                                                                     |
   | Globális          | nincs bejelölve             | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan a projekt összes parancsára vonatkozik-e |
   | Szükséges           | ellenőrizni               | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt          |
   | Válasz sablonja  | "– Melyik eszköz?"     | A paraméter értékének megadására vonatkozó kérés, ha nem ismert                                       |
   | Type (Típus)               | Sztring                | A paraméter típusa, például szám, karakterlánc vagy dátum/idő                                               |
   | Konfiguráció      | Karakterlánc-lista           | Karakterláncok esetén a sztringek listája a lehetséges értékek egy halmazára korlátozza a bemeneteket                                      |
   | Karakterlánc-lista értékei | TV, ventilátor               | Karakterlánc-lista paraméter esetén a lehetséges értékek és azok szinonimáik halmaza                                |
   | Szinonimák (TV)      | televízió, Teller     | A string List paraméter minden lehetséges értékének választható szinonimái                                      |

## <a name="add-sample-sentences"></a>Minta mondatok hozzáadása

A paraméterekkel hasznos lehet az összes lehetséges kombinációra kiterjedő minta mondatokat hozzáadni. Példa:

1. Teljes paraméter adatai – `"turn {OnOff} the {SubjectDevice}"`
1. Részleges paraméterek adatai – `"turn it {OnOff}"`
1. Nincs paraméter-információ – `"turn something"`

A különböző mennyiségű információval rendelkező mondatok többek között lehetővé teszik, hogy az egyéni parancsok alkalmazás az egylépéses felbontást és a többfordulatú feloldásokat részleges információkkal oldja fel.

Ezt szem előtt tartva szerkessze a minta mondatokat, hogy az alább javasolt paramétereket használják.

> [!TIP]
> A minta mondatok szerkesztő kapcsos zárójeleket használ a paraméterekre való hivatkozáshoz. - `turn {OnOff} the {SubjectDevice}` a TAB befejezését használja a korábban létrehozott paraméterekre való hivatkozáshoz.

> [!div class="mx-imgBorder"]
> ![minta mondatok paraméterekkel](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Paraméterek hozzáadása a befejezési szabályhoz

Módosítsa az [előző](./quickstart-custom-speech-commands-create-new.md)rövid útmutatóban létrehozott befejezési szabályt:

1. Adjon hozzá egy új feltételt, és válassza a szükséges paramétert. `OnOff` és `SubjectDevice` kijelölése
1. A beszédfelismerési válasz művelet szerkesztése `OnOff` és `SubjectDevice`:

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Próbálja ki!

Nyissa meg a teszt csevegés panelt, és próbálkozzon néhány interakcióval.

- Bemenet: a TV kikapcsolása
- Kimenet: ok, a TV kikapcsolása

- Bemenet: a televízió kikapcsolása
- Kimenet: ok, a TV kikapcsolása

- Bemenet: kikapcsolás
- Kimenet: melyik eszköz?
- Bemenet: a TV
- Kimenet: ok, a TV kikapcsolása

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Rövid útmutató: Kapcsolódás egyéni parancssori alkalmazáshoz a Speech SDK-val (előzetes verzió)](./quickstart-custom-speech-commands-speech-sdk.md)
