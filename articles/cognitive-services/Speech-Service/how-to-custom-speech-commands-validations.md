---
title: 'Útmutató: az egyéni parancsok paramétereinek megadása (előzetes verzió)'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá érvényesítéseket egy paraméterhez egyéni parancsokban.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156454"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Útmutató: az egyéni parancsok paramétereinek megadása (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá érvényességi paramétereket a paraméterekhez, és megkérheti a javítást.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:

- [Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature-parancs létrehozása

Az érvényesítések megjelenítéséhez hozzon létre egy új parancsot, amely lehetővé teszi a felhasználó számára a hőmérséklet beállítását.

1. A korábban létrehozott egyéni parancsok alkalmazás megnyitása a [Speech Studióban](https://speech.microsoft.com/)
1. Új **SetTemperature** -parancs létrehozása
1. Paraméter hozzáadása a célként megadott hőmérséklethez
1. Adja meg a hőmérséklet paraméter érvényesítését
   > [!div class="mx-imgBorder"]
   > ![tartomány-ellenőrzés hozzáadása](media/custom-speech-commands/validations-add-temperature.png)

   | Beállítás           | Ajánlott érték                                          | Leírás                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name (Név)              | Hőmérséklet                                              | A parancs paraméterének leíró neve                                                    |
   | Szükséges          | igaz                                                     | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
   | Válasz sablonja | "– Milyen hőmérsékletet szeretne?                     | A paraméter értékének megadására vonatkozó kérés, ha nem ismert                              |
   | Type (Típus)              | Szám                                                   | A paraméter típusa, például szám, karakterlánc vagy dátum/idő                                      |
   | Ellenőrzés        | Minimális érték: 60, maximális érték: 80                             | A Number paraméterek esetében a paraméter értékének megengedett tartománya                             |
   | Válasz sablonja | "– Sajnos csak 60 és 80 fok között lehet beállítani"      | Ha az ellenőrzés sikertelen, a rendszer kérni fogja a frissített érték megadását.                                       |

1. Néhány példa mondatok hozzáadása

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Befejezési szabály hozzáadása az eredmény megerősítéséhez

   | Beállítás    | Ajánlott érték                                           | Leírás                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve  | Megerősítő üzenet                                      | A szabály célját leíró név          |
   | Feltételek | Kötelező paraméter – hőmérséklet                          | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
   | Műveletek    | SpeechResponse-"-ok, beállítás: {hőmérséklet} fok" | A szabály feltételének teljesülésekor végrehajtandó művelet |

> [!TIP]
> Ez a példa egy beszédfelismerési választ használ az eredmény megerősítéséhez. Példák a parancs ügyfél-művelettel való végrehajtásához: útmutató: az [ügyfél parancsainak teljesítése a SPEECH SDK-val (előzetes verzió)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Próbálja ki!

Válassza ki a teszt panelt, és próbálkozzon néhány interakcióval.

- Bemenet: a hőmérséklet beállítása 72 fokos értékre
- Kimenet: "ok, beállítás 72 fok"

- Bemenet: a hőmérséklet beállítása 45 fokos értékre
- Kimenet: "sajnálom, csak 60 és 80 fok között lehet beállítani"
- Bemenet: tegyük 72 fok helyett
- Kimenet: "ok, beállítás 72 fok"

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md)
