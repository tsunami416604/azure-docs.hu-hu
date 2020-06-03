---
title: Megerősítések hozzáadása egyéni parancsokban az App-Speech szolgáltatás előzetes verziója
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan adhat hozzá jóváhagyásokat a parancsokhoz egyéni parancsok előzetes verziójának alkalmazásában.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310468"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Megerősítés hozzáadása egy parancshoz egyéni parancsok előnézeti alkalmazásban

Ebből a cikkből megtudhatja, hogyan hozhat létre megerősítést a parancsokhoz egyéni parancsok előzetes verziójának alkalmazásában.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő cikkekben ismertetett lépéseket:
> [!div class="checklist"]
> * [Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása](./quickstart-custom-speech-commands-create-new.md)
> * [Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása paraméterekkel](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>SetAlarm-parancs létrehozása

A visszaigazolások megjelenítéséhez hozzon létre egy új parancsot, amely riasztást állít be.

1. A [Speech Studióban](https://speech.microsoft.com/)nyissa meg az egyéni parancsok előnézeti alkalmazást, amelyet Ön hozott létre.
1. Hozzon létre egy új **SetAlarm** -parancsot.
1. Adjon hozzá egy **datetime** paramétert, amely a következő konfigurációval rendelkezik:

   | Beállítás                           | Ajánlott érték                     |  Leírás                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Name (Név)**                              | **Dátum/idő**                                | A paraméter leíró neve                                |
   | **Szükséges**                          | Bejelölve                                 | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
   | **A kötelező paraméterre adott válasz**   | **Egyszerű szerkesztő – > milyen időt?**                              | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
   | **Típus**                              | **Dátum/idő**                                | Paraméter típusa (például number, string, DateTime vagy földrajz)   |
   | **Dátum alapértelmezett értékei**                     | Ha a dátum hiányzik, használja a mai dátumot            | A felhasználó által nem biztosított változó alapértelmezett értéke  |  
   | **Idő alapértelmezett értékei**                     | Ha az idő hiányzik, használja a nap elejét     |  A változó alapértelmezett értéke, amelyet a felhasználó nem biztosít|

1. Vegyen fel néhány példát a mondatokra.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adja hozzá a befejezési szabályt az eredmény megerősítéséhez. Használja a következő konfigurációt:

   | Beállítás    | Ajánlott érték                               |Leírás                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Szabály neve**  | **Riasztás beállítása**                                               |    A szabály célját leíró név |
   | **Műveletek**    | **Beszédfelismerési válasz küldése – > OK, riasztás beállítva a következőhöz: {DateTime}**    |A szabály feltételének teljesülésekor végrehajtandó művelet

## <a name="try-it-out"></a>Próba

1. A jobb oldali ablaktábla tetején válassza a **betanítás** lehetőséget.

1. A képzés befejezése után válassza a **teszt**lehetőséget, majd próbálja ki a következő interakciókat:
    - Bemenet: riasztás beállítása holnap délben
    - Kimenet: OK, riasztás beállítása 2020-05-02 12:00:00
    - Bemenet: riasztás beállítása
    - Kimenet: mennyi idő?
    - Bemenet: 5
    - Kimenet: OK, riasztás beállítása 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Kapcsolatitevékenység-szabályok hozzáadása a megerősítéshez

Megerősítések létrehozása interakciós szabályok hozzáadásával.

1. A **SetAlarm** parancsban válassza a középső ablaktábla **Hozzáadás** elemét, majd válassza a **Kapcsolatitevékenység-szabályok**  >  **megerősítése parancsot**.

    Ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját. Használja ezeket a beállításokat:

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Szabály neve**             | **Dátum és idő megerősítése**                                                                | A szabály célját leíró név          |
   | **Feltételek**            | **Kötelező paraméter – > DateTime**                                                    | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | **Műveletek**               | **Beszédfelismerési válasz küldése – > biztosan beállít egy riasztást a következőhöz: {DateTime}?**     | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | **Elvárások**          | **A rendszer megerősítést vár a felhasználótól**                                                 | Várakozás a következő bekapcsolásra                      |
   | **Végrehajtás utáni állapot**  | **Várakozás a felhasználó bemenetére**                                                            | A felhasználó állapota a turn után                  |
  
      > [!div class="mx-imgBorder"]
      > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-validation-set-temperature.png)

1. Egy elfogadott jóváhagyáshoz tartozó interakciós szabály hozzáadása (a felhasználó az "igen" értéket mondta). Használja a következő konfigurációt:

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Szabály neve**             | **Jóváhagyás elfogadva**                                                            | A szabály célját leíró név          |
   | **Feltételek**            | **A megerősítés sikeres volt & kötelező paraméter-> DateTime**                      | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | **Végrehajtás utáni állapot** | **Befejezési szabályok végrehajtása**                                                          | A felhasználó állapota a turn után                   |

1. Egy megtagadott jóváhagyáshoz tartozó kapcsolatitevékenység-szabály hozzáadása (a felhasználó "nem"). Használja a következő konfigurációt:

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Szabály neve**             | **Megtagadási megerősítés**                                                                   | A szabály célját leíró név          |
   | **Feltételek**            | **A megerősítés megtagadva & kötelező paraméter-> DateTime**                               | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | **Műveletek**               | **Paraméter értékének törlése – > DateTime & a beszédfelismerési válasz küldése – > nincs probléma, milyen időt?**  | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | **Végrehajtás utáni állapot** | **Várakozás a bevitelre**                                                                   | A felhasználó állapota a turn után                   |
   | **Elvárások**          | **A rendszer paramétereket várt a felhasználótól > DateTime**                           | Várakozás a következő bekapcsolásra                      |

## <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

1. Válassza a **betanítás**lehetőséget.

1. A képzés befejezése után válassza a **test (teszt**) lehetőséget, majd próbálja meg ezeket az interakciókat:

    - Bemenet: riasztás beállítása holnap délben
    - Kimenet: biztos, hogy 2020-05-02 12:00:00-as riasztást szeretne beállítani?
    - Bemenet: nem
    - Kimenet: nincs probléma, milyen időt?
    - Bemenet: 5
    - Kimenet: "biztos, hogy 2020-05-01 17:00:00-as riasztást szeretne beállítani?"
    - Bemenet: igen
    - Kimenet: OK, riasztás beállítása 2020-05-01 17:00:00

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy lépésből álló javítás hozzáadása egy egyéni parancsok előzetes verziójának alkalmazásához](./how-to-custom-speech-commands-one-step-correction.md)
