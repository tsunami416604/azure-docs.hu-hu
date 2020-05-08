---
title: 'Útmutató: az egyéni parancs paramétereinek érvényesítése'
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
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857188"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Útmutató: az egyéni parancsok paramétereinek megadása (előzetes verzió)

Ebben a cikkben az érvényességi paramétereket és a hibajavítási kéréseket fogja hozzáadni.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:

> [!div class="checklist"]
> * [Gyors útmutató: Egyéni parancs létrehozása](./quickstart-custom-speech-commands-create-new.md)
> * [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature-parancs létrehozása

Az érvényesítések bemutatásához hozzon létre egy új parancsot, amely lehetővé teszi a felhasználók számára a hőmérséklet beállítását.

1. A korábban létrehozott egyéni parancsok alkalmazás megnyitása a [Speech Studióban](https://speech.microsoft.com/)
1. Új parancs létrehozása`SetTemperature`
1. Adjon hozzá egy paramétert a célként megadott hőmérséklethez.

   | Paraméter konfigurálása           | Ajánlott érték    |Leírás                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Name              | Hőmérséklet                       | A paraméter leíró neve                                |
   | Kötelező          | ellenőrizni                           | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
   | A kötelező paraméterre adott válasz     | Egyszerű szerkesztő – > milyen hőmérsékletet szeretne?  | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
   | Típus              | Szám                            | Paraméter típusa (például szám, karakterlánc, dátum vagy földrajzi idő)   |

1. Adja meg a hőmérséklet paraméter érvényesítését.

    - A `Temperature` `Add a validation` paraméter **Paraméterek** konfigurálása lapján válassza az érvényesítések szakaszt.
    - Adja meg az **új érvényesítési** előugró ablakban látható értékeket az alábbiak szerint, majd válassza a **Létrehozás**lehetőséget.

  
       | Paraméter konfigurálása         | Ajánlott érték                                          | Leírás                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Minimális érték        | 60               | A szám paraméternél a paraméter által feltételezhető minimális érték |
       | Maximális érték        | 80               | A szám paraméternél a paraméter által feltételezhető maximális érték |
       | Sikertelen válasz – egyszerű szerkesztő| Első változat – sajnos csak 60 és 80 fok között lehet beállítani      | Új érték kérése, ha az érvényesítés sikertelen                                       |

       > [!div class="mx-imgBorder"]
       > ![Tartomány érvényesítésének hozzáadása](media/custom-speech-commands/validations-add-temperature.png)

1. Néhány példa mondatok hozzáadása

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Befejezési szabály hozzáadása az eredmény megerősítéséhez

   | Beállítás    | Ajánlott érték                                           |Leírás                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Megerősítő üzenet                                      |A szabály célját leíró név |
   | Feltételek | Kötelező paraméterek –`Temperature`                       |Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek    | Beszédfelismerési válasz küldése –`Ok, setting temperature to {Temperature} degrees` | A szabály feltételének teljesülésekor végrehajtandó művelet |

> [!TIP]
> Ez a példa egy beszédfelismerési választ használ az eredmény megerősítéséhez. Példák a parancs ügyfél-művelettel való végrehajtásához: útmutató: az [ügyfél parancsainak teljesítése a SPEECH SDK-val](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Próba
1. Válassza `Train` a jobb oldali ablaktábla tetején található ikont.

1. Miután a betanítás befejeződik, `Test` válasszon ki néhány interakciót, és próbálkozzon újra.

    - Bemenet: a hőmérséklet beállítása 72 fokos értékre
    - Kimenet: ok, hőmérséklet beállítása 72 fok
    - Bemenet: a hőmérséklet beállítása 45 fokos értékre
    - Kimenet: sajnos csak 60 és 80 fok közötti érték adható meg
    - Bemenet: tegyük 72 fok helyett
    - Kimenet: ok, hőmérséklet beállítása 72 fok

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md)
