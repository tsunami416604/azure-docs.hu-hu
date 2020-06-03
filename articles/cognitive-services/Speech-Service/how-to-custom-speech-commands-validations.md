---
title: Érvényesítések hozzáadása az egyéni parancsok előnézetében – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan adhat hozzá érvényesítést egy parancs-paraméterhez egyéni parancsok előnézeti alkalmazásban.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310410"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Érvényesítések hozzáadása a parancs paraméteréhez egyéni parancsok előnézeti alkalmazásban

Ebből a cikkből megtudhatja, hogyan adhat hozzá érvényességi paramétereket a paraméterekhez, és kérheti a javítást.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő cikkekben ismertetett lépéseket:

> [!div class="checklist"]
 
> * [Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása](./quickstart-custom-speech-commands-create-new.md)
> * [Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása paraméterekkel](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature-parancs létrehozása

Az érvényesítések bemutatásához hozzon létre egy új parancsot, amely lehetővé teszi a felhasználók számára a hőmérséklet beállítását.

1. A [Speech Studióban](https://speech.microsoft.com/)nyissa meg az egyéni parancsok előnézeti alkalmazást, amelyet Ön hozott létre.
1. Hozzon létre egy új **SetTemperature** -parancsot.
1. Adjon hozzá egy hőmérséklet-paramétert, amely a következő konfigurációval rendelkezik:

   | Paraméter konfigurálása           | Ajánlott érték    |Leírás                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Name (Név)**              | **Hőmérséklet**                       | A paraméter leíró neve                                |
   | **Szükséges**          | Bejelölve                           | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
   | **A kötelező paraméterre adott válasz**     | **Egyszerű szerkesztő – > milyen hőmérsékletet szeretne?**  | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
   | **Típus**              | **Szám**                            | Paraméter típusa (például number, string, DateTime vagy földrajz)   |

1. Adja meg a hőmérséklet paraméter érvényesítését.

    1. A hőmérséklet paraméter **Paraméterek** konfigurálása lapján válassza az **Érvényesítés hozzáadása** lehetőséget az **érvényességek** szakaszban.

    1. Az **új érvényesítés** előugró ablakban konfigurálja az érvényesítést az alábbiak szerint:
  
       | Paraméter konfigurálása         | Ajánlott érték                                          | Leírás                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Minimális érték**        | **60**               | A szám paraméternél a paraméter által feltételezhető minimális érték |
       | **Maximális érték**        | **80**               | A szám paraméternél a paraméter által feltételezhető maximális érték |
       | **Sikertelen válasz – egyszerű szerkesztő**| **Első változat – sajnos csak 60 és 80 fok között lehet beállítani**      | Új érték kérése, ha az érvényesítés sikertelen                                       |

       > [!div class="mx-imgBorder"]
       > ![Tartomány érvényesítésének hozzáadása](media/custom-speech-commands/validations-add-temperature.png)

1. Kattintson a **Létrehozás** gombra.

1. Vegyen fel néhány példát a mondatokra.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adjon hozzá egy befejezési szabályt, amely a következő konfigurációval rendelkezik. Ez a szabály megerősíti az eredményt.

   | Beállítás    | Ajánlott érték                                           |Leírás                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Megerősítő üzenet                                      |A szabály célját leíró név |
   | **Feltételek** | **Szükséges paraméterek – hőmérséklet**                       |Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | **Műveletek**    | **Beszédfelismerési válasz küldése – ok, hőmérséklet beállítása {hőmérséklet} fok értékre** | A szabály feltételének teljesülésekor végrehajtandó művelet |

> [!TIP]
> Ez a példa egy beszédfelismerési választ használ az eredmény megerősítéséhez. A parancs ügyfél-művelettel való végrehajtásával kapcsolatos példákért tekintse meg a következő témakört [: Hogyan lehet parancsokat teljesíteni az ügyfélen a SPEECH SDK-val](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Próba

1. Válassza a **betanítás**lehetőséget.

1. A betanítás után válassza a **teszt**lehetőséget, majd próbálja meg ezeket az interakciókat:

    - Bemenet: a hőmérséklet beállítása 72 fokos értékre
    - Kimenet: ok, hőmérséklet beállítása 72 fok
    - Bemenet: a hőmérséklet beállítása 45 fokos értékre
    - Kimenet: sajnos csak 60 és 80 fok közötti érték adható meg
    - Bemenet: tegyük 72 fok helyett
    - Kimenet: ok, hőmérséklet beállítása 72 fok

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megerősítés hozzáadása egy parancshoz egyéni parancsok előzetes verziójának alkalmazásában](./how-to-custom-speech-commands-confirmations.md)
