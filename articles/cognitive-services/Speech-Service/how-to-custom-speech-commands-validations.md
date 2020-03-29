---
title: 'Útmutató: Érvényesítés hozzáadása az egyéni parancsparaméterekhez (előzetes verzió)'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben bemutatjuk, hogyan adhat érvényesítéseket egy paraméterhez az Egyéni parancsokban.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156454"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Útmutató: Érvényesítés hozzáadása az egyéni parancsparaméterekhez (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá érvényesítéseket a paraméterekhez, és hogyan kérheti a javítást.

## <a name="prerequisites"></a>Előfeltételek

El kell végeznie az alábbi cikkekben leírt lépéseket:

- [Rövid útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature parancs létrehozása

Az érvényesítések bemutatásához hozzunk létre egy új parancsot, amely lehetővé teszi a felhasználó számára a hőmérséklet beállítását.

1. A korábban létrehozott Egyéni parancsok alkalmazás megnyitása a [Speech Studióban](https://speech.microsoft.com/)
1. Új **SetTemperature** parancs létrehozása
1. Paraméter hozzáadása a célhőmérséklethez
1. Érvényesítés hozzáadása a hőmérsékleti paraméterhez
   > [!div class="mx-imgBorder"]
   > ![Tartomány-ellenőrzés hozzáadása](media/custom-speech-commands/validations-add-temperature.png)

   | Beállítás           | Ajánlott érték                                          | Leírás                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Név              | Hőmérséklet                                              | A Parancs paraméter leíró neve                                                    |
   | Kötelező          | igaz                                                     | Jelölőnégyzet, amely jelzi, hogy szükség van-e erre a paraméterre a parancs végrehajtása előtt |
   | Válaszsablon | "- Milyen hőmérsékletet szeretne?"                     | A kérdés, hogy kérje az értékét ezt a paramétert, ha nem ismert                              |
   | Típus              | Szám                                                   | A paraméter típusa, például Szám, Karakterlánc vagy Dátumidő                                      |
   | Ellenőrzés        | Min Érték: 60, Maximális érték: 80                             | A Szám paraméterek esetében a paraméter megengedett értéktartománya                             |
   | Válaszsablon | "- Sajnálom, én csak meg között 60 és 80 fok"      | A rendszer kéri a frissített értéket, ha az ellenőrzés sikertelen                                       |

1. Néhány mintamondat hozzáadása

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
   | Feltételek | Kötelező paraméter - Hőmérséklet                          | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat    |
   | Műveletek    | SpeechResponse - "- Ok, beállítás {Hőmérséklet} fokra" | A szabályfeltétel igaz állapotában végrehajtandó művelet |

> [!TIP]
> Ez a példa egy beszédválaszt használ az eredmény megerősítéséhez. A parancs ügyfélművelettel való kitöltésére vonatkozó példákat a következő témakörben talál: [Parancsok végrehajtása az ügyfélen a beszédfelismerési SDK-val (előzetes verzió)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Próba

Válassza ki a Teszt panelt, és próbáljon ki néhány interakciót.

- Bevitel: Állítsa be a hőmérsékletet 72 fokra
- Kimenet: "Ok, 72 fokos beállítás"

- Bevitel: Állítsa be a hőmérsékletet 45 fokra
- Kimenet: "Sajnáljuk, csak 60 és 80 fok között tudok beállítani"
- Bemenet: legyen 72 fok helyett
- Kimenet: "Ok, 72 fokos beállítás"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: Megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md)
