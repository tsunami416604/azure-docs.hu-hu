---
title: 'Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan valósítja meg az egyéni parancsok parancsainak megerősítését.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456497"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy megerősítést a parancshoz.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:

- [Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>SetAlarm-parancs létrehozása

Az érvényesítések megjelenítéséhez hozzon létre egy új parancsot, amely lehetővé teszi, hogy a felhasználó riasztást állítson be.

1. A korábban létrehozott egyéni parancsok alkalmazás megnyitása a [Speech Studióban](https://speech.microsoft.com/)
1. Új **SetAlarm** -parancs létrehozása
1. Adjon hozzá egy DateTime nevű paramétert

   | Beállítás           | Ajánlott érték                                          | Leírás                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Name (Név)              | DateTime                                                 | A parancs paraméterének leíró neve                                                    |
   | Kötelező          | igaz                                                     | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
   | Válasz sablonja | "– Mi idő?"                                           | A paraméter értékének megadására vonatkozó kérés, ha nem ismert                              |
   | Típus              | DateTime                                                 | A paraméter típusa, például szám, karakterlánc vagy dátum/idő                                      |
   | Dátum alapértelmezett értékei     | Ha a Date hiányzik a mai nap használata                             |                                                                                                  |
   | Idő alapértelmezett értékei     | Ha hiányzik az idő, használja a nap kezdetét                      |                                                                                                  | 

1. Néhány példa mondatok hozzáadása
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Befejezési szabály hozzáadása az eredmény megerősítéséhez

   | Beállítás    | Ajánlott érték                                         | Leírás                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve  | Riasztás beállítása                                               | A szabály célját leíró név          |
   | Műveletek    | SpeechResponse-"-ok, riasztás beállítva a következőhöz: {DateTime}"       | A szabály feltételének teljesülésekor végrehajtandó művelet |

## <a name="try-it-out"></a>Próba

Válassza ki a teszt panelt, és próbálkozzon néhány interakcióval.

- Bemenet: riasztás beállítása holnap délben
- Output: "ok, riasztás beállítása 12/06/2019 12:00:00"

- Bemenet: riasztás beállítása
- Kimenet: "mi idő?"
- Bemenet: 5
- Output: "ok, riasztás beállítása 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>A jóváhagyáshoz szükséges speciális szabályok hozzáadása

1. Adjon hozzá egy speciális szabályt a megerősítéshez. 

    Ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és egy megerősítést (igen/nem) vár a következő bekapcsoláshoz.

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Dátum és idő megerősítése                                                                | A szabály célját leíró név          |
   | Feltételek            | Kötelező paraméter – DateTime                                                    | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek               | SpeechResponse-"-biztos, hogy a (z) {DateTime} esetében riasztást szeretne beállítani?       | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | Végrehajtás utáni állapot | Várakozás a bevitelre                                                                   | A felhasználó állapota a turn után                  |
   | Elvárások          | Megerősítés                                                                     | Várakozás a következő bekapcsolásra                      |

1. Egy speciális szabály hozzáadása a sikeres megerősítés kezeléséhez (a felhasználó igent mondott)

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Jóváhagyás elfogadva                                                            | A szabály célját leíró név          |
   | Feltételek            | SuccessfulConfirmation & kötelező paraméter-DateTime                           | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Végrehajtás utáni állapot | Befejezésre kész                                                             | A felhasználó állapota a turn után                   |

1. Speciális szabály hozzáadása a megerősítő megtagadásának kezeléséhez (felhasználó által nem említett)

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Jóváhagyás megtagadva                                                                   | A szabály célját leíró név          |
   | Feltételek            | DeniedConfirmation & kötelező paraméter-DateTime                               | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek               | ClearParameter-DateTime & SpeechResponse-"-nincs probléma, mi az idő?"     | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | Végrehajtás utáni állapot | Várakozás a bevitelre                                                                   | A felhasználó állapota a turn után                   |
   | Elvárások          | ElicitParameters – DateTime                                                      | Várakozás a következő bekapcsolásra                      |

## <a name="try-it-out"></a>Próba

Válassza ki a teszt panelt, és próbálkozzon néhány interakcióval.

- Bemenet: riasztás beállítása holnap délben
- Kimenet: "biztos, hogy 12/07/2019 12:00:00-as riasztást szeretne beállítani?"
- Bemenet: nem
- Kimenet: "nincs probléma, mi az idő?"
- Bemenet: 5
- Kimenet: "biztos, hogy 12/06/2019 17:00:00-as riasztást szeretne beállítani?"
- Bemenet: igen
- Output: "ok, riasztás beállítása 12/06/2019 17:00:00"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: egy lépésből álló javítás hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-one-step-correction.md)