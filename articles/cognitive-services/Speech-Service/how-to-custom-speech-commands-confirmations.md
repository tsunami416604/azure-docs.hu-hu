---
title: 'Útmutató: Megerősítés hozzáadása egyéni parancshoz (előzetes verzió)'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben az egyéni parancsokban egy parancs megerősítésének megvalósítása.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456497"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Útmutató: Megerősítés hozzáadása egyéni parancshoz (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá megerősítést egy parancshoz.

## <a name="prerequisites"></a>Előfeltételek

El kell végeznie az alábbi cikkekben leírt lépéseket:

- [Rövid útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>SetAlarm parancs létrehozása

Az érvényesítések bemutatásához hozzunk létre egy új parancsot, amely lehetővé teszi a felhasználó számára a riasztás beállítását.

1. A korábban létrehozott Egyéni parancsok alkalmazás megnyitása a [Speech Studióban](https://speech.microsoft.com/)
1. Új **parancskészlet-riasztás** létrehozása
1. DateTime nevű paraméter hozzáadása

   | Beállítás           | Ajánlott érték                                          | Leírás                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Név              | DateTime                                                 | A Parancs paraméter leíró neve                                                    |
   | Kötelező          | igaz                                                     | Jelölőnégyzet, amely jelzi, hogy szükség van-e erre a paraméterre a parancs végrehajtása előtt |
   | Válaszsablon | "- Mikor?"                                           | A kérdés, hogy kérje az értékét ezt a paramétert, ha nem ismert                              |
   | Típus              | DateTime                                                 | A paraméter típusa, például Szám, Karakterlánc vagy Dátumidő                                      |
   | Alapértelmezett dátum     | Ha a dátum hiányzik a mai használatból                             |                                                                                                  |
   | Alapértelmezett idő     | Ha hiányzik az idő, használja a nap kezdetét                      |                                                                                                  | 

1. Néhány mintamondat hozzáadása
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Befejezési szabály hozzáadása az eredmény megerősítéséhez

   | Beállítás    | Ajánlott érték                                         | Leírás                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve  | Riasztás beállítása                                               | A szabály célját leíró név          |
   | Műveletek    | SpeechResponse - "- Ok, riasztás beállítva {DateTime}"       | A szabályfeltétel igaz állapotában végrehajtandó művelet |

## <a name="try-it-out"></a>Próba

Válassza ki a Teszt panelt, és próbáljon ki néhány interakciót.

- Bemenet: Állítsa be az ébresztést holnap délben
- Kimenet: "Ok, riasztás beállítása 2019.06.12.06.00- ra"

- Bemenet: Riasztás beállítása
- Kimenet: "Mikor?"
- Bemenet: 17:00
- Kimenet: "Ok, riasztás beállítása 2019.05.12-re 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>A megerősítéshez speciális szabályok hozzáadása

1. Speciális szabály hozzáadása megerősítéshez. 

    Ez a szabály megkéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és megerősítést (igent/nemet) vár a következő fordulóra.

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Dátum időnek megerősítése                                                                | A szabály célját leíró név          |
   | Feltételek            | Kötelező paraméter - DateTime                                                    | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat    |   
   | Műveletek               | SpeechResponse - "- Biztosan beállít egy riasztást a(z) {DateTime}?"       | A szabályfeltétel igaz állapotában végrehajtandó művelet |
   | Állapot a végrehajtás után | Várakozás a bevitelre                                                                   | A felhasználó állapota a fordulás után                  |
   | Elvárások          | Megerősítés                                                                     | Várakozás a következő fordulóra                      |

1. Speciális szabály hozzáadása a sikeres megerősítés kezeléséhez (a felhasználó igent mondott)

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Elfogadott visszaigazolás                                                            | A szabály célját leíró név          |
   | Feltételek            | Sikeres megerősítés & szükséges paraméter - DateTime                           | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat    |   
   | Állapot a végrehajtás után | Készen áll a befejezésre                                                             | A felhasználó állapota a fordulás után                   |

1. Speciális szabály hozzáadása a megtagadott megerősítés kezeléséhez (a felhasználó nemet mondott)

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Megtagadva megerősítés                                                                   | A szabály célját leíró név          |
   | Feltételek            | DeniedConfirmation & szükséges paraméter - DateTime                               | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat    |   
   | Műveletek               | ClearParameter - DateTime & SpeechResponse - "- Nincs probléma, akkor mikor?"     | A szabályfeltétel igaz állapotában végrehajtandó művelet |
   | Állapot a végrehajtás után | Várakozás a bevitelre                                                                   | A felhasználó állapota a fordulás után                   |
   | Elvárások          | ElicitParameters - DateTime                                                      | Várakozás a következő fordulóra                      |

## <a name="try-it-out"></a>Próba

Válassza ki a Teszt panelt, és próbáljon ki néhány interakciót.

- Bemenet: Állítsa be az ébresztést holnap délben
- Kimenet: "Biztosan beállít riasztást 2019.07.12.12:00:00- ra?"
- Bemenet: Nem
- Kimenet: "Nem probléma, akkor mikor?"
- Bemenet: 17:00
- Kimenet: "Biztosan beállít riasztást 2019.06.12-re 17:00:00?"
- Bemenet: Igen
- Kimenet: "Ok, riasztás beállítása 2019.06.12.17:00:00"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: Egylépéses javítás hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-one-step-correction.md)